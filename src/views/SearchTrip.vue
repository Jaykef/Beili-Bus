<template>
  <div class="container">

    <form @submit.prevent="search_trip">
      <div class="row align-items-center">
        <div class="col-sm-6">
          <label>始末站：</label>
          <select class="form-control" v-model="trip">
            <option value>请选择始末站</option>
            <option v-for="item in locations" :value="item.id" v-text="item.description"></option>
          </select>
        </div>
        <div class="col-sm-6">
          <label>日期：</label>
          <input type="date" class="form-control" v-model="departure_date" />
        </div>
        <label>&nbsp;</label>
        <button type="submit" class="btn btn-primary">Confirm 确定</button>
      </div>
    </form>
    <hr />
    <div v-if="isLoading">Trips Loading...</div>
    <div v-if="this.available_trips.length == 0">{{ message }}</div>
    <div v-if="available_trips.length">
      <h5>Available trips</h5>
          <div v-for="(item, index) in available_trips" :key="index" class="trip-card">
              <div class="col-sm-4">
                <h6>{{ item.description }}</h6>
                <p>长时间：{{ item.duration }}</p>
              </div>
              <div class="col-sm-4 price">
                <h6>价格</h6>
                <p>￥{{ item.ticket_price }} </p>
              </div>
              <div class="col-sm-4">
                <button class="btn btn-success btn-sm" @click="chooseTrip(item.id)">选座位</button>
              </div>
              
          </div>
    </div>
  </div>
</template>

<script>
//import db from "../assets/db.json";
import BusDataApi from '../services/BusDataApi.js';

export default {
  name: "SearchForm",
  props: {
    msg: String
  },
  data() {
    return {
      trip: "",
      departure_date: "",
      locations: [],
      trips: [],
      available_trips: [],
      isLoading: false,
      message: ""
    };
  },
  created() {
    BusDataApi.getLocations()
    .then(response => {
      this.locations = response.data;
      console.log(response)
    })
    .catch(error => {
      console.log(error)
    })

    BusDataApi.getTrips()
    .then(response => {
      this.trips = response.data;
    })
    .catch(error => {
      console.log(error)
    })
  },
  methods: {
    search_trip() {
      this.isLoading = true;
      this.available_trips = this.trips.filter(
        x =>
          x.trip === this.trip &&
          this.getDate(x.departure_date) === this.getDate(this.departure_date)
      );
      if (this.available_trips.length == 0) {
        this.message = "No trips available, please try check your inputs";
      }
      this.isLoading = false;
    },
    getDate(val) {
      var datetime = new Date(val);
      var date =
        datetime.getFullYear() +
        ":" +
        ("0" + datetime.getMonth()).slice(-2) +
        ":" +
        ("0" + datetime.getDate()).slice(-2);
      return date;
    },
    chooseTrip(trip_id) {
      this.$router.push({ name: "seat-selection", params: { trip_id } });
    }
  }
};
</script>


<style scoped>
p {
  font-size: 14px;
}
label {
  padding: 5px 0px 0px 5px;
}
.row {
  padding: 1rem;
  justify-content: space-between;
}
.form-row {
    display: inline-flex;
    flex-wrap: wrap;
    margin-right: 0px; 
    margin-left: 0px; 
}
.col-sm-6 {
  background-color: #ffffff;
  justify-content: space-between;
  border-radius: 8px;
  padding: 4px;
  margin-bottom: 8px;
  max-width: 100%;
  flex:none;
}
.col-sm-4 {
  display: inline-grid;
  vertical-align: middle;
  justify-content: center;
}
.trip-card {
    background-color: #ffffff;
    border-radius: 8px;
    margin-bottom: 8px;
    padding: 15px 8px 0px 0px;
    vertical-align: middle;
}
.price {
  left: 12px
}
@media (max-width: 992px) {
  .col-sm-4 {
    width: auto;
  }
}
@media (min-width: 576px){
.col-sm-4 {
    flex: 0 0 33.3333333333%;
    max-width: max-content;
}
}

</style>