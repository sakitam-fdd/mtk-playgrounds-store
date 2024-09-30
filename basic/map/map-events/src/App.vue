<template>
  <div id="map" ref="mapRef" class="content"></div>
  <div>{{ info }}</div>
</template>

<script setup lang="ts">
  import { onMounted, ref } from 'vue';
  import { Map, TileLayer } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();
  const info = ref('')

  function initMap() {
    const map = new Map(mapRef.value!, {
      center: [-0.113049, 51.498568],
      zoom: 14,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    map.on('click', function (param) {
      info.value =
        new Date().toLocaleTimeString() +
        ': click map on ' +
        param.coordinate.toFixed(5).toArray().join();
    });
  }

  onMounted(() => {
    initMap();
  });
</script>

<style>
  @import 'https://esm.sh/maptalks/dist/maptalks.css';

  html, body, #app {
    width: 100%;
    height: 100%;
    margin: 0;
    padding: 0;
    overflow: hidden;
  }

  .content {
    width: 100%;
    height: 100%;
  }
</style>
