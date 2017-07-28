<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h1>{{ msg }}</h1>
    <label for="home-price">Home price</label>
    <input type="number" id="home-price" value="30000" />
    <label for="down-payment">Down payment</label>
    <input type="number" id="down-payment" value="6000" />
    <label for="interest-rate">Interest rate</label>
    <input type="number" id="interest-rate" value="4" />
    <button v-on:click="calculate" type="submit">Calculate</button>
    <div id="loan-result">
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      msg: 'Simple Loan Calculator'
    }
  },
  methods: {
    calculate: function (event) {
      var homePrice = document.getElementById("home-price").value
      var downPayment = document.getElementById("down-payment").value
      var interestRate = document.getElementById("interest-rate").value
      var loanAmount = this.getLoanAmount(homePrice, downPayment)


      document.getElementById("loan-result").innerHTML = "loan-result"
    },
    getLoanAmount: function(homePrice, downPayment) {
      homePrice = Number(homePrice)
      if (typeof homePrice !== "number" || isNaN(homePrice)) {
        console.log("A home price is not a number.")
        return;
      }
      downPayment = Number(downPayment)
      if (typeof downPayment !== "number" || isNaN(downPayment)) {
        console.log("A down payment is not a number.")
        return;
      }
      return homePrice - downPayment
    },
    getBalance: function (loanAmount, loanTermMonth, interestRate, monthlyPayment) {
      var balance = loanAmount
      for (var i = 0; i < loanTermMonth; ++i) {
        // add interest
        balance *= (1.0 + (interestRate / 12.0) / 100.0)
        balance -= monthlyPayment
      }
      return balance
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
