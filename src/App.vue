<template>
  <div id="createApp">
    <button type="button" class="createToDoModalVisibilityToggle" @click = "showCreateTodoModal">Create New Todo List</button>

    <CreateToDoListModal v-show= isToDoCreationModalVisible @close="closeToDoCreateModal" />
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
       isToDoCreationModalVisible:false,
    };
  },
  methods:{
    showCreateTodoModal(){
      this.isToDoCreationModalVisible = true;
    },
    closeToDoCreateModal(){
      this.isToDoCreationModalVisible = false;
    }
  }
};
</script>

<style>
/* Your styles here */
</style>
