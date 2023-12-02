<script>
    import { onMount } from "svelte";
    import supabase from "$lib/db"
    import Todo from "../lib/Todo.svelte";
    import {goto} from "$app/navigation"

    let todos=[];
    let newTask=''
    let username=''
    
    onMount(async ()=>{
        await getAllTodos();
        const { data: { user }, error } = await supabase.auth.getUser()
        if (error){
            console.log("Error: User not Logged in")
            goto("/login")
        }
        username=user.user_metadata.full_name
    })

    const getAllTodos = async ()=>{
        let { data, error } = await supabase
        .from('todos')
        .select('*')
        todos = data
    }

    const updateTodo = async (todo)=>{
        // console.table(todo)
        const { data, error } = await supabase
            .from('todos')
            .update({ tasks: todo.tasks, isComplete: todo.isComplete})
            .eq('id', todo.id)
        await getAllTodos()
        if (error){
            console.log(error)
        }
    }

    const deleteTodo = async (todo)=>{
        // console.table(todo)

        const { data, error } = await supabase
            .from('todos')
            .delete()
            .eq('id', todo.id)
        await getAllTodos()
        if (error){
            console.log(error)
        }
    }

    const addTask = async ()=>{
        // console.table(todo)


        const { data, error } = await supabase
        .from('todos')
        .insert([
            { tasks: newTask},
        ])
        await getAllTodos()
        newTask=""
        if (error){
            console.log(error)
        }
    }
    const handleKeyPress =(e) =>{
        if(e.key === "Enter" && newTask !== ""){
            addTask()
        }
    }    
    
    const logOut = async() =>{
        console.log("Logout triggered")
        let { error } = await supabase.auth.signOut()
        goto("/")
    }
   
    
        
    
</script>

<div class="todolist-page">
    <h4>Welcome {username}!</h4>

    <div class="add">
        <input type="text" bind:value={newTask}>
        <button on:click={() => addTask()}>Add Task</button>
    </div>
    <div class="task-list">
        {#each todos as todo}
        <Todo {todo} {updateTodo} {deleteTodo}/>
        {:else}
            <p>No todos found</p>
        {/each}
    </div>

    <button on:click={() => logOut()} >Logout</button>
</div>

<svelte:window on:keypress={handleKeyPress} />




<style>
   @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;700&display=swap');

    :global(body) {
        font-family: 'Poppins', sans-serif;
        margin: 0;
        padding: 0;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
    }

    .todolist-page {
        text-align: center;
    }

    h4 {
        margin-bottom: 20px;
    }

    .add {
        display: flex;
        align-items: center;
        justify-content: center;
        margin-bottom: 20px;
    }

    input {
        padding: 10px;
        margin-right: 10px;
        border: 1px solid #ccc;
        border-radius: 5px;
    }

    button {
        background-color: #4285f4; 
        color: #ffffff;
        padding: 10px 20px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
    }

    .task-list {
        text-align: left;
        width: 100%; 
    }

</style>