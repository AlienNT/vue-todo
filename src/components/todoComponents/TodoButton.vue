<template>
  <button
      class="todo__action-button v-box-hover"
      type="button"
      :class="`todo-button_${type}`"
      :title="type"
      :disabled="disabled"
      @click.prevent.stop="$emit('onClick', `${type}Event`)"
  />
</template>

<script>
export default {
  name: "TodoButton",
  props: {
    disabled: {
      type: Boolean,
      default: false
    },
    type: {
      type: String,
      default: null
    }
  },
}
</script>

<style scoped lang="scss">
@import "src/assets/css/variables";
.todo__action-button {
  width: 20px;
  height: 20px;
  background-color: $secondaryColor;
  background-size: contain;
  mask-size: contain;
  mask-repeat: no-repeat;
  mask-position: center;

}

.todo-button_sort {
  -webkit-mask-image: url("../../assets/images/icons/expand.svg");
  mask-image: url("../../assets/images/icons/expand.svg");
}

.todo-button_edit {
  -webkit-mask-image: url("../../assets/images/icons/edit.svg");
  mask-image: url("../../assets/images/icons/edit.svg");
}

.todo-button_delete {
  -webkit-mask-image: url("../../assets/images/icons/delete-icon.svg");
  mask-image: url("../../assets/images/icons/delete-icon.svg");
}
</style>