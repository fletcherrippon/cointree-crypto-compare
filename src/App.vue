<template>
  <div id="app">
    <div class="container min-h-screen flex flex-col justify-center">
      <CoinInfo/>
    </div>
  </div>
</template>

<script>
import CoinInfo from "./components/CoinInfo"

export default {
  name: 'App',
  components: {
    CoinInfo
  }
}
</script>
