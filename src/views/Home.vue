<template>
  <div class="home">
    <p></p>
    <h1>Places</h1>

    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>

    <div>
      Name: <input type="text" v-model="newPlaceName"> <br>
      Address: <input type="text" v-model="newPlaceAddress"> <br>
      <button v-on:click="createPlace()">Create Place</button>
    </div>

    <div v-for="place in places">
      <h2>{{ place.name }}</h2>
      <p>{{ "$" + place.address }}</p>
      <button v-on:click="showPlace(place)">More Info</button>
    </div>

    <dialog id="place-details">
      <form method="dialog">
        <h1>Place Details</h1>
        <p>Name: <input type="text" v-model="currentPlace.name"></p>
        <p>Address: <input type="text" v-model="currentPlace.address"></p>
        <button v-on:click="updatePlace(currentPlace)">Update</button>
        <button v-on:click="destroyPlace(currentPlace)">Delete</button>
        <button>Close</button>
      </form>
    </dialog>

  </div>
</template>

<style>
</style>

<script>
import axios from "axios";
export default {
  data: function() {
    return {
      places: [],
      errors: [],
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: {}
    };
  },
  created: function() {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function() {
      axios.get("/api/places").then(response => {
        console.log("All Places:", response.data);
        this.places = response.data;
      });
    },
    createPlace: function() {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress
      };

      axios
        .post("/api/places", params)
        .then(response => {
          console.log("Success", response.data);
          this.places.push(response.data);
          this.newPlaceName = "";
          this.newPlaceAddress = "";
        })
        .catch(error => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function(place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function(place) {
      var updateParams = {
        name: place.name,
        address: place.address
      };

      axios
        .patch(`/api/places/${place.id}`, updateParams)
        .then(response => {
          console.log("Successfully updated place", response.data);
        })
        .catch(error => {
          console.log(error.response.data.errors);
        });
    },
    destroyPlace: function(place) {
      axios.delete(`/api/places/${place.id}`).then(respone => {
        console.log("Successfully deleted place", respone.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    }
  }
};
</script>