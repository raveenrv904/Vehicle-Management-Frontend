
<script>
    import axios from "axios";
    import { onMount } from "svelte";
    import Navbar from "../../components/Navbar/Navbar.svelte";
  import { pushState } from "$app/navigation";

  
    let vehicles = [];
  
    function openDealForm(id, type,name, model, carInfo) {

        const carDetails = {
            _id: id,
            type:type,
            name:name,
            model:model,
            carInfo:carInfo
        };

        localStorage.setItem("carDetails", JSON.stringify(carDetails));
        pushState("dealerInventory/createDeal");
        window.location.reload();
  }
  
    onMount(async () => {
      const tokenValue = localStorage.getItem("token");
      const userId = localStorage.getItem("userId");
      let config = {
        headers: {
          token: tokenValue,
        },
      };
      try {
        const response = await axios.get("https://alert-ruby-codfish.cyclic.app/api/v1/common/" + userId, config);
        vehicles = response.data.carDetails;
        // console.log(vehicles);
      } catch (error) {
        console.log('Failed to fetch available vehicles', error);
      }
    });

    function createNewCar() {
        pushState("/dealerInventory/createCar");
        window.location.reload();
  }

  const handleViewClick = (id, type,name, model, carInfo, state)=> {
    const carDetails = {
      _id: id,
      type:type,
      name:name,
      model:model,
      carInfo:carInfo,
      state : state
    };
    localStorage.setItem("carDetails", JSON.stringify(carDetails));
    pushState("/dealerCarDetails");
    window.location.reload()
    // console.log("Click");
  }
  </script>
  <style>
    .vehicle-card {
      @apply bg-white shadow-lg p-4 m-4 rounded-lg overflow-hidden;
    }
  
    .vehicle-info {
      @apply mb-4;
    }
  
    .vehicle-name {
      @apply text-xl font-semibold mb-2;
    }
  
    .vehicle-details {
      @apply text-gray-600;
    }
  

  
    .feature-list {
      @apply mt-4;
    }
  
    .feature-item {
      @apply flex items-center text-gray-600;
    }
  
    .feature-icon {
      @apply mr-2;
    }
  </style>
  
  <div>
    <Navbar user="dealer"/>
    <button on:click={createNewCar} class="bg-green-500 text-white px-4 py-2 mt-4 rounded-full">Create New Car</button>
    <h1 class="text-3xl font-semibold mb-6">Your Cars</h1>
    {#if vehicles.length===0}
    <h1>No Vehicles in your Inventory</h1>
    {:else}
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-8">
      {#each vehicles as vehicle (vehicle._id)}
        <div class="vehicle-card">
          <div class="vehicle-info">
            <h2 class="vehicle-name">{vehicle.name}</h2>
            <p class="vehicle-details"><strong>Type: </strong> {vehicle.type}</p>
            <p class="vehicle-details"><strong>Model: </strong> {vehicle.model}</p>
          </div>
  
          {#if !vehicle.sold}
            {#if vehicle.state === ''}
              <button on:click={() => openDealForm(vehicle._id , vehicle.type,vehicle.name, vehicle.model, vehicle.carInfo)} class="bg-green-500 text-white px-4 py-2 mt-4 rounded-full">Create Deal</button>
              <!-- <button class="bg-blue-500 text-white px-4 py-2 mt-4 rounded-full">View Details</button> -->
            {:else if vehicle.state === 'deal'}
              <button class="bg-gray-400 text-white px-4 py-2 mt-4 rounded-full" disabled>Not Sold Yet</button>
            {:else if vehicle.state === 'sold'}
              <button class="bg-blue-500 text-white px-4 py-2 mt-4 rounded-full" on:click={handleViewClick(vehicle._id, vehicle.type,vehicle.name, vehicle.model, vehicle.carInfo, vehicle.state)}>View Details</button>
              <button class="bg-red-500 text-white px-4 py-2 mt-4 rounded-full" >Sold</button>
            {/if}
          {/if}
  
          {#if vehicle.carInfo}
            <div class="feature-list">
              <h3 class="text-lg font-semibold mb-2">Car Info:</h3>
              <ul>
                {#each vehicle.carInfo.features as feature (feature)}
                  <li class="feature-item">
                    <span class="feature-icon">🚗</span> {feature}
                  </li>
                {/each}
              </ul>
              <p class="vehicle-details"><strong>Color:</strong> {vehicle.carInfo.color}</p>
              <p class="vehicle-details"><strong>Mileage:</strong> {vehicle.carInfo.mileage}</p>
            </div>
          {/if}
        </div>
      {/each}
    </div>
    {/if}
  </div>