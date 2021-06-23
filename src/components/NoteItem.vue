<template>
  <div class="note_item_wrapper">
    <p>{{ note.note }}</p>
    <div>
      <button @click="editItem">Edit</button>
      <button @click="removeItem">Remove</button>
    </div>
    <div v-if="isEdit">
      <input type="text" v-model="editNote" />
    </div>
  </div>
</template>

<script>
export default {
  props: {
    note: {
      type: Object,
      required: true,
    },
    tags: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      isEdit: false,
      editNote: this.note.note,
    };
  },

  methods: {
    matchName(current) {
      let reggie = new RegExp(this.note.note, "ig");
      let found = current.search(reggie) !== -1;
      return !found
        ? current
        : current.replace(reggie, "<b>" + this.note.note + "</b>");
    },
    removeItem() {
      this.$emit("removeItem", this.note.id);
    },
    editItem() {
      this.isEdit = !this.isEdit;
      this.$emit("editItem", { id: this.note.id, note: this.editNote });
    },
  },
};
</script>

<style lang="scss">
.note_item_wrapper {
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
  width: 100%;
  margin: 10px;
  background-color: rgb(245, 244, 244);
}
</style>
