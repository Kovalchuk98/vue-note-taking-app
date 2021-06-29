<template>
  <div class="note_item_wrapper">
    <div class="note_text_wrapper">
      <p>
        <span
          v-for="(s, i) in parsedMsg"
          :key="i"
          :class="getClass(i % 2 && isEdit)"
          >{{ s }}</span
        >
      </p>
      <input
        class="edit_input"
        @keyup.esc="isEdit = false"
        @keyup.enter="submitEdit"
        v-show="isEdit"
        type="text"
        v-model="editNote"
        ref="edit_input"
        @blur="isEdit = false"
      />
    </div>
    <div class="btns_wrapper">
      <button class="submit_btn" v-show="isEdit" @mousedown.prevent="submit">
        Submit
      </button>
      <button class="edit_btn" v-show="!isEdit" @click="edit">Edit</button>
      <button class="remove_btn" @click="removeItem">Remove</button>
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
  computed: {
    parsedSearch() {
      return "(" + this.tags.join(" ").trim().replace(/ +/g, "|") + ")";
    },
    parsedMsg() {
      return this.note.note.split(new RegExp(this.parsedSearch, "gi"));
    },
  },

  methods: {
    submit() {
      this.$refs.edit_input.blur();
      this.submitEdit();
    },
    getClass(i) {
      var myClass = {};
      myClass["myclass"] = !!i;
      return myClass;
    },
    removeItem() {
      this.$emit("removeItem", this.note.id);
    },
    edit() {
      this.isEdit = true;
      this.$nextTick(() => {
        this.$refs.edit_input.focus();
      });
      this.editNote = this.note.note;
    },
    submitEdit() {
      this.$emit("editItem", {
        id: this.note.id,
        note: this.editNote,
        oldnote: this.note.note,
      });
      this.isEdit = false;
    },
  },
};
</script>

<style lang="scss">
.note_item_wrapper {
  display: flex;
  flex-direction: row;
  align-items: center;
  flex-wrap: wrap;
  width: 100%;
  max-width: 700px;
  margin: 10px;
  min-height: 130px;
  border-radius: 0.75rem;
  background-color: rgb(226, 229, 248);
  .note_text_wrapper {
    display: flex;
    flex-direction: column;
    flex-wrap: wrap;
    align-items: stretch;
    justify-content: space-between;
    width: 100%;
    .edit_input {
      margin-top: 10px;
    }
    p {
      padding: 10px 10px 0 10px;
      margin: 0;
    }
  }
  .btns_wrapper {
    display: flex;
    flex-direction: row;
    height: auto;
    margin: 10px 0;
    .submit_btn,
    .edit_btn,
    .remove_btn {
      cursor: pointer;
      margin-left: 5px;
      border: none;
      border-radius: 5px;
      transition: 0.3s all ease;
      color: #fff;
      padding: 5px 10px;
      &:hover,
      :active {
        opacity: 0.6;
      }
    }
    .remove_btn {
      background-color: rgb(153, 17, 17);
    }
    .submit_btn {
      background-color: rgb(10, 122, 66);
    }
    .edit_btn {
      background-color: rgb(206, 78, 14);
    }
  }
}
.myclass {
  color: #fff !important;
}
</style>
