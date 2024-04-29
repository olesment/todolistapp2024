
import { toDisplayString } from 'vue';

import { TrackOpTypes } from 'vue';
<template>
    <div v-if="isVisible" class="modal">
        <h3>Tasks Editor</h3>
        <div v-for="(task, index) in tasksBeingEdited" :key="task.id">
            <input v-model="task.name" placeholder="task.name" @input="editTask(index, task.name)"/>
            <button @click="deleteTask(index)">Delete Task</button>
        </div>
        <button @click="confirmChanges">Confirm</button>
        <button @click="closeModal">Cancel</button>
    </div>
</template>

<script>
    export default{
        props:{
            tasks: Array,
            isVisible: Boolean
        },
        data(){
            return{
                tasksBeingEdited:[]
            };
        },
        watch:{
            tasks:{
                handler(newVal){
                    this.tasksBeingEdited = JSON.parse(JSON.stringify(newVal));
                },
                immediate:true
            }
        },
        methods:{
            editTask(index, newName){
                this.tasksBeingEdited[index].name = newName;
            },
            deleteTask(index){
                this.tasksBeingEdited.splice(index,1);
            },
            confirmChanges(){
                this.$emit('update-tasks', this.tasksBeingEdited);
                this.closeModal();
            },
            closeModal(){
                console.log("closeModal called, isVisible before set to false:", this.isVisible);
                this.$emit('close-modal');
                console.log("closeModal called, isVisible after set to false:", this.isVisible);
            }  
        }
    }
</script>