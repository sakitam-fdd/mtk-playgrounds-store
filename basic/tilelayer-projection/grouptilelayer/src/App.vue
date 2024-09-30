<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref } from 'vue';
  import { GroupTileLayer, Map, TileLayer } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const map = new Map(mapRef.value!, {
      center: [-0.113049, 51.498568],
      zoom: 6,
      pitch: 40,
      attribution: {
        content: ' &copy; carto',
      },
      // add 2 TileLayers with a GroupTileLayer
      baseLayer: new GroupTileLayer('base', [
        new TileLayer('tile2', {
          urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_nolabels/{z}/{x}/{y}.png',
          subdomains: ['a', 'b', 'c', 'd'],
        }),

        new TileLayer('boudaries', {
          urlTemplate: 'https://{s}.basemaps.cartocdn.com/dark_only_labels/{z}/{x}/{y}.png',
          subdomains: ['a', 'b', 'c', 'd'],
        }),
      ]),
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
