<template>
  <div id="main" :class="isDay ? 'day' : 'night'">
    <div class="container my-5" style="max-width: 400px; min-width: 360px">
      <h1 class="title text-center">Weather app</h1>
      <form class="search-location" v-on:submit.prevent="getWeather">
        <input
          type="text"
          class="form-control text-muted form-rounded p-4 shadow-sm"
          placeholder="What City?"
          v-model="citySearch"
          autocomplete="off"
        />
      </form>
      <p class="text-center my-3" v-if="cityFound">No city found</p>
      <div
        class="card rounded my-3 shadow-lg back-card overflow-hidden"
        v-if="visible"
      >
        <!-- weather animation container -->
        <div>
          <div icon="sunny" v-if="clearSky" data-label="Sunny">
            <span class="sun"></span>
            <!-- <span class="sunny"></span> -->
          </div>

          <div icon="snowy" v-if="snowy" data-label="Chilly">
            <ul>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
            </ul>
          </div>

          <div icon="stormy" v-if="stormy" data-label="Soggy">
            <span class="cloud"></span>
            <ul>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
            </ul>
          </div>
          <div icon="cloudy" v-if="cloudy" data-label="Perfect">
            <span class="cloud"></span>
            <span class="cloud"></span>
          </div>
          <div icon="nightmoon" v-if="clearNight" data-label="Cool!">
            <span class="moon"></span>
            <span class="meteor"></span>
          </div>
        </div>

        <!-- Top of card starts here -->
        <div class="card-top text-center" style="margin-bottom: 15rem">
          <div class="city-name my-3">
            <p>{{ weather.cityName }}</p>
            <span>...</span>
            <p >{{ weather.country }}</p>
            <div> {{dateCheck()}} </div>
          </div>
        </div>
        <!-- top of card ends here -->

        <!--card middle body, card body used cos I want to just update the innerHTML -->
        <div class="card-body">
          <!-- card middle starts here -->
          <div class="card-mid">
            <div class="row">
              <div class="col-12 text-center temp">
                <span>{{ weather.temperature }}&deg;C</span>
                <p class="my-4">{{ weather.description }}</p>
              </div>
            </div>
            <div class="row">
              <div class="col d-flex justify-content-between px-5 mx-5">
                <p>
                  <img src="./assets/down.svg" alt="" />
                  {{ weather.lowTemp }}&deg;C
                </p>
                <p>
                  <img src="./assets/up.svg" alt="" />
                  {{ weather.highTemp }}&deg;C
                </p>
              </div>
            </div>
          </div>
          <!-- card middle ends here -->

          <!-- card bottom starts here -->
          <div class="card-bottom px-5 py-4 row">
            <div class="col text-center">
              <p>{{ weather.feelsLike }}&deg;C</p>
              <span>Feels like</span>
            </div>
            <div class="col text-center">
              <p>{{ weather.humidity }}%</p>
              <span>humidity</span>
            </div>
          </div>

          <!-- card bottom ends here -->
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cityFound: false,
      visible: false,
      stormy: false,
      cloudy: false,
      clearSky: false,
      clearNight: false,
      snowy: false,
      isDay: true,
      citySearch: "",
      weather: {
        cityName: "Da Nang",
        country: "vN",
        temperature: 12,
        description: "Clouds everywhere",
        lowTemp: "19",
        highTemp: "30",
        feelsLike: "20",
        humidity: "55",
      },
    };
  },
  methods: {
    getWeather: async function () {
      console.log(this.citySearch);
      const key = "1db53721dbffe0f9b241843f3554d173";
      const baseURL = `https://api.openweathermap.org/data/2.5/weather?q=${this.citySearch}&appid=${key}&units=metric`;
      //fetch weather
      
        const response = await fetch(baseURL);
        const data = await response.json();
        console.log(data);
        this.citySearch = "";
        this.weather.cityName = data.name;
        this.weather.country = data.sys.country;
        this.weather.temperature = Math.round(data.main.temp);
        this.weather.description = data.weather[0].description;
        this.weather.lowTemp = Math.round(data.main.temp_min);
        this.weather.highTemp = Math.round(data.main.temp_max);
        this.weather.feelsLike = Math.round(data.main.feels_like);
        this.weather.humidity = Math.round(data.main.humidity);
        const timeOfDay = data.weather[0].icon;
        ///check for time of day
        if (timeOfDay.includes("n")) {
          this.isDay = false;
        } else {
          this.isDay = true;
        }
        const mainWeather = data.weather[0].main;
        //check weather animations
        if (mainWeather.includes("Clouds")) {
          this.stormy = false;
          this.cloudy = true;
          this.clearSky = false;
          this.clearNight = false;
          this.snowy = false;
        }
        if (mainWeather.includes("Clouds")) {
          this.stormy = false;
          this.cloudy = true;
          this.clearSky = false;
          this.clearNight = false;
          this.snowy = false;
        }
        if (
          mainWeather.includes("Thunderstorm") ||
          mainWeather.includes("Rain")
        ) {
          this.stormy = true;
          this.cloudy = false;
          this.clearSky = false;
          this.clearNight = false;
          this.snowy = false;
        }
        if (mainWeather.includes("Clear") && this.isDay) {
          this.stormy = false;
          this.cloudy = false;
          this.clearSky = true;
          this.clearNight = false;
          this.snowy = false;
        }
        if (mainWeather.includes("Clouds") && !this.isDay) {
          this.stormy = false;
          this.cloudy = false;
          this.clearSky = false;
          this.clearNight = true;
          this.snowy = false;
        }
        if (mainWeather.includes("Snow")) {
          this.stormy = false;
          this.cloudy = false;
          this.clearSky = false;
          this.clearNight = false;
          this.snowy = true;
        }
        this.visible = true;
        this.cityFound = false;
      
      
    },
    dateCheck(){
      let d = new Date();
      let months = ["Th??ng 1", "Th??ng 2","Th??ng 3","Th??ng 4","Th??ng 5","Th??ng 6","Th??ng 7","Th??ng 8",
      "Th??ng 9","Th??ng 10","Th??ng 11","Th??ng 12"];
      let days = ["Ch??? Nh???t", "Th??? 2", "Th??? 3", "Th??? 4", "Th??? 5", "Th??? 6", "Th??? 7"];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day}  ${date} , ${month} , n??m ${year} `;
    },
    catch (error) {
        console.log(error);
        this.cityFound = true;
        this.visible = false;
      }
  },
};
</script>

<style scoped>
/* @import "./assets/custom.css";
@import "./assets/imageChange.css"; */

#main {
    position: absolute;
    height: 100%;
    width: 100%;
  }
  .day {
    background: linear-gradient(to bottom left, #d7d3ac, #ffffff);
  }
  .night {
    background: linear-gradient(to bottom left, #4854a2, #3d3d3d);
    color: white;
  }
  
  .title {
    font-size: 50px;
    font-weight: 500;
  }
  
  .form-rounded {
    border-radius: 2rem;
  }
  .back-card {
    border-radius: 40px !important;
    color: white;
    background: linear-gradient(to bottom right, #707070, #434647);
    text-shadow: 2px 2px 2px #707070;
  }
  
  .city-name {
    position: absolute;
    width: 100%;
  }
  
  .city-name p {
    font-weight: 400;
    font-size: 16pt;
  }
  
  .city-name span {
    position: relative;
    top: -50px;
    font-size: 40pt;
    font-family: "Times New Roman", Times, serif;
  }
  
  .temp span {
    font-weight: 100;
    font-size: 5em;
    letter-spacing: -5px;
    white-space: nowrap;
  }
  .card-mid {
    line-height: 0.5;
  }
  .condition {
    font-size: 1em;
    font-weight: 700;
    line-height: 1em;
    text-transform: capitalize;
  }
  
  .high {
    font-size: 15px;
  }
  
  .low {
    font-size: 15px;
  }
  
  .card-bottom p {
    font-size: 50px;
    font-weight: 100;
    letter-spacing: -3px;
  }
  .card-bottom {
    line-height: 0.5;
  }
  
  .card-bottom span {
    font-size: 12px;
  }
  
  .form-control:focus {
    box-shadow: none;
    border-color: inherit;
  }
  /* style background */
  :root {
    --shadow: #fd6f21;
    --ring: currentColor;
    --blend1: #fc5830;
    --blend2: #f98c24;
    --blend-from: 0%;
    --blend-to: 100%;
    --blend-dir: top right;
  }
  
  [icon] {
    flex: none;
    display: none;
    position: absolute;
    overflow: hidden;
    font-size: calc(10em + 1vmin);
    width: 100%;
    height: 100%;
    background: linear-gradient(
      to top right,
      var(--blend1) var(--blend-from),
      var(--blend2) var(--blend-to)
    );
    /*   filter: saturate(0); */
  }
  [icon]:hover {
      filter: saturate(1);
    }
  [icon]::after {
    content: attr(data-label);
    position: absolute;
    top: calc(100% + 1vmin);
    left: 50%;
    transform: translateX(-50%);
    font: inherit;
    font-size: 0.15em;
  }
  [icon="sunny"] {
    --shadow: #fd6f21;
    --blend1: #fc5830;
    --blend2: #f98c24;
    --blend-to: 65%;
  }
  [icon="cloudy"] {
    --shadow: #1378bb;
    --blend1: #1b9ce2;
    --blend2: #1378bb;
    --shadow: #c9e8de;
    --blend1: #758595;
    --blend2: #e0e2e5;
    --blend1: #1b9ce2;
    --blend-to: 65%;
    --blend-to: 90%;
  }
  [icon="snowy"] {
    --shadow: #c9e8de;
    --blend1: #758595;
    --blend2: #2c4a77;
    --blend-to: 90%;
    --blend-dir: bottom left;
  }
  [icon="stormy"] {
    --shadow: #34c6d8;
    --blend1: #4b9cc2;
    --blend2: #9adbd9;
  }
  [icon="nightmoon"] {
    --shadow: #5133a5;
    --blend1: #4054b2;
    --blend2: #1b2038;
    --blend-to: 65%;
    --blend-dir: bottom right;
  }
  
  /* SUNNY */
  /* --------------------- */
  .sun {
    position: absolute;
    top: 20%;
    left: 80%;
    transform: translate(-50%, -50%);
    width: 30%;
    height: 20%;
    color: #e6e8db;
    border-radius: 100%;
    background: #ffeb3b;
    box-shadow: 0 0 0 0.02em var(--ring) inset, 0 0 0.3em -0.03em var(--shadow);
    transform-origin: 0.1em 0.1em;
  }
  .sun::after {
    content: "";
    position: absolute;
    top: 0.1em;
    left: 0;
    will-change: transform;
    transform: translate(-50%, -50%);
    width: 0.1em;
    height: 0.1em;
    border-radius: 100%;
    background: #ffeb3b;
    box-shadow: 0 0 0.1em 0 rgba(255, 255, 255, 0.7) inset,
      -0.1em -0.1em 0 0.2em rgba(255, 255, 255, 0.3);
    animation: flare 12000ms infinite alternate linear;
  }
  
  /* CLOUDY */
  /* --------------------- */
  .cloud {
    position: absolute;
    top: 0.1em;
    left: 65%;
    width: 0.37em;
    height: 0.13em;
    border-radius: 0.1em;
    background-color: #fff;
    box-shadow: 0 0 0.1em 0.02em var(--ring) inset,
      0 0 0.3em -0.03em var(--shadow);
    animation: move 3000ms infinite ease-in-out;
  }
  .cloud + .cloud {
    top: 25%;
    left: 40%;
    animation: move 3700ms infinite linear;
  }
  .cloud::before,
  .cloud::after {
    content: "";
    position: inherit;
    border-radius: inherit;
    background-color: inherit;
    box-shadow: inherit;
    bottom: 30%;
  }
  .cloud::before {
    left: 0.05em;
    width: 0.2em;
    height: 0.2em;
  }
  .cloud::after {
    left: 0.15em;
    width: 0.15em;
    height: 0.15em;
  }
  
  /* SNOWY */
  /* --------------------- */
  [icon="snowy"] ul {
    position: absolute;
    list-style: none;
    top: 0%;
    left: 10%;
    right: 0%;
    height: 100%;
    margin: 0;
    padding: 0;
  }
  [icon="snowy"] li::before,
  [icon="snowy"] li::after {
    content: "";
    position: absolute;
    list-style: none;
    width: 0.015em;
    height: 0.01em;
    border-radius: 100%;
    background-color: var(--ring);
    will-change: transform, opacity;
    animation: snow 3700ms infinite ease-out;
    opacity: 0;
  }
  [icon="snowy"] li:nth-child(2n + 1)::before,
  [icon="snowy"] li:nth-child(13n + 11)::after {
    top: -7%;
    left: 40%;
  }
  [icon="snowy"] li:nth-child(3n + 2)::before,
  [icon="snowy"] li:nth-child(11n + 7)::after {
    top: 5%;
    left: 90%;
    animation-delay: 1000ms;
  }
  [icon="snowy"] li:nth-child(5n + 3)::before,
  [icon="snowy"] li:nth-child(7n + 5)::after {
    top: -10%;
    left: 80%;
    animation-delay: 2000ms;
  }
  [icon="snowy"] li:nth-child(7n + 5)::before,
  [icon="snowy"] li:nth-child(5n + 3)::after {
    top: 10%;
    left: 10%;
    animation-delay: 1300ms;
  }
  [icon="snowy"] li:nth-child(11n + 7)::before,
  [icon="snowy"] li:nth-child(3n + 2)::after {
    top: 20%;
    left: 70%;
    animation-delay: 1500ms;
  }
  [icon="snowy"] li:nth-child(13n + 11)::before,
  [icon="snowy"] li:nth-child(2n + 1)::after {
    top: 35%;
    left: 20%;
    animation-delay: 500ms;
  }
  .snowman {
    position: absolute;
    bottom: 30%;
    left: 40%;
    width: 0.15em;
    height: 0.15em;
    opacity: 0.9;
    background: var(--ring);
    border-radius: 100%;
  }
  .snowman::after {
    content: "";
    position: absolute;
    top: 90%;
    left: 30%;
    transform: translate(-50%, 0%);
    width: 0.275em;
    height: 0.3em;
    border-radius: inherit;
    background-color: var(--ring);
  }
  .snowman::before {
    content: "";
    position: absolute;
    top: 0%;
    left: 50%;
    transform: translate(-55%, -50%);
    width: 0.45em;
    height: 0.4em;
    border-radius: 60%;
    border: 0.02em solid transparent;
    border-bottom-color: var(--blend1);
    will-change: border-radius;
    animation: snowman 9000ms infinite ease-in;
  }
  
  /* STORMY */
  /* --------------------- */
  [icon="stormy"]::before {
    --shadow: rgba(255, 255, 255, 0);
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    opacity: 0.4;
    will-change: background-color;
    animation: flash 2300ms infinite linear 80ms;
  }
  [icon="stormy"] .cloud {
    --shadow: #c9e8de;
    --ring: #f0f2f0;
    background-color: var(--shadow);
    font-size: 1.3em;
    left: 70%;
    will-change: background-color, transform, opacity;
    animation: flash 2300ms infinite linear, move 3700ms infinite linear;
  }
  [icon="stormy"] ul {
    position: absolute;
    list-style: none;
    top: 0%;
    left: 70%;
    right: 0%;
    height: 100%;
    margin: 0;
    padding: 0;
  }
  [icon="stormy"] li,
  [icon="stormy"] li::before,
  [icon="stormy"] li::after {
    position: absolute;
    width: 0.005em;
    height: 0.055em;
    border-radius: 10%;
    background-color: #fff;
    opacity: 0;
    will-change: transform, opacity;
    animation: rain 2000ms infinite linear;
    transform: rotate(25deg);
  }
  [icon="stormy"] li::before,
  [icon="stormy"] li::after {
    content: "";
  }
  [icon="stormy"] li:nth-child(5n + 3)::before,
  [icon="stormy"] li:nth-child(11n + 7)::after,
  [icon="stormy"] li:nth-child(2n + 1) {
    top: 10%;
    left: 68%;
    animation-delay: 500ms;
  }
  [icon="stormy"] li:nth-child(3n + 2)::after,
  [icon="stormy"] li:nth-child(7n + 5)::after,
  [icon="stormy"] li:nth-child(3n + 2) {
    top: 5%;
    left: 45%;
    animation-delay: 1250ms;
  }
  [icon="stormy"] li:nth-child(2n + 1)::before,
  [icon="stormy"] li:nth-child(5n + 3)::after,
  [icon="stormy"] li:nth-child(7n + 5) {
    top: 4%;
    left: 82%;
    animation-delay: 750ms;
  }
  [icon="stormy"] li:nth-child(11n + 7)::before,
  [icon="stormy"] li:nth-child(3n + 2)::after,
  [icon="stormy"] li:nth-child(7n + 5) {
    top: 15%;
    left: 15%;
    animation-delay: 2000ms;
  }
  [icon="stormy"] li:nth-child(7n + 5)::before,
  [icon="stormy"] li:nth-child(2n + 1)::after,
  [icon="stormy"] li:nth-child(11n + 7) {
    top: 10%;
    left: 33%;
    animation-delay: 2500ms;
  }
  
  /* nightmoon */
  /* --------------------- */
  [icon="nightmoon"]::before {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-image: radial-gradient(1px 1px at 50% 20%, #fff, rgba(0, 0, 0, 0)),
      radial-gradient(1px 1px at 30% 65%, #fff, rgba(0, 0, 0, 0)),
      radial-gradient(2px 2px at 15% 05%, #fff, rgba(0, 0, 0, 0)),
      radial-gradient(2px 2px at 37% 35%, #fff, rgba(0, 0, 0, 0)),
      radial-gradient(2px 2px at 65% 47%, #fff, rgba(0, 0, 0, 0)),
      radial-gradient(1px 1px at 42% 29%, #fff, rgba(0, 0, 0, 0)),
      radial-gradient(1px 1px at 73% 56%, #fff, rgba(0, 0, 0, 0)),
      radial-gradient(1px 1px at 24% 19%, #fff, rgba(0, 0, 0, 0)),
      radial-gradient(2px 2px at 31% 47%, #fff, rgba(0, 0, 0, 0)),
      radial-gradient(1px 1px at 18% 39%, #fff, rgba(0, 0, 0, 0));
    background-repeat: repeat;
    will-change: transform;
    animation: revolve 120000ms linear infinite;
  }
  
  .moon {
    position: absolute;
    top: 20%;
    left: 80%;
    transform: translate(-50%, -50%);
    width: 25%;
    height: 20%;
    border-radius: 100%;
    background: radial-gradient(circle at bottom left, var(--ring), #fef07e);
    box-shadow: 0 0 0 0.02em var(--ring) inset, 0 0 0.3em -0.03em var(--blend2);
  }
  .moon::before,
  .moon::after {
    content: "";
    position: absolute;
    border-radius: 100%;
    background-color: var(--blend1);
    box-shadow: 0.01em 0.01em 0.1em 0 var(--blend1);
  }
  
  .moon::before {
    top: 15%;
    left: 55%;
    width: 20%;
    height: 20%;
    opacity: 0.3;
  }
  .moon::after {
    bottom: 50%;
    left: 25%;
    width: 15%;
    height: 15%;
    opacity: 0.2;
  }
  
  .meteor {
    position: absolute;
    background-color: #fff;
    opacity: 0;
    top: 20%;
    left: 55%;
    width: 1px;
    height: 15px;
    transform: rotate(45deg);
    will-change: transform, opacity;
    animation: meteor 6250ms infinite ease-in;
  }
  
  [icon="sunny"] {
    display: block;
  }
  
  [icon="cloudy"] {
    display: block;
  }
  
  [icon="snowy"] {
    display: block;
  }
  
  [icon="stormy"] {
    display: block;
  }
  
  [icon="nightmoon"] {
    display: block;
  }
  
  @keyframes flare {
    to {
      transform: translate(-0.3em, 0.3em);
      opacity: 0.4;
      font-size: 0.2em;
    }
  }
  @keyframes move {
    50% {
      transform: translateX(-0.05em);
    }
  }
  @keyframes snow {
    50% {
      opacity: 1;
    }
    100% {
      transform: translate(-0.1em, 15vmin);
    }
  }
  @keyframes snowman {
    50% {
      border-radius: 60% 60% 30% 50%;
    }
  }
  @keyframes flash {
    49% {
      background-color: var(--shadow);
    }
    51% {
      background-color: var(--ring);
    }
    53% {
      background-color: var(--shadow);
    }
    57% {
      background-color: var(--ring);
    }
    85% {
      background-color: var(--shadow);
    }
  }
  @keyframes rain {
    10% {
      opacity: 0.4;
    }
    50% {
      opacity: 1;
    }
    100% {
      transform: translate(-0.1em, 0.5em);
    }
  }
  @keyframes revolve {
    to {
      transform: rotate(360deg);
    }
  }
  @keyframes meteor {
    5% {
      opacity: 1;
    }
    8% {
      transform: translate(-0.6em, 0.6em) rotate(45deg);
      opacity: 0;
    }
  }
</style>