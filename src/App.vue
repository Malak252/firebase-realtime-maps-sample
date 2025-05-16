<template>
  <div id="app-container">
    <div class="split-screen map-container">
       <myMap :cars="cars" />
    </div>
    <div class="split-screen dashboard-container">
      <div>
        <h2>Firebase Dashboard</h2>
        <p>This is where the Firebase data will be displayed.</p>

        <!-- Display car data here -->

        <ul class="car-list">
           <li v-for="(car, index) in cars" :key="car.id" class="car-item">
 <div class="car-prefix">car{{ index + 1 }}:</div>
 <div class="car-details">
 ID: {{ car.ID }}<br>
              Latitude: {{ car.latitude }}<br />
              Longitude: {{ car.longitude }}
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import myMap from "./view/map.vue";
import { db } from '@/plugins/firebase';
import { ref, onValue } from 'firebase/database';

export default {
  name: "app",
  components: { myMap },
  data() {
    return {
      cars: []
    }
  },
  mounted() {
    // Reference the /markerList path in Realtime Database
    const carsRef = ref(db, '/markerList')
    // Listen for changes in the /markerList path
    onValue(carsRef, (snapshot) => {
      // Get the data from the snapshot
      const data = snapshot.val();
      console.log("Firebase Data:", data);
      // Convert the object of cars into an array
      this.cars = data
        ? Object.keys(data).map(key => ({
            ID: key,
            latitude: data[key]['0'],
            longitude: data[key]['1']
          }))
        : [];

    });
  }
};
</script>

<style>
html,
body {
  margin: 0;
  padding: 0;
  height: 100%;
  width: 100%;
}

#app-container {
  display: flex; /* Use flexbox for layout */
  height: 100vh; /* Set container height to full viewport height */
}

.split-screen {
  flex: 1; /* Allow both children to grow and shrink */
  overflow: auto; /* Add scrollbars if content exceeds container height */
}

.map-container {
  flex-basis: 50%; /* Give map container an initial width of 50% */
}

/* Added styles for car list */
.car-list {
  list-style: none;
  padding: 0;
}

.car-item {
  border: 1px solid #eee;
  padding: 10px;
  margin-bottom: 10px;
}

.car-prefix {
  font-weight: bold;
  margin-bottom: 5px;
}
/* Styles from FirebaseDashboard.vue */
div {
  padding: 20px;
  /* The margin-top might create extra space depending on where this div is placed */
  border: 1px solid #ccc;
  margin-top: 20px;
}
</style>

