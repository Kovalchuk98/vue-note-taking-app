<template>
  <div id="app">
    <Header />
    <NoteList />
    <Footer />
  </div>
</template>

<script>
import Header from "@/components/UI/Header";
import NoteList from "@/components/NoteList.vue";

export default {
  name: "App",
  components: {
    Header,
    NoteList,
  },
};
</script>

<style lang="scss">
html,
body {
  padding: 0 !important;
  margin: 0 !important;
  width: 100%;
  min-height: 100vh;
}
</style>
