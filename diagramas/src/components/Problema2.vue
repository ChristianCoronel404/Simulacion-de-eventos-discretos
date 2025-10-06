<template>
    <div class="app-container">
  
        <!-- Columna izquierda: parámetros de la simulación -->
        <div class="column input-column">
            <h1>Juego de Dados</h1>
            <p>
                El apostador lanza 2 dados y si la suma es 7 gana, de lo contrario pierde.
            </p>
  
            <h2>Parámetros de la simulación</h2>
            <div class="form-group">
                <label>Número de simulaciones:</label>
                <input type="number" v-model.number="numSimulaciones" min="1" />
            </div>

            <div class="form-group">
                <label>Número de juegos (Juegos):</label>
                <input type="number" v-model.number="numJuegos" min="1" />
            </div>
  
            <div class="form-group">
                <label>Costo del juego (Bs):</label>
                <input type="number" v-model.number="costoJuego" min="0" />
            </div>
  
            <div class="form-group">
                <label>Pérdida de la casa si el jugador gana (Bs):</label>
                <input type="number" v-model.number="perdidaCasa" min="0" />
            </div>
  
            <button @click="simularJuego">Simular</button>
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
                            <th>GNETA (Bs)</th>
                            <th>NJGC</th>
                            <th>PJC (%)</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(r, index) in resultados" :key="index">
                            <td>{{ index + 1 }}</td>
                            <td>{{ r.ganancia }}</td>
                            <td>{{ r.juegosGanados }}</td>
                            <td>{{ r.porcentaje.toFixed(2) }}</td>
                        </tr>
                    </tbody>
                </table>
                <p v-else class="text-center-message">
                    Ingresa los parámetros y haz click en "Simular".
                </p>
            </div>
  
            <!-- Promedios de resultados -->
            <div v-if="resultados.length" class="totals">
                <h3>Promedio de ganancia neta: {{ promedioGanancia.toFixed(2) }} Bs</h3>
                <h3>Promedio de juegos ganados por la casa: {{ promedioJuegosGanados.toFixed(2) }}</h3>
                <h3>Promedio de porcentaje de juegos ganados por la casa: {{ promedioPorcentaje.toFixed(2) }}%</h3>
            </div>
        </div>
    </div>
  </template>
  
  <script>
  export default {
    name: "Problema2",
    data() {
        return {
            numJuegos: 10,
            numSimulaciones: 5,
            costoJuego: 2,
            perdidaCasa: 5,
            resultados: [],
            errorMessage: ''
        };
    },
    computed: {
        promedioGanancia() {
            if (!this.resultados.length) return 0;
            return this.resultados.reduce((sum, r) => sum + r.ganancia, 0) / this.resultados.length;
        },
        promedioJuegosGanados() {
            if (!this.resultados.length) return 0;
            return this.resultados.reduce((sum, r) => sum + r.juegosGanados, 0) / this.resultados.length;
        },
        promedioPorcentaje() {
            if (!this.resultados.length) return 0;
            return this.resultados.reduce((sum, r) => sum + r.porcentaje, 0) / this.resultados.length;
        }
    },
    methods: {
        simularJuego() {
            if (this.numJuegos < 1 || this.numSimulaciones < 1 || this.costoJuego < 0 || this.perdidaCasa < 0) {
                this.errorMessage = "Todos los valores deben ser válidos y mayores o iguales a cero.";
                return;
            }
            this.errorMessage = '';
            this.resultados = [];
  
            for (let s = 0; s < this.numSimulaciones; s++) {
                let gananciaCasa = 0;
                let juegosGanadosCasa = 0;
  
                for (let i = 0; i < this.numJuegos; i++) {
                    gananciaCasa += this.costoJuego;
  
                    const dado1 = Math.floor(Math.random() * 6) + 1;
                    const dado2 = Math.floor(Math.random() * 6) + 1;
                    const suma = dado1 + dado2;
  
                    if (suma === 7) {
                        gananciaCasa -= this.perdidaCasa;
                    } else {
                        juegosGanadosCasa++;
                    }
                }
  
                const porcentaje = (juegosGanadosCasa / this.numJuegos) * 100;
                this.resultados.push({
                    ganancia: gananciaCasa,
                    juegosGanados: juegosGanadosCasa,
                    porcentaje
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
  