<template>
  <div class="author-link">
    created by
    <a
        :href="link"
        :title="`link to ${link}`"
    >
      {{title}}
    </a>
  </div>
</template>

<script>
export default {
  name: "AuthorLink",
  props: {
    title: {
      type: String,
      default: null
    },
    link: {
      type: String,
      default: null
    },
  }
}
</script>

<style scoped lang="scss">
@import "src/assets/css/variables";

.author-link {
  width: 100%;
  color: darken($primaryColor, 15%);
  font-weight: bold;
  text-align: center;
  font-size: 12px;

  a {
    color: $secondaryColor;
    transition: .2s ease;
    &:hover {
      color: lighten($secondaryColor, 5%);
    }
  }
}
</style>