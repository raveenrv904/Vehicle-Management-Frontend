

<script>

  import DisplayCar from "../../components/DisplayCar/DisplayCar.svelte";

  import { onMount } from 'svelte';
  import axios from "axios";
  import {pushState} from "$app/navigation"


  let dealerData = [];
  

  onMount(async () => {
    const tokenValue = localStorage.getItem("token");
    const config = {
        headers: {
            token: tokenValue
        }
    }
    const dealers = await axios.get("https://alert-ruby-codfish.cyclic.app/api/v1/users/getAllDealers", config);
    // console.log(dealers.data);
    dealerData = dealers.data; 
  });


  let isCollapsed = true;

function toggleSidebar() {
    isCollapsed = !isCollapsed;
}

const dealerClicked = (dealer)=> {
    // console.log("Dealer Clicked")

    localStorage.setItem("dealerDetails", JSON.stringify(dealer));
    pushState("/singleDealer");
    window.location.reload();
}

</script>

<style>

    
    .sidebar {
        width: 200px;
        height: 100%;
        background-color: #777;
        color: white;
        position: fixed;
        top: 0;
        left: 0;
        transition: transform 0.3s ease-in-out;
        overflow-y: auto;
    }

    .sidebar-collapsed {
        transform: translateX(-200px);
    }

    .content {
        margin-left: 200px;
        transition: margin-left 0.3s ease-in-out;
    }

    .content-collapsed {
        margin-left: 0;
    }

    .toggle-btn {
        background-color: #555;
        color: white;
        border: none;
        padding: 5px;
        cursor: pointer;
        outline: none;
        position: absolute;
    }
    
    .icon {
        font-size: 20px;
        margin-right: 8px;
    }

    .dealer {
        cursor: pointer;
    }

</style>
<main class="container mx-auto">

  <div class="{`sidebar ${isCollapsed ? 'sidebar-collapsed' : ''}`}">
    <h2 class="text-center pt-5 font-mono text-lg bg-red-200 pb-5 text-black">Top Dealers</h2>
    <ul class="sidebar-content mt-4">
        {#each dealerData as dealer (dealer._id)}
        {#if dealer.whom === "dealer"}
            <!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
            <!-- svelte-ignore a11y-click-events-have-key-events -->
            <li class="dealer mb-3 ml-3 hover:bg-red-400 rounded-md py-3 pl-3 transition-all duration-300 ease-in-out" on:click={dealerClicked(dealer)}>{dealer.name}</li>
            {/if}
        {/each}
    </ul>
</div>

<div class="{`content ${isCollapsed ? 'content-collapsed' : ''}`}">
    <button class="toggle-btn" on:click={toggleSidebar}>
        <span class="icon">{isCollapsed ? '➤' : '➤'}</span>
        {#if isCollapsed}
            Open Sidebar
        {:else}
            Close Sidebar
        {/if}
    </button>
    <DisplayCar/>
  </div>
  

</main>

