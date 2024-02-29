<!-- src/routes/MyVehicles.svelte -->
<script>
    import axios from 'axios';
    import { onMount } from 'svelte';
    import {pushState} from "$app/navigation";
    import Navbar from '../../components/Navbar/Navbar.svelte';
    let allData = [];
    
    onMount(async () => {
      try {
        const tokenValue = localStorage.getItem("token");
      let config = {
        headers: {
          token: tokenValue,
        },
      };
      const newData = [];
      const userId = localStorage.getItem("userId");
      const carResponse = await axios.get("https://alert-ruby-codfish.cyclic.app/api/v1/users/userVehicle/" + userId, config);
      
      for (const response of carResponse.data) {
        const dealer = await axios.get("https://alert-ruby-codfish.cyclic.app/api/v1/users/getCarDealership/" + response._id, config);
        const data = [
          
          dealer.data[0], response
        ];
        newData.push(data);
      }
      allData = newData;
      // console.log(allData)
      } catch (error) {
        console.log(error);
      }
      
    });
    const handleBack = ()=> {
      pushState("/client")
      window.location.reload();

    }

  </script>
  
  <div class="mx-auto">
    <Navbar user="client"/>
    <button on:click={handleBack} class="back-button mb-4 ml-4 bg-blue-500 text-white rounded-xl px-5 py-1 mt-4 hover:bg-blue-600 hover:shadow-lg transition duration-200 ease-in-out">Back</button>

    <h1 class="text-4xl font-bold mb-6">My Vehicles</h1>

    <div class="grid grid-cols-2 sm:grid-cols-1 lg:grid-cols-2 xl:grid-cols-2 gap-8 justify-center">
        {#if allData.length !==0}
        {#each allData as [user, vehicle] (vehicle._id)}
        <div class="vehicle-container ml-4 grid grid-cols-2 sm:grid-cols-1 lg:grid-cols-2 xl:grid-cols-2 gap-8 justify-center">

            <div class="bg-slate-200 max-w-lg p-6 rounded-lg">
                <h2 class="text-xl font-bold mb-2">{user.name}</h2>
                <p>Email: {user.email}</p>
                <p>Location: {user.location}</p>
                <p>Age: {user.info.age}</p>
                <p>Gender: {user.info.gender}</p>
                <div class="bg-slate-200 max-w-md p-6 rounded-lg">
                    <h3 class="text-lg font-bold mb-2">{vehicle.name}</h3>
                    <p>Type: {vehicle.type}</p>
                    <p>Model: {vehicle.model}</p>
                    <p>Color: {vehicle.carInfo.color}</p>
                    <p>Mileage: {vehicle.carInfo.mileage}</p>
                    <div class="mt-2">
                        <p class="font-semibold">Features:</p>
                        <ul class="list-disc ml-6">
                            {#each vehicle.carInfo.features as feature (feature)}
                                <li>{feature}</li>
                            {/each}
                        </ul>
                    </div>
            </div>
            
            </div>
        </div>

            
        {/each}

        {:else if allData.length == 0}
        <h1>No Vehicle Available</h1>
        {/if}
    </div>
</div>