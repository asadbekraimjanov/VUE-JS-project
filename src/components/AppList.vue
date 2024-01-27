<template>
  <tr v-if="userList.length > 0" v-for="(item,index) in userList" :key="item.id">
    <th>{{index + 1}}</th>
    <th>{{item.id}}</th>
    <th>{{item.name}}</th>
    <th>{{ item.username }}</th>
    <th>{{ item.email }}</th>
    <th>{{ item.address.city }}</th>
    <th><button class="btn" @click="$emit('editList', index)"><i class="fa-solid fa-pen-to-square"></i></button></th>
    <th><button class="btn" @click="$emit('deleteList', index)"><i class="fa-solid fa-trash"></i></button></th>
    <th><button class="btn" @click="$emit('addMiddleList', index)"><i class="fa-solid fa-circle-plus"></i></button></th>
  </tr>
</template>

<script>
export default {
  props:{
    userList: {
      required:true
    }
  },
  methods:{

  }
}
</script>

<style scoped>
tr{
  width: 70%;
}
tr th:last-child{
  padding: 5px;
}
.fa-pen-to-square{
  background-color: #07cc07;
  width: 100%;
  height: 100%;
  padding: 5px;
  border-radius: 4px;
  color: #ffffff;
  font-size: 14px;
}
.fa-trash{
  background-color: red;
  width: 100%;
  height: 100%;
  padding: 5px;
  border-radius: 4px;
  color: #ffffff;
  font-size: 14px;
}
.fa-circle-plus{
  width: 100%;
  height: 100%;
  scale: 1.4;
  color: darkorange;
}
.btn{
  padding: 0;
  width: 70%;
}
.btn:active{
  border: 1px solid white;
}
.btn:active .fa-solid{
  color: #8d8d8d;
}
</style>
