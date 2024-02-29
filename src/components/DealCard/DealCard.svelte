<script>
    export let dealDetails;
  </script>
  
  <div class="max-w-md mx-auto bg-white rounded-xl overflow-hidden shadow-md mb-3 mt-3">
    <div class="md:flex">
      <div class="p-8">
        <div class="uppercase tracking-wide text-sm text-indigo-500 font-semibold">Deal Information</div>
        <p class="mt-2 text-gray-500">
          Price: ₹{dealDetails.dealInfo.price}
          <br />
          Discount: ₹{dealDetails.dealInfo.discount}
          <br />
          Offer Price: ₹{dealDetails.dealInfo.price - dealDetails.dealInfo.discount}
        </p>
      </div>
    </div>
  </div>
  