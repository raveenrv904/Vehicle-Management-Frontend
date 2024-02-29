<script>
  import axios from "axios";
  import { pushState } from "$app/navigation";

  let email = "";
  let password = "";

  const handleSubmit = async () => {
    let data = {
      email: email,
      password: password,
    };

    try {
      const response = await axios.post("https://alert-ruby-codfish.cyclic.app/api/v1/auth/login", data);
      
      email = "";
      password = "";
      
      const whom = response.data.result.whom;
      // console.log(response.data);
      localStorage.setItem("token", response.data.token);
      localStorage.setItem("userId",response.data.result._id);
      if (whom === "user") {
        alert("Success");
        pushState("/client");
        window.location.reload();
      } else {
        alert("Success");
        pushState("/dealerInventory");
        window.location.reload();
      }
    } catch (error) {
      console.error("Login Failed", error);
      alert(error);
    }
  };
</script>

<main class="text-center h-screen flex items-center justify-center">
  <div class="p-10">
    <h1 class="text-2xl font-bold">Login</h1>
    <form on:submit|preventDefault={handleSubmit} class="mt-4 space-y-4">
      <label for="email" class="text-left block">Email:</label>
      <input type="email" id="email" bind:value={email} required class="w-full px-3 py-2 border" />

      <label for="password" class="text-left block">Password:</label>
      <input type="password" id="password" bind:value={password} required class="w-full px-3 py-2 border" />

      <button type="submit" class="bg-green-500 text-white px-4 py-2 rounded">Login</button>
    </form>
    <p class="mt-4">
      Don't have an account? <a href="/register">Register</a>
    </p>
  </div>
</main>
