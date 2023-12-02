<script>
    import supabase from '$lib/db'

    const logIn = async () =>{
        let { user: data, error } = await supabase.auth.signInWithOAuth({
        provider: 'google'
        })
    }

</script>
<div class="login-page">
    <h1>Sveltekit x Supabase Todo List Web Application</h1>
    <div class="login-form">
        <button on:click={logIn}>Login With Google</button>
    </div>
</div>


<style>
    @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;700&display=swap');
    :global(body) {
        font-family: 'Poppins', sans-serif;
        margin: 0;
        padding: 0;
    }

    .login-page {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        height: 100vh;
    }

    .login-form {
        margin-top: 20px;
    }

    h1 {
        font-family: 'Poppins', sans-serif;
    }

    button {
        background-color: #4285f4; /* Google Blue */
        color: #ffffff;
        padding: 10px 20px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        display: flex;
        align-items: center;
    }
</style>
