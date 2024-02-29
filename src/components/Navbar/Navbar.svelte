<!-- Navbar.svelte -->

<script>
  export let user = "";
  import axios from "axios";
  import { pushState } from "$app/navigation";
  import profile from "../../profile.png";
  import { navigate } from "svelte-routing";

  let showDropdown = false;

  function toggleDropdown() {
    showDropdown = !showDropdown;
  }

  const handleLogout = async () => {
    const logout = await axios.get("https://alert-ruby-codfish.cyclic.app/api/v1/auth/logout");
    localStorage.setItem("token", "");
    alert(logout.data.msg);
    pushState("/");
    window.location.reload();
  };

  const handleMyVehicle = ()=> {
    navigate("/myVehicles")
    window.location.reload();
  }
</script>

<style>
  .navbar-container {
    @apply bg-green-500 p-4 flex justify-between items-center text-white;
  }

  .profile-dropdown {
    @apply relative ml-auto ;
  }

 
</style>

<div class="navbar-container" >
  <!-- svelte-ignore a11y-click-events-have-key-events -->
  <!-- svelte-ignore a11y-no-static-element-interactions -->
  <div class="profile-dropdown" on:click={toggleDropdown}>
    <img src={profile} alt="User Icon" class="w-10 h-10 cursor-pointer " />
    {#if showDropdown}
      <div class="absolute  top-100% right-4 bg-gray-800 text-white p-2 rounded shadow" >
        {#if user === "client"}
        <button class="cursor-pointer hover:bg-gray-700 rounded" on:click={handleMyVehicle}>My Vehicles</button>
        {/if}
        <button class="cursor-pointer p-2 hover:bg-gray-700 rounded-lg" on:click={handleLogout}>Logout</button>
      </div>
    {/if}
  </div>
</div>
