<template>
  <div class="note_list_wrapper">
    <div class="note_list">
      <div>
        <input type="text" v-model="noteText" />
        <button @click="addNote" :disabled="!noteText">Add note</button>
      </div>
      <template v-if="notes">
        <NoteItem
          v-for="note in notes"
          :key="note.id"
          :note="note"
          @removeItem="removeItem"
          @editItem="editItem"
          :tags="tags"
        />
      </template>
      <p v-else>Nothing found</p>
    </div>
    <TagList :tags="tags" @removeTag="removeTag" />
  </div>
</template>

<script>
import TagList from "@/components/TagList";
import NoteItem from "@/components/NoteItem";

export default {
  components: {
    TagList,
    NoteItem,
  },
  data() {
    return {
      noteText: "",
      tagText: "",
      notes: [
        { id: 1, note: "note1" },
        { id: 2, note: "note2" },
        { id: 3, note: "note3" },
        { id: 4, note: "note4" },
        { id: 5, note: "note5" },
      ],
      tags: ["#testtag", "#test2", "#tag3"],
      json: [],
    };
  },
  methods: {
    generateId() {
      return Math.random().toString(16).slice(2);
    },
    addNote() {
      let id = this.generateId();
      let newObj = {
        id,
        note: this.noteText,
      };
      this.notes.push(newObj);

      let val = this.noteText.split(/(#[a-z\d-]+)/gi);
      for (let i = 0; i < val.length; i++) {
        if (val[i].charAt(0) === "#") {
          let array = [];
          array.push(val[i]);
          this.tags.push(...array);
        }
        //Unique tags in array
        this.tags = [...new Set(this.tags)];
      }
      console.log(this.tags);

      this.noteText = "";
    },
    removeItem(id) {
      this.notes = this.notes.filter((item) => item.id !== id);
    },
    editItem({ id, note }) {
      let itemIndex = this.notes.findIndex((item) => {
        return item.id === id;
      });
      this.$set(this.notes[itemIndex], "note", note);

      let val = note.split(/(#[a-z\d-]+)/gi);
      for (let i = 0; i < val.length; i++) {
        if (val[i].charAt(0) === "#") {
          let array = [];
          array.push(val[i]);
          this.tags.push(...array);
        }
      }
      console.log(this.tags);
    },
    removeTag(index) {
      let val = this.noteText;
      let del = this.tags.splice(index, 1);
      val.substring(0, val.length - 1).replace(del, "");
    },
  },
};
</script>

<style lang="scss">
.note_list_wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 80%;
  margin: 0 auto;

  .note_list {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100%;
  }
}
</style>
