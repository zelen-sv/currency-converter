<template>
  <div class="currency-converter" :class="{'currency-converter--loading': loading}">
    <div class="currency-converter__form">
      <div class="currency-converter__field">
        <input v-model.number="fromValue"
               @input="updateToValue"
               type="number"
               class="currency-converter__input">

        <currency-picker v-model="currencyFrom"
                         :currencies="currencies"
                         @change="updateFromValue" />
      </div>

      <div class="currency-converter__field">
        <input v-model.number="toValue"
               @input="updateFromValue"
               type="number"
               class="currency-converter__input">

        <currency-picker v-model="currencyTo"
                         :currencies="currencies"
                         @change="updateToValue" />
      </div>

      <button class="currency-converter__sync" @click="sync">
        Sync
      </button>
    </div>
  </div>
</template>

<script>
  import axios from 'axios';
  import CurrencyPicker from '@/components/CurrencyPicker.vue';

  const CURRENCIES_LIST = ['BTC','USD','EUR'],
        API_TOKEN = '34ec37bc31cfc816df251fb1e2a6b9f97506e53a1614fe455673e96b15941b3c',
        API_URL = 'https://min-api.cryptocompare.com/data/pricemulti',
        SYNC_TIME = 1000 * 60;

  export default {
    components: {
      CurrencyPicker
    },
    data () {
      return {
        currencies: CURRENCIES_LIST,
        currencyFrom: CURRENCIES_LIST[0],
        currencyTo: CURRENCIES_LIST[0],
        fromValue: 1,
        toValue: 1,
        currenciesApiData: null,
        loading: false,
        syncer: null
      }
    },
    created () {
      this.fetchCurrenciesData()
      this.syncer = setInterval(() => this.sync(), SYNC_TIME);
    },
    beforeDestroy() {
        clearInterval(this.syncer);
    },
    methods: {
      async fetchCurrenciesData () {
        const url = `${API_URL}?api_key=${API_TOKEN}&fsyms=${CURRENCIES_LIST}&tsyms=${CURRENCIES_LIST}`;
        this.loading = true;
        let { data } = await axios.get(url);
        this.currenciesApiData = data
        this.loading = false;
      },
      updateToValue () {
        this.toValue = this.fromValue * this.currenciesApiData[this.currencyFrom][this.currencyTo]
      },
      updateFromValue () {
        this.fromValue = this.toValue * this.currenciesApiData[this.currencyTo][this.currencyFrom]
      },
      async sync () {
        await this.fetchCurrenciesData();
        this.updateToValue();
      }
    }
  };
</script>

<style lang="scss">
  body {
    margin: 0;
    background-color: $dark;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    color: $white;
  }

  .currency-converter {
    background-color: $grey;
    border-radius: 4px;
    padding: 40px;
  }

  .currency-converter--loading {
    opacity: 0.5;
    cursor: progress;
    pointer-events: none;
  }

  .currency-converter__field {
    margin-bottom: 30px;
    display: flex;
    flex-wrap: wrap;
  }

  .currency-converter__input {
    font-style: normal;
    font-weight: 600;
    font-size: 16px;
    line-height: 20px;
    color: white;
    background-color: $dark;
    border-radius: 4px;
    border: none;
    padding: 10px 20px;
    box-sizing: border-box;
  }

  .currency-converter__sync {
    width: 100%;
    font-weight: 600;
    font-size: 16px;
    line-height: 20px;
    padding: 10px 20px;
    box-sizing: border-box;
    background-color: $orange;
    color: $white;
    border-radius: 4px;
    border: none;
    cursor: pointer;
    text-transform: uppercase;

    position: relative;
    bottom: 0;

    &:hover {
      bottom: 3px;
      transition: bottom 0.3s ease;
    }
  }
</style>
