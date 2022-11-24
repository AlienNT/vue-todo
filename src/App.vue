<template>
  <Todo/>
</template>

<script>

import Todo from "@/components/Todo";
export default {
  name: 'App',
  components: {
    Todo
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
