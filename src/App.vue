<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h1>{{ msg }}</h1>
    <div id="loan-input">
      <label for="home-price">Home price</label>
      <input type="number" id="home-price" value="300000" />
      <label for="down-payment">Down payment</label>
      <input type="number" id="down-payment" value="60000" />
      <label for="interest-rate">Interest rate</label>
      <input type="number" id="interest-rate" value="4.0" />
      <label for="loan-term-month">Loan term month</label>
      <input type="number" id="loan-term-month" value="360" />
      <button v-on:click="calculate" type="submit">Calculate</button>
    </div>
    <div id="loan-result">
      <div id="monthly-payment"></div>
      <div id="taxes"></div>
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
      var homePrice = Number(document.getElementById("home-price").value)
      var downPayment = Number(document.getElementById("down-payment").value)
      var interestRate = Number(document.getElementById("interest-rate").value)
      var loanTermMonth = Number(document.getElementById("loan-term-month").value)

      var loanAmount = this.getLoanAmount(homePrice, downPayment)
      var monthlyPayment = this.getMonthlyPayment(loanAmount, loanTermMonth, interestRate)

      document.getElementById("monthly-payment").innerHTML = Math.round(monthlyPayment)
      document.getElementById("taxes").innerHTML = Math.round(this.getTaxes(homePrice))
    },
    getTaxes: function(homePrice) {
      var annualPropertyTaxRate = 0.0058
      return homePrice * annualPropertyTaxRate / 12
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
    },
    getMonthlyPayment(loanAmount, loanTermMonth, interestRate) {
      var lo = 0
      var hi = loanAmount * (1.0 + (interestRate / 12.0) / 100.0)

      for (var i = 0; i < 100; ++i) {
        var mid = (lo + hi) / 2.0
        if (this.getBalance(loanAmount, loanTermMonth, interestRate, mid) <= 0) {
          hi = mid
        } else {
          lo = mid
        }
      }
      return hi
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

h1 {
  font-weight: normal;
}

#loan-input {
  width: 200px;
  height: 100%;
  display: inline-block;
  text-align: left;

  label {
    display: block;
  }

  input {
    width: 90%;
    margin-bottom: 10px;
  }
}

#loan-result {
  display: inline-block;
  width: 400px;
  height: 100%;
}

</style>
