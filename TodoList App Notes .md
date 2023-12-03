# Todo List app

1. setup `sveltekit` project

```jsx
npm init svelte@next todo-list-app
```

1. cd `todo-list-app`
2. `npm install`
3. `npm run dev`
4. create basic home page with heading and input field with add button
5. to create `Supabase` connection
    1. create a new `lib` folder under `src`
    2. create `db.js` like following
    
    ```jsx
    import { createClient } from "@supabase/supabase-js";
    
    const supabase = createClient(
        import.meta.env.VITE_SUPABASE_URL,
        import.meta.env.VITE_SUPABASE_ANON_KEY
    )
    
    export default supabase
    ```
    

7. create a component called `Todo` inside the `lib` folder that has the basic functionality of a single task

```jsx
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
```

1. create a new table in `Supabase` with fields as 
    1. Tasks
    2. isComplete
    3. id and created_at is present by default
2. now in the home route i.e `/routes` folder, `onMount` trigger a function to get all the todos

```jsx
let todos=[];
const getAllTodos = async ()=>{
        let { data, error } = await supabase
        .from('todos')
        .select('*')
        todos = data
    }
```

1. now the todos are stored in an array and we can use a `for loop` to iterate through them and pass each todo as a prop to the `Todo` component 

```
{#each todos as todo}
		<Todo {todo} {updateTodo} {deleteTodo}/>
{:else}
    <p>No todos found</p>
{/each}
```

1. in the input field in the home page, bind the input field to a variable (say `newTask` ) and pass trigger a function to add this task to `Supabase`

```jsx
<button on:click={() => addTask()}>Add Task</button>
```

```jsx
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
```

1. once we’re able to fetch data from Supabase and display it on the front-end and also add new data to the database we have to implement other things to update the data
2. in the `Todo` component accept functions like `updateTodo` and `deleteTodo` as props and trigger `updateTodo` each time there is some changes in the check box or input field of the particular todo and trigger `deleteTodo` when the user clicks on delete button
3. when these functions are triggered the call goes to the main component where these methods are passed as props like

```jsx
<Todo {todo} {updateTodo} {deleteTodo}/>
```

1. now we need to define these functions as follows

```jsx
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
```

```jsx
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
```

### Implementing login using Google OAuth

1. create a new login component with a simple button that says `Login with google`
2. go to [https://developers.google.com/identity/oauth2/web/guides/get-google-api-clientid](https://developers.google.com/identity/oauth2/web/guides/get-google-api-clientid) and create a new project and once setting up the project click on the link to head to the `Google Could Console`
3. in Google Cloud Console go to `Credentials` tab and click on `OAuth Client` 
4. set the `Authorized Javascript Origins` to `[http://localhost:5173](http://localhost:5173)` 
5. now go to `Supabase` and go to the `Authentication` tab and click on `Providers`
6. expand the `Google` provider and paste the `ClientId` and `Client Secret` from Google cloud console here and copy the callback url from here and enable Google Oauth in supabase
7. go the google cloud console and paste the copied url under **`Authorized redirect URIs`** and click on save
8. now in the svelte login page’s `Login with google` button trigger a function on click like

```jsx
<button on:click={logIn}>Login With Google</button>
```

```jsx
<script>
    import supabase from '$lib/db'

    const logIn = async () =>{
        let { user: data, error } = await supabase.auth.signInWithOAuth({
        provider: 'google'
        })
    }
</script>
```

# Thats it!!

### Displaying `User Name` on home page

1. get the user details onMount in the homepage like follows

```jsx
const { data: { user }, error } = await supabase.auth.getUser()
```

1. extract the username form this user and store it in a variable as follows

```jsx
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
```

1. now you can use this `username` variable inside the webpage to display it on the homepage like

```jsx
<h4>Welcome {username}!</h4>
```

### Implementing Logout in home page

1. add a logout button on bottom and trigger a logout function on click like

```jsx
<button on:click={() => logOut()} >Logout</button>
```

```jsx
const logOut = async() =>{
        console.log("Logout triggered")
        let { error } = await supabase.auth.signOut()
        goto("/")
    }
```

# Done!!