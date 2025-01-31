<template>
  <button @click="openRegisterForm" class="new-user-btn">New User</button>
  <div v-if="this.openForm" class="user-form">
    <button class="close-btn" @click="closeForm">x</button>
    <h1>{{ isEditing ? "Edit User" : "Register User" }}</h1>
    <form  @submit.prevent="submitForm">
      
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
        <div class="radio-group">
        <div v-for="role in roles" 
          :key="role.id" 
          class="radio-option" 
          :class="{ selected: user.userRole?.id == role.id}"
          @click="assignRoleToUser(role.id)">
          <input type="radio" 
            :id="role.name" 
            :value="role.id" 
            v-model="userRoleId" 
          required>
          <label :for="role.name">{{role.name}}</label>
        </div>
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
  <div class="user-form">
    <label for="search">Search Users : </label>
    <input type="text" id="searchUserName" v-model="searchUserName" placeholder="Search by username"/>
    <button @click="searchUserByName">Search</button>
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
      <tr v-for="(user_obj, index) in users" :key="index">
        <th>{{ user_obj.id }}</th>
        <td>{{ user_obj.userName }}</td>
        <td>{{ user_obj.email }}</td>
        <td>{{ user_obj.userRole?.name }}</td>
        <td>{{ user_obj.address }}</td>
        <td>
          <a href="#" @click.prevent="editUser(user_obj.id)"> Edit</a>&nbsp;
          <a href="#" @click.prevent="deleteUser(user_obj.id)">Delete</a>
        </td>
      </tr>
    </tbody>
  </table>
  <div class="pagination">
    <button @click="prevPage">Previous</button>
    <span>Page {{this.currentPage}}</span>
    <button @click="nextPage" :disabled="checkNextPage">Next</button>
  </div>
</template>
<script>
import axios from "axios";
export default{
  data(){
    return {
      userRoleId: {
        get(){
          return this.user.userRole ? this.user.userRole.id : null;
        },
        set(value){
          if(!this.user.userRole){
            this.user.userRole = {};
          }
          this.user.userRole.id = value;
        }
      },
      searchUserName: '',
      hasNextPage: false,
      totalPages: 0,
      currentPage: 1,
      pageSize: 3,
      openForm: false,
      isEditing: false,
      users: [],
      roles: [],
      user: {
        id: null,
        username: "",
        email: "",
        address: "",
        userRole: {
          id: null,
          name: "",
          description: ""
        } 
      },
      message: ""
    };
  },
  methods: {
    prevPage(){
      if(this.currentPage > 1){
        this.currentPage--;
        this.fetchUsers();
      }
    },
    searchUserByName(){
      this.currentPage = 1;
      this.fetchUsers();
    },
    nextPage(){
      this.currentPage++;
      this.fetchUsers();
    },
    async deleteUser(userId){
      await axios.delete(`http://localhost:8080/api/users/${userId}`)
      this.fetchUsers()
    },
    closeForm(){
      this.openForm = false;
    }
    ,
    openRegisterForm(){
      this.openForm = true;
    }
    ,
    assignRoleToUser(roleId){
      this.user.userRole = this.user.userRole || {};
      this.user.userRole.id = roleId;
    },
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
          userRole: null,
        };
    },
    async fetchRoles(){
      const response = await axios.get("http://localhost:8080/api/roles")
      this.roles = response.data
    },
    async fetchUsers(){
      const response = await axios.get(`http://localhost:8080/api/users?currentPage=${this.currentPage}&pageSize=${this.pageSize}&username=${this.searchUserName}`)
      this.users = response.data.data
      this.totalPages = response.data.numberOfPages
      this.hasNextPage = response.data.hasNextPage
    },
    async submitForm(){
      try{
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
  computed: {
    checkNextPage(){
      console.log("Hash Next page : ", this.hasNextPage)
      return !this.hasNextPage
    }
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

.pagination {
  margin-top: 20px;
  text-align: center;
}

.pagination button {
  padding: 8px 15px;
  margin: 5px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
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

/* Radio Button Styles */
.radio-group {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.radio-option {
  display: inline-flex;
  align-items: center;
  gap: 5px;
  border: 1px solid #ccc;
  padding: 5px 10px;
  border-radius: 20px;
  cursor: pointer;
  user-select: none;
  transition: 0.3s ease;
}

.radio-option.selected {
  background-color: #28a745;
  color: white;
  border-color: #28a745;
}

.radio-option input {
  display: none;
}

.search-form{
  margin: 20px 0;
  text-align: center;
}

.search-form input {
  width: 200px;
  padding: 8px;
  margin-top: 5px;
  border-radius: 4px;
  border: 1px solid #ccc;
}
.new-user-btn{
  background-color: #007bff;
  margin-bottom: 10px;
}

.radio-option:hover {
  border-color: #28a745;
}
.close-btn{
  position: relative;
  top: 10px;
  left: 200px;
  background: none;
  border: none;
  font-size: 20px;
  cursor: pointer;
  color: red;
}
</style>