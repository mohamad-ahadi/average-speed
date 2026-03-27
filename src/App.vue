<script setup>
import { ref, computed, onMounted } from "vue";
import movies from "./movies";

// const movies = ref([]);
const allMovies = ref(movies);

const query = ref("");

onMounted(async () => {
  // const html = await fetch("/movies.html").then((r) => r.text());
  // // turn string into DOM
  // const doc = new DOMParser().parseFromString(html, "text/html");
  // const nodes = [...doc.body.children];
  // let current = null;
  // let section = null;
  // for (const el of nodes) {
  //   if (el.tagName === "H3") {
  //     current = {
  //       title: el.textContent.replace(/\d{4}$/, "").trim(),
  //       year: el.textContent.match(/\d{4}$/)?.[0],
  //       imdbCode: "",
  //       type: "",
  //       votes: "",
  //       rating: "",
  //       downloads: { softsub: [], dubbed: [] },
  //     };
  //   } else if (el.textContent.includes("IMDb Code")) {
  //     current.imdbCode = el.textContent.split(":")[1].trim();
  //   } else if (el.textContent.includes("Title Type")) {
  //     current.type = el.textContent.split(":")[1].trim();
  //   } else if (el.textContent.includes("IMDb Votes")) {
  //     current.votes = el.textContent.split(":")[1].trim();
  //   } else if (el.textContent.includes("IMDb Rates")) {
  //     current.rating = el.textContent.split(":")[1].trim();
  //   } else if (el.textContent.includes("SoftSub")) {
  //     section = "softsub";
  //   } else if (el.textContent.includes("Dubbed")) {
  //     section = "dubbed";
  //   } else if (el.querySelector("a") && section) {
  //     const a = el.querySelector("a");
  //     const size = el.textContent.split("/")[1]?.trim();
  //     current.downloads[section].push({
  //       label: a.textContent.trim(),
  //       size,
  //       link: a.href,
  //     });
  //   } else if (el.tagName === "HR") {
  //     movies.value.push(current);
  //     current = null;
  //     section = null;
  //   }
  // }
  // console.log(movies.value);
  // exportMovies();
});

// function exportMovies() {
//   const content = `export default ${JSON.stringify(movies.value, null, 2)}\n`;

//   const blob = new Blob([content], { type: "text/javascript" });
//   const url = URL.createObjectURL(blob);

//   const a = document.createElement("a");
//   a.href = url;
//   a.download = "movies.js";
//   a.click();

//   URL.revokeObjectURL(url);
// }

const filteredMovies = computed(() =>
  allMovies.value.filter(
    (m) =>
      m.title.toLowerCase().includes(query.value.toLowerCase()) ||
      m.imdbCode.includes(query.value)
  )
);

</script>

<template>
  <div class="page">
    <input
      v-model="query"
      placeholder="Search title or IMDb code…"
      class="search"
    />

    <div class="grid">
      <div v-for="movie in filteredMovies" :key="movie.imdbCode" class="card">
        <h3>{{ movie.title }} ({{ movie.year }})</h3>
        <p>⭐ {{ movie.rating }} • 👥 {{ movie.votes }}</p>

        <div v-if="movie.downloads.softsub.length">
          <h4>SoftSub</h4>
          <ul>
            <li v-for="d in movie.downloads.softsub" :key="d.link">
              <a :href="d.link">{{ d.label }}</a> – {{ d.size }}
            </li>
          </ul>
        </div>

        <div v-if="movie.downloads.dubbed.length">
          <h4>Dubbed</h4>
          <ul>
            <li v-for="d in movie.downloads.dubbed" :key="d.link">
              <a :href="d.link">{{ d.label }}</a> – {{ d.size }}
            </li>
          </ul>
        </div>
      </div>
    </div>

    <button type="button" @click="loadMore">load more</button>
  </div>
</template>

<style>
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
  gap: 16px;
  align-items: stretch;
}

.card {
  background: #111;
  border-radius: 12px;
  padding: 16px;
  color: #eee;
  display: flex;
  flex-direction: column;
}

.card h3 {
  margin: 0 0 8px;
  font-size: 18px;
}

.card h4 {
  margin: 12px 0 6px;
  font-size: 14px;
  color: #aaa;
}

.card p {
  margin: 4px 0;
  font-size: 14px;
  color: #bbb;
}

.card a {
  color: #4ea1ff;
  text-decoration: none;
}

.card a:hover {
  text-decoration: underline;
}

.search {
  width: 100%;
  padding: 12px 14px;
  margin-bottom: 20px;
  font-size: 16px;
  border-radius: 10px;
  border: 1px solid #333;
  background: #0b0b0b;
  color: #fff;
}

.page {
  max-width: 100%;
  margin: 0 auto;
  padding: 20px;
}

ul, li{
  list-style-type: none;
  text-align:left;
}
</style>

<!--  -->
<!--  -->
<!--  -->
<!--  -->
<!--  -->
<!--  -->
<!--  -->
<!--  -->
<!--  -->
<!--  -->
<!--  -->
<!--  -->

<!-- 
<template>
  <div class="container">
    <button @click="showMoviesPage">movies page</button>

    <div v-if="showMovies" class="movies">
      <input v-model="query" placeholder="Search movies…" class="search" />

      <div class="grid">
        <div v-for="movie in filteredMovies" :key="movie.link" class="card">
          <h3>{{ movie.title }}</h3>
          <p>{{ movie.info }}</p>
          <a :href="movie.link" target="_blank">Download</a>
        </div>
      </div>
    </div>

    <div v-else class="speed-tracker">
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
  </div>
</template>

<script setup>
import { ref, watchEffect, computed, onMounted } from "vue";

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

const showMovies = ref(false);
const showMoviesPage = () => {
  showMovies.value = true;
  fetch().then((r) => console.log(r));
};

const movies = ref([]);
const query = ref("");

onMounted(() => {
  const nodes = document.querySelectorAll("h3");

  movies.value = [...nodes].map((el) => ({
    title: el.querySelector("h3")?.textContent ?? "",
    info: el.querySelector("p")?.textContent ?? "",
    link: el.querySelector("a")?.href ?? "",
  }));
});

const filteredMovies = computed(() =>
  movies.value.filter((m) =>
    m.title.toLowerCase().includes(query.value.toLowerCase())
  )
);
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
</style> -->
