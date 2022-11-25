<template>
  <div class="todo__checkbox">
    <button
        class="todo-check-all v-box-hover"
        :class="{
            'all-check': amount === selectedAmount,
            'any-check': selectedAmount && amount >= selectedAmount
                    }"
        type="button"
        title="select"
        :disabled="disabled"
        @click.prevent.stop="$emit('selectedEvent')"
    />
  </div>
</template>

<script>
export default {
  name: "TodoCheckbox",
  props: {
    amount: {
      type: Number,
      default: 0
    },
    selectedAmount: {
      type: Number,
      default: 0
    },
    disabled: {
      type: Boolean,
      default: false
    }
  }
}
</script>

<style scoped lang="scss">
@import "src/assets/css/variables";

.todo-check-all {
  width: 20px;
  height: 20px;
  border-radius: 4px;
  border: 2px solid $secondaryColor;
  display: flex;
  align-items: center;
  justify-content: center;

  &:after {
    transition: .2s ease;
    content: '';
    background-color: $secondaryColor;
    opacity: 0;
  }

  &.any-check {
    &:after {
      opacity: 1;
      width: 10px;
      height: 10px;
      border-radius: 2px;
    }
  }

  &.all-check {
    background-color: $secondaryColor;

    &:after {
      opacity: 1;
      mask: url("../../assets/images/icons/check-mark-2).svg") no-repeat center / 80%;
      background-color: #fff;
      width: 102%;
      height: 102%;
      border-radius: 0;
    }
  }
}

.todo__checkbox {
  display: flex;
  align-items: center;
  justify-content: flex-start;
}

</style>