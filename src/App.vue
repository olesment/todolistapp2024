<template>
  <div id="createApp">
    <button type="button" class="createToDoModalVisibilityToggle" @click = "showCreateTodoModal" >Create New Todo List</button>

    <CreateToDoListModal v-show="isToDoCreationModalVisible" 
                         @freshToDoListCreationModalClosed="closeModal"    
                         @freshToDoListCreatedAndSent="handleFreshToDoInApp"                       
                         />
    <div v-for="(toDoList, index) in toDoListsBank" :key="index">
        {{ toDoList.toDoListName }} - {{ toDoList.toDoListClass }} - Tasks:
        <div v-for="(task, taskIndex) in toDoList.toDoListTasksArray" :key="taskIndex"> {{ task.name }}</div>
        <!--div v-for="(toDoLists.toDoList.tasks, index) in toDoLists.toDoList.tasks" :key="index">{{  }}</!--div-->
      <button></button>
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
    }
    
}
  }

</script>

<style>
/* Your styles here */
</style>
