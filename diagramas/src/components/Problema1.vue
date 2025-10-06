<template>
  <div class="app-container">

    <!-- Columna izquierda: datos de entrada -->
    <div class="column input-column">
      <h1>Depósito a Plazo Fijo</h1>
      <p>Calcula el capital final y los intereses año por año según la tasa aplicable.</p>

      <h2>Parámetros de la Simulación</h2>
      <div class="form-group">
        <label>Capital inicial (Bs):</label>
        <input type="number" v-model.number="capital" min="0" />
      </div>

      <div class="form-group">
        <label>Tiempo de depósito (años):</label>
        <input type="number" v-model.number="tiempo" min="1" />
      </div>

      <p class="interest-rules">
        <strong>Reglas de interés:</strong><br>
        - 0 ≤ capital ≤ 10,000 → tasa = 3.5%<br>
        - 10,000 < capital ≤ 100,000 → tasa=3.7%<br>
          - capital > 100,000 → tasa = 4%
      </p>

      <button @click="calcular">Calcular</button>

      <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
    </div>

    <!-- Columna derecha: resultados -->
    <div class="column result-column">
      <h2>Resultados por año</h2>
      <div class="table-container">
        <table v-if="resultado.length">
          <thead>
            <tr>
              <th>Año</th>
              <th>Capital inicial</th>
              <th>Interés ganado</th>
              <th>Capital final</th>
              <th>Tasa aplicada</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(r, index) in resultado" :key="index">
              <td>{{ index + 1 }}</td>
              <td>{{ r.capitalInicial.toFixed(2) }}</td>
              <td>{{ r.interes.toFixed(2) }}</td>
              <td>{{ r.capitalFinal.toFixed(2) }}</td>
              <td>{{ (r.tasa * 100).toFixed(2) }}%</td>
            </tr>
          </tbody>
        </table>
        <p v-else class="text-center-message">
          Ingresa los datos y haz click en "Calcular".
        </p>
      </div>

      <div v-if="resultado.length" class="totals">
        <h3>Capital total: {{ capitalTotal.toFixed(2) }} Bs</h3>
        <h3>Interés total: {{ interesTotal.toFixed(2) }} Bs</h3>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Problema1",
  data() {
    return {
      capital: 0,
      tiempo: 1,
      resultado: [],
      errorMessage: ''
    };
  },
  computed: {
    capitalTotal() {
      return this.resultado.length
        ? this.resultado[this.resultado.length - 1].capitalFinal
        : 0;
    },
    interesTotal() {
      return this.resultado.reduce((acc, r) => acc + r.interes, 0);
    }
  },
  methods: {
    calcular() {
      if (this.capital < 0) {
        this.errorMessage = "El capital inicial debe ser mayor o igual a 0.";
        this.resultado = [];
        return;
      }
      if (this.tiempo < 1) {
        this.errorMessage = "El tiempo de depósito debe ser al menos 1 año.";
        this.resultado = [];
        return;
      }
      this.errorMessage = '';
      this.resultado = [];

      let k = this.capital;
      let t = this.tiempo;
      let c = 0;

      while (c < t) {
        let i = 0;
        if (k >= 0 && k <= 10000) i = 0.035;
        else if (k > 10000 && k <= 100000) i = 0.037;
        else i = 0.04;

        let I = k * i;
        let capitalFinal = k + I;

        this.resultado.push({
          capitalInicial: k,
          interes: I,
          capitalFinal: capitalFinal,
          tasa: i
        });

        k = capitalFinal;
        c++;
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

.interest-rules {
  font-size: 0.85rem;
  margin: 0.5rem 0 1rem 0;
  color: #333;
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
}

th,
td {
  padding: 0.6rem;
  text-align: right;
  border: 1px solid #ccc;
  background-color: white;
}

th {
  background-color: #2e7d32;
  color: white;
  text-align: center;
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
</style>
