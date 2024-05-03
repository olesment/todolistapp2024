<template>
  <div id="createApp">
    <button type="button" class="showCreateTodoModal" @click = "showCreateTodoModal" >Create New Todo List</button>

    <CreateToDoListModal 
            @freshToDoListCreationModalClosed="closeModal"    
            @freshToDoListCreatedAndSent="handleFreshToDoInApp"                 ref="createToDoListModal"      />
    <!--TODO MASTER DIV-->
    <div id="toDoLists" 
         v-for="(toDoList, index) in toDoListsBank" 
         :key="index">
      <span>Name: {{ toDoList.toDoListName }} Class: {{ toDoList.toDoListClass }} <br></span>

      <!--PLACE FOR ADDING NEW TASK WITHIN A RENDERED TODO-->
      <input class="new-task-adding-input-field" 
             placeholder="New Task Name Here" 
             v-model="newTaskName[toDoList.id]"/>

      <button class="task-adding-button" 
              @click="addTaskToThisTasksList(toDoList)">Add new task</button>
      Tasks:
      <!--TASK EDITING INVOCATION BUTTON-->
      <button @click="openTasksEditingModal(toDoList)">Edit Tasks</button>
       <!--TASK-LIST CLASS-->
       <div class="task-list">
       <!--INDIVIDUAL TASK DIV-->
        <div class="task-item" 
             v-for="task in toDoList.toDoListTasksArray" 
             :key="task.id">

          <div class="checkbox-container">
            <input type="checkbox" 
                   :id="'checkbox-' + task.id"
                   @click="toggleTaskStatus(toDoList, task)"/>
          </div>

          <div class="task-name" 
               @click="toggleTaskStatus(toDoList, task)">
               {{ task.name }}
          </div>

          <div class="placeholder-btn-container">
            <button>Edit/Delete</button>
          </div>
        </div>

        <!---DONE TASKS SECTION-->
        <div class="done-tasks-container" 
           v-if="toDoList.doneTasksArray.length>0">

           <h3>Completed tasks</h3>
           
          <div class="done-task-items" 
                v-for="doneTask in toDoList.doneTasksArray" :key="'doneTask' + doneTask.id" >
                {{ doneTask.name }}
          </div>
        </div>
      </div>
    </div>
    <tasks-editing-modal
      ref="tasksEditingModal"
      :tasks="currentTasks"
      @update-tasks="updateTasks"
      @close-modal="closeTasksEditingModal"/>
  </div>
</template>

<script>
//import { createApp } from 'vue';
import CreateToDoListModal from './components/CreateToDoListModal.vue'
import TasksEditingModal from './components/TasksEditingModal.vue'
export default {
  name: 'App',
  components: {
    CreateToDoListModal,
    TasksEditingModal
  },
  data() {
    return{
      //Array that holds all rendered ToDoListObjects
      toDoListsBank:[], //Lists that are manipulated on the main page

      //Empty object to manipulate tasks that come from ToDoCreationModal's fresh toDoList                         
      processedToDoListArray:         // Empty object to give tasks from 
            {                         // freshToDoList
              toDoListName:'',        // that come from initial tasks 
              toDoListTrimmedName:'', // creating modal same parameters
              toDoListClass:'',
              toDoListTasksArray:[],
              doneTasksArray:[]
            },
      
      
      //TASKS EDITING MODAL PARTS
      currentTasks:[],
      currentListIndex:null,

      //empty object for adding new tasks within a toDoList
      newTaskName:{}
    };
  },
  methods:{
    showCreateTodoModal(){
      this.$refs.createToDoListModal.open();
      console.log(`Location: App.vue
Pressed: showCreateTodoModal
Method: showCreateToDoModal()  
Effect: ToDoList Creation Modal should be open now`)
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

      this.freshToDoList = {...freshToDoList};

      //State Check of the freshToDoListsTasksArrayAfter adding isEdited and id
      console.log(`Location: App.vue
FreshtoDoList was sent from CreateToDoListModal
now so that isEdited value would get changed to same in all tasks in App.vue
Method: showCreateToDoModal()  
Effect: All tasks should have their property isEdited = false
Tasks: ${JSON.stringify(this.freshToDoList.freshToDoListsTasksArray, null, 2)}`);

      //Maturing of fresh list to real list
      this.processedToDoList={
        toDoListName:        freshToDoList.freshToDoListName,
        toDoListClass:       freshToDoList.freshToDoListClass,
        toDoListTrimmedName: freshToDoList.freshToDoListTrimmedName,
        // if this is not mentioned it gets booted from the composition
        doneTasksArray:[], 
        toDoListTasksArray:  freshToDoList.freshToDoListsTasksArray  
      };

      console.log(`Location: App.vue
Item: processedToDoList
After freshToDoList gets Id and isEdited updated
Now freshToDoList gets remapped to processedToDoList
now so that isEdited value would get changed to same in all tasks in App.vue 
Effect: processedToDoList should have all the freshToDoList properties
processedToDoList contents: ${JSON.stringify(this.processedToDoList, null, 2)}`);
      this.toDoListsBank.push(this.processedToDoList);
      //State check
      console.log(`Location: App.vue
Item: ToDoListBank
ProcessedtoDoList should get pushed to toDoListBank
Effect: processedToDoList should have processedToDoListsTasksArray tasks that have all the freshToDoList initial and added
ToDoListBanks contents: ${JSON.stringify(this.toDoListsBank, null, 2)}`);
      //State check
      console.log("ToDoListBanks state after handleFreshTodo method ends", this.toDoListsBank); 
    },
    closeCreateToDoModal(){
      this.$refs.createToDoListModal.confirmNewToDoListAndEndCreation();
      //this.isToDoCreationModalVisible = false;
    },
    toggleTaskStatus(toDoList, task){
      task.done = true;
 
        if (task.done===true) {
          const index = toDoList.toDoListTasksArray.indexOf(task);
        if (index > -1) {
          toDoList.doneTasksArray.push(task);
          toDoList.toDoListTasksArray.splice(index, 1);
        }
      }
      //State check
      console.log(JSON.stringify(toDoList.toDoListTasksArray));
      console.log("This is done tasks array" + JSON.stringify(toDoList.doneTasksArray));
    },
    openTasksEditingModal(toDoList){
      this.$refs.tasksEditingModal.open();
      this.currentTasks = toDoList.toDoListTasksArray;
      this.currentListIndex=this.toDoListsBank.indexOf(toDoList);
    },
    updateTasks(updatedTasks){
      if(this.currentListIndex !==null){
        this.toDoListsBank[this.currentListIndex].toDoListTasksArray = updatedTasks;
      }
      this.$refs.tasksEditingModal.close();
    },
    closeTasksEditingModal() {
      console.log('Closing modal now' + this.isTasksEditingModalVisible);
      this.$refs.tasksEditingModal.close();
      this.$nextTick(() => {
      console.log('Modal should be closed now' + this.isTasksEditingModalVisible);
    });
    },
    addTaskToThisTasksList(toDoList){
      const taskName = this.newTaskName[toDoList.id];
      if (taskName && taskName.trim().length>0) {
        const newTask = {
          name: taskName,
          done: false,
          isEdited:false
        };
        toDoList.toDoListTasksArray.push(newTask);
        this.newTaskName[toDoList.id]='';
      }
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
