<template>
  <main class="v-main-component">
    <div class="v-container">
      <div class="v-todo">
        <div class="todo__title">
          <h1 class="title">Add todo</h1>
        </div>
        <div class="todo__form">
          <TodoForm
              :form-data-edit="formData"
              @createTodo="addTodoItem"
          >
            <template v-slot:checkbox>
              <div class="todo__checkbox v-col">
                <button
                    v-if="todoAmount"
                    class="todo-check-all v-box-hover"
                    :class="{
                      'all-check': todoAmount === selectedTodoAmount,
                      'any-check': selectedTodoAmount && todoAmount >= selectedTodoAmount
                    }"
                    type="button"
                    :disabled="selectAllBtnDisable"
                    @click.prevent.stop="selectAction"
                />
              </div>
            </template>
            <template v-slot:actions>
              <div class="todo__action-buttons v-col">
                <button
                    v-if="todoAmount"
                    class="todo__action-button todo-button_edit v-box-hover"
                    type="button"
                    :disabled="editBtnDisable"
                    @click.prevent.stop="editAction"
                />
                <button
                    v-if="todoAmount"
                    class="todo__action-button todo-button_delete v-box-hover"
                    type="button"
                    :disabled="deleteBtnDisable"
                    @click.prevent.stop="deleteAction"
                />
              </div>
            </template>
          </TodoForm>
        </div>
        <div class="todo__list">
          <div class="title todo-amount" v-if="todoAmount">Amount: {{ todoAmount }}</div>
          <transition name="todo-list">
            <TodoList
                v-if="todoAmount"
                :todo-list="todoList"
                @setStatus="setTodoStatus"
            />
          </transition>
        </div>
        <div class="todo__author">
          created by <a href="https://github.com/AlienNT/">AlienNT</a>
        </div>
      </div>
    </div>
  </main>
</template>

<script>
import TodoForm from "@/components/todoComponents/TodoForm";
import TodoList from "@/components/todoComponents/TodoList";


export default {
  name: "MainComponent",
  components: {
    TodoForm,
    TodoList
  },
  data() {
    return {
      todoList: [],
      formData: {
        title: null,
        description: null
      }
    }
  },
  computed: {
    editBtnDisable() {
      return !this.selectedTodoAmount || (this.selectedTodoAmount > 1)
    },
    deleteBtnDisable() {
      return !this.selectedTodoAmount
    },
    selectAllBtnDisable() {
      return false
    },
    selectedTodos() {
      return this.todoList.filter(item => item.status)
    },
    todoAmount() {
      return this.todoList.length || 0
    },
    selectedTodoAmount() {
      return this.selectedTodos?.length || 0
    },
    localStorage: {
      get() {
        const storage = localStorage.getItem('todoList')

        return storage ?
            JSON.parse(storage) :
            localStorage.setItem('todoList', '[]')
      },
      set(data) {
        localStorage.setItem('todoList', JSON.stringify(data))
      }
    }
  },
  methods: {
    updateTodoItem(data, index) {
      this.todoList[index] = {...data}
      this.todoList[index].status = false
      this.formData = {}
    },
    createTodoItem(data) {
      this.todoList.push({
        id: data?.id || Date.now(),
        status: false,
        title: data?.title || null,
        description: data?.description || null,
        date: Date.now(),
      })
    },
    addTodoItem(data) {
      const index = this.todoList.findIndex(item => item.id === data?.id)

      index > -1 ?
          this.updateTodoItem(data, index) :
          this.createTodoItem(data)
    },
    setTodoStatus(e) {
      this.todoList.find(item => item.id === e.id).status = e.status
    },
    selectAction() {
      this.selectedTodoAmount === this.todoAmount ?
          this.todoList.forEach(item => item.status = false) :
          this.todoList.forEach(item => item.status = true)
    },
    editAction() {
      this.formData = this.todoList.find(item => item.status)
    },
    deleteAction() {
      this.todoList = this.todoList.filter(todo => !todo.status)
    },
    setTodosInStorage() {
      return this.localStorage?.length ?
          this.todoList = this.localStorage :
          undefined
    }
  },
  watch: {
    todoList: {
      handler(data) {
        this.localStorage = data
      },
      deep: true
    }
  },
  created() {
    if (this.localStorage?.length) {
      this.setTodosInStorage()
    }
  },
}
</script>

<style scoped lang="scss">
@import "src/assets/css/variables";

.v-main-component {
  margin: auto;
  display: flex;
  align-items: center;
  justify-content: center;
  padding-top: 30px;
  padding-bottom: 30px;
  width: 100%;
  min-width: 240px;
  max-height: 100vh;
  height: -webkit-fill-available;
  overflow-y: hidden;
}

.v-todo {
  display: flex;
  flex-direction: column;
  gap: 10px;
  max-width: 800px;
  overflow-y: auto;
  width: 100%;
  margin: auto;
  background: $primaryColor;
  padding: 15px;
  border-radius: 8px;
  box-shadow: $boxShadow;

}

.todo__title {
  text-align: center;
  color: darken($primaryColor, 15%);
}

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
      mask: url("../assets/images/icons/check-mark-2).svg") no-repeat center / 80%;
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

.todo__action-buttons {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: 10px;
}

.todo__action-button {
  width: 20px;
  height: 20px;
  background-color: $secondaryColor;
  background-size: contain;
  mask-size: contain;
  mask-repeat: no-repeat;
  mask-position: center;

}

.todo-button_delete {
  -webkit-mask-image: url("../assets/images/icons/delete-icon.svg");
  mask-image: url("../assets/images/icons/delete-icon.svg");
}

.todo-button_edit {
  -webkit-mask-image: url("../assets/images/icons/edit.svg");
  mask-image: url("../assets/images/icons/edit.svg");
}

.todo__list {
  .todo-amount {
    //color: darken($secondaryColor, 10%);
    color: darken($primaryColor, 15%);
    font-size: 14px;
    font-weight: bold;
  }
}

.todo__author {
  width: 100%;
  color: darken($primaryColor, 15%);
  font-weight: bold;
  text-align: center;
  font-size: 12px;

  > * {
    color: $secondaryColor;
  }
}
</style>