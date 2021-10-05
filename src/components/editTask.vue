<template>
<div>
    <div class="full-width row justify-end">
        <q-icon class="col-2 cursor-pointer" name="fas fa-ellipsis-h" size="xs" >
            <q-menu fit anchor="bottom left" self="top left" >
                <q-list style="min-width: 50px">
                    <q-item dense clickable v-close-popup>
                        <q-item-section @click="$emit('deleted')">
                            Delete
                        </q-item-section>
                    </q-item> 
                </q-list>               
            </q-menu>
        </q-icon>
    </div>
    <div class="full-width row items-center">
        <div class="col-8">
            <q-input size="md" v-model="task.title" borderless placeolder="Title" />            
        </div>
        <div class="col-4">
            <q-select dense borderless :options="statusOptions" v-model="status" hide-dropdown-icon 
            rounded class="statusSelectLabel"  >

                <template v-slot:selected>
                    <q-item class="row"  >
                        <div class="col-3 q-pr-md" :style="statusColorStyle(status.color)" >
                            <q-icon name="fas fa-square" />
                        </div>
                      <div class="col-9 statusSelectLabel">
                            {{status.label}} 
                      </div>
                    </q-item>
                </template> 

                <template v-slot:option="scope">
                    <q-item class="row" clickable @click="status=scope" v-close-popup >
                        <div class="col-3" :style="statusColorStyle(scope.opt.color)" >
                            <q-icon name="fas fa-square" />
                        </div>  
                      <div class="col-9 statusSelectLabel">
                            {{scope.label}} 
                      </div>
                    </q-item>
                </template> 
                
            </q-select>
        </div>
    </div>

    <div class="full-width">
        <q-input size="xs" v-model="task.description" borderless placeholder="Description" />
    </div>    


    


</div>
</template>

<script>
import {defineComponent} from 'vue'

export default defineComponent({
    props:['inp'],
    emits:['deleted'],
    data(){
        return{
            task:{
                title:"",
                description:"",
                id:""
            },
            status:{
                label:"",
                color:"",
                id:""
            },
                        
        }
    },
    computed:{
        statusOptions(){
            return this.$store.getters['app/statusOptions']
        }
    },
    methods:{
        statusColorStyle(color){
            return `color:${color}`;
        }
    },
    created(){
        this.task = this.inp.task;
        this.status = this.inp.status;
    }
});
</script>

<style>
.statusSelectLabel{
    font-size:0.9em;
}
</style>