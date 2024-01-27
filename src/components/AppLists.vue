<template>
  <table :class="{'main-hidden' : hideViewer}" class="table">
    <thead>
      <tr>
        <th>№</th>
        <th>ID</th>
        <th>Name</th>
        <th>Username</th>
        <th>email</th>
        <th>Address</th>
        <th>Edit</th>
        <th style="width: 70px">Delete</th>
        <th>Add</th>
      </tr>
    </thead>
    <tbody>
      <app-list @editList="editListItem" @deleteList="deleteListItem" @addMiddleList="addMiddle" :userList="userList"></app-list>
    </tbody>
  </table>
  <button @click="addNewUser" class="btn btn-add" :class="{'main-hidden' : hideViewer}">New User</button>
  <div v-if="newUserWindow" class="new-user">
    <button class="exit-btn" @click="addWindowClose">X</button>
    <p v-if="openNewUser">Yangi user qo'shish</p>
    <p v-if="openEditUser">O'zgartirish</p>
    <form class="form-control">
<!--      <label for="addId">ID <input id="addId" type="number" placeholder="id" v-model="newUser.id"></label>-->
      <label for="addName">Nomi <input id="addName" type="text" placeholder="Nomi" v-model="newUser.name"></label>
      <label for="addUsername">Username <input id="addUsername" type="text" placeholder="username" v-model="newUser.username"></label>
      <label for="addEmail">Email <input id="addEmail" type="email" placeholder="e-mail" v-model="newUser.email"></label>
      <label for="addAddress">Address <input id="addAddress" type="text" placeholder="address" v-model="newUser.address.city"></label>
      <button v-if="openNewUser" @click="addNewUserlist" class="btn btn-add_2">Qo'shish</button>
      <button v-if="openEditUser" @click="editUserList" class="btn btn-add_2">O'zgartirish</button>
      <button v-if="middleAdd" @click="middleAddList" class="btn btn-add_2">Qo'shish</button>
    </form>
  </div>
</template>

<script>
import AppList from './AppList.vue'
import axios from "axios";
export default {
  data(){
    return {
      middleAdd: false,
      openEditUser: false,
      openNewUser: false,
      hideViewer: false,
      newUserWindow: false,
      userList: [],
      idx: null,
      num: null,
      id: null,
      newUser: {
        name: '',
        username: '',
        email: '',
        address: {
          city: ''
        }
      }
    }
  },
  methods:{
    middleAddList(){
      console.log('idx: ' + this.idx)
      axios.post('https://jsonplaceholder.typicode.com/users', {
        name: this.newUser.name,
        username: this.newUser.username,
        email: this.newUser.email,
        address: {
          city: this.newUser.address.city
        }
      }).then(rest => {
        this.userList.splice(this.idx, 0, rest.data)
        // console.log(rest.data)
      }).catch(function (error){
        console.log(error)
      })
      console.log(this.userList)

      this.newUser.name = ''
      this.newUser.username = ''
      this.newUser.email = ''
      this.newUser.address.city = ''

      this.hideViewer = false
      this.newUserWindow = false
      this.middleAdd = false
    },
    addMiddle(idx){
      this.idx = idx
      this.hideViewer = true
      this.newUserWindow = true
      this.middleAdd = true
    },
    editUserList(){
      this.hideViewer = false
      this.newUserWindow = false
      this.openEditUser = false
      axios.put('https://jsonplaceholder.typicode.com/users/'+this.id,{
        name: this.newUser.name,
        username: this.newUser.username,
        email: this.newUser.email,
        address: {
          city: this.newUser.address.city
        }
      }).then(rest => {
        this.userList[this.id].name = rest.data.name
        this.userList[this.id].username = rest.data.username
        this.userList[this.id].email = rest.data.email
        this.userList[this.id].address.city = rest.data.address.city
      }).catch(function (error){
        console.log(error)
      })

      this.newUser.name = ''
      this.newUser.username = ''
      this.newUser.email = ''
      this.newUser.address.city = ''
    },
    editListItem(id){
      this.id = id
      this.hideViewer = true
      this.newUserWindow = true
      this.openEditUser = true
      this.newUser.name = this.userList[id].name
      this.newUser.username = this.userList[id].username
      this.newUser.email = this.userList[id].email
      this.newUser.address.city = this.userList[id].address.city
    },
    addWindowClose(){
      this.hideViewer = false
      this.newUserWindow = false
      this.openNewUser = false
      this.openEditUser = false

      this.newUser.name = ''
      this.newUser.username = ''
      this.newUser.email = ''
      this.newUser.address.city = ''
    },
    deleteListItem(id){
      // this.userList.splice(id, 1)
      axios
        .delete("https://jsonplaceholder.typicode.com/users/" + id)
        .then(response => {
          this.userList.splice(id, 1);
          // console.log(response);
      });
    },
    addNewUser(){
      this.hideViewer = true
      this.newUserWindow = true
      this.openNewUser = true
    },
    getUsers(){
      // console.log(this.userList)
      axios.get('https://jsonplaceholder.typicode.com/users')
          .then(rest => {
            this.userList = rest.data
          }).catch(error => console.log(error))
    },
    addNewUserlist(){
      this.hideViewer = false
      this.newUserWindow = false
      axios.post('https://jsonplaceholder.typicode.com/users', {
        name: this.newUser.name,
        username: this.newUser.username,
        email: this.newUser.email,
        address: {
          city: this.newUser.address.city
        }
      }).then(rest => {
        this.userList.push(rest.data)
        // console.log(rest.data)
      }).catch(function (error){
        console.log(error)
      })
      this.newUser.name = ''
      this.newUser.username = ''
      this.newUser.email = ''
      this.newUser.address.city = ''
      this.openNewUser = false
    },
  },
  created() {
    this.getUsers()
  },
  components: {
    'app-list': AppList
  }
}
</script>

<style>
.exit-btn{
  position: absolute;
  border: none;
  margin-left: 93%;
}
.btn-add{
  background-color: #00c4ff;
  font-weight: 600;
  color: #ffffff;
  margin-left: 20px;
}
.btn-add_2{
  background-color: #00c4ff;
  font-weight: 600;
  color: #ffffff;
  margin-top: 10px;
}
.btn-add:hover{
  background-color: #00c4ff;
  color: #939393;
}
.main-hidden{
  opacity: 0.1;
}
.new-user{
  position: absolute;
  background-color: #ffffff;
  width: 70vh;
  margin-left: 650px;
  margin-top: -350px;
  padding: 10px;
  box-shadow: 0 0 3px 1px #c0c0c0;
}

.table{
  font-weight: 300;
  width: 70%;
  margin: 20px;
}
th{
  border: 1px solid #181818;
  font-weight: 400;
  text-align: center;
}
</style>
