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
              <TodoCheckbox
                  v-if="todoAmount"
                  class="v-col"
                  :amount="todoAmount"
                  :selected-amount="selectedTodoAmount"
                  :disabled="selectAllBtnDisable"
                  @selectedEvent="selectAction"
              />
            </template>
            <template v-slot:actions>
              <TodoActionsButtons
                  v-if="todoAmount"
                  class="v-col"
                  :buttons="buttons"
                  @sortEvent="sortAction"
                  @editEvent="editAction"
                  @deleteEvent="deleteAction"
              />
            </template>
          </TodoForm>
        </div>
        <div
            v-if="todoAmount"
            class="title todo__amount"
        >
          Amount: {{ todoAmount }}
        </div>
        <div class="todo__list v-scroll-hidden">
          <transition name="todo-list">
            <TodoList
                v-if="todoAmount"
                :todo-list="sortedTodoList"
                @setStatus="setTodoStatus"
            />
          </transition>
        </div>
        <div class="todo__author">
          <AuthorLink
              title="AlienNT"
              link="https://github.com/AlienNT/"
          />
        </div>
      </div>
    </div>
  </main>
</template>

<script>
import TodoForm from "@/components/todoComponents/TodoForm";
import TodoList from "@/components/todoComponents/TodoList";
import AuthorLink from "@/components/AuthorLink";
import TodoCheckbox from "@/components/todoComponents/TodoCheckbox";
import TodoActionsButtons from "@/components/todoComponents/TodoActionsButtons";


export default {
  name: "MainComponent",
  components: {
    TodoForm,
    TodoList,
    AuthorLink,
    TodoCheckbox,
    TodoActionsButtons
  },
  data() {
    return {
      todoList: [],
      formData: {
        title: null,
        description: null
      },
      isSort: true
    }
  },
  computed: {
    buttons() {
      return [
        {
          type: 'sort',
          disabled: this.sortBtnDisable
        },
        {
          type: 'edit',
          disabled: this.editBtnDisable
        },
        {
          type: 'delete',
          disabled: this.deleteBtnDisable
        }
      ]
    },
    editBtnDisable() {
      return !this.selectedTodoAmount || (this.selectedTodoAmount > 1)
    },
    deleteBtnDisable() {
      return !this.selectedTodoAmount
    },
    sortBtnDisable() {
      return this.todoAmount <= 1
    },
    selectAllBtnDisable() {
      return !this.todoAmount
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
    sortedTodoList() {
      return this.sortByField(this.todoList, 'date', this.isSort)
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
    sortByField(array, fieldName, order = true) {
      return array && fieldName &&
          array.sort((a, b) => order ?
              a[fieldName] - b[fieldName] :
              b[fieldName] - a[fieldName])
    },
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
    sortAction() {
      this.isSort = !this.isSort
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
  padding-top: 30px;
  padding-bottom: 30px;
  width: 100%;
  min-width: 240px;
  height: -webkit-fill-available;

  .v-container {
    height: 100%;
    display: flex;
  }
}

.v-todo {
  display: flex;
  flex-direction: column;
  gap: 10px;
  width: 100%;
  margin: auto;
  max-width: 600px;
  background: $primaryColor;
  padding: 15px;
  border-radius: 8px;
  box-shadow: $boxShadow;
  max-height: 100%;

}

.todo__title {
  text-align: center;
  color: darken($primaryColor, 15%);
}

.todo__amount {
  color: darken($primaryColor, 15%);
  font-size: 14px;
  font-weight: bold;
}

.todo__list {
  position: relative;
  overflow: auto;
  height: 100%;
}
</style>