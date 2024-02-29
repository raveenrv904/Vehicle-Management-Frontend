<script>
    import axios from "axios";
  import { pushState } from "$app/navigation";
  import { onMount } from 'svelte';

  import Card from "../../components/Card/Card.svelte";
  import Navbar from "../../components/Navbar/Navbar.svelte";

  import { createEventDispatcher } from 'svelte';

  let filterName = '';
  const dispatch = createEventDispatcher();

  function applyFilter() {
    dispatch('filter', filterName);
  }

  let availableVehicles = [];

  onMount(async () => {
    const tokenValue = localStorage.getItem("token");
    let config = {
      headers: {
        token: tokenValue,
      }
    };

    try {
      const response = await axios.get("https://alert-ruby-codfish.cyclic.app/api/v1/common", config);
      availableVehicles = response.data.result;
      // console.log('Available Vehicles:', availableVehicles);
    } catch (error) {
      console.log('Failed to fetch available vehicles', error);
      localStorage.setItem("token", "");
      // pushState("/login"); 
    }
  });
</script>

<Navbar user="client" />
    
    <h2 class="text-3xl font-bold mb-4">Available Vehicles</h2>
    
    <div class="mb-4">
      <label for="filterInput" class="block text-sm font-medium text-gray-600">Filter by Name:</label>
      <input
        type="text"
        id="filterInput"
        class="mt-1 p-2 border rounded-md block w-1/4 pl-3 pr-10 py-2 leading-5 bg-white focus:outline-none focus:border-blue-300 focus:ring focus:ring-blue-200 transition duration-150 ease-in-out sm:text-sm sm:leading-5"
        bind:value={filterName}
        on:input={applyFilter}
        placeholder="Enter vehicle name"
      />
    </div>
    
    {#if availableVehicles.length > 0}
    <ul class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4">
      {#each availableVehicles as vehicle (vehicle._id)}
        {#if !filterName || vehicle.name.toLowerCase().includes(filterName.toLowerCase())}
          <li class="bg-white shadow-lg p-4 rounded-lg w-1/4 sm:w-1/4 md:w-1/4 lg:w-1/4 xl:w-1/4 h-1/4 sm:h-1/4 md:h-1/4 lg:h-1/4 xl:h-1/4">
            <Card
            id={vehicle._id}
              carType={vehicle.type}
              name={vehicle.name}
              model={vehicle.model}
              color={vehicle.carInfo.color}
              mileage={vehicle.carInfo.mileage}
              features={vehicle.carInfo.features}
              state={vehicle.state}
            />
          </li>
        {/if}
      {/each}
    </ul>
    {:else}
      <p class="text-gray-600">No vehicles available at the moment.</p>
    {/if}