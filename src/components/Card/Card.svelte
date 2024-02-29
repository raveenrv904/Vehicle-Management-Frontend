<script>
 import axios from "axios";
 import { pushState } from "$app/navigation";
 import {onMount} from "svelte";
  import DealCard from "../DealCard/DealCard.svelte";


    export let id="";
    export let carType = '';
    export let name = '';
    export let model = '';
    export let color = '';
    export let mileage = '';
    export let features = [];
    export let state = ''; 

    // let carId;
    // let dealerId;

    const tokenValue = localStorage.getItem("token");
  let config = {
    headers: {
      token: tokenValue,
    },
  };

  let dealData = {
  _id: "",
  carId: "",
  dealInfo: {
    price: 0,
    discount: 0
  }
};


  onMount(async()=> {
      const response = await axios.get("https://alert-ruby-codfish.cyclic.app/api/v1/users/getAllDeals/"+id, config);
      
    dealData=response.data[0];
    // console.log(response.data);
  })
    async function handleBuyClick(price, discount) {

        const total = price - discount;


        const amount = prompt(`Price: ₹${price}\nDiscount: ₹${discount}\nTotal: ₹${total}`);
        // console.log(amount);
        if(Number(amount) === Number(total)) {
            try {
                
                const dealerData = await axios.get("https://alert-ruby-codfish.cyclic.app/api/v1/users/getCarDealership/"+id, config);
                const dealerId = dealerData.data[0]._id;
                const carId = id;
        
                const buyVehicle = await axios.post("https://alert-ruby-codfish.cyclic.app/api/v1/common/buyNow/"+carId,dealerId ,config);
                alert("Success");
                window.location.reload();
            } catch (error) {
                console.log(error);
                alert("Payment Failed\nPlease Retry After Sometime.");
            }
        } else {
            alert("Enter the Correct Amount")
        }
    }

    const handleViewDetailsClick = async()=> {


      let data = {};
      
      try {
        const response = await axios.get("https://alert-ruby-codfish.cyclic.app/api/v1/users/getAllDeals/"+id, config);
        const dealerData = await axios.get("https://alert-ruby-codfish.cyclic.app/api/v1/users/getCarDealership/"+id, config);
        console.log(response.data);
        console.log(dealerData.data)
        data = {
            id: id, 
            name : name,
            model : model,
            type: carType,
            color: color,
            features: features,
            mileage: mileage,
            state: state,
            dealInfo: response.data,
            dealerInfo: dealerData.data
        }
        localStorage.setItem("carDetails", JSON.stringify(data));
        pushState("/cardetails");
        window.location.reload();
      } catch (error) {
        console.log('Failed to fetch available vehicles', error);
      }

    }
</script>

<style>
    .header {
        @apply text-xl font-bold mb-2 text-blue-600;
    }

    .info {
        @apply mb-4;
    }

    .info div {
        @apply mb-2;
    }

    .features {
        @apply italic text-gray-600;
    }

    .features ul {
        @apply pl-4 list-disc;
    }

    .features li {
        @apply mb-1;
    }

    /* .buy-button {
        @apply bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-700 cursor-pointer;
    } */
    /* .view-details-button {
        @apply bg-blue-500 text-white rounded-xl px-10 py-1 mt-2 hover:bg-blue-700 hover:shadow-lg transition duration-200 ease-in-out;
    } */
</style>

<div class="border p-4 m-4 w-80 rounded cursor-pointer shadow-md bg-white hover:shadow-lg transition duration-200 ease-in-out">
    <div class="header">{carType}</div>
    <div class="info">
        <div><strong>Name:</strong> {name}</div>
        <div><strong>Model:</strong> {model}</div>
        <div><strong>Color:</strong> {color}</div>
        <div><strong>Mileage:</strong> {mileage}</div>
    </div>
    {#if features.length > 0}
    <div class="features">
        <strong>Features:</strong>
        <ul>
            {#each features as feature (feature)}
            <li>{feature}</li>
            {/each}
        </ul>
    </div>
    {/if}
    
    {#if state === 'deal'}
        <DealCard dealDetails={dealData} />

        <button class="bg-green-500 text-white rounded-xl px-5 py-2 mt-2 hover:bg-green-600 hover:shadow-lg transition duration-200 ease-in-out" on:click={() => handleBuyClick(dealData.dealInfo.price,  dealData.dealInfo.discount)}>Buy Now</button>
        <button class="bg-blue-500 text-white rounded-xl px-5 py-2 mt-2 hover:bg-blue-700 hover:shadow-lg transition duration-200 ease-in-out;" on:click={() => handleViewDetailsClick()}>View Details</button>
    {:else if state === 'sold'}
        <button class="bg-red-500 text-white rounded-xl px-10 py-2 mt-2" disabled>Sold</button>
        <button class="bg-blue-500 text-white rounded-xl px-5 py-2 mt-2 hover:bg-blue-700 hover:shadow-lg transition duration-200 ease-in-out;" on:click={() => handleViewDetailsClick()}>View Details</button>
    {:else if state === ""}
        <button class="bg-gray-400 text-white rounded-xl px-10 py-2 mt-2" disabled>Unavailable</button>
        <button class="bg-blue-500 text-white rounded-xl px-5 py-2 mt-2 hover:bg-blue-700 hover:shadow-lg transition duration-200 ease-in-out;" on:click={() => handleViewDetailsClick()} >View Details</button>
    {/if}

</div>