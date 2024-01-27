<template>
  <div class="content">
    <app-lists></app-lists>
  </div>

</template>

<script>
import AppLists from './components/AppLists.vue'
export default {
  data(){
    return{

    }
  },
  components: {
    'app-lists': AppLists
  },
  methods:{

  }
}
</script>

<style>

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
