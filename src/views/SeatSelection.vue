<template>
  <div class="container">
    <p class="text-center heading">座位预定</p>
      <table class="table text-center" style="width: inherit">
        <tr>
          <td v-for="item in getSeatList('Couple', 'Window')">
            <button 
              :class="{'seat': item.occupancy === 0, 'seat seat-booked': item.occupancy === 1, 'seat seat-res': item.occupancy === 2}"
              :disabled="item.occupancy === 1"
              @click="choose_seat(item.id)"
            ><p class="num">{{ item.seat_number }}</p></button>
          </td>
        </tr>
        <tr>
          <td v-for="item in getSeatList('Couple', 'Aisle')">
            <button 
              :class="{'seat': item.occupancy === 0, 'seat seat-booked': item.occupancy === 1, 'seat seat-res': item.occupancy === 2}"
              :disabled="item.occupancy === 1"
              @click="choose_seat(item.id)"
            ><p class="num">{{ item.seat_number }}</p></button>
          </td>
        </tr>
        <tr>
          <td colpan="20"></td>
        </tr>
        <tr>
          <td v-for="item in getSeatList('Aisle', 'Window')">
            <button 
              :class="{'seat': item.occupancy === 0, 'seat seat-booked': item.occupancy === 1, 'seat seat-res': item.occupancy === 2}"
              :disabled="item.occupancy === 1"
              @click="choose_seat(item.id)"
            ><p class="num">{{ item.seat_number }}</p></button>
          </td>
        </tr>
        <tr>
          <td v-for="item in getSeatList('Single', 'Window')">
            <button 
              :class="{'seat': item.occupancy === 0, 'seat seat-booked': item.occupancy === 1, 'seat seat-res': item.occupancy === 2}"
              :disabled="item.occupancy === 1"
              @click="choose_seat(item.id)"
            ><p class="num">{{ item.seat_number }}</p></button>
          </td>
        </tr>
      </table>
    
    <button
      class="btn btn-success float-right"
      @click="enter_passenger_info"
      v-if="choosen_seats.length>0"
    >选座位</button>
  </div>
</template>


<script>
import db from "../assets/db.json";
import { GlobalData } from "../main";

export default {
  data() {
    return {
      trips: [],
      seats: [],
      found_seats: [],
      choosen_seats: [],
      choosen_trip: {}
    };
  },
  created() {
      this.trips = db.trips
      this.seats = db.seats
      this.fetchSeats()
  },
  methods: {
    fetchSeats() {
      let trip_id;
      if (this.$route.params.trip_id)
        trip_id = parseInt(this.$route.params.trip_id);
      else if (GlobalData.choosen_trip !== null && GlobalData.choosen_trip.id)
        trip_id = parseInt(GlobalData.choosen_trip.id);
      else this.$router.push({ name: "search-trip" });

      this.choosen_trip = this.trips.find(t => t.id === trip_id);
      this.found_seats = this.seats.filter(s => s.trip_id === trip_id);
    },
    getSeatList(seat_type, seat_location) {
      return this.found_seats.filter(
        s => s.seat_type === seat_type && s.seat_location === seat_location
      );
    },
    choose_seat(seat_id) {
      let seat = this.found_seats.find(s => s.id === seat_id);
      seat.occupancy = seat.occupancy === 0 ? 2 : 0;
      if (seat.occupancy === 0) {
        this.choosen_seats = this.choosen_seats.filter(s => s.id !== seat.id);
      } else {
        this.choosen_seats.push(seat);
      }
    },
    enter_passenger_info() {
      GlobalData.choosen_trip = this.choosen_trip;
      GlobalData.choosen_seats = this.choosen_seats;
      this.$router.push({ name: "passenger-info" });
    }
  }
};
</script>

<style>
.seat {
  background: url(../assets/img/seat-empty.svg) no-repeat;
  background-size: contain !important;
  display: inline-block;
  width: 31px;
  height: 49px;
  line-height: 70px;
  text-align: center;
  color: #555;
  border: 0;
  text-decoration: none !important;
  cursor: pointer;
  font-size: 14px;
  padding-right: 5px;
  margin: 3px 0px 0px 3px;
}

.num {
  transform: rotate(90deg);
  margin: -2.5rem 0rem -3rem;
}

.seat-res {
  background: url(../assets/img/seat-res.svg) no-repeat;
  color: #fff !important;
}

.seat-booked {
  background: url(../assets/img/seat-booked.svg) no-repeat;
  color: #fff !important;
}
.heading {
  margin-bottom: 7rem;
}
table {
    border-collapse: collapse;
    margin-left: -3.8rem;
    transform: rotate(270deg);
}
.table {
  margin-bottom: 4rem;
}
.table th, .table td {
    padding: 0.75rem;
    vertical-align: top;
    border-top: none
}
.button-success {
  margin-top: 0;
}
.float-right {
  width: 75%;
  margin-right: 50px;
  margin-top: 3rem;
  
}
</style>