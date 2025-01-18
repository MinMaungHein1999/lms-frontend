<template>
  <h1>Create User</h1>
  <form @submit.prevent="submitForm">
    <div>
      <label for="name">UserName :</label>
      <input type="text" id="name" v-model="user.username" required>
    </div>
    <div>
      <label for="name">Email :</label>
      <input type="text" id="email" v-model="user.email" required>
    </div>
    <div>
      <label for="name">Address :</label>
      <input type="text" id="address" v-model="user.address" required>
    </div>
    <button type="submit">Create</button>
  </form>
</template>
<script>
import axios from "axios";
export default{
  data(){
    return {
      user: {
        username: "",
        email: "",
        address: ""
      }
    };
  },
  methods: {
    async submitForm(){
        await axios.post("http://localhost:8080/api/users", this.user);
    }
  }
}
</script>