<template>
  <div id="createApp">
    <button type="button" class="createToDoModalVisibilityToggle" @click = "showCreateTodoModal">Create New Todo List</button>

    <CreateToDoListModal 
      v-show="isToDoCreationModalVisible"
      @close="closeToDoCreateModal"
      :to-do-lists="toDoLists" 
      @toDoListCreated="handleCreatedToDoList"
      @newTaskCreated="handleNewTask"
    />
    <div v-for="(toDoList, index) in toDoLists" :key="index">
        {{ toDoList.fullName }} - {{ toDoList.class }}
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
      toDoLists:[],
      isToDoCreationModalVisible:false,
    };
  },
  methods:{
    showCreateTodoModal(){
      this.isToDoCreationModalVisible = true;
    },
    handleCreatedtoDoList(newToDoList){
      this.toDoLists.push(newToDoList);
      this.isToDoCreationModalVisible=false;
    },
    closeToDoCreateModal(){
      this.isToDoCreationModalVisible = false;
    },
    
}
  }

</script>

<style>
/* Your styles here */
</style>
