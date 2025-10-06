<template>
    <div class="app-container">
  
      <!-- Columna izquierda: parámetros de la simulación -->
      <div class="column input-column">
        <h1>Simulación de Granero</h1>
        <p>
          La gallina pone huevos siguiendo una distribución de Poisson. <br>
          Se simula el destino de los huevos y pollos para calcular ingresos.
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
          <label>Precio venta huevo (Bs/huevo):</label>
          <input type="number" v-model.number="precioHuevo" min="0" step="0.01"/>
        </div>
  
        <div class="form-group">
          <label>Precio venta pollo (Bs/pollo):</label>
          <input type="number" v-model.number="precioPollo" min="0" step="0.01"/>
        </div>
  
        <div class="form-group">
          <label>Media huevos/día:</label>
          <input type="number" v-model.number="mediaHuevos" min="0" step="0.1"/>
        </div>
  
        <div class="form-group">
          <label>Destino de los huevos (sumar a 1)</label>
          <div class="probabilities-row">
            <div><label>Roto:</label><input type="number" v-model.number="probRoto" min="0" max="1" step="0.01"/></div>
            <div><label>Pollo:</label><input type="number" v-model.number="probPollo" min="0" max="1" step="0.01"/></div>
            <div><label>Huevo:</label><input type="number" v-model.number="probHuevo" min="0" max="1" step="0.01"/></div>
          </div>
        </div>
  
        <div class="form-group">
          <label>Destino del pollo (sumar a 1)</label>
          <div class="probabilities-row">
            <div><label>Muere:</label><input type="number" v-model.number="probMuere" min="0" max="1" step="0.01"/></div>
            <div><label>Sobrevive:</label><input type="number" v-model.number="probSobrevive" min="0" max="1" step="0.01"/></div>
          </div>
        </div>
  
        <button @click="simularGranja">Simular</button>
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
                <th>Ingreso bruto (Bs)</th>
                <th>Ingreso diario promedio (Bs/día)</th>
                <th>Huevos rotos</th>
                <th>Pollos muertos</th>
                
              </tr>
            </thead>
            <tbody>
              <tr v-for="(r, index) in resultados" :key="index">
                <td>{{ index + 1 }}</td>
                <td>{{ r.ingresoBruto.toFixed(2) }}</td>
                <td>{{ r.ingresoDiario.toFixed(2) }}</td>
                <td>{{ r.huevosRotos }}</td>
                <td>{{ r.pollosMuertos }}</td>
                
              </tr>
            </tbody>
          </table>
          <p v-else class="text-center-message">
            Ingresa los datos y haz click en "Simular".
          </p>
        </div>
  
        <div v-if="resultados.length" class="totals">
          <h3>Promedio ingreso bruto: {{ promedioIngresoBruto.toFixed(2) }} Bs</h3>
          <h3>Promedio ingreso diario: {{ promedioIngresoDiario.toFixed(2) }} Bs/día</h3>
          <h3>Promedio huevos rotos: {{ promedioHuevosRotos.toFixed(2) }}</h3>
          <h3>Promedio pollos muertos: {{ promedioPollosMuertos.toFixed(2) }}</h3>
          
        </div>
      </div>
  
    </div>
  </template>
  
  <script>
  export default {
    name: "Problema4",
    data() {
      return {
        numSimulaciones: 10,
        numDias: 30,
        precioHuevo: 1.5,
        precioPollo: 5,
        mediaHuevos: 1,
        probRoto: 0.2,
        probPollo: 0.3,
        probHuevo: 0.5,
        probMuere: 0.2,
        probSobrevive: 0.8,
        resultados: [],
        errorMessage: ''
      };
    },
    computed: {
      promedioIngresoBruto() {
        if (!this.resultados.length) return 0;
        return this.resultados.reduce((sum, r) => sum + r.ingresoBruto, 0) / this.resultados.length;
      },
      promedioIngresoDiario() {
        if (!this.resultados.length) return 0;
        return this.resultados.reduce((sum, r) => sum + r.ingresoDiario, 0) / this.resultados.length;
      },
      promedioHuevosRotos() {
        if (!this.resultados.length) return 0;
        return this.resultados.reduce((sum, r) => sum + r.huevosRotos, 0) / this.resultados.length;
      },
      promedioPollosMuertos() {
        if (!this.resultados.length) return 0;
        return this.resultados.reduce((sum, r) => sum + r.pollosMuertos, 0) / this.resultados.length;
      },
      promedioPollosSobreviven() {
        if (!this.resultados.length) return 0;
        return this.resultados.reduce((sum, r) => sum + r.pollosSobreviven, 0) / this.resultados.length;
      }
    },
    methods: {
      generarPoisson(lambda) {
        // Algoritmo Knuth para Poisson
        let L = Math.exp(-lambda);
        let k = 0;
        let p = 1;
        do {
          k++;
          p *= Math.random();
        } while (p > L);
        return k - 1;
      },
      simularGranja() {
        const sumaProbHuevos = this.probRoto + this.probPollo + this.probHuevo;
        const sumaProbPollos = this.probMuere + this.probSobrevive;
  
        if (this.numSimulaciones < 1 || this.numDias < 1 ||
            this.precioHuevo < 0 || this.precioPollo < 0 || this.mediaHuevos < 0 ||
            sumaProbHuevos.toFixed(2) != 1.00 || sumaProbPollos.toFixed(2) != 1.00) {
          this.errorMessage = "Valores inválidos o probabilidades no suman 1.";
          return;
        }
  
        this.errorMessage = '';
        this.resultados = [];
  
        for (let s = 0; s < this.numSimulaciones; s++) {
          let huevosRotos = 0;
          let huevosVendibles = 0;
          let pollosSobreviven = 0;
          let pollosMuertos = 0;
  
          for (let d = 0; d < this.numDias; d++) {
            const huevosHoy = this.generarPoisson(this.mediaHuevos);
  
            for (let h = 0; h < huevosHoy; h++) {
              const r = Math.random();
              if (r < this.probRoto) {
                huevosRotos++;
              } else if (r < this.probRoto + this.probPollo) {
                const rPollo = Math.random();
                if (rPollo < this.probMuere) pollosMuertos++;
                else pollosSobreviven++;
              } else {
                huevosVendibles++;
              }
            }
          }
  
          const ingresoBruto = huevosVendibles * this.precioHuevo + pollosSobreviven * this.precioPollo;
          const ingresoDiario = ingresoBruto / this.numDias;
  
          this.resultados.push({
            ingresoBruto,
            ingresoDiario,
            huevosRotos,
            pollosMuertos,
            pollosSobreviven
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
  
  .probabilities-row {
    display: flex;
    gap: 1rem;
  }
  
  .probabilities-row > div {
    display: flex;
    flex-direction: column;
    flex: 1;
  }
  </style>
  