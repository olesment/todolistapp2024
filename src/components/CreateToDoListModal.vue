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
    props: {
        toDoLists: Array
    },
    data(){
        return {
            message: "Create and name ToDoList",
            toDoListName:'',
            //toDoLists: [], Moved to App.vue
            toDoListCustomClass:''
        }
    },
    methods: 
    {
        createToDoList: function()
        {
            const trimmedName = this.toDoListName.trim().toLowerCase(); //Take inputted To Do List Name and trim it and unify the name

            if(trimmedName !== '')
            {
                if (this.$props.toDoLists.some(function(list){return list.trimmedName === trimmedName;}))
                {
                    alert("This name Exists, pick another name");
                    return;
                } else {
                    const newToDoList = {fullName: this.toDoListName,
                                        trimmedName: trimmedName,
                                        class:this.toDoListCustomClass}
                    this.$props.toDoLists.push(newToDoList);
                    console.log("Added List:" ,this.toDoLists + "to toDo Lists");
                    this.$emit("toDoListCreated", newToDoList)
                }
            } else {
                alert("Please name the toDoList")   
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