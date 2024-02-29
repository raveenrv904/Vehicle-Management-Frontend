
<script>
    import axios from "axios";
      import { onMount } from "svelte";
    import { pushState } from "$app/navigation";
  import Navbar from "../../../components/Navbar/Navbar.svelte";
  
    
      let carDetails = {
        type: "",
        name: "",
        model: "",
        carInfo: {
          mileage: "",
          color: "",
          features: [],
        },
      };
    
      let dealInfo = {
        price: "",
        discount: "",
      };
    
      onMount(async () => {
        let details = JSON.parse(localStorage.getItem("carDetails"));
        if (details) {
          carDetails = details;
          console.log("Car details loaded:", carDetails);
          localStorage.removeItem("carDetails");
        } else {
          console.log("No car details found in localStorage.");
        }
      });
  
  
    
      async function createDeal() {
  
          const data = {
              carId: carDetails._id,
              dealInfo: {
                  price: dealInfo.price,
                  discount: dealInfo.discount,
              }
          }
  
       const tokenValue = localStorage.getItem("token");
      //  console.log(tokenValue)
        let config = {
          headers: {
            token: tokenValue,
          },
        };
        try {
  
          const response = await axios.post("https://alert-ruby-codfish.cyclic.app/api/v1/dealers/createDeal" , data, config);
          alert("Deal Created");
          pushState("/dealerInventory");
        window.location.reload();

  
        } catch (error) {
          console.log('Failed to Create  vehicles', error);
        }
  
          
          
  
        // console.log("Deal created:", data);;
      }

    const handleBack = ()=> {
      pushState("/dealerInventory");
      window.location.reload();
        }
  </script>
    
  <!-- Add width to the components -->
  
  <style>
      .container {
        @apply mx-auto p-4 max-w-screen-lg;
      }
    
      .vehicle-card {
        @apply bg-white shadow-lg p-4 m-4 rounded-lg overflow-hidden w-1/2; /* Added w-full for full width */
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
    
      .form-container {
        @apply mt-8 max-w-md; 
      }
    
      .form-label {
        @apply block mb-2;
      }
    
     
      
    </style>
    
    
    
    <Navbar user="dealer"/>
    <div class="container">
      <button on:click={handleBack} class="back-button mb-4 bg-blue-500 text-white rounded-xl px-5 py-1 mt-4 hover:bg-blue-600 hover:shadow-lg transition duration-200 ease-in-out">Back</button>
      <h1 class="text-3xl font-semibold mb-6">Vehicle Details</h1>
    
      <div class="vehicle-card">
        <div class="vehicle-info">
          <h2 class="vehicle-name">{carDetails.name}</h2>
          <p class="vehicle-details"><strong>Type: </strong>{carDetails.type}</p>
          <p class="vehicle-details"><strong>Model: </strong>{carDetails.model}</p>
        </div>
    
        {#if carDetails.carInfo}
          <div class="feature-list">
            <h3 class="text-lg font-semibold mb-2">Car Info:</h3>
            <ul>
              {#each carDetails.carInfo.features as feature (feature)}
                <li class="feature-item">
                  <span class="feature-icon">🚗</span> {feature}
                </li>
              {/each}
            </ul>
            <p class="vehicle-details"><strong>Color:</strong> {carDetails.carInfo.color}</p>
            <p class="vehicle-details"><strong>Mileage:</strong> {carDetails.carInfo.mileage}</p>
          </div>
        {/if}
      </div>
    
      <h2 class="text-2xl font-semibold my-4">Create Deal</h2>
      <form on:submit|preventDefault={createDeal} class="form-container">
        <label for="price" class="form-label">Price:</label>
        <input type="text" id="price" placeholder="e.g : 5000000" bind:value={dealInfo.price} class="border p-2 mb-4 w-full rounded-lg leading-5 bg-white focus:outline-none focus:border-blue-300 focus:ring focus:ring-blue-200 transition duration-150 ease-in-out sm:text-sm sm:leading-5" required />
      
        <label for="discount" class="form-label">Discount:</label>
        <input type="text" placeholder="e.g: 30000" id="discount" bind:value={dealInfo.discount} class="border p-2 mb-4 w-full rounded-lg leading-5 bg-white focus:outline-none focus:border-blue-300 focus:ring focus:ring-blue-200 transition duration-150 ease-in-out sm:text-sm sm:leading-5" required />
        <button type="submit" class="bg-blue-500 text-white rounded-xl px-5 py-1 mt-4 hover:bg-blue-600 hover:shadow-lg transition duration-200 ease-in-out">Create Deal</button>
      </form>
    </div> 