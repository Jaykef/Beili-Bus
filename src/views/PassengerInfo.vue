<template>
  <div class="container">
    <form method="post" action="#" @submit.prevent="payment_info">
      
      <div v-for="item in choosen_seats" class="form-inline mb-2 ">

        <h5>{{choosen_trip.description}}</h5>
        <p>长时：{{choosen_trip.duration}}</p>
        <label class="mr-2" for="seat">座位号</label>
        <input
          type="text"
          class="form-control col-1 mr-3"
          v-model="item.seat_number"
          id="seat"
          readonly
        />
        <label class="mr-2" for="fiyat">价格</label>
        <input
          type="text"
          class="form-control col-1 mr-3"
          v-model="choosen_trip.ticket_price"
          id="price"
          readonly
        />

      </div>

      <p>总付：{{(choosen_trip.ticket_price)}}</p>
      <button class="btn btn-success btn-sm mt-2 float-right">支付</button>
    </form>
  </div>
</template>


<script>
import { GlobalData } from "../main";

export default {
  data() {
    return {
      choosen_trip: {},
      choosen_seats: []
    };
  },
  created() {
    if (GlobalData.choosen_trip == null) {
      this.$router.push({ name: "search-trip" });
    }
    this.choosen_trip = GlobalData.choosen_trip;
    this.choosen_seats = GlobalData.choosen_seats;
  },
  methods: {
    payment_info() {
      this.$router.push({ name: "payment-info" });
    }
  }
};
</script>
<style scoped>


</style>