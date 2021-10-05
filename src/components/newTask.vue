<template>
<div>
    <q-card v-if="clicked" class="q-px-xs cursor-pointer" >                
        <q-input autofocus  class="q-mx-xs q-my-sm newTaskInput" borderless v-model="task.title" dense 
        @focusout="createTask"  />
    </q-card>  
    <div v-else class="newTaskLabel cursor-pointer" @click="clicked=true"  >
        + New Task
    </div>
    
</div>
</template>

<script>
import { defineComponent } from 'vue';
import { nanoid } from 'nanoid'


export default defineComponent({
    emits:["created"],
    props:["statusId"],
    data(){
        return{
            clicked:false,
            taskTemplate:{
                title:"",
                description:"",
                id:""
            },            
            task:{
                id:"",
                title:"",
                description:""
            }
        }
    },
    methods:{
        async createTask(){
            var task = this.task;
            if(task.title.length>0) {
                task.id = nanoid();
                task.statusId = this.statusId;
                this.$emit("created",task);   
            };
         
            this.task = this.taskTemplate;
            this.clicked=false;
        }
    }
});

</script>

<style>
.newTaskInput{

    caret-color: transparent;
}
.newTaskLabel{
    font-size:0.9em;
    font-weight:400;
    color:grey;
}
.q-field__control{
    height:40px !important;
}
</style>