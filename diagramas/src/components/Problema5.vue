<template>
    <div class="app-container">

        <!-- Columna izquierda: parámetros de simulación -->
        <div class="column input-column">
            <h1>Simulación Inventario de Azúcar</h1>
            <p>
                La demanda diaria sigue distribución exponencial. <br>
                El pedido se realiza cada 7 días y la entrega es uniforme.
            </p>

            <h2>Parámetros de la simulación</h2>

            <div class="form-group">
                <label>Número de simulaciones:</label>
                <input type="number" v-model.number="numSimulaciones" min="1" />
            </div>

            <div class="form-group">
                <label>Número de días (días):</label>
                <input type="number" v-model.number="numDias" min="1" />
            </div>

            <div class="form-group">
                <label>Media demanda diaria (Kg/día):</label>
                <input type="number" v-model.number="mediaDemanda" min="1" step="0.1" />
            </div>

            <div class="form-group">
                <label>Capacidad bodega (Kg):</label>
                <input type="number" v-model.number="capacidadBodega" min="1" />
            </div>

            <div class="form-group">
                <label>Días entrega (mínimo y máximo):</label>
                <input type="number" v-model.number="diasEntregaMin" min="1" style="width:45%; display:inline-block;" />
                <span style="display:inline-block; width:10%; text-align:center;">-</span>
                <input type="number" v-model.number="diasEntregaMax" min="1" style="width:45%; display:inline-block;" />
            </div>

            <div class="form-group">
                <label>Costo ordenar (Bs/orden):</label>
                <input type="number" v-model.number="costoOrden" min="0" step="0.01" />
            </div>

            <div class="form-group">
                <label>Costo inventario (Bs/Kg/día):</label>
                <input type="number" v-model.number="costoInventario" min="0" step="0.01" />
            </div>

            <div class="form-group">
                <label>Costo adquisición (Bs/Kg):</label>
                <input type="number" v-model.number="costoAdquisicion" min="0" step="0.01" />
            </div>

            <div class="form-group">
                <label>Precio venta (Bs/Kg):</label>
                <input type="number" v-model.number="precioVenta" min="0" step="0.01" />
            </div>

            <button @click="simularInventario">Simular</button>
            <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
        </div>

        <!-- Columna derecha: resultados -->
        <div class="column result-column">
            <h2>Resultados por simulación</h2>
            <div class="table-container">
                <table v-if="resultados.length">
                    <thead>
                        <tr>
                            <th>Simulación</th>
                            <th>Costo Total (Bs)</th>
                            <th>Ingreso Total (Bs)</th>
                            <th>Ganancia Neta (Bs)</th>
                            <th>Demanda Generada (Kg)</th>
                            <th>Demanda Insatisfecha (Kg)</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(r, index) in resultados" :key="index">
                            <td>{{ index + 1 }}</td>
                            <td>{{ r.costoTotal.toFixed(2) }}</td>
                            <td>{{ r.ingresoTotal.toFixed(2) }}</td>
                            <td>{{ r.gananciaNeta.toFixed(2) }}</td>
                            <td>{{ r.demandaTotal }}</td>
                            <td>{{ r.demandaInsatisfecha }}</td>
                        </tr>
                    </tbody>
                </table>
                <p v-else class="text-center-message">
                    Ingresa los datos y haz click en "Simular".
                </p>
            </div>

            <div v-if="resultados.length" class="totals">
                <h3>Promedio costo total: {{ promedioCostoTotal.toFixed(2) }} Bs</h3>
                <h3>Promedio ingreso total: {{ promedioIngresoTotal.toFixed(2) }} Bs</h3>
                <h3>Promedio ganancia neta: {{ promedioGananciaNeta.toFixed(2) }} Bs</h3>
                <h3>Promedio demanda generada: {{ promedioDemandaTotal.toFixed(2) }} Kg</h3>
                <h3>Promedio demanda insatisfecha: {{ promedioDemandaInsatisfecha.toFixed(2) }} Kg</h3>
            </div>
        </div>

    </div>
</template>

<script>
export default {
    name: "Problema5",
    data() {
        return {
            numSimulaciones: 10,
            numDias: 27,
            mediaDemanda: 100,
            capacidadBodega: 700,
            diasEntregaMin: 1,
            diasEntregaMax: 3,
            costoOrden: 100,
            costoInventario: 0.1,
            costoAdquisicion: 3.5,
            precioVenta: 5,
            resultados: [],
            errorMessage: ''
        };
    },
    computed: {
        promedioCostoTotal() { return this.resultados.reduce((sum, r) => sum + r.costoTotal, 0) / this.resultados.length || 0; },
        promedioIngresoTotal() { return this.resultados.reduce((sum, r) => sum + r.ingresoTotal, 0) / this.resultados.length || 0; },
        promedioGananciaNeta() { return this.resultados.reduce((sum, r) => sum + r.gananciaNeta, 0) / this.resultados.length || 0; },
        promedioDemandaTotal() { return this.resultados.reduce((sum, r) => sum + r.demandaTotal, 0) / this.resultados.length || 0; },
        promedioDemandaInsatisfecha() { return this.resultados.reduce((sum, r) => sum + r.demandaInsatisfecha, 0) / this.resultados.length || 0; }
    },
    methods: {
        generarExponencial(lambda) {
            return -Math.log(1 - Math.random()) / lambda;
        },
        generarUniforme(min, max) {
            return Math.floor(Math.random() * (max - min + 1)) + min;
        },
        simularInventario() {
            if (this.numSimulaciones < 1 || this.numDias < 1 || this.mediaDemanda <= 0 || this.capacidadBodega <= 0 ||
                this.diasEntregaMin < 1 || this.diasEntregaMax < 1 || this.diasEntregaMax < this.diasEntregaMin ||
                this.costoOrden < 0 || this.costoInventario < 0 || this.costoAdquisicion < 0 || this.precioVenta < 0) {
                this.errorMessage = "Valores de entrada inválidos";
                return;
            }
            this.errorMessage = '';
            this.resultados = [];

            const PERIODO_REVISION = 7; 

            for (let s = 0; s < this.numSimulaciones; s++) {
                let inv = this.capacidadBodega;
                let cantPed = 0;
                let diaLleg = -1;

                let costoOrd = 0, costoMan = 0;
                // **Costo de adquisición inicial = capacidad de bodega * costo por kg**
                let costoAdq = this.capacidadBodega * this.costoAdquisicion;
                let ingrTot = 0;
                let demTot = 0, demInsat = 0;

                for (let dia = 1; dia <= this.numDias; dia++) {
                    let invIni = inv;

                    // Recepción pedido
                    if (dia == diaLleg) {
                        inv += cantPed;
                        if (inv > this.capacidadBodega) inv = this.capacidadBodega;
                        cantPed = 0;
                        diaLleg = -1;
                    }

                    // Demanda y venta
                    let demGen = Math.round(this.generarExponencial(1 / this.mediaDemanda));
                    demTot += demGen;
                    let demSat, demFal;

                    if (inv >= demGen) {
                        demSat = demGen;
                        demFal = 0;
                        inv -= demSat;
                    } else {
                        demSat = inv;
                        demFal = demGen - inv;
                        inv = 0;
                    }

                    demInsat += demFal;
                    ingrTot += demSat * this.precioVenta;

                    // Costo mantenimiento
                    let invProm = (invIni + inv) / 2;
                    costoMan += invProm * this.costoInventario;

                    // Orden de pedido (cada 7 días)
                    if (dia % PERIODO_REVISION == 0 && diaLleg == -1) {
                        let pedido = this.capacidadBodega - inv;
                        if (pedido > 0) {
                            let tEntrega = this.generarUniforme(this.diasEntregaMin, this.diasEntregaMax);
                            cantPed = pedido;
                            diaLleg = dia + tEntrega;
                            costoOrd += this.costoOrden;
                            costoAdq += pedido * this.costoAdquisicion;
                        }
                    }
                }

                let costoTot = costoOrd + costoMan + costoAdq;
                let gananciaNeta = ingrTot - costoTot;

                this.resultados.push({
                    costoTotal: costoTot,
                    ingresoTotal: ingrTot,
                    gananciaNeta,
                    demandaTotal: demTot,
                    demandaInsatisfecha: demInsat
                });
            }
        }
    }
};
</script>

<style scoped>
.app-container { display: flex; width: 100%; height: 100%; font-family: 'Inter', sans-serif; }
.column { flex: 1; display: flex; flex-direction: column; padding: 1.5rem; box-sizing: border-box; overflow-y: auto; }
.input-column { background: #d0f0c0; border-top-left-radius: 15px; border-bottom-left-radius: 15px; }
.result-column { background: #ffe0b2; border-top-right-radius: 15px; border-bottom-right-radius: 15px; }
h1 { color: #2e7d32; font-size: 1.8rem; margin-bottom: 0.5rem; }
h2 { font-size: 1.2rem; margin: 1rem 0 0.8rem 0; }
.form-group { margin-bottom: 1rem; width: 100%; }
label { font-weight: bold; display: block; margin-bottom: 0.3rem; }
input { padding: 0.6rem; border-radius: 8px; border: 1px solid #a8a8a8; width: 100%; font-size: 1rem; }
button { padding: 0.8rem 1.2rem; background-color: #2e7d32; color: white; border: none; border-radius: 10px; cursor: pointer; font-weight: bold; width: 100%; margin-top: 0.8rem; }
button:hover { background-color: #1b5e20; }
.error { color: red; margin-top: 0.5rem; }
.table-container { flex-grow: 1; overflow-y: auto; margin-top: 1rem; }
table { width: 100%; border-collapse: collapse; font-size: 0.9rem; border: 1px solid #ccc; }
th, td { padding: 0.6rem; text-align: center; border: 1px solid #ccc; background-color: white; }
th { background-color: #2e7d32; color: white; }
.totals { margin-top: 1.5rem; border-top: 2px solid #aed581; padding-top: 0.8rem; }
.totals h3 { font-size: 1rem; margin: 0.5rem 0; }
</style>
