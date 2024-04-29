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
        <div class="task-item" v-for="task in toDoList.toDoListTasksArray" :key="task.id"> 
          <div class="checkbox-container">
            <input 
            type="checkbox" 
            :id="'checkbox-' + task.id"
            @click="toggleTaskStatus(toDoList, task)"
            />
          </div>
          <div class="task-name" 
               @click="toggleTaskStatus(toDoList, task)">
                {{ task.name }}
          </div>
          <div class="placeholder-btn-container">
            <button>Placeholder</button>
          </div>
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
      let taskIdTicker=1;
      //tasks need to get on same state which means i have to manually set is Edited to flase. 
      freshToDoList.freshToDoListsTasksArray.forEach(function(task)
      { task.isEdited=false;
        //for checkboxes to work properly i need proper unique id, which otherwise vue or dom mismanage checkbox state. 
        task.id=`${freshToDoList.freshToDoListTrimmedName}-${taskIdTicker}`;
        taskIdTicker++;
      }); 
      //State Check
      console.log("this is fresh toDoList after adding iS Edited", freshToDoList);
      //Maturing of fresh list to real list
      this.processedToDoList={
        toDoListName:        freshToDoList.freshToDoListName,
        toDoListClass:       freshToDoList.freshToDoListClass,
        toDoListTrimmedName: freshToDoList.freshToDoListTrimmedName,
        toDoListTasksArray:  freshToDoList.freshToDoListsTasksArray
      };
      this.toDoListsBank.push(this.processedToDoList);
      //State check
      console.log("This Is Processed To Do List", this.processedToDoList)
      //State check
      console.log("This Is state of the bank after processing", this.toDoListsBank); 
    },
    closeModal(){
      this.isToDoCreationModalVisible = false;
    },
    toggleTaskStatus(toDoList, task){
      task.done = true;
      //toDoList.toDoListTasksArray[taskIndex].done = !toDoList.toDoListTasksArray[taskIndex].done;
      if (task.done===true) {
         const index = toDoList.toDoListTasksArray.indexOf(task);
      if (index > -1) {
        toDoList.toDoListTasksArray.splice(index, 1);
      }
    }
    //State check
    console.log(JSON.stringify(toDoList.toDoListTasksArray));
    }
    
}
  }

</script>

<style>
#toDoLists{
  max-width: 50%;
}
.task-list{
  max-width: 25%;
  outline: 3px solid black;
  display:flex;
  flex-direction: column;
}
.task-item{
  display: inline-grid;
  grid-template-columns: min-content 1fr min-content;
  align-items: center;
  gap: 10px;
  outline: 3px dotted red;
}
.checkbox-container{
  outline: 3px dashed darkgoldenrod;
  display:grid;
  place-items: center;
}
input[type="checkbox"]{
  transform: scale(1.5);
  cursor:pointer;
}

.placeholder-btn-container{
  outline: 2px dashed yellow;
}

.task-item>.task-name{
  text-align: center;
  outline: 3px dashed rgb(30, 0, 255);
  cursor:pointer;
}


</style>
