<template>
  <main class="min-vh-100">
    <header class="header position-relative pb-5">
      <img class="w-100 h-100 position-absolute desk" src="/images/pattern-bg-desktop.png" alt="">
      <img class="w-100 h-100 position-absolute mobile" src="/images/pattern-bg-mobile.png" alt="">
      <div class="container py-4 pb-5 text-center position-relative">
        <h1 class="fs-2">IP Address Tracker</h1>
        <form class="search d-flex w-50 mx-auto justify-content-center my-4 position-relative" @submit.prevent="">
          <input type="text" class="flex-fill p-3 px-4 border-0" placeholder="Search for any IP address or domain"
            v-model="ipAddressInput">
          <span class="text-danger fst-italic d-block position-absolute w-100 ps-4 text-start" style="bottom: -25px;"
            v-if="validation.$error">
            {{ validation.$errors[0].$validator === 'required' ? 'Field can\'t be empty' : 'Invalid IP Address' }}
          </span>
          <div class="submit-butn p-3 px-4" @click="submission">
            <img src="/images/icon-arrow.svg" alt="">
          </div>
        </form>
        <div class="info-box text-start d-flex justify-content-between rounded-4 p-4 bg-white">
          <div class="box ip min-h-100">
            <span class="text-uppercase d-block fw-bold">IP Address</span>
            <h3 class="mb-0 mt-2">{{ ipOutput.ip || '00.00.00' }}</h3>
          </div>
          <div class="box location min-h-100">
            <span class="text-uppercase d-block fw-bold">Location</span>
            <h3 class="mb-0 mt-2" v-if="loading">{{ 'City, Region, Country' }}</h3>
            <h3 class="mb-0 mt-2" v-else>{{ ipOutput.location.city }}, {{ ipOutput.location.region }},
              {{ ipOutput.location.country }}</h3>
          </div>
          <div class="box time min-h-100">
            <span class="text-uppercase d-block fw-bold">Timezone</span>
            <h3 class="mb-0 mt-2" v-if="loading">UTC Timezone</h3>
            <h3 class="mb-0 mt-2" v-else>UTC {{ ipOutput.location.timezone }}</h3>
          </div>
          <div class="box time min-h-100">
            <span class="text-uppercase d-block fw-bold">ISP</span>
            <h3 class="mb-0 mt-2">{{ ipOutput.isp || 'ISP' }}</h3>
          </div>
          <Loading v-if="loading"></Loading>
        </div>
      </div>
    </header>
    <div id="map" class="vh-100 position-relative">
      <loading v-if="loading"></loading>
      <LMap v-if="!loading" ref="map" v-model:zoom="zoom" :center="[ipOutput.location.lat, ipOutput.location.lng]">
        <LTileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" layer-type="base" name="OpenStreetMap"
          class="p-5"></LTileLayer>
        <LMarker :lat-lng="[ipOutput.location.lat, ipOutput.location.lng]">
          <LIcon icon-url="./images/icon-location.svg" :icon-size="[30, 30 * 1.15]"
            :icon-anchor="[30 / 2, 30 * 1.15]"></LIcon>
        </LMarker>
      </LMap>
    </div>
  </main>
</template>
<script setup>
import { ref, onMounted } from 'vue';
import { useVuelidate } from '@vuelidate/core'
import { required, ipAddress } from '@vuelidate/validators'
import Loading from './components/Loading.vue'
import "leaflet/dist/leaflet.css";
import { LMap, LTileLayer, LMarker, LIcon } from "@vue-leaflet/vue-leaflet";

const ipAddressInput = ref('')
const ipOutput = ref({})
const loading = ref(true)
const zoom = ref(13)

const validation = useVuelidate({ required, ipAddress }, ipAddressInput)

const submission = () => {
  validation.value.$validate()
  if (!validation.value.$error) {
    loading.value = true
    fetch(`https://geo.ipify.org/api/v2/country,city?apiKey=at_XxZ5BPNq9X7Eb3dtICXB9PloLDk6X&ipAddress=${ipAddressInput.value}`)
      .then(response => response.json()).then(data => {
        loading.value = false
        ipOutput.value = data
      })
  }
}

onMounted(() => {
  loading.value = true
  fetch('https://api.ipify.org?format=json')
    .then(response => response.json())
    .then(data => {
      fetch(`https://geo.ipify.org/api/v2/country,city?apiKey=at_XxZ5BPNq9X7Eb3dtICXB9PloLDk6X&ipAddress=${data.ip}`)
        .then(response => response.json()).then(data => {
          loading.value = false
          ipOutput.value = data
        })
    })
})




// Google Maps With CDN in HTML File With API Key And Callback Function initMap() Take A Look Bro ♥

/* let initMap = navigator.geolocation.getCurrentPosition((position) => {
  let coords = new google.maps.LatLng(position.coords.latitude, position.coords.longitude)

  let options = {
    zoom: 13,
    center: coords,
    mapTypeId: google.maps.MapTypeId.ROADMAP
  }

  let map = new google.maps.Map(document.getElementById('map'), options)

  let marker = new google.maps.Marker({
    map: map,
    position: coords
  })
}, (error) => {
  alert(error)
}) */


</script>
<style lang="scss">
* {
  box-sizing: border-box;
}

:root {
  // ## Colors
  --Very-Dark-Gray: hsl(0, 0%, 17%);
  --Dark-Gray: hsl(0, 0%, 59%);
}

body {
  font-family: 'Rubik', sans-serif;
}

ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

input {
  font-size: 18px;
}

.mobile {
  display: none;
}

main {
  header {
    img {
      z-index: -1;
    }

    h1 {
      color: white;
    }

    form {
      input {
        border-top-left-radius: 13px;
        border-bottom-left-radius: 13px;
        outline: none;
      }

      .submit-butn {
        background-color: black;
        border-top-right-radius: 13px;
        border-bottom-right-radius: 13px;
        transition: 0.2s;
        cursor: pointer;

        &:hover {
          background-color: var(--Very-Dark-Gray);
        }
      }
    }

    .info-box {
      overflow: hidden;
      position: absolute;
      width: 100%;
      top: 195px;
      box-shadow: 0px 5px 20px #00000033;
      z-index: 5555;

      .box {
        position: relative;
        width: 20%;

        &:not(:last-child)::before {
          content: '';
          position: absolute;
          height: 75%;
          width: 1px;
          top: 50%;
          transform: translateY(-50%);
          background-color: var(--Dark-Gray);
          border-radius: 50px;
          opacity: 0.3;
          right: -40px;
        }

        span {
          font-size: 12px;
          letter-spacing: 2px;
          color: var(--Dark-Gray);
        }

        h3 {
          color: var(--Very-Dark-Gray);
        }
      }
    }
  }

  @media (max-width: 767px) {
    .desk {
      display: none;
    }

    .mobile {
      display: block;
    }

    header {
      form.search {
        width: 100% !important;
      }
    }

    .info-box {
      flex-direction: column;
      gap: 15px;
      left: 50%;
      top: 165px !important;
      text-align: center !important;
      width: calc(100% - 24px) !important;
      transform: translateX(-50%);

      .box {
        width: 100% !important;

        h3 {
          font-size: 17px;
        }

        &::before {
          display: none;
        }
      }
    }
  }
}
</style>