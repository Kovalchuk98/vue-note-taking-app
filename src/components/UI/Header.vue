<template>
  <div class="header_wrapper">
    <p class="logo">Note Taking App</p>
  </div>
</template>

<script>
export default {};
</script>

<style lang="scss">
.header_wrapper {
  position: fixed;
  top: 0;
  display: flex;
  align-items: center;
  height: 60px;
  background-color: #1f0885;
  width: 100%;

  .logo {
    color: #fff;
    margin-left: 1rem;
    font-weight: bold;
    font-family: Arial, Helvetica, sans-serif;
  }
}
</style>
