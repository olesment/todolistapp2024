<template>
    <div class="create-todo-modal-backdrop">
        <div class="create-todo-modal">
            <header class="create-todo-modal-header">{{ message }}</header>
            <body class="create-todo-modal-body">
                <slot><input v-model="toDoListName" placeholder="Enter To Do list Name"/></slot>
                
                <slot><input v-model="toDoListCustomClass" placeholder="Designate todo in a class"/></slot>
                <slot>
                    <button v-on:click="createToDoList">Create</button>
                </slot>
                <footer class="create-todo-modal-footer">
                    <slot name="create-todo-modal-footer-slot">
                        <button class="create-todo-modal-close-button">Close Dialog</button>
                    </slot>
                </footer>
            </body>
        </div>
    </div>
</template>

<script>
export default 
{
    name:"CreateToDoModal",
    data(){
        return {
            message: "Create and name ToDoList",
            toDoListName:'',
            toDoLists: [], // array of todo lists
            toDoListCustomClass:''
        }
    },
    methods: 
    {
        createToDoList: function()
        {
            const trimmedName = this.toDoListName.trim().toLowerCase(); //Take inputted To Do List Name and trim it and unify the name

            if(this.toDoListName.trim().toLowerCase() !== '')
            {
                let nameAlreadyExists=false;
                for(let i=0; i<this.toDoLists.length; i++)
                {
                    if (this.toDoLists[i].name===trimmedName)
                    {
                        nameAlreadyExists = true;
                        alert("This name Exists, pick another name");
                        break;
                    }
                }
                if(!nameAlreadyExists)
                {
                    const newToDoList = 
                    {
                        fullName: this.toDoListName,
                        trimmedName: trimmedName,
                        class:this.toDoListCustomClass
                    }
                    this.toDoLists.push(newToDoList);
                    console.log("Added List:" ,this.toDoLists + "to toDo Lists");
                }
                else
                {
                    alert("Todo with this name already exists, pick another name")
                }
            }
            else
            {
            console.log('please enter a name for ToDoList.')
            }
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
</style>