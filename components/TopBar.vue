<template>
  <v-app-bar
      height="50rem" 
  >
    <v-toolbar-title class="ml-20"><b>MoVue</b></v-toolbar-title>
  </v-app-bar>
</template>

<script>
export default {
  name: 'TopBar',
}
</script>

<style lang="scss" scoped>
@import "./assets/variables.scss";
.v-sheet.v-app-bar.v-toolbar {
  position: relative;
  background-color: $primary-color;
  margin: auto;
  @media (min-width: 750px) {
    height: 200px;
  }

  // .v-toolbar__content{
  //   justify-content: center !important;
  // not working}
  .v-toolbar__title {
    font-family: $font-family;
  }
}

</style>