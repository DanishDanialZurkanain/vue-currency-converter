<template>
  <div>
    <div class="d-flex align-items-center min-vh-100">
      <div class="container">
        <b-card header="Currency Converter">
          <b-alert
            v-if="fromRates != '' && toRates != ''"
            variant="warning"
            show
          >
            {{ fromRates.country }} {{ selectedFromRates }} =
            {{ toRates.country }}
            {{ selectedToRates }}
          </b-alert>
          <div class="row">
            <div class="col-5">
              <b-input-group>
                <template #prepend>
                  <b-form-select
                    @change="getRates"
                    v-model="fromRates"
                    :options="currency"
                    class="from-rates"
                    required
                  >
                  </b-form-select>
                </template>
                <b-form-input
                  id="number"
                  v-model="inputRates"
                  type="number"
                  required
                  placeholder="Enter Number"
                ></b-form-input>
              </b-input-group>
            </div>
            <div class="col-2 my-auto">
              <b-icon-arrow-left-right></b-icon-arrow-left-right>
            </div>
            <div class="col-5">
              <b-input-group>
                <template #prepend>
                  <b-form-select
                    @change="getRates"
                    v-model="toRates"
                    :options="currency"
                    class="to-rates"
                    required
                  >
                  </b-form-select>
                </template>
                <b-form-input
                  id="number"
                  :value="result"
                  type="number"
                  placeholder="Result"
                  disabled
                ></b-form-input>
              </b-input-group>
            </div>
          </div>
          <hr />

          <b-button @click="calculateRates" variant="outline-primary" block
            >Calculate</b-button
          >
          <div class="mt-3">
            <small class="text-muted"> Powered by Exchange Rates API </small>
          </div>
        </b-card>
      </div>
    </div>
  </div>
</template>

<script>
var axios = require("axios");
export default {
  data() {
    return {
      inputRates: "",
      currency: [
        {
          value: {
            country: "",
            rates: "",
          },
          text: "",
        },
      ],
      fromRates: "",
      toRates: "",
      selectedFromRates: "",
      selectedToRates: "",
      result: "",
    };
  },
  created() {
    this.loadRates();
  },
  methods: {
    loadRates() {
      axios
        .get("https://api.exchangeratesapi.io/latest")
        .then((res) => {
          for (var line in res.data.rates) {
            this.currency.push({
              value: {
                country: line,
                rates: res.data.rates[line],
              },
              text: line,
            });
          }
        })
        .catch((err) => console.log(err));
    },
    getRates() {
      axios
        .get(
          `https://api.exchangeratesapi.io/latest?base=${this.fromRates.country}`
        )
        .then((res) => {
          if (this.toRates.country != "") {
            this.selectedFromRates =
              res.data.rates[`${this.fromRates.country}`];
            this.selectedToRates = res.data.rates[`${this.toRates.country}`];
          }
        });
    },
    calculateRates() {
      if (this.inputRates != "") {
        this.result = (this.inputRates * this.selectedToRates).toFixed(2);
      } else {
        alert("⚠ Enter Some Number");
      }
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.from-rates {
  border-right: none;
  border-radius: 5px 0 0 5px;
}
.to-rates {
  border-right: none;
  border-radius: 5px 0 0 5px;
}
</style>
