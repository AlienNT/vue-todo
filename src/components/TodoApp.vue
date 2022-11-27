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
    /**
     * возвращает настройки для кнопок взаимодействия с элеметнами списка
     * @returns {[{disabled: boolean, type: string}]}
     */
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
    /**
     * правило блокирования кнопки редактирования
     * срабатывает когда выбрано 0 или более 1 элемента
     * @returns {boolean}
     */
    editBtnDisable() {
      return !this.selectedTodoAmount || (this.selectedTodoAmount > 1)
    },
    /**
     * правило блокирования кнопки удаления
     * срабатывает когда нет выбранных элементов
     * @returns {boolean}
     */
    deleteBtnDisable() {
      return !this.selectedTodoAmount
    },
    /**
     * правило блокирования кпопки сортировки
     * срабатывает при количестве элементов меньше 2
     * @returns {boolean}
     */
    sortBtnDisable() {
      return this.todoAmount <= 1
    },
    /**
     * правило блокирования кнопки выбора записей
     * срабатывает при отсутствии записей
     * @returns {boolean}
     */
    selectAllBtnDisable() {
      return !this.todoAmount
    },
    /**
     * возвращает список выбранных элементов
     * @returns {*[]}
     */
    selectedTodos() {
      return this.todoList.filter(item => item.status)
    },
    /**
     * возвращает число элементов
     * @returns {number|number}
     */
    todoAmount() {
      return this.todoList.length || 0
    },
    /**
     * возвращает число выбранных элементов
     * @returns {number|number}
     */
    selectedTodoAmount() {
      return this.selectedTodos?.length || 0
    },
    /**
     * возвращает отсортированный список элементов по полю даты
     * @returns {*}
     */
    sortedTodoList() {
      return this.sortByField(this.todoList, 'date', this.isSort)
    },
    /**
     * получает и записывает данные о элементах в localStorage
     */
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
    /**
     * возвращает сортированный массив объектов по заданным параметрам
     * @param array - исходный массив
     * @param fieldName - имя поля сортировки
     * @param order - порядок сортировки (true - от старых к новым)
     * @returns {*}
     */
    sortByField(array, fieldName, order = true) {
      return array && fieldName &&
          array.sort((a, b) => order ?
              a[fieldName] - b[fieldName] :
              b[fieldName] - a[fieldName])
    },
    /**
     * обновляет элемент в массиве
     * @param data - исходный массив
     * @param index - индекс элемента в массиве
     */
    updateTodoItem(data, index) {
      this.todoList[index] = {...data}
      this.todoList[index].status = false
      this.formData = {}
    },
    /**
     * добавляет новый элемент в массив
     * @param data - новый элемент
     */
    createTodoItem(data) {
      this.todoList.push({
        id: data?.id || Date.now(),
        status: false,
        title: data?.title || null,
        description: data?.description || null,
        date: Date.now(),
      })
    },
    /**
     * определяет и вызывает метод обновления или создания нового элемента
     * @param data - элемент
     */
    addTodoItem(data) {
      const index = this.todoList.findIndex(item => item.id === data?.id)

      index > -1 ?
          this.updateTodoItem(data, index) :
          this.createTodoItem(data)
    },
    /**
     * устанавливает статус элемента
     * @param e - переданный элемент
     */
    setTodoStatus(e) {
      this.todoList.find(item => item.id === e.id).status = e.status
    },
    /**
     * меняет статус выделения всех элементов массива дел
     */
    selectAction() {
      this.selectedTodoAmount === this.todoAmount ?
          this.todoList.forEach(item => item.status = false) :
          this.todoList.forEach(item => item.status = true)
    },
    /**
     * записывает выделенный элемент в объект редактирования
     */
    editAction() {
      this.formData = this.todoList.find(item => item.status)
    },
    /**
     * удаляет выбранные элементы путем перезаписывания исходного массива оставшимися элементами
     */
    deleteAction() {
      this.todoList = this.todoList.filter(todo => !todo.status)
    },
    /**
     * меняет направление сортировки массива
     */
    sortAction() {
      this.isSort = !this.isSort
    },
    /**
     * записывает массив дел из localStorage, если таковой имеется
     * @returns {{length}|*|undefined}
     */
    setTodosInStorage() {
      return this.localStorage?.length ?
          this.todoList = this.localStorage :
          undefined
    }
  },
  watch: {
    /**
     * следит за изменением массива дел и перезаписывает localStorage
     */
    todoList: {
      handler(data) {
        this.localStorage = data
      },
      deep: true
    }
  },
  created() {
    this.setTodosInStorage()
  }
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
  margin: 0 0 auto 0;
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