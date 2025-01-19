<template>
  <div class="user-form">
    <h1>{{ isEditing ? "Edit User" : "Register User" }}</h1>
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
        <label for="role">Select Role :</label>
        <div v-for="role in roles" :key="role.id">
          <label :for="role.name">{{role.name}}</label>
          <input type="radio" :id="role.name" :value="role.id" v-model="user.role_id" required>
        </div>
      </div>
      <div>
        <label for="name">Address :</label>
        <input type="text" id="address" v-model="user.address" required>
      </div>
      <button type="submit">{{ isEditing ? "Update" : "Register"}}</button>
    </form>
  <p v-if="message">{{ message }}</p>
  </div>
  <table>
    <thead>
      <tr>
        <th>Id</th>
        <th>User Name</th>
        <th>Email</th>
        <th>Role</th>
        <th>Address</th>
        <th>Actions</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(user, index) in users" :key="index">
        <th>{{ user.id }}</th>
        <td>{{ user.username }}</td>
        <td>{{ user.email }}</td>
        <td>{{ user.userRole }}</td>
        <td>{{ user.address }}</td>
        <td>
          <a href="#" @click.prevent="editUser(user.id)"> Edit</a>
        </td>
      </tr>
    </tbody>
  </table>
</template>
<script>
import axios from "axios";
export default{
  data(){
    return {
      isEditing: false,
      users: [],
      roles: [],
      user: {
        id: null,
        username: "",
        email: "",
        role_id: null,
        address: ""
      },
      message: ""
    };
  },
  methods: {
    async editUser(selectedId){
      const response = await axios.get(`http://localhost:8080/api/users/${selectedId}`);
      this.user = response.data
      this.isEditing = true
    },
    resetForm(){
        this.isEditing = false;
        this.user = {
          username: "",
          email: "",
          address: "",
          role_id: null,
          userRole: null,
        };
    },
    async fetchRoles(){
      const response = await axios.get("http://localhost:8080/api/roles")
      this.roles = response.data
      console.log(this.roles)
    },
    async fetchUsers(){
      const response = await axios.get("http://localhost:8080/api/users")
      this.users = response.data
    },
    async submitForm(){
      try{
        this.user.userRole = { id: this.user.role_id };
        if (this.isEditing){
          await axios.put(`http://localhost:8080/api/users/${this.user.id}`, this.user)
          this.message = "User Updated Successfully!"
        } else {
          await axios.post("http://localhost:8080/api/users", this.user);
          this.message = "User created successfully!"
        }
        this.fetchUsers();
        this.resetForm();
      } catch(error){
        this.message = "Error creating user. Please try again."
      }
    },
  },
  mounted() {
    this.fetchUsers();
    this.fetchRoles();
  },
};
</script>
<style scoped>
table {
  width: 100%;
  margin-top: 30px;
  border-collapse: collapse ;
}
table th, table td {
  padding: 10px;
  border: 1px solid #ddd;
  text-align: left;
}

table th {
  background-color: #f2f2f2;
}
.user-form {
  max-width: 400px;
  padding: 20px;
  border-radius: 7px;
  margin: auto;
  border: 1px solid #ddd;
  background-color: #f9f9f9;
}
input{
  width: 100%;
  padding: 8px;
  border: 1px solid #ccc;
  margin-bottom: 10px;
  border-radius: 4px;
}

label{
  float: left;
  display: block;
  margin-bottom: 5px;
}
button{
  padding: 10px 20px;
  background-color: #28a745;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
</style>