<script>
import currencyService from "../api/currencyService.js";
import SelectCurrency from "./SelectCurrency.vue";
import InputFunds from "./InputFunds.vue";

export default {
  components: {InputFunds, SelectCurrency},
  data() {
    return {
      directions: [],
      fromCurrencies: [],
      toCurrencies: [],
      from: '',
      to: '',
      amountFrom: 0,
      amountTo: 0,
      selectedFrom: '',
      selectedTo: '',
      direction: {},
      isUpdatingFrom: false,
      isUpdatingTo: false
    };
  },
  methods: {
    searchDirections(from, to) {
      this.directions // не дуже мені подобається такий алгоритм (лінійний пошук), але пока не придумав краще
          .forEach( direction => {
            if (direction.from === from && direction.to === to) {
              this.direction = direction
            }
          })
    },
    rotateDirection() {
      const temp = this.from
      this.from = this.to
      this.to = temp

      this.searchDirections(this.from, this.to)

      this.amountFrom = 1
      this.amountTo = this.direction.out


    },
      onSelectCurrency({target}) {
          if (target.name === 'from') {
              this.from = target.value
          } else if (target.name === 'to') {
              this.to = target.value
          }
          this.searchDirections(this.from, this.to)
          this.amountFrom = 1
          this.amountTo = this.direction.out
      },
    calculateAmountTo() {
      if (this.direction && this.direction.out) {
        this.isUpdatingTo = true;
        this.amountTo = this.amountFrom * this.direction.out;
      }
    },
    calculateAmountFrom() {
      if (this.direction && this.direction.out) {
        this.isUpdatingFrom = true;
        this.amountFrom = this.amountTo / this.direction.out;
      }
    }
  },
  computed: {
    allowedFromCurrencies() {
      const allowedCurrencies = [];

      this.directions
          .forEach( direction => {
            if (!allowedCurrencies.includes(direction.from)) {
              allowedCurrencies.push(direction.from)
            }
          })

      return allowedCurrencies
    },
    allowedToCurrencies() {
      const allowedCurrencies = [];

      this.directions
          .forEach( direction => {
            if (!allowedCurrencies.includes(direction.to) && direction.from === this.from) {
              allowedCurrencies.push(direction.to)
            }
          })

      return allowedCurrencies
    }
  },
  watch: {
    amountFrom(newValue) {
      if (!this.isUpdatingFrom) {
        this.calculateAmountTo();
      }
      this.isUpdatingFrom = false;
    },
    amountTo(newValue) {
      if (!this.isUpdatingTo) {
        this.calculateAmountFrom();
      }
      this.isUpdatingTo = false;
    }
  },
  created() {
    try {
      this.directions = currencyService.getDirection();
      this.direction = this.directions[0]
      this.from = this.direction.from;
      this.to = this.direction.to;

      this.amountFrom = 1;
      this.amountTo = this.direction.out
    } catch (error) {
      console.error('Failed to fetch currencies:', error);
    }
  },

};
</script>

<template>
  <div class="banner" id="home">
    <div class="hero-area">
      <div class="container">
        <div class="row align-items-center justify-content-between">
          <div class="col-xl-12 col-lg-12 col-md-12 col-sm-12 col-xs-12 wow fadeInRightBig" data-wow-delay="0.3s" data-wow-duration="0.5s">
            <div class="exchange">
              <h5 class="ex-head">Cryptocurrency Exchange</h5>
              <div class="exchange-box">
                <div class="selector">
                  <p class="text">You Change</p>
                  <div class="coin">
                    <div class="language-select">
                      <SelectCurrency
                          name="from"
                          :allowedCurrencies="allowedFromCurrencies"
                          :onSelectCurrency="onSelectCurrency"
                          :selectedCurrency="from"
                      />
                    </div>
                  </div>
                </div>
                <form action="#" id="faq-form">
                  <div class="form-group">
                      <InputFunds
                          :direction="direction"
                          v-model="amountFrom"
                      />
                  </div>
                </form>
                <span class="rate">{{ amountFrom }} {{ from }} = {{ amountTo }} {{ to }}</span>
              </div>

              <a class="rotate" href="#">
                <img @click.prevent="rotateDirection" src="../assets/img/exchange-img.png" alt="">
              </a>

              <div class="exchange-box">
                <div class="selector">
                  <p class="text">You Get</p>
                  <div class="coin">
                    <div class="language-select">
                      <SelectCurrency
                          name="to"
                          :allowedCurrencies="allowedToCurrencies"
                          :onSelectCurrency="onSelectCurrency"
                          :selectedCurrency="to"
                      />
                    </div>
                  </div>
                </div>
                <form action="#" id="faq-form-2">
                  <div class="form-group">
                    <InputFunds
                        :direction="direction"
                        v-model="amountTo"
                    />
                  </div>
                </form>
                <span class="rate">1 {{ to }} = {{ 1/this.direction.out }} {{ from }}</span>
              </div>

              <a href="#" class="button button-1">Exchange</a>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>

</style>
