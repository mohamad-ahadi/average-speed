<template>
  <div class="container">
    <h1>Car Speed Tracker</h1>
    <button @click="toggleTracking" id="myBtn">
      {{ isTracking ? "Stop" : "Start" }}
    </button>

    <div class="display">
      <p>Current Speed: {{ currentSpeed.toFixed(2) }} km/h</p>
      <p>Distance: {{ totalDistance.toFixed(2) }} km</p>
      <p>Average Speed: {{ averageSpeed.toFixed(2) }} km/h</p>
    </div>
  </div>
</template>

<script setup>
import { ref, watchEffect, computed } from "vue";

// Reactive states
const isTracking = ref(false);
const currentSpeed = ref(0); // Current speed in km/h
const totalDistance = ref(0); // Total distance in km
const totalTime = ref(0); // Total time in seconds
const startTime = ref(0);
const lastPosition = ref(null);
let timer;

// Method to calculate distance between two geographic points
const calculateDistance = (lat1, lon1, lat2, lon2) => {
  const R = 6371; // Radius of the Earth in km
  const dLat = (lat2 - lat1) * (Math.PI / 180);
  const dLon = (lon2 - lon1) * (Math.PI / 180);
  const a =
    Math.sin(dLat / 2) * Math.sin(dLat / 2) +
    Math.cos(lat1 * (Math.PI / 180)) *
      Math.cos(lat2 * (Math.PI / 180)) *
      Math.sin(dLon / 2) *
      Math.sin(dLon / 2);
  const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
  return R * c; // Distance in km
};

// Method to start tracking
const startTracking = () => {
  isTracking.value = true;
  startTime.value = Date.now();
  lastPosition.value = null;
  totalDistance.value = 0;
  totalTime.value = 0;
  timer = setInterval(updatePosition, 1000); // Update every second
};

// Method to stop tracking
const stopTracking = () => {
  isTracking.value = false;
  clearInterval(timer);
};

// Method to toggle tracking on and off
const toggleTracking = () => {
  if (isTracking.value) {
    stopTracking();
  } else {
    startTracking();
  }
};

// Method to update position and calculate speed and distance
const updatePosition = () => {
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition((position) => {
      const lat = position.coords.latitude;
      const lon = position.coords.longitude;
      const speed = position.coords.speed || 0; // speed in m/s

      const currentSpeedInKmH = speed * 3.6; // Convert speed from m/s to km/h

      // If there's a last position, calculate the distance
      if (lastPosition.value) {
        const distance = calculateDistance(
          lastPosition.value.lat,
          lastPosition.value.lon,
          lat,
          lon
        );
        totalDistance.value += distance;
      }

      // Update last position
      lastPosition.value = { lat, lon };

      // Calculate total time elapsed
      const elapsedTime = (Date.now() - startTime.value) / 1000; // seconds
      totalTime.value = elapsedTime;

      // Update the current speed
      currentSpeed.value = currentSpeedInKmH;
    });
  } else {
    alert("Geolocation is not supported by this browser.");
  }
};

// Calculate average speed based on total distance and time
const averageSpeed = computed(() => {
  return totalTime.value > 0
    ? totalDistance.value / (totalTime.value / 3600)
    : 0;
});
</script>

<style scoped>
body {
  font-family: Arial, sans-serif;
  text-align: center;
  margin-top: 20px;
}

button {
  padding: 10px 20px;
  font-size: 18px;
  cursor: pointer;
}

.display {
  margin-top: 20px;
  font-size: 20px;
}

.container {
  margin: 0 auto;
  max-width: 600px;
}

#myBtn {
  border: 2px solid blue;
  background: lightblue;
}
</style>
