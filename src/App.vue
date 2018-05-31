<template>
  <v-app dark>
    <v-content>
      <v-container fluid fill-height>
        <v-layout align-center justify-center>
          <v-flex xs12 sm8 md4>
            <v-card class="elevation-12">
              <v-toolbar dark>
                <v-toolbar-title>
                  Amortization Calculator
                </v-toolbar-title>
              </v-toolbar>
              <v-card-text>
                <v-form>
                  <v-container grid-list-md>
                    <v-layout row wrap>
                      <v-flex xs12>
                        <v-text-field
                          label="Bid Price"
                          prefix="₱"
                          value="0"
                          v-model="price"
                        />
                      </v-flex>
                      <v-flex xs12 md4>
                        <v-text-field
                          label="Downpayment"
                          suffix="%"
                          v-model="downpaymentPct"
                        />
                      </v-flex>
                      <v-flex xs12 md4>
                        <v-text-field
                          label="Interest rate"
                          suffix="%"
                          v-model="interestRate"
                        />
                      </v-flex>
                      <v-flex xs12 md4>
                        <v-text-field
                          label="Payable in?"
                          v-model="payableInYears"
                          suffix="year(s)"
                        />
                      </v-flex>
                      <v-flex xs12 v-if="showMonthlyAmortization">
                        <p>Amortization Factor Rate: {{ this.amortizationFactorRate }}</p>
                        <p>Monthly Amortization: ₱ {{ monthlyAmortization | toCurrency }}</p>
                      </v-flex>
                    </v-layout>
                  </v-container>
                </v-form>
              </v-card-text>
            </v-card>
          </v-flex>
        </v-layout>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
export default {
  name: 'App',
  data () {
    return {
      price: '',
      downpaymentPct: '',
      payableInYears: '',
      interestRate: ''
    }
  },
  computed: {
    showMonthlyAmortization () {
      return this.price !== '' && this.downpaymentPct !== '' && this.payableInYears !== '' && this.interestRate !== ''
    },
    monthlyAmortization () {
      var downpaymentPrice = this.price * (this.downpaymentPct / 100)
      var netPrice = this.price - downpaymentPrice
      return netPrice * this.amortizationFactorRate
    },
    amortizationFactorRate () {
      // Formula is based here: http://gprrealty.weebly.com/realty-tips/how-to-compute-the-monthly-amortization
      let interest = (this.interestRate / 100) / 12
      let dividend = interest
      let termMonths = this.payableInYears * 12
      let divisor = 1 - Math.pow(1 + interest, -termMonths)
      return dividend / divisor
    }
  },
  filters: {
    toCurrency (value) {
      return value.toFixed(2).replace(/(\d)(?=(\d{3})+\.)/g, '$1,')
    }
  }
}
</script>
