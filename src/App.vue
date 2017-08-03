<template>
  <div id="app">
    <!--<img src="./assets/logo.png">-->
    <h1>{{ msg }}</h1>
    <div id="loan-content">
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
        <canvas width="300" height="300"></canvas>
      </div>
    </div>
  </div>
</template>

<script>
import * as d3 from 'd3'

export default {
  name: 'app',
  data () {
    return {
      msg: 'Simple Loan Calculator'
    }
  },
  methods: {
    calculate: function (event) {
      var homePrice = Number(document.getElementById("home-price").value),
          downPayment = Number(document.getElementById("down-payment").value),
          interestRate = Number(document.getElementById("interest-rate").value),
          loanTermMonth = Number(document.getElementById("loan-term-month").value)

      var loanAmount = this.getLoanAmount(homePrice, downPayment),
          monthlyPayment = Math.round(this.getMonthlyPayment(loanAmount, loanTermMonth, interestRate)),
          monthlyTaxes = Math.round(this.getMonthlyTaxes(homePrice)),
          monthlyInsurance = Math.round(this.getMonthlyInsurance())

      this.drawPieChart(monthlyPayment, monthlyTaxes, monthlyInsurance)
    },
    getMonthlyInsurance: function() {
      var annualInsurance = 800
      return annualInsurance / 12
    },
    getMonthlyTaxes: function(homePrice) {
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
    },
    createNewCanvas: function() {
      var loanResult = document.querySelector('#loan-result'),
          oldCanvas = document.querySelector('#loan-result canvas'),
          newCanvas = document.createElement('canvas')
      newCanvas.width = 300
      newCanvas.height = 300
      loanResult.replaceChild(newCanvas, oldCanvas)
      return newCanvas
    },
    // Donut chart - https://bl.ocks.org/mbostock/2394b23da1994fc202e1
    drawPieChart: function(payment, taxes, insurance) {
      var data = [
        {'text': 'P&I', 'amount': payment},
        {'text': 'Taxes', 'amount': taxes},
        {'text': 'insurance', 'amount': insurance}
      ]

      var canvas = this.createNewCanvas(),
          context = canvas.getContext("2d")

      var width = canvas.width,
          height = canvas.height,
          radius = Math.min(width, height) / 2

      context.translate(width / 2, height / 2)

      var pie = d3.pie()
                  .sort(null)
                  .value(function(d) {
                    return d.amount
                  }),
          arcs = pie(data),
          colors = ["#7EC0EE", "#FFB6C1", "#98FF98"]

      var arc = d3.arc()
                  .outerRadius(radius - 10)
                  .innerRadius(radius - 70)
                  .context(context)
      arcs.forEach(function(d, i) {
        context.beginPath()
        arc(d)
        context.fillStyle = colors[i]
        context.fill()
      });
      context.beginPath()
      arcs.forEach(arc)
      context.strokeStyle = "#fff"
      context.stroke()

      var labelArc = d3.arc()
                       .outerRadius(radius - 40)
                       .innerRadius(radius - 40)
                       .context(context)
      context.textAlign = "center"
      context.textBaseline = "middle"
      context.font="bold 12px Arial"
      context.fillStyle = "#000"
      arcs.forEach(function(d) {
        let c = labelArc.centroid(d)
        let displayData = d.data.text + ': ' + d.data.amount
        context.fillText(displayData, c[0], c[1])
      })
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

#loan-content {
  width: 530px;
  height: 330px;
  margin: auto;
  margin-top: 50px;

  #loan-input {
    width: 200px;
    height: 330px;
    display: inline;
    float: left;
    text-align: left;

    input {
      width: 90%;
      margin-bottom: 10px;
    }
  }

  #loan-result {
    width: 330px;
    height: 330px;
    display: inline;
    float: left;
  }
}

</style>
