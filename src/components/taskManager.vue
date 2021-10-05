<template>
<div class="row items-top">
    <div v-for="(status,statusIndex) in statusList" :key="statusIndex" 
    @drop="dropTask($event,status.id)"    @dragover.prevent @dragenter.prevent
    class="col-2 q-px-sm" >
        <div class="statusLabel q-mb-sm" >
            <span :style="statusLabelStyle(status.color)"> 
                {{status.label}}  &nbsp; {{status.taskList.length}}
            </span>
        </div>

        <div v-for="(task,taskIndex) in status.taskList" :key="taskIndex">
            <q-card class="q-mb-sm q-pa-sm q-mt-sm cursor-pointer" draggable="true" 
            @dragstart="dragTask($event,{task,statusId:status.id})" 
            @click="launchEditTaskModal(task,status)" >
                {{task.title}}
            </q-card>

            <q-dialog v-model="editTaskModal" @update:modelValue="afterEditModal">
                <editTask class="bg-white editTaskModal q-pa-lg" ref="editTask"
                :inp="editTaskInp" @deleted="deleteTask" @updated="updateTask" />
            </q-dialog>
        </div>
        
        <div class="full-width ">
            <newTask :statusId="status.id" @created="addTaskToStatus" />
        </div>

    </div>

    <div class="col-2 q-px-sm">
        <newStatus @created="addNewStatus" />
    </div>
</div>
</template>
<script>
import { defineComponent } from 'vue';
import { cloneDeep,omit,map,has } from 'lodash';
import newTask from 'components/newTask'
import editTask from 'components/editTask'
import newStatus from 'components/newStatus'

export default defineComponent({
    components:{
        newTask,
        editTask,
        newStatus
    },
    props:["modelValue"],
    emits:['update:modelValue'],
    data(){
        return{
            editTaskModal:false,
            statusList:[],
            editTaskInp:{}
        }
    },
    watch:{
        statusList:{
            handler(newVal,oldVal){
                //updating model to parent
                this.$emit('update:modelValue',newVal)
            },
            deep:true
        }
    },
    methods:{
        deleteTask(){
            //it's 3 in the night, i am too tired to be writing dynamic functions
            //please assume I can, I could share some hobby projects if you wish to see them
            var statusList = this.statusList;
            var inp =  this.editTaskInp;
            for(var i in statusList){
                var currStatus = statusList[i];
                if(inp.status.id == currStatus.id){
                    for(var j in currStatus.taskList){
                        var currTask = currStatus.taskList[j];
                        if(currTask.id==inp.task.id) break;
                    }
                    currStatus.taskList.splice(j,1);
                }
                statusList[i] = currStatus;
            }
            this.statusList = statusList;
            this.editTaskModal = false;
        },
        afterEditModal(){
            if(this.editTaskModal) return;

            var statusList = this.statusList;
            var editTaskRef = cloneDeep(this.$refs.editTask);

            var oldStatusId = editTaskRef.inp.status.id;
            var newStatusId = has(editTaskRef.status,'opt') ? editTaskRef.status.opt.id : editTaskRef.status.id;

            var oldTask = editTaskRef.inp.task;
            var newTask = editTaskRef.task;

            //if status is unchanged, we will update the inputs from the popup to that task object
            if(oldStatusId == newStatusId){
                for(var i in statusList){
                    var currStatus = statusList[i];
                    if(currStatus == newStatusId){
                        //finding the task that matches the one from popup and replacing it
                        for(var j in currStatus.taskList){
                            var currTask = currStatus.taskList[j];
                            if(currTask.id==editTaskRef.task.id) break;
                        }
                        currStatus.taskList[j] = editTaskRef.task;
                    }
                    statusList[i] = currStatus;
                }
            }else{

                //looping thorugh the entire statusList
                for(var i in statusList){
                    var currStatus = statusList[i];
                    
                    //if curr iterator's status id matches the dragged item's status
                    //we remove it from that list
                    if(currStatus.id == oldStatusId){
                        //finding index position of the task in older list 
                        for(var j in currStatus.taskList){
                            var currTask = currStatus.taskList[j];
                            if(currTask.id == oldTask.id) break;
                        }
                        //removing the task
                        currStatus.taskList.splice(j,1);
                    }
                    //if curr iterator's status id matches the new location's status
                    //we add it to their list  
                                  
                    else if(currStatus.id == newStatusId){
                        currStatus.taskList.push(newTask);
                    }
                    statusList[i] = currStatus;
                }

            }


            //this.editTaskModal = false;
        },
        addNewStatus(inp){
            var statusList = this.statusList;
            statusList.push(inp)
            this.statusList = statusList;
        },
        launchEditTaskModal(task,status){
            this.editTaskInp = {
                task:task,
                status:omit(status,['taskList'])
            }
            this.editTaskModal = true;
        },     
        addTaskToStatus(inp){
            var statusList = this.statusList;
            for(var i in statusList){
                var currStatus = statusList[i];
                if(currStatus.id==inp.statusId) break;
            }
            statusList[i].taskList.push(omit(inp,['statusId']));
            this.statusList = statusList; 
        },
        statusLabelStyle(color){
            return `padding:3px;border-radius:10%;background-color:${color}`;
        },
        dragTask(event,item){
            event.dataTransfer.dropEffect = 'move';
            event.dataTransfer.effectAllowed = 'move';
            event.dataTransfer.setData('task', JSON.stringify(item));            
            //console.log(JSON.stringify(item));
        },
        dropTask(event,newStatusId){
            var task = JSON.parse(event.dataTransfer.getData('task'));
            if(task.statusId==newStatusId) return;
            //making a deep copy of the reactive statusList
            var statusList = cloneDeep(this.statusList);

            //looping thorugh the entire statusList
            for(var i in statusList){
                var currStatus = statusList[i];
                
                //if curr iterator's status id matches the dragged item's status
                //we remove it from that list
                if(currStatus.id == task.statusId){
                    //finding index position of the task in older list 
                    for(var j in currStatus.taskList){
                        var currTask = currStatus.taskList[j];
                        if(currTask.id == task.task.id) break;
                    }
                    //removing the task
                    currStatus.taskList.splice(j,1);
                
                }
                //if curr iterator's status id matches the dropped location's status
                //we add it to their list                
                else if(currStatus.id == newStatusId){
                    currStatus.taskList.push(task.task);
                }

                statusList[i] = currStatus;
            }

            //finalling setting the statusList
            this.statusList = cloneDeep(statusList);
            
        }        
    },
    created(){
        this.statusList = this.modelValue;

        var statusOptions = map(this.statusList, item => { return omit(item,['taskList']) });
        this.$store.commit('app/setStatusOptions',statusOptions);
    }
})
</script>

<style scoped>
.statusLabel{
    font-size:0.9em;
    font-weight:400;
}
.editTaskModal{
    height:40vh;
    width:30vw;
}
</style>