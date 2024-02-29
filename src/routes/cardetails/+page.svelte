<script>
    import { onMount } from "svelte";
    import { pushState } from "$app/navigation";
  import axios from "axios";
  import Navbar from "../../components/Navbar/Navbar.svelte";
  
    let carDetails = {
      color: "",
      dealInfo: [
        {
          _id: "",
          carId: "",
          dealInfo: {
            price: 0,
            discount: 0,
            status: "",
          },
        },
      ],
      features: [],
      id: "",
      mileage: "",
      model: "",
      name: "",
      type: "",
      
    };
    
    let carId;
    let dealerId;
    let config;
    onMount(() => {
      carDetails = JSON.parse(localStorage.getItem("carDetails"));
      carId = carDetails.id;
      // console.log(carDetails)
      const tokenValue = localStorage.getItem("token");
       config = {
        headers: {
          token: tokenValue,
        },
      };
    });
      
      function goBack() {
        const where = localStorage.getItem("where");
        // console.log(where);
        localStorage.removeItem("where");
        localStorage.removeItem("carDetails");
        // console.log(where);
        if(where !== null){
          pushState("singleDealer");
          window.location.reload();
        } else {
          pushState("client");
          window.location.reload();
        }

        // console.log("Going back...");
      }
      
      
      
  
    async function buyNow(price, discount) {
      const total = price - discount;


        const amount = prompt(`Price: ₹${price}\nDiscount: ₹${discount}\nTotal: ₹${total}`);
        if (total == amount) {
          const dealerData = await axios.get("https://alert-ruby-codfish.cyclic.app/api/v1/users/getCarDealership/"+id, config);
          dealerId = dealerData.data[0]._id;
          carId = id;
  
          const buyNow = {
              carId: carId, 
              dealerId: dealerId
          }
  
          const buyVehicle = await axios.post("https://alert-ruby-codfish.cyclic.app/api/v1/common/buyNow/"+carId,dealerId ,config);
          alert("Success")
        } else {
          alert("Enter the correct Amount");
        }

    }
  </script>
  
  <style>
    .card {
      @apply border p-4 rounded-md shadow-md bg-white;
      width: 400px;
    }
  
    .title {
      @apply text-lg font-semibold mb-2 text-indigo-600;
    }
  
    .info {
      @apply mb-2 text-gray-700;
    }
  
    .features {
      @apply mt-4;
    }
  
    .feature {
      @apply mb-2 text-green-500; 
    }
  
    .deal-info {
      @apply mt-4;
    }
  
    .deal-label {
      @apply font-semibold text-gray-800; 
    }
  
    .deal-value {
      @apply text-blue-600; 
    }
  
    .button {
      @apply bg-blue-500 text-white px-4 py-2 rounded-md mr-2 cursor-pointer;
    }
  
    .unavailable-button {
      @apply bg-gray-400 text-white rounded-xl px-10 py-2 mt-2 cursor-not-allowed;
    }
  
    .dealer-info {
      @apply mt-4;
      @apply border-t pt-4;
    }
  
    .dealer-label {
      @apply font-semibold text-gray-800;
    }
  
    .dealer-value {
      @apply text-gray-600;
    }
  </style>
  
  <Navbar user="client" />
  <div class="container flex justify-center items-center h-screen">
    <div class="card">
      <p class="title">{carDetails.type}</p>
      <p class="info">Name: {carDetails.name}</p>
      <p class="info">Color: {carDetails.color}</p>
      <p class="info">Model: {carDetails.model}</p>
      <p class="info">Mileage: {carDetails.mileage}</p>
  
      <div class="features">
        <h3 class="font-semibold mb-2">Features:</h3>
        <ul>
          {#each carDetails.features as feature (feature)}
            <li class="feature">{feature}</li>
          {/each}
        </ul>
      </div>
  
      {#if carDetails.state === "deal"}
        <div class="deal-info mt-4">
          <h3 class="font-semibold mb-2">Deal Information:</h3>
          {#each carDetails.dealInfo as deal (deal)}
            <p class="deal-label">Price: <span class="deal-value">${deal.dealInfo.price}</span></p>
            <p class="deal-label">Discount: <span class="deal-value">${deal.dealInfo.discount}</span></p>
            <!-- <p class="deal-label">Status: <span class="deal-value">{deal.dealInfo.status}</span></p> -->
          {/each}
        </div>
        <button on:click={()=>buyNow(carDetails.dealInfo[0].dealInfo.price, carDetails.dealInfo[0].dealInfo.discount)} class="bg-green-500 text-white rounded-xl px-5 py-2 mt-2 hover:bg-green-600 hover:shadow-lg transition duration-200 ease-in-out">Buy Now</button>
        <div class="flex mt-3">
          <button on:click={goBack} class="button">Back</button>
        </div>
      {:else if carDetails.state === "" }
        <button class="unavailable-button" disabled>Unavailable</button>
        <div class="flex mt-3">
          <button on:click={goBack} class="button">Back</button>
        </div>
        {:else if carDetails.state === "sold"}
        <button class="bg-red-500 text-white rounded-xl px-10 py-2 mt-2" disabled>Sold</button>
        <div class="flex mt-3">
          <button on:click={goBack} class="button">Back</button>
        </div>
      {/if}
  
      {#if carDetails.dealerInfo}
        <div class="dealer-info">
          <h3 class="font-semibold mb-2">Dealer Information:</h3>
          <p class="dealer-label">Dealer Name: <span class="dealer-value">{carDetails.dealerInfo[0].name}</span></p>
          <p class="dealer-label">Dealer Email: <span class="dealer-value">{carDetails.dealerInfo[0].email}</span></p>
          <p class="dealer-label">Dealer Location: <span class="dealer-value">{carDetails.dealerInfo[0].location}</span></p>
          <p class="dealer-label">Dealer Age: <span class="dealer-value">{carDetails.dealerInfo[0].info.age}</span></p>
          <p class="dealer-label">Dealer Gender: <span class="dealer-value">{carDetails.dealerInfo[0].info.gender}</span></p>
        </div>
      {/if}
    </div>
  </div>
  