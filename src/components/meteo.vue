<template>
    <div v-if="index" class="meteo--layer">
        <div style="height:42px;width:100%;"></div>
        <h2 style="text-align:center;">Météo actuelle</h2>
        <div>&nbsp;<br></div> 
        <div class="meteo">
            <b>Lattitude</b><span>{{ props.pos.lattitude }}N</span>
            <b>Longitude</b><span>{{ props.pos.longitude }}E</span>
            <b>Humidité</b><span>{{ data.hourly.relativehumidity_2m[index] }}%</span>
            <b></b><span></span>
            <b>Température</b><span>{{ data.hourly.temperature_2m[index] }}°C</span>
            <b>resentie</b><span>{{ data.hourly.apparent_temperature[index] }}°C</span>
            <b>Couverture</b><span>{{ Cloud() }}</span>
            <b>Pression</b><span>{{ data.hourly.pressure_msl[index] }} hPa</span>
            <b>Pluie : </b><span>{{ data.hourly.rain[index] }} mm</span>
            <b>Neige : </b><span>{{ data.hourly.snow_depth[index] }} m</span>
            <b>Visibilité : </b><span>{{ data.hourly.visibility[index] }} m</span>
            <b>Vent : </b><span>{{ data.hourly.windspeed_10m[index] }} km/h</span>
        </div>    
    </div>
</template>
<script setup>
    import { ref } from 'vue'
    const props = defineProps(['data','pos'])

    const time = ref()
    let data = props.data

    const index = ref(false)
    const NZ = (a) => a>9?`${a}`:`0${a}`
    const refreshTime = () => {
        let D = new Date()
        time.value = D.getFullYear()+'-'
        time.value+= NZ(D.getMonth()+1)+'-'
        time.value+= NZ(D.getDate())+'T'
        time.value+= NZ(D.getHours())+':00'
    }
    const CycleTime = () =>{
        refreshTime()
        index.value = data.hourly.time.map((v,i) => v==time.value?i:false).find(i => (typeof i == 'number'))        
    }
    const Cloud = () => {
        if (data.hourly.cloudcover[index.value]>75) return 'Fortement nuageux'
        if (data.hourly.cloudcover[index.value]>50) return 'Nuageux'
        if (data.hourly.cloudcover[index.value]>25) return 'Fiablement nuageux'
    }
    let hwnd = setInterval(() => { CycleTime() }, 10000)
    CycleTime()


</script>
<style>
    .meteo {
        display:grid;
        grid-template-columns:repeat(2,1fr);
        width:95%;
        margin:0 auto;
    }
    .meteo * {
        line-height:40px;
        height:40px;
    }
    .meteo--layer {
        background:white;
        color:black;
        z-index:0;
    }
    .meteo--layer * {
        background:inherit;
        color:inherit;
    }
</style>