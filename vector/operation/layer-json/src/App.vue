<template>
    <div id="map" ref="mapRef" class="container"></div>
</template>

<script setup lang="ts">
    import { onMounted, ref } from 'vue';
    import * as maptalks from "maptalks";

    const mapRef = ref<HTMLDivElement>();

    function initMap() {
        

  const map = new maptalks.Map("map", {
    center: [10.03808, 51.199505],
    zoom: 14,
  });

  const vtLayer = new maptalks.VectorTileLayer("vt", {
    urlTemplate: "https://tile.maptalks.com/test/planet-single/{z}/{x}/{y}.mvt",
    style: "{res}/styles/maptalks-common/style.json",
  });

  const groupLayer = new maptalks.GroupGLLayer("group", [vtLayer]).addTo(map);

  document.mapRef.value.innerHTML = JSON.stringify(vtLayer.toJSON());

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