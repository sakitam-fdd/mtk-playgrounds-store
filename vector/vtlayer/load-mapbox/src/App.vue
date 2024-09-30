<template>
    <div id="map" ref="mapRef" class="container"></div>
</template>

<script setup lang="ts">
    import { onMounted, ref } from 'vue';
    import * as maptalks from "maptalks";

    const mapRef = ref<HTMLDivElement>();

    function initMap() {
        

  const token =
    "pk.eyJ1IjoidGFuZ21pbmd5YW5nIiwiYSI6ImNqczVqZmw2eTBnOW0zem96OHN4Zm41bTgifQ.0EPVaDmwmlr8iXf9cT34Kg";

  const map = new maptalks.Map("map", {
    center: [-74.00912099912109, 40.71107610933129],
    zoom: 16,
  });

  const vt = new maptalks.VectorTileLayer("vt", {
    urlTemplate: `https://api.mapbox.com/v4/mapbox.mapbox-streets-v8/{z}/{x}/{y}.vector.pbf?access_token=${token}`,
    style: "{res}/styles/mapbox-light/style.json",
  });

  const group = new maptalks.GroupGLLayer("group", [vt]).addTo(map);

    }

    onMounted(() => {
        initMap();
    });
</script>

<style>

  @import 'https://esm.sh/maptalks/dist/maptalks.css';

  .content {
    width: 100%;
    height: 450px;
  }
  
</style>