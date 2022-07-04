<template>
  <div class="container">
    <div class="wrapper">
  <div class="title">
    <h1>
      💵 converter
    </h1>
  </div>

    <div class="select-container">
  <select class="select" @change="convert" v-model="selected[0]">
    <option v-for="country in countries" v-bind:value="country" :key="country">
      {{country}}
    </option>
  </select>
  <input class="select-container__input" @input="convert" v-model="inputted" type="number">
    </div>
    <div class="select-container">
      <div><ArrowDown /></div>
  <select class="select" @change="convert" v-model="selected[1]">
    <option v-for="country in countries" v-bind:value="country" :key="country">
      {{country}}
    </option>
  </select>
  <input class="select-container__input" v-model="result" type="number" readonly>
    </div>
  </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',
  data() {
    return {
      currencies: {},
      selected: ['RUB', 'USD'],
      inputted: "",
      result: null,
      countries: ['RUB'],
    }
  },

  methods: {
    convert() {
      let defaultCurrency = {
        Value: 1,
        Nominal: 1
      };

      let firstCurrency = this.currencies[this.selected[0]] ?? defaultCurrency,
        firstCurrencyValue = firstCurrency.Value * Number(this.inputted),
        firstCurrencyNominal = firstCurrency.Nominal;

      let secondCurrency = this.currencies[this.selected[1]] ?? defaultCurrency,
        secondCurrencyValue = secondCurrency.Value,
        secondCurrencyNominal = secondCurrency.Nominal;

      const result = (firstCurrencyValue / firstCurrencyNominal) / (secondCurrencyValue / secondCurrencyNominal);

      this.result = result ? Math.floor(result * 10000) / 10000 : null;
    }
  },

  mounted() {
    axios({
      method: 'get',
      url: 'https://www.cbr-xml-daily.ru/daily_json.js',
    }).then((response) => {
      this.currencies = response.data.Valute

      for (let code in this.currencies) {
        this.countries.push(code)
      }
    }).catch((error) => {
      console.log(error)
    })
  },
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Lato:wght@300;400;700&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Lato";
  background: #82AEF3;
  color: #3A3A3A;
}

.container {
  display: flex;
  flex-direction: column;
  margin: 0 100px;
  align-items: center;
}
.title {
  margin: 100px 0 40px 0;
  font-weight: 700;
}

.select-container {
  display: flex;
  flex-direction: row;
  margin-bottom: 20px;
}

.select-container:last-of-type {
  margin-bottom: 0;
}

.select {
  margin-right: 10px;
  border: none;
  border-bottom: 1px solid #747474;
}

.select:hover {
  cursor: pointer;
}

.select:focus {
  outline: 0;
}

.select-container__input {
  border: none;
  border-bottom: 1px solid #747474;
}

.select-container__input:focus {
  outline: 0;
}

.select-container__input:last-of-type:hover {
  cursor: auto;
}
</style>
