<script>
  import axios from "axios";
  import { pushState } from "$app/navigation";

    let name = "";
    let email = "";
    let password = "";
    let gender = "";
    let age = "";
    let location = "";
    let role = "";
  
    const handleSubmit = async() => {

        const data = {
            name:name,
            email:email,
            password: password,
            location:location,
            info: {
                age:age,
                gender: gender
            },
            whom: role==="client"? "user" : "dealer"
        }
        console.log(data);
        try {
      const response = await axios.post("https://alert-ruby-codfish.cyclic.app/api/v1/auth/register", data);
      const whom = role;

       name = "";
       email = "";
       password = "";
       gender = "";
       age = "";
       location = "";
       role = "";
      
      pushState("/login");
      window.location.reload();

      
    } catch (error) {
      console.error("Login Failed", error);
      alert(error);
    }
  };
    
  </script>
  
  <main class="text-center h-screen flex items-center justify-center">
    <div class="p-10 bg-white shadow-md rounded max-w-md w-full">
      <h1 class="text-2xl font-bold mb-4">Register</h1>
      <form on:submit|preventDefault={handleSubmit} class="space-y-4">
        <label for="name" class="text-left block">Name:</label>
        <input type="text" id="name" bind:value={name} required class="w-full px-3 py-2 border" />
  
        <label for="email" class="text-left block">Email:</label>
        <input type="email" id="email" bind:value={email} required class="w-full px-3 py-2 border" />
  
        <label for="password" class="text-left block">Password:</label>
        <input type="password" id="password" bind:value={password} required class="w-full px-3 py-2 border" />
  

        <label for="gender" class="text-left block">Gender:</label>
        <select id="gender" bind:value={gender} class="w-full px-3 py-2 border">
            <option value="" disabled selected>Select Gender</option>
            <option value="male">Male</option>
            <option value="female">Female</option>
            <option value="other">Other</option>
        </select>
        <!-- <label for="gender" class="text-left block">Gender:</label>
        <input type="text" id="gender" bind:value={gender} required class="w-full px-3 py-2 border" /> -->
  
        <label for="age" class="text-left block">Age:</label>
        <input type="text" id="age" bind:value={age} required class="w-full px-3 py-2 border" />
  
        <label for="location" class="text-left block">Location:</label>
        <input type="text" id="location" bind:value={location} required class="w-full px-3 py-2 border" />
  
        <label for="role" class="text-left block">Role:</label>
        <select id="role" bind:value={role} class="w-full px-3 py-2 border">
          <option value="" disabled selected>Select Role</option>
          <option value="dealer">Dealer</option>
          <option value="client">Client</option>
        </select>
  
        <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded">Register</button>
      </form>
      <p class="mt-4">
        Already have an account? <a href="/login">Login</a>
      </p>
    </div>
  </main>
  