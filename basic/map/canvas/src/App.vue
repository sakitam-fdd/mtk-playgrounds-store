<template>
  <canvas id="map" ref="mapRef" class="content"></canvas>
</template>

<script setup lang="ts">
  import { onMounted, ref } from 'vue';
  import * as maptalks from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const canvas = mapRef.value;
    const res = (window.devicePixelRatio || window.screen.deviceXDPI / window.screen.logicalXDPI) > 1;
    if (res) {
      // retina, see https://developer.mozilla.org/en-US/docs/Web/API/Window/devicePixelRatio
      const r = 2;
      canvas.width = r * canvas.clientWidth;
      canvas.height = r * canvas.clientHeight;
    } else {
      canvas.width = canvas.clientWidth;
      canvas.height = canvas.clientHeight;
    }

    const map = new maptalks.Map(mapRef.value, {
      center: [-0.113049, 51.498568],
      zoom: 14,
      zoomControl: true, // ignored in a canvas container
      scaleControl: true, // ignored in a canvas container
      baseLayer: new maptalks.TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
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
