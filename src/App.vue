<template>
  <TodoApp/>
</template>

<script>

import TodoApp from "@/components/TodoApp";
export default {
  name: 'App',
  components: {
    TodoApp
  },
  created() {
    document.title='Simple Vue Todo'
  }
}
</script>

<style lang="scss">
@import "assets/css/main";
#app {
  height: 100vh;
  max-height: -webkit-fill-available;
  display: flex;
}
</style>
