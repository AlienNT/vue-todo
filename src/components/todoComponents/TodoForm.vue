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
      >
    </label>
    <label class="todo-form__label">
      <textarea
          v-model="formDescription"
          class="todo-form__textarea v-box-hover"
          placeholder="Description"
          title="input description"
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
      }
    }
  },
  computed: {
    submitDisable() {
      return !(
          this.getCharactersCounter(this.formTitle) >= 2 &&
          this.getCharactersCounter(this.formDescription) <= 200
      )
    },
    formTitle: {
      get() {
        return this.formData.title
      },
      set(e) {
        this.formData.title = e.trim()
      }
    },
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
    getCharactersCounter(field) {
      return field ? field.length : 0
    },
    addTodoItem() {
      const data = []
      Object.keys(this.formData).forEach(key => {
        data[key] = this.formData[key]
        this.formData[key] = null
      })
      this.$emit('createTodo', data)
    },
    setEditFields(e) {
      Object.keys(e).forEach(key => {
        this.formData[key] = e[key]
      })
    },
  },
  watch: {
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