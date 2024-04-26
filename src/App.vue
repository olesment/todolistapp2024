<template>
  <div id="createApp">
    <button type="button" class="createToDoModalVisibilityToggle" @click = "showCreateTodoModal" >Create New Todo List</button>

    <CreateToDoListModal v-show="isToDoCreationModalVisible" 
                         @freshToDoListCreationModalClosed="closeModal"    
                         @freshToDoListCreatedAndSent="handleFreshToDoInApp"                       
                         />
    <!--TODO MASTER DIV-->
    <div id="toDoLists" v-for="(toDoList, index) in toDoListsBank" :key="index">
      <span>Name: {{ toDoList.toDoListName }} Class: {{ toDoList.toDoListClass }} <br></span>
      Tasks:
    <!--TASK-LIST CLASS-->
      <div class="task-list">
    <!--INDIVIDUAL TASK DIV-->
        <div class="task-item" v-for="(task, taskIndex) in toDoList.toDoListTasksArray" :key="taskIndex"> 
          <div class="checkbox-container">
            <input 
              type="checkbox" 
              :id="'checkbox-' + taskIndex"
              :checked="toDoList.toDoListTasksArray[taskIndex].done" 
              :key="taskIndex" 
              v-on:click="toggleTaskStatus(toDoList, taskIndex)"
              />
            <label :for="'checkbox-' + taskIndex"><!--See ongi effectively mu checkbox, siia ei tohi midagi kirjutada, muidu renderdab CB sees. Siia ri tohi panna ka @ click, millel on sama nimi, mis on ka [lesandel, kuna label triggerdab ka CB [leval, mis siis omakorda eemaldab 2 list itemit--></label>
            <div class="task-name" @click="toggleTaskStatus(toDoList, taskIndex)">{{ task.name }}</div>
          </div>
          <button>Placeholder</button>
        </div>
      </div>
    </div> 
  </div>
</template>

<script>
//import { createApp } from 'vue';
import CreateToDoListModal from './components/CreateToDoListModal.vue'

export default {
  name: 'App',
  components: {
    CreateToDoListModal
  },
  data() {
    return{
      toDoListsBank:[], // Array that holds in it toDo Lists that are manipulated on the main page
      isToDoCreationModalVisible:false, // modal visibility toggle property
      //Empty object to give tasks from freshToDoList that come from Modal same parameters
      processedToDoListArray: 
            { 
              toDoListName:'',
              toDoListTrimmedName:'',
              toDoListClass:'',
              toDoListTasksArray:[],
            }
    };
  },
  methods:{
    showCreateTodoModal(){
      this.isToDoCreationModalVisible = true;
    },
    handleFreshToDoInApp(freshToDoList){
      //tasks need to get on same state
      freshToDoList.freshToDoListsTasksArray.forEach(task => {task.isEdited=false;}); 
      console.log("this is fresh toDoList after adding iS Edited", freshToDoList);
      this.processedToDoList={
        toDoListName:        freshToDoList.freshToDoListName,
        toDoListClass:       freshToDoList.freshToDoListClass,
        toDoListTrimmedName: freshToDoList.freshToDoListTrimmedName,
        toDoListTasksArray:  freshToDoList.freshToDoListsTasksArray
      };
      this.toDoListsBank.push(this.processedToDoList);
      console.log("This Is Processed To Do List", this.processedToDoList)
      console.log("This Is state of the bank after processing", this.toDoListsBank); // check - whats in the Bank?
    },
    closeModal(){
      this.isToDoCreationModalVisible = false;
    },
    toggleTaskStatus(toDoList, taskIndex){
      toDoList.toDoListTasksArray[taskIndex].done = !toDoList.toDoListTasksArray[taskIndex].done;
      if (toDoList.toDoListTasksArray[taskIndex].done===true) {
        toDoList.toDoListTasksArray.splice(taskIndex, 1);
      }
      console.log(toDoList.toDoListTasksArray);
    }
    
}
  }

</script>

<style>

.task-list{
  outline: 1px solid red;
  display:grid;
  grid-template-columns:auto 1fr auto;
  align-items:center;
  column-gap:10px;
}
.task-item{
  outline: 1px solid red;
}
input[type="checkbox"]{
  display:none;
  
}
input[type="checkbox"]+label{
  display:inline-block;
  width:20px;
  height:20px;
  background-color:#fff;
  border:1px solid #ccc;
  cursor:pointer;
}
/* input[type="checkbox"]:checked+label{
  background-color: #007bff;
  border-color:#007bff;
} */
label{
  margin:0;
}


</style>
