
<script>


import axios from "axios";
import { pushState } from "$app/navigation";
import Navbar from "../../../components/Navbar/Navbar.svelte";



    let carDetails = {
      type: "",
      name: "",
      model: "",
      carInfo: {
        color: "",
        mileage: "",
        features: [],
      },
    };

    
    let feature = "";
    async function handleSubmit() {
        carDetails.carInfo.features = feature.split(",");
        feature = "";
        
      console.log("Car details submitted:", carDetails);

      const tokenValue = localStorage.getItem("token");
        let config = {
          headers: {
            token: tokenValue,
          },
        };
        try {
  
          await axios.post("https://alert-ruby-codfish.cyclic.app/api/v1/dealers/createCar" , carDetails, config);
          alert("New Car Added");
          pushState("/dealerInventory");
          window.location.reload();

  
        } catch (error) {
          console.log('Failed to Create  vehicles', error);
        }
  

      carDetails.type = "";
      carDetails.name = "";
      carDetails.model = "";
      carDetails.carInfo.color = "";
      carDetails.carInfo.mileage = "";
    }

    const moveBack = ()=> {
      pushState("/dealerInventory");
      window.location.reload();
    }

  </script>
  
  <style>
    .container {
      @apply mx-auto p-4 max-w-screen-lg;
    }
  
    .form-container {
      @apply mt-8 max-w-md;
    }
  
    .form-label {
      @apply block text-gray-700 mb-2;
    }
  
    .form-input {
      @apply border border-gray-300 p-2 mb-4 w-full rounded-md ;
    }
  
    .form-button {
      @apply bg-green-500 text-white px-4 py-2 rounded-full;
    }
  </style>
  
  <Navbar user="dealer"/>
  <div class="container">
    <h1 class="text-3xl font-semibold mb-6">Create New Car</h1>
  
    <div class="form-container">
      <button on:click={moveBack} class="back-button mb-4 bg-blue-500 text-white px-4 py-2 rounded-full">Back</button>
      <form on:submit|preventDefault={handleSubmit}>
        <div class="mb-4">
          <label for="type" class="form-label">Type:</label>
          <input type="text" id="type" placeholder="e.g: Compact" bind:value={carDetails.type} class="form-input" required />
        </div>
  
        <div class="mb-4">
          <label for="name" class="form-label">Name:</label>
          <input type="text" id="name" placeholder="e.g: Swift" bind:value={carDetails.name} class="form-input" required />
        </div>
  
        <div class="mb-4">
          <label for="model" class="form-label">Model:</label>
          <input type="text" id="model" placeholder="e.g: 2025" bind:value={carDetails.model} class="form-input" required />
        </div>
  
        <div class="mb-4">
          <label for="color" class="form-label">Color:</label>
          <input type="text" id="color" placeholder="e.g: Red" bind:value={carDetails.carInfo.color} class="form-input" required />
        </div>
  
        <div class="mb-4">
          <label for="mileage" class="form-label">Mileage:</label>
          <input type="text" id="mileage" placeholder="e.g: 32 mpg" bind:value={carDetails.carInfo.mileage} class="form-input" required />
        </div>
  
        <div class="mb-4">
          <label for="features" class="form-label">Features:</label>
          <input type="text" id="features" placeholder="e.g: Apple CarPlay, Blind Spot Monitoring" bind:value={feature} class="form-input" />
        </div>

        <button type="submit" class="form-button">Create Car</button>
      </form>
    </div>
  </div>
