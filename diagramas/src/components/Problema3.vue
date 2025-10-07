<template>
    <div class="app-container">

        <!-- Columna izquierda: parámetros de la simulación -->
        <div class="column input-column">
            <h1>Simulación de Tienda</h1>
            <p>
                Las llegadas de clientes siguen una distribución uniforme. <br>
                Cada cliente compra artículos según las probabilidades ingresadas.
            </p>

            <h2>Parámetros de la simulación</h2>

            <div class="form-group">
                <label>Número de simulaciones:</label>
                <input type="number" v-model.number="numSimulaciones" min="1" />
            </div>

            <div class="form-group">
                <label>Número de horas por día (horas):</label>
                <input type="number" v-model.number="numHoras" min="1" max="24" />
            </div>



            <div class="form-group">
                <label>Costo por día (Bs/día):</label>
                <input type="number" v-model.number="costoDia" min="0" />
            </div>

            <div class="form-group">
                <label>Costo por artículo (Bs/art):</label>
                <input type="number" v-model.number="costoArticulo" min="0" />
            </div>

            <div class="form-group">
                <label>Precio de venta por artículo (Bs/art):</label>
                <input type="number" v-model.number="precioVenta" min="0" />
            </div>

            <!-- Min/Max clientes en la misma fila -->
            <div class="form-group row-inputs">
                <div>
                    <label>Mínimo de clientes por hora:</label>
                    <input type="number" v-model.number="minClientes" min="0" />
                </div>
                <div>
                    <label>Máximo de clientes por hora:</label>
                    <input type="number" v-model.number="maxClientes" :min="minClientes" />
                </div>
            </div>

            <!-- Probabilidades como tabla horizontal -->
            <div class="form-group">
                <label>Probabilidades de compra por artículo (suma = 1):</label>
                <div class="probabilities-row">
                    <div>
                        <label>0 artículos:</label>
                        <input type="number" v-model.number="probArt0" min="0" max="1" step="0.01" />
                    </div>
                    <div>
                        <label>1 artículo:</label>
                        <input type="number" v-model.number="probArt1" min="0" max="1" step="0.01" />
                    </div>
                    <div>
                        <label>2 artículos:</label>
                        <input type="number" v-model.number="probArt2" min="0" max="1" step="0.01" />
                    </div>
                    <div>
                        <label>3 artículos:</label>
                        <input type="number" v-model.number="probArt3" min="0" max="1" step="0.01" />
                    </div>
                </div>
            </div>

            <button @click="simularTienda">Simular</button>
            <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
        </div>

        <!-- Columna derecha: resultados -->
        <div class="column result-column">
            <h2>Resultados de las simulaciones</h2>
            <div class="table-container">
                <table v-if="resultados.length">
                    <thead>
                        <tr>
                            <th># Simulación</th>
                            <th>Total artículos vendidos (TARTCC)</th>
                            <th>Ganancia neta (GNETA Bs)</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(r, index) in resultados" :key="index">
                            <td>{{ index + 1 }}</td>
                            <td>{{ r.totalArticulos }}</td>
                            <td>{{ r.gananciaNeta }}</td>
                        </tr>
                    </tbody>
                </table>
                <p v-else class="text-center-message">
                    Ingresa los parámetros y haz click en "Simular".
                </p>
            </div>

            <div v-if="resultados.length" class="totals">
                <h3>Promedio de artículos vendidos: {{ promedioArticulos.toFixed(2) }}</h3>
                <h3>Promedio de ganancia neta: {{ promedioGanancia.toFixed(2) }} Bs</h3>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "Problema3",
    data() {
        return {
            numHoras: 10,
            numSimulaciones: 5,
            costoDia: 300,
            costoArticulo: 50,
            precioVenta: 75,
            minClientes: 0,
            maxClientes: 4,
            probArt0: 0.2,
            probArt1: 0.3,
            probArt2: 0.4,
            probArt3: 0.1,
            resultados: [],
            errorMessage: ''
        };
    },
    computed: {
        promedioArticulos() {
            if (!this.resultados.length) return 0;
            const total = this.resultados.reduce((sum, r) => sum + r.totalArticulos, 0);
            return total / this.resultados.length;
        },
        promedioGanancia() {
            if (!this.resultados.length) return 0;
            const total = this.resultados.reduce((sum, r) => sum + r.gananciaNeta, 0);
            return total / this.resultados.length;
        }
    },
    methods: {
        generarNumeroAleatorio(min, max) {
            return Math.floor(Math.random() * (max - min + 1)) + min;
        },
        generarArticulosComprados() {
            const r = Math.random();
            if (r <= this.probArt0) return 0;
            else if (r <= this.probArt0 + this.probArt1) return 1;
            else if (r <= this.probArt0 + this.probArt1 + this.probArt2) return 2;
            else return 3;
        },
        simularTienda() {
            // Validaciones
            const sumaProb = this.probArt0 + this.probArt1 + this.probArt2 + this.probArt3;
            if (
                this.numHoras < 1 || 
                this.numHoras > 24 || // Validación adicional
                this.numSimulaciones < 1 || 
                this.costoDia < 0 || 
                this.costoArticulo < 0 || 
                this.precioVenta < 0 || 
                this.minClientes < 0 || 
                this.maxClientes < this.minClientes || 
                Math.abs(sumaProb - 1) > 0.01
            ) {
                this.errorMessage = "Verifica que todos los valores sean válidos y que las probabilidades sumen 1. Además, el número de horas no puede ser mayor a 24.";
                return;
            }
            this.errorMessage = '';
            this.resultados = [];

            for (let sim = 0; sim < this.numSimulaciones; sim++) {
                let totalArticulos = 0;
                let totalIngresos = 0;
                let totalCostos = this.costoDia;

                for (let h = 0; h < this.numHoras; h++) {
                    const clientes = this.generarNumeroAleatorio(this.minClientes, this.maxClientes);
                    for (let c = 0; c < clientes; c++) {
                        const artComprados = this.generarArticulosComprados();
                        totalArticulos += artComprados;
                        totalIngresos += artComprados * this.precioVenta;
                    }
                }

                totalCostos += totalArticulos * this.costoArticulo;
                const gananciaNeta = totalIngresos - totalCostos;

                this.resultados.push({
                    totalArticulos,
                    gananciaNeta
                });
            }
        }
    }
};
</script>

<style scoped>
.app-container {
    display: flex;
    width: 100%;
    height: 100%;
    font-family: 'Inter', sans-serif;
}

.column {
    flex: 1;
    display: flex;
    flex-direction: column;
    padding: 1.5rem;
    box-sizing: border-box;
    overflow-y: auto;
}

.input-column {
    background: #d0f0c0;
    border-top-left-radius: 15px;
    border-bottom-left-radius: 15px;
}

.result-column {
    background: #ffe0b2;
    border-top-right-radius: 15px;
    border-bottom-right-radius: 15px;
}

h1 {
    color: #2e7d32;
    font-size: 1.8rem;
    margin-bottom: 0.5rem;
}

h2 {
    font-size: 1.2rem;
    margin: 1rem 0 0.8rem 0;
}

.form-group {
    margin-bottom: 1rem;
    width: 100%;
}

label {
    font-weight: bold;
    display: block;
    margin-bottom: 0.3rem;
}

input {
    padding: 0.6rem;
    border-radius: 8px;
    border: 1px solid #a8a8a8;
    width: 100%;
    font-size: 1rem;
}

button {
    padding: 0.8rem 1.2rem;
    background-color: #2e7d32;
    color: white;
    border: none;
    border-radius: 10px;
    cursor: pointer;
    font-weight: bold;
    width: 100%;
    margin-top: 0.8rem;
}

button:hover {
    background-color: #1b5e20;
}

.error {
    color: red;
    margin-top: 0.5rem;
}

.table-container {
    flex-grow: 1;
    overflow-y: auto;
    margin-top: 1rem;
}

table {
    width: 100%;
    border-collapse: collapse;
    font-size: 0.9rem;
    border: 1px solid #ccc;
}

th,
td {
    padding: 0.6rem;
    text-align: center;
    border: 1px solid #ccc;
    background-color: white;
}

th {
    background-color: #2e7d32;
    color: white;
}

.totals {
    margin-top: 1.5rem;
    border-top: 2px solid #aed581;
    padding-top: 0.8rem;
}

.totals h3 {
    font-size: 1rem;
    margin: 0.5rem 0;
}

.probabilities-row {
    display: flex;
    gap: 1rem;
}

.probabilities-row>div {
    display: flex;
    flex-direction: column;
    flex: 1;
}

.row-inputs {
    display: flex;
    gap: 1rem;
}

.row-inputs>div {
    flex: 1;
    display: flex;
    flex-direction: column;
}
</style>