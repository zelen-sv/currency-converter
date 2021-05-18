<template>
  <div class="currency-picker">
    <div v-for="item in currencies"
         :key="`currency-${item}`"
         class="currency-picker__currency"
         :class="{'currency-picker__currency--active': item == modelValue }"
         @click="updateCurrency(item)">
      {{ item }}
    </div>
  </div>
</template>

<script>
  export default {
    model: {
      prop: 'modelValue',
      event: 'change'
    },
    props: {
      currencies: {
        type: Array,
        required: true
      },
      modelValue: {
        type: String,
        required: true
      }
    },
    methods: {
      updateCurrency (item) {
        this.$emit('change', item)
      }
    }
  }
</script>

<style lang="scss" scoped>
  .currency-picker {
    display: flex;
    margin-left: 30px;
  }

  .currency-picker__currency {
    font-weight: 600;
    font-size: 16px;
    line-height: 20px;
    padding: 10px 20px;
    background-color: $dark;
    cursor: pointer;

    &:first-child {
      border-top-left-radius: 4px;
      border-bottom-left-radius: 4px;
    }

    &:last-child {
      border-top-right-radius: 4px;
      border-bottom-right-radius: 4px;
    }
  }

  .currency-picker__currency--active {
    border: 1px solid $orange;
    color: $orange;
  }
</style>
