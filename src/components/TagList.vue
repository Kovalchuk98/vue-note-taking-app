<template>
  <div class="tag_list_wrapper">
    <div class="tag_item" v-for="(tag, index) in tags" :key="index">
      <span @click="search(tag)">
        {{ tag }}
      </span>
      <button
        title="Delete tag"
        class="tag_btn"
        @click.prevent="removeTag(index)"
      >
        X
      </button>
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
    search(tag) {
      this.$emit("searchbytag", tag);
    },
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
  justify-content: center;
  margin: 10px;
  .tag_item {
    cursor: pointer;
    display: flex;
    align-items: center;
    margin: 10px;
    padding: 5px 8px;
    border-radius: 10px;
    background-color: rgb(226, 226, 226);
    transition: 0.5s all ease;
    &:hover,
    :active {
      background-color: rgb(190, 189, 189);
    }
    .tag_btn {
      cursor: pointer;
      border: none;
      margin-left: 10px;
      border-radius: 30px;
      padding: 3px 6px;
    }
  }
}
</style>
