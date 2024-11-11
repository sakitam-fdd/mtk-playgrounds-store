<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref, onUnmounted } from 'vue';
  import { Map, TileLayer } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const center = [105.08052356963802, 36.04231948670001];

    // set maxNativeZoom to 24
    const MAX_ZOOM = 25;
    const spatialReference = {
      projection: 'EPSG:3857',
      resolutions: (function () {
        const resolutions = [];
        const d = 2 * 6378137 * Math.PI;
        for (let i = 0; i < MAX_ZOOM; i++) {
          resolutions[i] = d / (256 * Math.pow(2, i));
        }
        return resolutions;
      })(),
    };

    const map = new Map(mapRef.value!, {
      center: center,
      spatialReference,
      zoom: 4,
      baseLayer: new TileLayer('base', {
        maxAvailableZoom: 22,
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    return () => {
      map.remove();
    };
  }

  onMounted(() => {
    const dispose = initMap();
    onUnmounted(() => {
      dispose();
    });
  });
</script>

<style>
  @import 'https://esm.sh/maptalks/dist/maptalks.css';

  .content {
    width: 100%;
    height: 100%;
  }
</style>
