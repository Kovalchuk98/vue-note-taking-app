<template>
  <div class="tag_list_wrapper">
    <div class="tag_item" v-for="(tag, index) in tags" :key="index">
      {{ tag }}
      <button @click="removeTag(index)">X</button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    tags: {
      type: Array,
      required: true,
    },
  },
  methods: {
    removeTag(index) {
      this.$emit("removeTag", index);
    },
  },
};
</script>

<style lang="scss">
.tag_list_wrapper {
  display: flex;
  flex-wrap: wrap;
  width: 100%;
  .tag_item {
    margin: 10px;
  }
}
</style>
