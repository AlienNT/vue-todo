<template>
  <div class="todo__action-buttons">
    <template
        :key="key"
        v-for="(button, key) in buttons"
    >
      <TodoButton
          :disabled="button.disabled"
          :type="button.type"
          @onClick="e => $emit(e)"
      />
    </template>
  </div>
</template>

<script>
import TodoButton from "@/components/todoComponents/TodoButton";

export default {
  name: "TodoActionsButtons",
  props: {
    buttons: {
      type: Array,
      default: null
    }
  },
  components: {
    TodoButton
  }
}
</script>

<style scoped lang="scss">
@import "src/assets/css/variables";

.todo__action-buttons {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: 10px;
}

</style>