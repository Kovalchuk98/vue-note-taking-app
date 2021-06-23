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

// #app {
//   display: flex;
//   flex-direction: column;
//   justify-content: space-between;
//   align-items: center;
// }
// body,
// html {
//   width: 100%;
//   min-height: 100vh;
//   margin: 0 !important;
//   padding: 0 !important;
// }
</style>
