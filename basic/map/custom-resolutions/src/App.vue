<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import * as maptalks from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const MAX_ZOOM = 28;
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
      fullExtent: {
        top: 6378137 * Math.PI,
        left: -6378137 * Math.PI,
        bottom: -6378137 * Math.PI,
        right: 6378137 * Math.PI,
      },
    };

    const map = new maptalks.Map('map', {
      center: [-0.113049, 51.498568],
      zoom: 14,
      spatialReference: spatialReference,
      baseLayer: new maptalks.TileLayer('base', {
        maxAvailableZoom: 20,
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
      layers: [new maptalks.VectorLayer('v', [])],
    });

    return () => {
      map.remove();
    };
  }

  onMounted(() => {
    const dispose = initMap();
    onUnmounted(() => {
      dispose?.();
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
