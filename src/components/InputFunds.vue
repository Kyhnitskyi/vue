<script>
export default {
  name: "InputFunds",
  props: {
    direction: Object,
    modelValue: Number
  },
  emits: ['update:modelValue'],
}

</script>

<template>
  <input
      :value="modelValue"
      @input="$emit('update:modelValue',  parseFloat($event.target.value) || 0)"
      :placeholder="direction.in"
      type="text"
      name="amountTo"
      class="form-control">
</template>

<style scoped>

</style>