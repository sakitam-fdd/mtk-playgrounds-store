<template>
    <div id="map" ref="mapRef" class="container"></div>
</template>

<script setup lang="ts">
    import { onMounted, ref } from 'vue';
    import * as maptalks from "maptalks";

    const mapRef = ref<HTMLDivElement>();

    function initMap() {
        

  const map = new maptalks.Map("map", {
    center: [9.936716, 51.067612],
    zoom: 15,
    zoomControl: true,
  });

  const vtLayer = new maptalks.VectorTileLayer("vt", {
    urlTemplate: "https://tile.maptalks.com/test/planet-single/{z}/{x}/{y}.mvt",
    style: "{res}/styles/maptalks-common/style.json",
    maxAvailableZoom: 16,
  });

  const groupLayer = new maptalks.GroupGLLayer("group", [vtLayer]).addTo(map);

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