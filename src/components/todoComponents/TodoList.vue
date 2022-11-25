<template>
  <ul
      class="v-todo-list v-scroll-hidden"
      ref="todos"
  >
    <transition-group name="todo-list">
      <li
          class="todo-list__item"
          v-for="(todoItem, key) in todoList"
          :key="todoItem.id || key"
      >

        <TodoItem
            :data="todoItem"
            @setStatus="e => $emit('setStatus', e)"
        />
      </li>
    </transition-group>
  </ul>
</template>

<script>
import TodoItem from "@/components/todoComponents/TodoItem";

export default {
  name: "TodoList",
  components: {
    TodoItem
  },
  props: {
    todoList: {
      type: Array,
      default: null
    }
  }
}
</script>

<style scoped lang="scss">
.v-todo-list {
  height: 100%;

  > * {
    margin-bottom: 10px;

    &:last-of-type {
      margin-bottom: 0;
    }
  }
}
</style>