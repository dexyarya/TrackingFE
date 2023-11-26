<script setup>
import axios from "axios"
import { computed, onUnmounted, ref, watch } from "vue"
import L from "leaflet"
import "leaflet/dist/leaflet.css"
import "leaflet-routing-machine/dist/leaflet-routing-machine.css"
import "leaflet-routing-machine"
import icon from "../assets/fast-delivery.png"

const firstLocation = ref("")
const lastLocation = ref("")
const awb = ref("")
const courier = ref("")
const longitude = ref(0)
const latitude = ref(0)
const longitudeEnd = ref(0)
const latitudeEnd = ref(0)

const listCourier = [
  {
    value: "jnt",
    name: "JNT",
  },
  {
    value: "jne",
    name: "JNE",
  },
  {
    value: "ninja",
    name: "NINJA",
  },
  {
    value: "ateraja",
    name: "AnterAja",
  },
]

const map = ref(null)
let startPoint = [] // Ganti dengan koordinat awal pengiriman
let endPoint = []

const getDataLokasi = async () => {
  // getLokasiNinja()
  getLokasiNinja()
}

const getLokasiNinja = async () => {
  const payload = {
    courier: courier.value,
    awb: awb.value,
  }
  try {
    const response = await axios.get(
      `http://localhost:3000/api-v1/getResiLocation?courier=${payload.courier}&awb=${payload.awb}`
    )

    const coordinatesStart = response.data.startLocationCoordinates
    const coordinateEnd = response.data.endLocationCoordinates

    startPoint = [coordinatesStart.latitude, coordinatesStart.longitude]
    endPoint = [coordinateEnd.latitude, coordinateEnd.longitude]

    firstLocation.value = response.data.firstData
    lastLocation.value = response.data.lastData

    console.log(startPoint.value, endPoint.value)

    if (startPoint.value !== null && endPoint !== null) {
      getMaps(startPoint, endPoint)
    }
  } catch (error) {
    console.log(error)
  }
}

// const getLokasiNinja = async () => {
//     const apyKey = '5357e28a40496b4c92be86d8cfdbbca91e99593dce7766440749884df1288941'

//     try {
//         const response = await axios.get(`https://api.binderbyte.com/v1/track?api_key=${apyKey}&courier=ninja&awb=${awb.value}`)
//         console.log(response.data.data.history)
//         const data = response.data.data.history

//         firstLocation.value = data[data.length - 1].location
//         lastLocation.value = data[0].location
//         if (firstLocation.value !== '' && lastLocation.value !== '') {
//             await getLocationStart()
//             await getLocationEnd()
//             getMaps(startPoint, endPoint)

//         }

//         console.log(data)
//     } catch (error) {

//     }

// }

// const getLocationStart = async () => {
//     const locationName = firstLocation.value
//     try {
//         const response = await axios.get(`https://positionstack.com/geo_api.php?query=${locationName}`)
//         longitude.value = response.data.data[0].longitude
//         latitude.value = response.data.data[0].latitude
//         startPoint = [latitude.value, longitude.value]
//         console.log(startPoint)
//     } catch (error) {
//         console.error('Error in getLocationStart:', error)
//     }
// }

// const getLocationEnd = async () => {
//     const locationName = lastLocation.value
//     try {
//         const response = await axios.get(`https://positionstack.com/geo_api.php?query=${locationName}`)
//         longitudeEnd.value = response.data.data[0].longitude
//         latitudeEnd.value = response.data.data[0].latitude
//         endPoint = [latitudeEnd.value, longitudeEnd.value]
//         console.log(endPoint)
//     } catch (error) {
//         console.error('Error in getLocationEnd:', error)
//     }
// }

// Ganti dengan koordinat lokasi terakhir
const getMaps = (startPoint, endPoint) => {
  try {
    map.value = L.map("map").setView(startPoint, 13)
    L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png").addTo(
      map.value
    )

    L.Routing.control({
      waypoints: [L.latLng(startPoint), L.latLng(endPoint)],
      lineOptions: {
        styles: [{ color: "blue", weight: 6 }], // Ganti warna dan ketebalan garis sesuai keinginan
      },
      routeWhileDragging: true, // Mengizinkan pembaruan rute saat marker ditarik
    }).addTo(map.value)

    // polyline.remove()

    console.log(icon)

    // Tambahkan marker untuk titik awal dan akhir
    const carIcon = L.icon({
      iconUrl: icon, // Ganti dengan path gambar mobil Anda
      iconSize: [60, 60], // Sesuaikan dengan ukuran ikon Anda
      iconAnchor: [32, 32], // Sesuaikan dengan titik tengah ikon Anda
    })

    // if (startPoint)

    const carMarker = L.marker(endPoint, { icon: carIcon }).addTo(map.value)
    const startMarker = L.marker(startPoint).addTo(map.value)
    startMarker.bindPopup(`Lokasi Pertama (${firstLocation.value})`).openPopup()
    carMarker.bindPopup(`Lokasi saat ini (${lastLocation.value})`).openPopup()
  } catch (error) {
    console.error("Error in getMaps:", error)
  }
}

// check jika inputan sama maka akan mereset maps
watch(awb, (newValue, oldValue) => {
  if (newValue === oldValue) {
    map.value.remove()
  }
})
// validasi jika courier === '' maka user tidak data memasukan inputan ke form awb
const awbIsnotEmpt = computed(() => {
  return courier.value === ""
})

onUnmounted(() => {
  if (map.value) {
    map.value = null
  }
})
</script>

<template>
  <div class="h-screen">
    <div class="flex w-full justify-center mt-3">
      <h1 class="text-3xl font-bold">
        <span class="text-yellow-500 font-extrabold text-5xl">Tracking</span
        ><span class="font-bold text-lg">Resi</span>
      </h1>
    </div>
    <div class="px-14">
      <div
        class="col xs:px-20 sm:px-14 md:px-36 lg:px-48 xl:px-64 gap-6 mb-6 md:grid-cols-2"
      >
        <div class="mb-3">
          <label
            for="countries"
            class="block mb-2 text-sm font-medium text-gray-900 dark:text-black"
            >Select Your Courier</label
          >
          <select
            v-model="courier"
            id="countries"
            class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
          >
            <option selected disabled>Select Yout Couirer</option>
            <option
              v-for="(couriers, index) in listCourier"
              :key="index"
              :value="couriers.value"
            >
              {{ couriers.name }}
            </option>
          </select>
        </div>
        <div class="mb-3">
          <label
            for="first_name"
            class="block mb-2 text-sm font-medium text-gray-900 dark:text-black"
            >Input Your Resi</label
          >
          <input
            type="text"
            id="first_name"
            :disabled="awbIsnotEmpt"
            :class="awbIsnotEmpt ? 'cursor-not-allowed' : ''"
            v-model="awb"
            class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
            placeholder="Input Your Resi"
            required
          />
        </div>

        <div class="flex justify-end mt-6">
          <button
            @click="getDataLokasi"
            class="mb-3 bg-gray-600 hover:bg-gray-500 text-white font-bold py-2 px-4 border border-gray-900 rounded-lg"
          >
            Check Resi
          </button>
        </div>
      </div>
    </div>

    <div class="w-auto z-40">
      <div class="h-96 w-auto z-20" id="map"></div>
    </div>
  </div>
</template>

<style scoped></style>
