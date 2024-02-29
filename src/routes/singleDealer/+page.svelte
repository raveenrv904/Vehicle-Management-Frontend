<script>
    import { onMount } from "svelte";
    import {pushState} from "$app/navigation";

    import axios from "axios";
  import Navbar from "../../components/Navbar/Navbar.svelte";
  import DealCard from "../../components/DealCard/DealCard.svelte";
  
    let dealerData = {
        "_id": "",
        "email": "",
        "name": "",
        "password": "",
        "location": "",
        "info": {
            "age": "",
            "gender": ""
        },
        "whom": "",
        "cars": [
        ],
        "deals": [
        ],
        "soldVehicles": []
    };

    let carDetails =  [
    ];
  
    onMount(async () => {
      const tokenValue = localStorage.getItem("token");
      const config = {
        headers: {
          token: tokenValue,
        },
      };
  
      dealerData = JSON.parse(localStorage.getItem("dealerDetails"));
  
      const dealerId = dealerData._id;
      // console.log(dealerId);
      const dealerCars = await axios.get("https://alert-ruby-codfish.cyclic.app/api/v1/common/"+dealerId, config);
      const dealerDeals = await axios.get("https://alert-ruby-codfish.cyclic.app/api/v1/common/getDeals/"+dealerId, config);

    carDetails = dealerCars.data.carDetails;

let temp = [];
    for(let car of carDetails) {
        let found = dealerDeals.data.find(function (element) {
            if (element !== null) {

                return element.carId === car._id;
            }
            
        });
        car.dealData = found;
        temp.push(car)

    }
    carDetails = temp;



      // console.log(carDetails);
    });
  
   
    
    const goBack = () => {
        pushState("/client");
        window.location.reload();
  };


  const handleViewDetailsClick = async(car)=> {

    const dealInfo = car.dealData;
    // car.dealData = "";
    car.dealInfo = dealInfo;
    car.mileage=  car.carInfo.mileage;
    car.color = car.carInfo.color,
    car.features = car.carInfo.features
    // car.carInfo = "";

    localStorage.setItem("carDetails", JSON.stringify(car));
    localStorage.setItem("where", "dealer");
    pushState("/cardetails");
    window.location.reload();

    // console.log(car)

let data = {};

// try {
    
//   const response = await axios.get("http://localhost:8000/api/v1/users/getAllDeals/"+id, config);
//   const dealerData = await axios.get("http://localhost:8000/aspi/v1/users/getCarDealership/"+id, config);
//   console.log(response.data);
//   console.log(dealerData.data)
//   data = {
//       id: id, 
//       name : name,
//       model : model,
//       type: carType,
//       color: color,
//       features: features,
//       mileage: mileage,
//       state: state,
//       dealInfo: response.data,
//       dealerInfo: dealerData.data
//   }
//   localStorage.setItem("carDetails", JSON.stringify(data));
//   pushState("/cardetails");
//   window.location.reload();
// } catch (error) {
//   console.log('Failed to fetch available vehicles', error);
// }

}

async function handleBuyClick(price, discount) {
  const total = price - discount;


        const amount = prompt(`Price: ₹${price}\nDiscount: ₹${discount}\nTotal: ₹${total}`);
        if (total == amount) {
          const dealerData = await axios.get("https://amused-sweatshirt-calf.cyclic.app/api/v1/users/getCarDealership/"+id, config);
          dealerId = dealerData.data[0]._id;
          carId = id;
  
          const buyNow = {
              carId: carId, 
              dealerId: dealerId
          }
  
          const buyVehicle = await axios.post("https://amused-sweatshirt-calf.cyclic.app/api/v1/common/buyNow/"+carId,dealerId ,config);
          alert("Success")
        } else {
          alert("Enter the correct Amount");
        }

        // alert("Success");
    }

</script>

<style>
    .card-container {
      max-width: 350px;
      margin: 20px;
      padding: 16px;
      background-color: #f0f0f0;
      border-radius: 8px;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    }
  
    .info-section p {
      margin-bottom: 8px;
    }
  
    .info-section .info-item {
      display: flex;
      margin-bottom: 8px;
    }
  
    .info-section .info-item span {
      font-weight: bold;
      margin-right: 4px;
    }
  
  
  
    .back-button {
      background-color: #6b7280;
      color: white;
      font-weight: bold;
      padding: 8px 13px;
      border-radius: 7px;
    }

  </style>

  <Navbar user="dealer"/>
  
  <div class="card-container">
    <h2 class="text-2xl font-bold mb-4">Dealer Information</h2>
  
    <div class="info-section">
      <div class="info-item">
        <span>Name:</span>
        <p class="pl-2">{dealerData.name}</p>
      </div>
      <div class="info-item">
        <span>Email:</span>
        <p class="pl-2">{dealerData.email}</p>
      </div>
      <div class="info-item">
        <span>Location:</span>
        <p class="pl-2">{dealerData.location}</p>
      </div>
      <div class="info-item">
        <span>Info:</span>
        {#each Object.entries(dealerData.info) as [key, value] (key)}
          <br>
          <p class="pl-2">{key}: {value}</p>
        {/each}
      </div>
    </div>

    
    <div class="button-section">
        <button class="back-button" on:click={goBack}>
            Back
        </button>
    </div>
</div>

<h3 class="text-2xl font-bold mb-4 ml-3">Car Details</h3>

<div class="grid grid-cols-1 xl:grid-cols-4 lg:grid-cols-3 md:grid-cols-2 sm:grid-cols-2">

    {#if carDetails.length !== 0}

    {#each carDetails as car (car._id)}
    <div class="card-container">
        <h2 class="text-lg font-bold mb-2">{car.name} - {car.model}</h2>

  <div class="info-section">
    <div class="info-item">
      <span>Type:</span>
      <p class="pl-2">{car.type}</p>
    </div>
    <div class="info-item">
        <span>Color:</span>
      <p class="pl-2">{car.carInfo.color}</p>
    </div>
    <div class="info-item">
        <span>Mileage:</span>
      <p class="pl-2">{car.carInfo.mileage}</p>
    </div>
    <div class="info-item">
      <span>Features:</span>
      <ul>

          {#each car.carInfo.features as feature (feature)}
          <li class="pl-2">⭐ {feature}</li>
          {/each}
    </ul>
</div>
<div class="button-section">
    {#if car.state === "sold"}
    <!-- <DealCard dealDetails={car.dealData} /> -->
    <button class="bg-blue-500 text-white rounded-xl px-5 py-2 mt-2 hover:bg-blue-700 hover:shadow-lg transition duration-200 ease-in-out;" on:click={() => handleViewDetailsClick(car)}>View Details</button>
    <button class="bg-red-500 text-white rounded-xl px-10 py-2 mt-2" disabled>Sold</button>
    {:else if car.state === "deal"}
     <DealCard dealDetails={car.dealData} />

      <button class="bg-green-500 text-white rounded-xl px-5 py-2 mt-2 hover:bg-green-600 hover:shadow-lg transition duration-200 ease-in-out" on:click={()=> handleBuyClick(car.dealData.dealInfo.price, car.dealData.dealInfo.discount)}>Buy Now</button>
      {:else}
      <button class="bg-gray-400 text-white rounded-xl px-10 py-2 mt-2" disabled>Unavailable</button>
      {/if}
    </div>
</div>
</div>
{/each}
{:else if carDetails.length == 0} 
<p>No Vehicle Found</p>
{/if}
</div>
  
