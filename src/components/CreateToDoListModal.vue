<template>
    <div class="create-todo-modal-backdrop">
        <div class="create-todo-modal">
            <header class="create-todo-modal-header">{{ message }}</header>
            <body class="create-todo-modal-body">
                <slot><input v-model="toDoListName" placeholder="Enter To Do list Name"/></slot>
                
                <slot><input v-model="toDoListCustomClass" placeholder="Designate todo in a class"/></slot>
                <slot>
                    <button v-on:click="createToDoList">Assign</button>
                </slot>
                
                <div v-if="showTaskInput">
                    <input v-model="newTaskName" placeholder="EnterTaskName">
                    <button v-on:click="addTask">Add task</button>
                </div>
                    <div v-if="toDoListNamePreview">{{ toDoListNamePreview }}</div>
                    <div v-if="tasksPreview.length>0">
                        <div id="taskDiv" v-for="(task, index) in tasksPreview" :key="index">{{ task }}</div>
                        <button class="createToDoModal" @click="createToDo">Create the ToDo List</button>
                    </div>
                <footer class="create-todo-modal-footer"> 
                    <!--<button class="createToDoModal" @click="createToDo">Create the ToDo List</button>-->
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
            message: "Create and name ToDoList",
            toDoListName:'',
            //toDoLists: [], Moved to App.vue
            toDoListCustomClass:'',
            newTaskName:'',
            showTaskInput:false,
            toDoListNamePreview:false,
            tasksPreview:[],
            toDoList:
            {
                fullName:"",
                trimmedName:"",
                class:"",
                tasks:[]
            }
        };
    },
    methods: 
    {
        createToDoList: function()
        {
            const trimmedName = this.toDoListName.trim().toLowerCase(); //Take inputted To Do List Name and trim it and unify the name

            if(trimmedName !== '')
            {   
                this.toDoList = {
                    fullName: this.toDoListName,
                    trimmedName: trimmedName,
                    class: this.ToDoListCustomClass,
                    tasks: this.toDoList.tasks
                }

                this.showTaskInput=true;
                this.toDoListNamePreview=this.toDoListName;

            } else {
                alert("Please name the toDoList");
            }},
            addTask: function() {
                const newTask = { name: this.newTaskName, done: false };
                this.toDoList.tasks.push(newTask);
                this.newTaskName = "";
                this.tasksPreview.push(newTask.name);
            },
            createToDo: function(){    
                this.$emit("toDoListCreated",this.toDoList)
                alert(JSON.stringify(this.toDoList));
            },
            closeModal: function(){
                this.$emit("close");
            }
        },
        watch: {
            toDoList:{
                handler(){
                    this.createToDo();
                },
                deep: true
            }
        }
}

</script>
<style>
    .create-todo-modal-backdop{
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