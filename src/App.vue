<template>
  <UserRegister></UserRegister>
</template>

<script>
import UserRegister from './components/UserRegister.vue';

export default {
  name: 'App',
  components: {
    UserRegister
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
