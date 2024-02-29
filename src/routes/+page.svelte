<!-- src/App.svelte -->
<script>
  import { navigate } from 'svelte-routing';
</script>

<style>
  .center {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }
</style>

<div class="center">
  <div class="container mx-auto text-center">
    <h1 class="text-4xl font-bold mb-6">Welcome to the Home Page</h1>

    <div class="flex justify-center">
      <button
        class="bg-blue-500 text-white px-4 py-2 mr-2 rounded"
        on:click={() => {
          navigate('/login');
        window.location.reload();

      }}
      >
        Login
      </button>
      <button
        class="bg-green-500 text-white px-4 py-2 rounded"
        on:click={() => {navigate('/register')
        window.location.reload();
      }}
      >
        Register
      </button>
    </div>
  </div>
</div>
