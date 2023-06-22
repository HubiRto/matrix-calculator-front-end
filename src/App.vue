<script setup></script>

<template>
  <div>
    <h1>Matrix Calculator</h1>
    <div>
      <label for="operation">Operation:</label>
      <select id="operation" v-model="selectedOperation">
        <option value="add">Add</option>
        <option value="subtract">Subtract</option>
        <option value="multiply">Multiply</option>
      </select>
    </div>
    <div>
      <label for="matrixARows">Matrix A Rows:</label>
      <input id="matrixARows" type="number" v-model="matrixARows" />
    </div>
    <div>
      <label for="matrixAColumns">Matrix A Columns:</label>
      <input id="matrixAColumns" type="number" v-model="matrixAColumns" />
    </div>
    <div>
      <label for="matrixBRows">Matrix B Rows:</label>
      <input id="matrixBRows" type="number" v-model="matrixBRows" />
    </div>
    <div>
      <label for="matrixBColumns">Matrix B Columns:</label>
      <input id="matrixBColumns" type="number" v-model="matrixBColumns" />
    </div>
    <div>
      <h2>Matrix A:</h2>
      <table>
        <tbody>
        <tr v-for="(row, rowIndex) in matrixA" :key="rowIndex">
          <td v-for="(cell, colIndex) in row" :key="colIndex">
            <input type="number" v-model="matrixA[rowIndex][colIndex]" />
          </td>
        </tr>
        </tbody>
      </table>
    </div>
    <div>
      <h2>Matrix B:</h2>
      <table>
        <tbody>
        <tr v-for="(row, rowIndex) in matrixB" :key="rowIndex">
          <td v-for="(cell, colIndex) in row" :key="colIndex">
            <input type="number" v-model="matrixB[rowIndex][colIndex]" />
          </td>
        </tr>
        </tbody>
      </table>
    </div>
    <button @click="calculate">Calculate</button>
    <div>
      <h2>Result:</h2>
      <table>
        <tbody>
        <tr v-for="(row, rowIndex) in result" :key="rowIndex">
          <td v-for="(cell, colIndex) in row" :key="colIndex">
            <input type="number" v-model="result[rowIndex][colIndex]" readonly />
          </td>
        </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedOperation: 'add',
      matrixARows: 2,
      matrixAColumns: 2,
      matrixBRows: 2,
      matrixBColumns: 2,
      matrixA: [],
      matrixB: [],
      result: []
    };
  },
  methods: {
    initializeMatrices() {
      this.matrixA = Array.from({ length: this.matrixARows }, () =>
          Array.from({ length: this.matrixAColumns }, () => 0)
      );
      this.matrixB = Array.from({ length: this.matrixBRows }, () =>
          Array.from({ length: this.matrixBColumns }, () => 0)
      );
      this.result = Array.from({ length: this.matrixARows }, () =>
          Array.from({ length: this.matrixBColumns }, () => 0)
      );
    },
    calculate() {
      // Wyślij żądanie do backendu (Spring Boot) z operacją i macierzami A i B
      fetch('http://localhost:8080/matrix', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          operation: this.selectedOperation,
          matrixA: this.matrixA,
          matrixB: this.matrixB
        })
      })
          .then(response => response.json())
          .then(data => {
            // Ustaw wynik w polu tekstowym
            this.result = data.result;
          })
          .catch(error => {
            console.error('Error:', error);
          });
    }
  },
  watch: {
    matrixARows() {
      this.initializeMatrices();
    },
    matrixAColumns() {
      this.initializeMatrices();
    },
    matrixBRows() {
      this.initializeMatrices();
    },
    matrixBColumns() {
      this.initializeMatrices();
    }
  },
  mounted() {
    this.initializeMatrices();
  }
};
</script>

<style scoped>
table {
  border-collapse: collapse;
}

td {
  padding: 5px;
  border: 1px solid black;
}
</style>
