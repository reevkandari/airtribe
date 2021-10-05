<template>
<div >
    <div v-if="clicked" class="q-px-xs cursor-pointer" >                
        <q-input autofocus  class="q-px-xs q-mx-xs q-my-sm newstatusInput"  borderless v-model="status.label" dense 
        @focusout="createStatus" :class="{'bg-grey-3':clicked}" />
    </div>  
    <div v-else class="newstatusLabel cursor-pointer" @click="clicked=true"  >
        + New Status
    </div>
    
</div>
</template>

<script>
import { defineComponent } from 'vue';
import { nanoid } from 'nanoid'
import {cloneDeep} from 'lodash'

export default defineComponent({
    emits:["created"],
    data(){
        return{
            clicked:false,
            statusTemplate:{
                id:"",
                label:""
            },            
            status:{
                id:"",
                label:""
            }
        }
    },
    methods:{
        async createStatus(){
            var lightness = Math.floor(Math.random() * (95 - 85 + 1) + 85);
            var hue = Math.random() * 360;
            
            var status = this.status;
            if(status.label.length>0) {
                status.id = nanoid();
                status.color  = `hsl(${hue}, 100%, ${lightness}%)`;
                status.taskList = [];
                this.$emit("created",status);
            };
          
            this.status = cloneDeep(this.statusTemplate);
            this.clicked=false;
        }
    }
});

</script>

<style >
.newstatusInput{
    margin-top:0%;
    caret-color: transparent;

}
.newstatusLabel{
    font-size:0.9em;
    font-weight:400;
    color:grey;
}
.q-field__control{
    height:20px !important;
}
</style>