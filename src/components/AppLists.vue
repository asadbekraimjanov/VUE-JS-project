<template>
  <table :class="{'main-hidden' : hideViewer}" class="table">
    <thead>
      <tr>
        <th>№</th>
        <th>ID</th>
        <th>Name<br><input v-model="nameInput" @input="filter" class="search-input" type="search" placeholder="Seach.."></th>
        <th>Username<br><input v-model="usernameInput" @input="filter" class="search-input" type="search" placeholder="Seach.."></th>
        <th>email<br><input v-model="emailInput" @input="filter" class="search-input" type="search" placeholder="Seach.."></th>
        <th>Address<br><input v-model="addressInput" @input="filter" class="search-input" type="search" placeholder="Seach.."></th>
        <th>Edit</th>
        <th style="width: 50px">Delete</th>
        <th>Add</th>
      </tr>
    </thead>
    <tbody>
      <app-list
          @editList="editListItem"
          @deleteList="deleteListItem"
          @addMiddleList="addMiddle"
          :counter1="counter1"
          :counter2="counter2"
          :counter3="counter3"
          :counter4="counter4"
          :viewer="viewer"
          :userList="userList"
          :filteredList="filteredUserList"
      ></app-list>
    </tbody>
  </table>
  <button @click="addNewUser" class="btn btn-add" :class="{'main-hidden' : hideViewer}">New User</button>
  <button @click="inputClr" class="btn delete-input">Clr</button>
  <div v-if="newUserWindow" class="new-user">
    <button class="exit-btn" @click="addWindowClose">X</button>
    <p v-if="openNewUser">Yangi user qo'shish</p>
    <p v-if="openEditUser">O'zgartirish</p>
    <form class="form-control">
      <label for="addName">Nomi <input id="addName" type="text" placeholder="Nomi" v-model="newUser.name"></label>
      <label for="addUsername">Username <input id="addUsername" type="text" placeholder="username" v-model="newUser.username"></label>
      <label for="addEmail">Email <input id="addEmail" type="email" placeholder="e-mail" v-model="newUser.email"></label>
      <label for="addAddress">Address <input id="addAddress" type="text" placeholder="address" v-model="newUser.address.city"></label>
      <button v-if="openNewUser" :disabled="newUser.name.length===0" @click="addNewUserlist" class="btn btn-add_2">Qo'shish</button>
      <button v-if="openEditUser" :disabled="newUser.name.length===0" @click="editUserList" class="btn btn-add_2">O'zgartirish</button>
      <button v-if="middleAdd" :disabled="newUser.name.length===0" @click="middleAddList" class="btn btn-add_2">Qo'shish</button>
    </form>
  </div>
</template>

<script>
import AppList from './AppList.vue'
import axios from "axios";
export default {
  data(){
    return {
      viewer: 0,
      middleAdd: false,
      openEditUser: false,
      openNewUser: false,
      hideViewer: false,
      newUserWindow: false,
      nameInput: '',
      usernameInput: '',
      emailInput: '',
      addressInput: '',
      filteredUserList: [],
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
    inputClr(){
      this.nameInput = ''
      this.usernameInput = ''
      this.emailInput = ''
      this.addressInput = ''
      this.viewer = 0
    },
    filter(){
      this.filteredUserList = []
        for (let i = 0; i < this.userList.length; i++){
          if (this.userList[i].name.toLowerCase().includes(this.nameInput) && this.userList[i].username.toLowerCase().includes(this.usernameInput) && this.userList[i].email.toLowerCase().includes(this.emailInput) && this.userList[i].address.city.toLowerCase().includes(this.addressInput)) {
            this.filteredUserList.push(this.userList[i])
          }
        }
        if (this.nameInput == '' && this.usernameInput == '' && this.emailInput == '' && this.addressInput == ''){
          this.viewer = 0
        }
        else {
          this.viewer = 1
        }
    },

    middleAddList(){
      axios.post('https://jsonplaceholder.typicode.com/users', {
        name: this.newUser.name,
        username: this.newUser.username,
        email: this.newUser.email,
        address: {
          city: this.newUser.address.city
        }
      }).then(rest => {
        this.userList.splice(this.idx, 0, rest.data)
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
      this.middleAdd = false

      this.newUser.name = ''
      this.newUser.username = ''
      this.newUser.email = ''
      this.newUser.address.city = ''
    },
    deleteListItem(id){
      axios
        .delete("https://jsonplaceholder.typicode.com/users/" + id)
        .then(response => {
          this.userList.splice(id, 1);
      });
    },
    addNewUser(){
      this.hideViewer = true
      this.newUserWindow = true
      this.openNewUser = true
    },
    getUsers(){
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
.delete-input{
  background-color: #757575;
  font-weight: 600;
  width: 100px;
  color: #ffffff;
  margin-left: 20px;
}
.delete-input:hover{
  background-color: #3a3a3a;
  color: #ffffff;
}
.search-input{
  border-radius: 20px;
  height: 30px;
  width: 100%;
  border: 2px solid #525252;
  font-size: 16px;
}
.search-form input::placeholder{
  padding-left: 30px;
}
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
  box-shadow: var(--el-box-shadow-lighter)
}
th{
  border: 1px solid #181818;
  font-weight: 400;
  text-align: center;
}
</style>
