<template>
  <div>
    <div>
      <p>temperature minimale: </p>
      <input v-on:change="temperatureFilter" v-model="minTemp" type="range" min="-10" max="30">
      <p>{{minTemp}}°C</p>
    </div>
    <div>
      <p>temperature maximale: </p>
      <input v-on:change="temperatureFilter" v-model="maxTemp" type="range" min="-10" max="30">
      <p>{{maxTemp}}°C</p>
    </div>
    <ul>
      <li v-for="day in isBetweenMinAndMax">date: {{day.date}} | temp min: {{day.temp.min}}°C | temp max: {{day.temp.max}}°C</li>
    </ul>
  </div>
</template>

<script>
    export default {
        name: "Weather",
        data() {
            return {
              forecast: [],
              minTemp: -10,
              maxTemp: 30,
              isBetweenMinAndMax: []
            }
        },
        methods: {
          temperatureFilter() {
              this.isBetweenMinAndMax = this.forecast.filter(day => day.temp.min >= this.minTemp && day.temp.max <= this.maxTemp)
          }
        },
        async fetch() {
            const weatherOfTheWeek = await fetch(`https://api.openweathermap.org/data/2.5/forecast/daily?q=paris&cnt=7&units=metric&appid=${process.env.NUXT_ENV_API_KEY}`)
              .then(response => response.text())
              .then(data => {
                  const jsonData = JSON.parse(data);

                  this.forecast = jsonData.list.map(obj => {
                      const date = new Date(obj.dt * 1000);
                      obj.date = date.toLocaleDateString('fr');
                      return obj
                  });
                  this.temperatureFilter()
              })
        },
    }
</script>

<style scoped>

</style>
