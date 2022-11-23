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
          ref="todo-item"
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
  data() {
    return {
      showScroll: true,
      counter: null
    }
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
  position: relative;
  margin-left: -15px;
  margin-right: -15px;
  padding: 15px;

  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  overflow: hidden auto;
  max-height: 600px;

}
</style>