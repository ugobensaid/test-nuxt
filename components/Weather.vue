<template>
  <div class="container">
    <div class="weather">
      <div class="weather__titles">
        <h1>Weather forecast</h1>
        <p>NEXT 7 DAYS</p>
      </div>
      <div class="weather__sliders">
        <div class="weather__slider__block">
          <div class="weather__slider__block__text">
            <p>MIN TEMP</p>
            <p class="temperature">{{temperature.min}}°C</p>
          </div>
          <div class="weather__slider__input">
            <input v-model="temperature.min" class="slider" type="range" min="-10" max="30">
          </div>
        </div>
        <div class="weather__slider__block">
          <div class="weather__slider__block__text">
            <p>MAX TEMP</p>
            <p class="temperature">{{temperature.max}}°C</p>
          </div>
          <div class="weather__slider__input">
            <input v-model="temperature.max" class="slider" type="range" min="-10" max="30">
          </div>
        </div>
      </div>
      <div class="weather__list">
        <div class="weather__card" v-for="day in forecast"
          v-if="roundTemperatures(day.temp.min) >= temperature.min && roundTemperatures(day.temp.max) <= temperature.max">
          <div class="weather__card__left">
            <img :src="iconUrl + day.weather[0].icon + '.png'" alt="weather-icon"/>
            <div>
              <p>{{daysOfWeek[day.date.weekDay]}}</p>
              <p>{{day.date.day}}</p>
              <p>{{day.date.month}}</p>
            </div>
          </div>
          <div class="weather__card__right">
            <p>MIN TEMP <span>{{roundTemperatures(day.temp.min)}}°C</span></p>
            <p>MAX TEMP <span>{{roundTemperatures(day.temp.max)}}°C</span></p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "Weather",
    data() {
      return {
        /**
         * forecast store an array of objects that contains the weather informations for the next 7 days
         * @type {Array}
         */
        forecast: [],
        temperature: {
          min: -10,
          max: 30
        },

          /**
           * iconUrl store the URL to get the weather icon
           * @type {String}
           */
        iconUrl: "https://openweathermap.org/img/wn/",

          /**
           * daysOfWeek contains the diminutive of the 7 days of the week to display them thanks to an index
           * @type {Array}
           */
        daysOfWeek: ['sun.', 'mon.', 'tue.', 'wed.', 'thu.', 'fri.', 'sat.']
      }
    },
    methods: {
        /**
         * Round the temperature to get an integer number
         * @param temp the temperature to round
         * @returns {number}
         */
      roundTemperatures(temp) {
        return Math.round(temp);
      }
    },
    async fetch() {
        /**
         * Get data of Paris weather for the next 7 days in degrees celsius
         */
      await fetch(`https://api.openweathermap.org/data/2.5/forecast/daily?q=paris&cnt=7&units=metric&appid=${process.env.NUXT_ENV_API_KEY}`)
        .then(response => response.text())
        .then(data => {
          const jsonData = JSON.parse(data);

            /**
             * Store the fetched data into forecast to use them
             */
          this.forecast = jsonData.list.map(obj => {
            const date = new Date(obj.dt * 1000);

              /**
               * Add an Object to forecast containing the day of the week, the day of the month and the month
               * @type {{month: string, weekDay: number, day: string}}
               */
            obj.date = {
              weekDay: date.getDay(),
              day: ('0' + date.getDate()).slice(-2),
              month: date.toLocaleString('en-US', { month: 'long' }).toLowerCase(),
            };
            return obj
          });
        })
    },
  }
</script>

<style scoped lang="scss">
  @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;700&display=swap');
  $sass-violet: #B5ABFF;
  $sass-light-violet: #DDD9FC;
  $sass-blue: #5887FF;
  $sass-grey: rgba(255, 255, 255, 0.4);
  $sass-light-grey: #DBE5FF;

  $background-main-theme-color: $sass-violet;
  $background-secondary-theme-color: $sass-light-violet;
  $temperature-color: $sass-blue;

  $font-weight-bold: 700;
  $font-weight-medium: 500;

  .container {
    background: linear-gradient(1.87turn, $background-secondary-theme-color, $background-main-theme-color, $background-secondary-theme-color);
    min-height: 100vh;
    font-family: 'Inter', sans-serif;
    font-weight: $font-weight-medium;
  }

  .weather {
    width: 412px;
    display: block;
    margin: auto;
  }

  .weather__titles {
    text-align: center;
    padding-top: 80px;
    h1 {
      color: white;
      font-size: 40px;
      margin-bottom: 0;
    }
    p {
      color: $temperature-color;
      margin-top: 10px;
      font-size: 14px;
    }
  }

  .weather__sliders {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    margin-bottom: 25px;
    margin-top: 35px;
  }

  .weather__slider__block {
    width: 180px;
  }

  .weather__slider__block__text {
    display: flex;
    flex-direction: row;
    font-size: 10px;
    justify-content: space-between;
    align-items: center;
    p:nth-child(1) {
      color: white;
    }
  }

  .temperature {
    font-size: 16px;
    font-weight: $font-weight-bold;
    color: $temperature-color;
  }

  .weather__slider__input {
    background: $sass-grey;
    border-radius: 110px;
    padding: 15px 0 15px 0;
    width: 180px;
    input {
      width: 80%;
      display: block;
      margin: auto;
    }
  }

  .slider {
    -webkit-appearance: none;
    height: 2px;
    border-radius: 5px;
    background: $sass-light-grey;
  }

  .slider::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 15px;
    height: 15px;
    border-radius: 100%;
    background: $temperature-color;
    cursor: pointer;
  }

  .weather__card {
    background: $sass-grey;
    backdrop-filter: blur(18px);
    border-radius: 20px;
    display: flex;
    flex-direction: row;
    margin-bottom: 20px;
    align-items: center;
    justify-content: space-between;
  }

  .weather__card__left {
    display: flex;
    margin-left: 10px;
    padding: 15px 0 15px 0;
    img {
      display: block;
      margin: auto;
    }
    p {
      margin: 5px;
      font-weight: $font-weight-bold;
    }
    div p:nth-child(2) {
      font-size: 30px;
    }
  }

  .weather__card__right {
    p {
      font-size: 10px;
      span {
        font-size: 16px;
        font-weight: $font-weight-bold;
        color: $temperature-color;
        margin-left: 50px;
        margin-right: 20px;
      }
    }
  }
</style>
