<script>
export default {
  name: "SelectCurrency",
  props: {
    allowedCurrencies: Array,
    onSelectCurrency: Function,
    selectedCurrency: String
  },
}
</script>

<template>
  <select name="from" class="select-bar nice-select select-bar" @change="onSelectCurrency">
    <option
        v-for="currency in allowedCurrencies"
        :value="currency"
        :key="currency"
        :selected="currency === selectedCurrency"
    >{{ currency }}</option>
  </select>
</template>

<style scoped>

</style>