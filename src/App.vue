<template>
  <MCombobox
    
    :dataSource="[
                {genderCode:0, genderName:'Nam'},
                {genderCode:1, genderName:'Nữ'},
                {genderCode:2, genderName:'Khác'}
            ]"
    v-model:value="val"
    propVal="genderCode"
    propLabel="genderName"
    propName=""
  ></MCombobox>

  <h2>{{ val }}</h2>
  
</template>

<script>
import MCombobox from './components/MCombobox.vue'

export default {
  name: 'App',
  components: {
    MCombobox,
  },

  data() {
    return {
      val:null
    }
  },
}
</script>

<style>
  #app{
    background-color: #ccc;
    height: 100vh;
  }
</style>
