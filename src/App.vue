<template>
  <div class="content">
    <app-lists></app-lists>
  </div>
  <input id="input-file" ref="inputFile" @input="inputElement" class="file-input" type="file">
  <el-button @mouseup="saveFile" type="primary" class="btn1">Save</el-button>

</template>
<script>
import AppLists from './components/AppLists.vue'
import axios from "axios";
import {ElMessage} from "element-plus";
import {User, Warning} from "@element-plus/icons-vue";
export default {
  data(){
    return{
      file: null,
      clickedRadio: "3",
    }
  },
  methods: {
    inputElement(event){
      this.file = event.target.files[0]
      console.log(event.target.files)
      // console.log(this.$refs.inputFile.files)
    },
     async saveFile(){
      const response = await fetch('https://project-1-7f7d0-default-rtdb.firebaseio.com/images.json', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          name: this.file.name,
          size: this.file.size/1000 + ' kb',
          date: this.file.lastModifiedDate
        })
      })

       const firebaseData = await response.json()
       console.log(firebaseData)
    }
  },
  components: {
    User,
    Warning,
    'app-lists': AppLists
  }
}
</script>

<style>
.file-input{
  margin-top: 20px;
  translate: 20px;
  width: 270px;
  margin-right: 20px;
  float: left;
}
.btn1{
  margin: 20px;
  font-weight: 600;
  width: 100px;
  min-height: 35px;
}

label{
  font-weight: 600;
  width: 100%;
  margin: 5px 0;
}
input{
  outline: unset;
  float: right;
  width: 100%;
}
input:focus{
  border: 1px solid #0e556b;
  padding: 2px 3px;
  box-shadow: 0 0 1px 2px #00c4ff;
}
.new-user p{
text-align: center;
}
</style>
