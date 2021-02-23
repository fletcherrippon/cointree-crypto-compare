<template>
  <div class="w-100 border shadow-lg rounded-lg flex justify-center px-3 py-12 bg-white flex-col relative">
    <div>
      <div class="flex justify-center gap-1">
        <img 
          class="w-10 h-10 mt-auto mb-1 mr-4" 
          :src="`https://cdn.jsdelivr.net/gh/atomiclabs/cryptocurrency-icons@9ab8d6934b83a4aa8ae5e8711609a70ca0ab1b2b/svg/color/${preferredCoinValue}.svg`" 
          :alt="`${ preferredCoinValue } Icon`"
        >
        <div>
          <div v-if="lastCoinPriceValue && !isNaN(comparison)" class="flex gap-2">
            <span class="text-gray-600">{{ lastCoinPriceValue }}</span>
            <span :class="comparison >= 0 ? 'text-green-600' : 'text-red-600'">{{ comparison }}%</span>
          </div>
          <div v-else>
            <span class="text-gray-500">No previous coin data...</span>
          </div>
          <h1 class="font-light text-5xl">{{ coinData.ask }}</h1>
        </div>
        <span class="uppercase mt-auto font-semibold text-3xl text-gray-800">{{ preferredCoinValue }}</span>
        <div class="absolute right-0 h-full top-0 py-5 flex flex-col justify-around pr-24">
          <ul>
            <li v-for="(coin, key) of coins" class="py-2" :key="key">
              <span 
                @click="changeCoin(coin.name)" 
                :class="coin.name === preferredCoinValue ? `shadow-md text-white ${coin.brand}` : ''" 
                class="p-1 rounded uppercase click-me"
              >
              {{ coin.name }}
              </span>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'preferredCoin',
  data() {
    return {
      coins: [
        {
          name: 'btc',
          brand: 'bg-yellow-500'
        },
        {
          name: 'eth',
          brand: 'bg-purple-500'
        },
        {
          name: 'xrp',
          brand: 'bg-gray-800'
        }
      ],
      coinData: {},
      preferredCoinValue: localStorage.getItem('preferred-coin') || 'btc',
      lastCoinPriceValue: localStorage.getItem(`last-${this.preferredCoin}-price`)
    }
  },
  methods: {
    async changeCoin(coinName = this.preferredCoin) {
      try {
        // Get data from API
        const { data } = await axios.get(`https://trade.cointree.com/api/prices/aud/${coinName}`);

        this.lastCoinPrice = this.coinData.ask 
        // Store Preferred
        this.preferredCoin = coinName;

        this.lastCoinPriceValue = localStorage.getItem(`last-${this.preferredCoinValue}-price`)

        // Set coin to coin data
        this.coinData = data;
      } catch (e) {
        // Log error if something goes wrong
        console.error(e);
      }
    }
  },
  computed: {
    // Get preferred coin
    preferredCoin: {
      get: function() {
        return localStorage.getItem('preferred-coin') ? localStorage.getItem('preferred-coin') : 'btc';
      },
      set: function(coinName) {
        localStorage.setItem('preferred-coin', coinName)
        this.lastCoinPrice = this.coinData.ask
        this.preferredCoinValue = coinName
      }
    },
    // Get last value of preferred coin
    lastCoinPrice: {
      get: function () {
        return localStorage.getItem(`last-${this.preferredCoin}-price`)
      },
      set: function (price) {
        localStorage.setItem(`last-${this.preferredCoinValue}-price`, price)
        this.lastCoinPriceValue = price
      }
    },
    comparison() {
      return parseFloat((this.coinData.ask - this.lastCoinPriceValue)/this.lastCoinPriceValue * 100).toFixed(5)
    }
  },
  async beforeMount() {
    await this.changeCoin()
  },
  created() {
    // Listen for page to be unloaded and save the last loaded coin price
    window.addEventListener('beforeunload', () => localStorage.setItem(`last-${this.preferredCoinValue}-price`, this.coinData.ask));
  }
};
</script>