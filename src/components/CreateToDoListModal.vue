<template>
    <div v-show="isModalVisible" 
         class="create-todo-modal-backdrop"
         @click.self="close">
        <div  class="create-todo-modal">
            <header class="create-todo-modal-header">{{ message }}</header>
            <body class="create-todo-modal-body">
                <!--input for Fresh Todo Lists name-->
                <input v-model="freshToDoListName" type="text" placeholder="Enter To Do list Name"/>
                <!--Fresh ToDo Lists class assignment: for later use, someday-->
                <input v-model="freshToDoListCustomClass" type="text" placeholder="Designate todo in a class"/>
                <!--this should initialize new Fresh To Do List and actually start the creation-->
                <button v-on:click="createFreshToDoList">Assign</button>
                <!--When adding name is clicked, the tasks input and preview pane should get activated-->
                <!---FRESH TODO LIST PREVIEW IN MODAL-->
                    <!-- Value becomes true after assign is pushed above and then it becomes visible. -->
                <div v-if="freshToDoListPreview" id="FreshToDoPreview" >
                    <div id="freshToDoPreviewToDoListNameAndButtonsWrapper">
                        <div id="freshToDoListNameInPreviewDiv">Name: {{ freshToDoListName }}</div>
                        <div id="freshToDoClassPreviewDiv">Class: {{ freshToDoListCustomClass }}</div>
                        
                        <br>
                        <input v-model="freshToDoListNewTaskName" 
                               ref="freshToDoListNewTaskNameInputField"
                               id="freshToDoListTaskInput"
                               type="text"
                               placeholder="Enter Task name"
                               @keydown.enter="addTaskToFreshToDoListTasks"/>
                        <button v-on:click="addTaskToFreshToDoListTasks" id="addTaskBtn">Add task</button>
                    </div>
                <!--Start showing the tasks preview when the freshToDoList freshToDoListTaskArray starts to have members-->
                    <div v-if="freshToDoList.freshToDoListsTasksArray !=='' ">   
                        <!--For each task this should create a DIV that exerts the tasks in the preview and adds edit and delete button behind the task-->
                        <div id="freshToDoListTasksPreviewDiv" 
                                v-for="(freshTask, index) in freshToDoList.freshToDoListsTasksArray" 
                                :key="index">
                                    {{ freshTask.done ? 'Done' : 'Not Done' }} - {{ freshTask.name }}
                                <div v-if="taskNameChangeVisibilityToggleDiv"> 
                                    <div v-if="freshTask.isBeingEdited">
                                        <input v-model="newTaskName" 
                                                type="text" 
                                                placeholder="Enter new task name"/>
                                        <br>
                                        <button id="newTaskNewNameSaveButton" v-on:click="saveNewNameForTask(index)">Save</button>
                                        
                                    </div>
                                 </div>
                                
                        <!--FOR FURTHER IMPLEMENTATION 
                            Edit and delete buttons should be next to or after the task for the case when user would want to change something in the task. Ideally there should be only delete buttonand edit it would be nice to have A field where user could place a curson and edit on the fly-->
                                <button                                  v-if="!taskNameChangeVisibilityToggleDiv"                                       v-on:click="editTaskInFreshToDoListTasksArray(index, freshTask.name)" id="freshToDoListsTaskEditButton">Edit</button>
                                <button v-on:click="deleteTaskInFreshToDoListTasksArray(index)" id="freshToDoListsTaskDeleteButton">Delete</button>
                                
                        </div>  
                    </div>
                </div>
                <footer class="create-todo-modal-footer"> 
                    <button id="confirmCreationAndEndbtn" @click="confirmNewToDoListAndEndCreation">Confirm fresh to Do list creation</button>
                    <button id="modalClosingButton" @click="close">Close</button>
                </footer>
            </body>
        </div>
    </div>
</template>

<script>
export default 
{
    name:"CreateToDoModal",
    data()
    {
        return {
            isModalVisible:false,
            freshToDoList:{
                freshToDoListName:'',
                freshToDoListClass:'',
                freshToDoListTrimmedName:'',
                freshToDoListsTasksArray: []
            },
            //freshToDoListTasks:[], this i think is unnecessary, id like to access fresh toDoList tasks in the freshToDoList tasks array directly.
            freshToDoListCustomClass:'',
            freshToDoListNewTaskName:'', // Ensures that corresponding input is empty in the beginning.
            message: "Create and name ToDoList",
            freshToDoListName:'', // Ensures that fresh ToDo List name field is empty in the beginning. 
            //toDoLists: [], Moved to App.vue
            freshToDoListPreview:false,
            taskNameChangeVisibilityToggleDiv:false,
            newTaskName:'',
            isBeingEdited:false //necessary component to be able invoke editing input for specific task beoing edited, not for all tasks
        };
    },
    methods: 
    {   
        open(){
          this.isModalVisible=true;  
        },
        createFreshToDoList: function(){
            const freshToDoListTrimmedName = this.freshToDoListName.trim().toLowerCase().replace(/\s/g, '');
            

            if(freshToDoListTrimmedName !==''){
                //I think that here the properties assigned thus far sould be put into the to do list. 
                //Not sure how to handle tasks.
                this.freshToDoList.freshToDoListName=this.freshToDoListName;
                this.freshToDoList.freshToDoListClass=this.freshToDoListCustomClass; 
                this.freshToDoList.freshToDoListTrimmedName = freshToDoListTrimmedName;
                //Id like a checkpoint. I dont know if this. should be used. I imagine that there is a 
                //"freshToDoList" at this point and id like to access IT specifically. 
                console.log("The assign button has been pressed, this has been done:" + 
                                this.freshToDoList.freshToDoListName+
                                this.freshToDoList.freshToDoListClass+
                                this.freshToDoList.freshToDoListTrimmedName);
                
                //FRESH TO DO LIST PREVIEW ACTIVATION
                    //Fresh toDo list task naming should become active
                    //tasks adding possibility should now become active
                    //tasks shouldnow be visible in the preview
                this.freshToDoListPreview=true;
                //?? Maybe add the V-ifs to contol visibility explicitly. 
            } else {
                alert("To Do list can not be nameless")
            }
        },
        addTaskToFreshToDoListTasks()
        {
            // This should capture thge name of the fresh toDoListTask that was just inputted to the input field. And put that in an object. Object because task must get tis doneness property too, that should get changed later as task gets done on the main page. 
            const freshToDoListNewTask = {
                                            name:this.freshToDoListNewTaskName, 
                                            done:false};
            // as soon as the new task is created it gets pushed to the task artray of object that later gets emitted from the Modal.
            this.freshToDoList.freshToDoListsTasksArray.push(freshToDoListNewTask);

            this.freshToDoListNewTaskName=''; 
            console.log("fresh ToDo List Status" + this.freshToDoList.freshToDoListsTasksArray);
            this.$refs.freshToDoListNewTaskNameInputField.focus();
        },
        deleteTaskInFreshToDoListTasksArray: function(index){
            this.freshToDoList.freshToDoListsTasksArray.splice(index,1);
        },
        editTaskInFreshToDoListTasksArray: function(index, newTaskName){
            //makes the task name editing dialog visible
            this.taskNameChangeVisibilityToggleDiv=true;
            
            this.freshToDoList.freshToDoListsTasksArray[index].isBeingEdited=true;
            //deletes old task name hopefully
            newTaskName=this.newTaskName;
            this.freshToDoList.freshToDoListsTasksArray[index].name=newTaskName;
            
        },
        saveNewNameForTask(index){
            //this should change the name of this task.
            this.freshToDoList.freshToDoListsTasksArray[index].name=this.newTaskName;
            this.newTaskName='';
            //closes the task name editing dialog. 
            this.freshToDoList.freshToDoListsTasksArray[index].isBeingEdited=false;
            this.taskNameChangeVisibilityToggleDiv=false;
            
        },
        confirmNewToDoListAndEndCreation: function(){
            this.$emit("freshToDoListCreatedAndSent", this.freshToDoList)
            console.log("This freshToDoList Was created, confirm clicked, event emitted, following    sent:\n" 
                        + "name:" + this.freshToDoList.freshToDoListName + 
                        "\nclass:" + this.freshToDoList.freshToDoListClass + 
                        "\ntrimmedName" + this.freshToDoList.freshToDoListTrimmedName + 
                        "\ntasks" + this.freshToDoList.freshToDoListsTasksArray);
            console.log(this.freshToDoList+'.');
            
            //clear all fields:
            this.freshToDoListName='';
            this.freshToDoListCustomClass='';
            this.freshToDoListPreview=false;
            this.freshToDoListNewTaskName=''; // unsure about this one
            //this.freshToDoList={}; //clear list entirely from here. This was said to be unnecessary
            this.freshToDoListsTasksArray=[];
            this.freshToDoList = {
                freshToDoListName: '',
                freshToDoListClass: '',
                freshToDoListTrimmedName: '',
                freshToDoListsTasksArray: []
            //need to close the modal too now. 
            
            };
            console.log("This freshToDoList state after the event was sent:", this.freshToDoList);
            this.$emit("freshToDoListCreationModalClosed");
            this.isModalVisible=false;
        },
        close(){
            this.freshToDoListName='';
            this.freshToDoListCustomClass='';
            this.freshToDoListPreview=false;
            this.freshToDoListNewTaskName='';
            this.freshToDoListsTasksArray=[];
            this.freshToDoList = {
                freshToDoListName: '',
                freshToDoListClass: '',
                freshToDoListTrimmedName: '',
                freshToDoListsTasksArray: []
            }
            this.isModalVisible=false;
        },
        handleEscapeKey(event){
            if(event.key==='Escape'){
                this.close();
            }
        }
    },
    mounted(){
        document.addEventListener('keydown', this.handleEscapeKey);
    },
    beforeUnmount(){
        document.removeEventListener('keydown', this.handleEscapeKey)
    }   
}

</script>
<style>
    .create-todo-modal-backdrop{
        position: fixed;
        top:0;
        bottom:0;
        right:0;
        left:0;
        background-color: rgba(0,0,0,0.3);
        display:flex;
        justify-content:center;
        align-items:center;
    }
    .create-todo-modal {
        background: #FFFF;
        box-shadow: 2px,2px,20px,1px;
        overflow-x:auto;
        display:flex;
        flex-direction:column;
    }
    .create-todo-modal-header, .create-todo-modal-footer{
        position:relative;
        border-bottom:1px solid, #eeee;
        color: #4aae9b;
        justify-content: space-between;
    }
    .create-todo-modal-footer {
        border-top:1px solid #eeee;
        flex-direction: column;
        justify-content: flex-end;
    }
    .create-todo-modal-body{
        position:relative;
        padding:20px 10px;
    }
    .create-todo-modal-close-button{
        color:white;
        background:#4aae9b;
        border-radius:2px;
    }
    #taskDiv {
        border:solid 2px;
        border-color:black;
        
    }
</style>