<template>
  <div class="note_list_wrapper">
    <div class="add_input">
      <input type="text" v-model="noteText" />
      <button class="add_btn" @click="addItems" :disabled="!noteText">
        Add note
      </button>
    </div>
    <TagList :tags="tags" @removeTag="removeTag" @searchbytag="searchByTag" />
    <div class="search_wrapper">
      <input type="text" placeholder="Search by tag" v-model="searchTag" />
      <button @click="clear" class="clear_btn" v-if="searchTag">X</button>
      <button class="search_btn" @click="search">Search</button>
    </div>
    <div v-if="!isLoading" class="note_list">
      <NoteItem
        v-for="note of searchArray ? searchArray : articles"
        :key="note.id"
        :note="note"
        @removeItem="removeItem"
        @editItem="editItem"
        :tags="tags"
      />
      <button
        v-if="articles.length >= 4 && totalSize <= articles.length && isEmpty"
        @click="loadmore"
        class="load_more"
      >
        Load more
      </button>
    </div>
    <p v-if="!isEmpty && searchArray.length === 0">Nothing found</p>
    <h1 v-if="isLoading">Loading...</h1>
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
      isEmpty: true,
      totalSize: 4,
      searchTag: "",
      searchArray: null,
      noteText: "",
      notes: [],
      tags: [],
      isLoading: false,
    };
  },
  computed: {
    articles() {
      return this.notes.slice(0, this.totalSize);
    },
  },
  created() {
    this.fetchData();
  },
  methods: {
    async fetchData() {
      this.isLoading = true;
      await fetch("/api/notes")
        .then((response) => response.json())
        .then((data) => {
          this.notes = data.notes;
          this.tags = data.tags;
          this.isLoading = false;
        })
        .catch((err) => {
          console.error(err);
          this.isEmpty = false;
        });
    },

    loadmore() {
      this.totalSize *= 2;
    },

    checkTag(str) {
      let array = [];
      for (let i = 0; i < str.length; i++) {
        if (str[i].charAt(0) === "#") {
          array.push(str[i]);
        }
      }
      let tags = [...new Set(array)];
      return tags;
    },
    async addItems() {
      let id = this.generateId();
      let newObj = {
        id,
        note: this.noteText,
      };

      let val = this.noteText.split(/(#[a-z\d-]+)/gi);
      let tags = this.checkTag(val);

      const requestOptions = {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ obj: newObj }),
      };
      const requestOptions2 = {
        method: "PUT",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ tags }),
      };
      if (tags.length) {
        await fetch("/api/addtag", requestOptions2).then(() => {
          this.tags = [...new Set([...this.tags, ...tags])];
        });
      }
      await fetch("/api/add", requestOptions)
        .then((response) => {
          if (response.status === 201) {
            this.notes.unshift(newObj);
            this.noteText = "";
          }
        })
        .catch((err) => console.error(err));
    },

    clear() {
      this.searchTag = "";
      this.searchArray = [];
      this.search();
    },

    search() {
      if (this.searchTag) {
        this.searchArray = this.notes.filter((i) =>
          i.note.toLowerCase().includes(this.searchTag.toLowerCase())
        );
        this.isEmpty = false;
      } else {
        this.searchArray = null;
        this.isEmpty = true;
      }
    },
    searchByTag(tag) {
      this.searchTag = tag;
      this.search();
    },
    generateId() {
      return Math.random().toString(16).slice(2);
    },
    async removeItem(id) {
      const requestOptions = {
        method: "DELETE",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ id }),
      };
      await fetch("/api/removeitem", requestOptions)
        .then((response) => {
          if (response.status === 200) {
            if (this.searchArray) {
              this.searchArray = this.searchArray.filter(
                (item) => item.id !== id
              );
            }
            this.notes = this.notes.filter((item) => item.id !== id);
          }
        })
        .catch((err) => console.error(err));
    },
    async editItem({ id, note, oldnote }) {
      let val = note.split(/(#[a-z\d-]+)/gi);
      let tags = this.checkTag(val);
      const requestOptions = {
        method: "PATCH",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ id, note }),
      };
      const requestOptions2 = {
        method: "PUT",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ tags }),
      };

      if (tags.length && note !== oldnote) {
        await fetch("/api/addtag", requestOptions2).then(() => {
          this.tags = [...new Set([...this.tags, ...tags])];
        });
      }

      await fetch("/api/update", requestOptions)
        .then((response) => response.json())
        .then((data) => {
          this.notes.find((item) => {
            if (item.id === data.item.id) {
              item.note = data.item.note;
            }
          });
        })
        .catch((err) => console.log(err));
    },
    async removeTag(index) {
      const requestOptions = {
        method: "DELETE",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ tagIndex: index }),
      };
      await fetch("/api/removetag", requestOptions)
        .then((response) => {
          if (response.status === 200) {
            this.tags.splice(index, 1);
          }
        })
        .catch((err) => console.error(err));
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
  max-width: 1000px;
  margin: 5rem auto 0;
  .search_wrapper {
    display: flex;
    justify-content: center;
    width: 100%;
    margin: 0 auto;
    max-width: 700px;
    input {
      min-width: 150px;
      max-width: 500px;
    }
    .clear_btn,
    .search_btn {
      cursor: pointer;
      margin-left: 5px;
      transition: 0.5s all ease;
      color: #fff;
      padding: 5px 10px;
      border: none;
      border-radius: 5px;
      &:hover,
      :active {
        opacity: 0.6;
      }
    }
    .clear_btn {
      background-color: rgb(153, 17, 17);
    }
    .search_btn {
      background-color: rgb(31, 3, 158);
    }
  }
  .add_input {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    width: 100%;
    input {
      width: 70% !important;
      max-width: 600px;
    }
    .add_btn {
      cursor: pointer;
      border: none;
      border-radius: 5px;
      color: #fff;
      background-color: rgb(184, 68, 11);
      margin: 3px;
      padding: 8px 14px;
      transition: 0.5s all ease;
      &:disabled {
        color: gray;
        background-color: rgb(207, 207, 207);
      }
    }
  }

  .note_list {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100%;
    .load_more {
      cursor: pointer;
      border: none;
      width: auto;
      padding: 10px 70px;
      border-radius: 0.7rem;
      background-color: rgb(184, 68, 11);
      color: #fff;
      transition: 0.5s all easy;
      margin-bottom: 3rem;
      &:hover,
      :active {
        background-color: rgb(199, 68, 3);
      }
    }
  }
}
</style>
