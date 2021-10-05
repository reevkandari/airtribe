<template>
<q-page class="">

  <div class="full-width q-mt-xl">

    <div class="row justify-center">
      <taskManager class="col-10" v-model="taskManagerInp" @update:modelValue="saveToLocal" />
    </div>

  </div>

</q-page>
</template>

<script>
import { defineComponent } from 'vue';
const initial = require('boot/initial.json');
import  taskManager from 'components/taskManager'

export default defineComponent({
  name: 'indexPage',
  components:{
    taskManager
  },
  data(){
    return{
      render:false,
      taskManagerInp:{}
    }
  },
  methods:{
    saveToLocal(val){
      
      //This function will be called everytime the model value is updated 
      //it saves the data to local storage
      this.$q.localStorage.set('statusList',val);
    }
  },
  created(){
    var statusList = this.$q.localStorage.getItem('statusList');
    this.taskManagerInp = statusList || initial;
    this.render = false;
  }
})
</script>
