<template>
  <div
      class="v-todo-item v-box-hover"
      ref="todo-item"
      :class="{
        'todo-active': data.status
      }"
      @click.prevent.stop="setStatus"
  >
    <div class="v-row">
      <div class="v-col todo__col">
        <div class="v-row">
          <div class="todo__field todo__date">
            {{ date }}
          </div>
          <div class="todo__field todo__title">
            {{ data.title }}
          </div>
        </div>
        <div class="todo__field todo__description">
          {{ data.description }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "TodoItem",
  props: {
    data: {
      type: Object,
      default: null,
    },
  },
  data() {
    return {
      show: true
    }
  },
  computed: {
    /**
     * возвращает дату создания элемента
     * @returns {null|string}
     */
    date() {
      return this.getDateFormat(this.data?.date)
    },
    refItem() {
      return this.$refs["todo-item"]
    }
  },
  methods: {
    setStatus() {
      this.$emit('setStatus', {
        id: this.data.id,
        status: !this.data.status
      })
    },
    /**
     * возвращает дату в формате ДД.ММ.ГГГГ
     * @param date - дата в миллисекундах
     * @returns {string|null}
     */
    getDateFormat(date) {
      return date ? new Date(date).toLocaleDateString() : null
    },
    /**
     * скроллит к элементу до зоны видимости
     * @returns {void|undefined}
     */
    scrollToView() {
      return this.refItem ? this.refItem.scrollIntoView() : undefined
    }
  },
  mounted() {
    this.scrollToView()
  }
}
</script>

<style scoped lang="scss">
@import "src/assets/css/variables";

.v-todo-item {
  cursor: pointer;
  background: #ffffff;
  border-radius: 8px;
  padding: 5px;
  word-break: break-all;

  &:hover {
    transform: scale(0.99);
  }
}

.todo__col {
  padding-left: 5px;
  padding-right: 5px;
  flex-shrink: 1;

  .v-row {
    gap: 15px;
    align-items: flex-start;
  }

  &:first-of-type {
    flex-shrink: 1;
  }

  &:last-of-type {
    flex-shrink: 11;
  }
}

.todo__date {
  color: darken($primaryColor, 15%);
  opacity: .8;
  flex-shrink: 0;
  font-size: 12px;
}

.todo__title {
  color: $secondaryColor;
  font-weight: bold;

  &:first-letter {
    text-transform: uppercase;
  }
}

.todo__description {
  margin-top: 10px;
  font-style: italic;
  color: darken($primaryColor, 10%);
  font-weight: 500;
  line-height: 1.1;
  opacity: .8;

  &:first-letter {
    text-transform: uppercase;
    font-weight: bold;
  }
}

.todo-active {
  background: lighten($primaryColor, 20%);
  //border-right: 2px solid $secondaryColor;
  opacity: .8;
  transform: scale(0.99);
}
</style>