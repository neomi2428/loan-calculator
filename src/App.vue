<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h1>{{ msg }}</h1>
    <h2>Essential Links</h2>
    <label for="home-price">Home price</label>
    <input type="text" id="home-price" />
    <label for="down-payment">Down payment</label>
    <input type="text" id="down-payment" />
    <label for="interest-rate">Interest rate</label>
    <input type="text" id="interest-rate" />
    <button v-on:click="calculate" type="submit">Calculate</button>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      msg: 'Loan Calculator'
    }
  },
  methods: {
    calculate: function (event) {
      console.log('calculate')
    }
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
