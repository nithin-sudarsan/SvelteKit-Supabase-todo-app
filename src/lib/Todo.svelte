<script>
    export let todo
    export let updateTodo
    export let deleteTodo
</script>

<div class="todos" class:done={todo.isComplete}>
    <input type="checkbox" checked={todo.isComplete} on:change={(e)=>{
        todo.isComplete= e.currentTarget.checked
        updateTodo(todo)
    }}>
    <input type="text" value={todo.tasks} on:input={(e) => {
        todo.tasks = e.currentTarget.value;
        updateTodo(todo)}}>
    <button on:click={() => deleteTodo(todo)}>Delete</button>
</div>

<style>
    .todos{
        display: flex;
        margin: 2em;
    }
    .done{
        opacity: 0.5;
    }
    .done input[type="text"]{
        text-decoration: line-through;
    }
</style>