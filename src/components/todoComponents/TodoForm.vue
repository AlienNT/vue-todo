<template>
  <form
      class="v-todo-form"
      @submit.prevent.stop="addTodoItem"
  >
    <label class="todo-form__label">
      <input
          v-model="formTitle"
          type="text"
          class="todo-form__input v-box-hover"
          name="title"
          placeholder="Title"
          title="input title"
          minlength="2"
          maxlength="50"
      >
    </label>
    <label class="todo-form__label">
      <textarea
          v-model="formDescription"
          ref="description"
          class="todo-form__textarea v-box-hover v-scroll-hidden"
          placeholder="Description"
          title="input description"
          maxlength="200"
      />
    </label>
    <div class="todo-form__button-wrapper">
      <slot name="checkbox"/>
      <button
          class="todo-form__button v-box-hover"
          type="submit"
          :disabled="submitDisable"
      >
        {{ formDataEdit?.id ? 'Save' : 'Add' }}
      </button>
      <slot name="actions"/>
    </div>
  </form>
</template>

<script>
export default {
  name: "TodoForm",
  props: {
    formDataEdit: {
      type: Object,
      default: null
    }
  },
  data() {
    return {
      formData: {
        title: null,
        description: null
      },
      rules: {
        title: {
          minLength: 2,
          maxLength: 50
        },
        description: {
          maxLength: 200
        }
      }
    }
  },
  computed: {
    /**
     * возвращает правильность введенной в поле названия строки
     * @returns {boolean}
     */
    correctTitle() {
      return this.checkValue(this.formTitle, this.rules.title)
    },
    /**
     * возвращает правильность введенной в поле описания строки
     * @returns {boolean}
     */
    correctDescription() {
      return this.checkValue(this.formDescription, this.rules.description)
    },
    /**
     * правило блокирования кнопки отправки формы
     * @returns {boolean}
     */
    submitDisable() {
      return !this.correctDescription || !this.correctTitle
    },
    /**
     * возвращает и задает значение поля названия
     */
    formTitle: {
      get() {
        return this.formData.title
      },
      set(e) {
        this.formData.title = e.trim()
      }
    },
    /**
     * возвращает и задает значение поля описания
     */
    formDescription: {
      get() {
        return this.formData.description
      },
      set(e) {
        this.formData.description = e.trim()
      }
    }
  },
  methods: {
    /**
     * возвращает истинность поля согласно правилам
     * @param value - значение поля
     * @param rules - правила
     * @returns {boolean}
     */
    checkValue(value, rules) {
      const length = this.getCharactersCounter(value)

      return Object.keys(rules).map(key => {
        switch (key) {
          case 'minLength':
            return length >= rules[key]
          case 'maxLength':
            return length <= rules[key]
          default:
            return false
        }
      }).every(item => item)
    },
    /**
     * возвращает число символов в строке без пробелов
     * @param field - строка
     * @returns {*|number}
     */
    getCharactersCounter(field) {
      return field ? field.trim().length : 0
    },
    /**
     * формирует объект с полями формы и вызывает событие создания элемента
     */
    addTodoItem() {
      const data = []
      Object.keys(this.formData).forEach(key => {
        data[key] = this.formData[key]
        this.formData[key] = null
      })
      this.$emit('createTodo', data)
    },
    /**
     * устанавливает поля для редактирования в поля формы
     * @param e - объект для редактирования
     */
    setEditFields(e) {
      Object.keys(e).forEach(key => {
        this.formData[key] = e[key]
      })
    },
  },
  watch: {
    /**
     * следит за изменением объекта для редактирования
     */
    formDataEdit: {
      handler(e) {
        this.setEditFields(e)
      }
    }
  }
}
</script>

<style scoped lang="scss">
@import "src/assets/css/variables";

.v-todo-form {
  gap: 10px;
  display: flex;
  flex-direction: column;
}

.todo-form__label {
  width: 100%;
  display: block;
  position: relative;
  transition: .2s ease;
}

.todo-form__input,
.todo-form__textarea {
  border-radius: 8px;
  padding: 10px 15px;
  font-size: 14px;
  font-family: sans-serif;
  color: $secondaryColor;

  &::placeholder {
    transition: .2s ease;
    font-size: 14px;
    color: $secondaryColor;
    font-weight: bold;
    opacity: .5;
  }

  &:focus {
    &::placeholder {
      color: transparent;
    }
  }
}
.todo-form__textarea {
  transition: .2s ease;
  overflow: auto;
  height: 40px;
  &:focus {
    height: 80px;
  }
}
.todo-form__button-wrapper {
  display: flex;
  justify-content: center;
}

.todo-form__button {
  padding: 10px 15px;
  border-radius: 8px;
  color: #fff;
  background: $secondaryColor;
  max-width: 150px;
  width: 100%;
  font-weight: bold;
}

</style>