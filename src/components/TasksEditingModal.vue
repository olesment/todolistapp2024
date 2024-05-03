
import { toDisplayString } from 'vue';

import { TrackOpTypes } from 'vue';
<template>
    <div v-show="visible" id="tasks-editing-modal-backdrop">
        <div  class="modal">
            <div id="tasks-editing-modal-title>">
                <h3>Tasks Editor</h3>
                <a>Edit tasks names, click confirm after all editing is done</a>
            </div>
            <div class = "tasks-container" 
                 v-for="(task, index) in tasksBeingEdited" :key="task.id">
                <input v-model="task.name" 
                        placeholder="{{ task.name }}" 
                        @input="editTask(index, task.name)"/>
                <button @click="deleteTask(index)">Delete Task</button>
            </div>
            <div class="end-buttons-container">
                <button @click="confirmChanges">Confirm</button>
                <button @click="close">Cancel</button>
            </div>
        </div>
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
                visible: this.isVisible, // This controls visibility 
                                  //internally, moved fom above
                tasksBeingEdited:[]
            };
        },
        watch:{
            isVisible(newVal){
                this.visible=newVal;
            },
            tasks:{
                handler(newVal){
                    this.tasksBeingEdited = JSON.parse(JSON.stringify(newVal));
                },
                immediate:true
            }
        },
        methods:{
            open(){
                this.visible = true;
            },
            editTask(index, newName){
                this.tasksBeingEdited[index].name = newName;
            },
            deleteTask(index){
                this.tasksBeingEdited.splice(index,1);
            },
            confirmChanges(){
                this.$emit('update-tasks', this.tasksBeingEdited);
                this.close();
            },
            close(){
                this.visible=false;

                //this.$emit('close-modal');
            }  
        }
    }
</script>

<style>
    #tasks-editing-modal-backdrop{
        display:flex;
        align-items:center;
        justify-content: center;
        position:fixed;
        top:0;
        left:0;
        width:100%;
        height:100%;
        
        z-index:1000;
    }
    .modal{
        display:flex;
        flex-direction: column;
        background-color:rgba(229, 143, 223, 0.6);
        padding:20px;
        border: 2px solid black;
    }
    .end-buttons-container{
        display:flex;
        flex-direction: row;
        padding-top: 20px;
    }
    .tasks-container{
        outline: 2px solkid black;
    }
</style>