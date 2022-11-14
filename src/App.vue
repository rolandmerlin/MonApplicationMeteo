<template>
  <nav>
    <img src="./assets/cloud-sun-w.svg" height="28" style="margin:6px;"/>
    <span>
      <span><b>App Météo Android/iPhone</b></span>
    </span>
    <div></div>
  </nav>
  <div style="display:flex;justify-content:center;align-items:center;height:calc(100vh - 42px);"><meteo :data="data" :pos="position" v-if="data"/></div>
</template>

<script setup>
  import { ref, watch } from 'vue'
  import { Geolocation } from '@capacitor/geolocation'
  import axios from 'axios'
  import meteo from './components/meteo.vue'

  const position = ref(false)
  const data = ref(false)

  Geolocation.getCurrentPosition()
    .then(d => { position.value={ lattitude:d.coords.latitude, longitude:d.coords.longitude } })
    .catch(()=> {
      alert('Impossible de trouver votre position GPS')
    })
   
  watch(position,()=>{
    // https://api.open-meteo.com/v1/forecast?latitude=48.77&longitude=0.81&hourly=temperature_2m,relativehumidity_2m,dewpoint_2m,apparent_temperature,precipitation,rain,showers,snowfall,snow_depth,freezinglevel_height,weathercode,pressure_msl,surface_pressure,cloudcover,cloudcover_low,cloudcover_mid,cloudcover_high,visibility,evapotranspiration,et0_fao_evapotranspiration,vapor_pressure_deficit,windspeed_10m,winddirection_10m,windgusts_10m,temperature_80m&current_weather=true
    let options = '&hourly=temperature_2m,relativehumidity_2m,dewpoint_2m,apparent_temperature,precipitation,rain,showers,snowfall,snow_depth,freezinglevel_height,weathercode,pressure_msl,surface_pressure,cloudcover,cloudcover_low,cloudcover_mid,cloudcover_high,visibility,evapotranspiration,et0_fao_evapotranspiration,vapor_pressure_deficit,windspeed_10m,winddirection_10m,windgusts_10m,temperature_80m&current_weather=true'
    axios('https://api.open-meteo.com/v1/forecast?latitude='+position.value.lattitude+'&longitude='+position.value.longitude+''+options).then(d => {
      data.value = d.data
    }).catch(()=>{
      alert('Impossible de contacter le serveur de météo')
    }) 
  })
</script>
<style>
  nav {
    display:block;
    height:40px;
    line-height:40px;
    border-width:0;
    border-bottom:2px solid white;
    display:grid;
    grid-template-columns:40px 1fr 40px;
    background:#41B883;
    color:white;
  }
  nav * {
    line-height:40px;
    background-color:inherit;
    color:inherit;
    z-index:1;
  }
  nav>span {
    padding-left:10px;
  }
  nav>div {
    padding:2px 5px;
    cursor:pointer;
  }
  nav>div>hr {
    margin:7px 0 7px 0;
    border:1px solid white;
  }
  * {
    padding:0;
    margin:0;
    background-color:white;
    color:black;
  }
</style>
