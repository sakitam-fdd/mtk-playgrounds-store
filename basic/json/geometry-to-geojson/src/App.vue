<template>
  <div id="map" ref="mapRef" class="content"></div>
  <div class="info" v-html="info"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, TileLayer, VectorLayer, Marker } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();
  const info = ref('');

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
      layers: [new VectorLayer('v')],
    });

    const marker = new Marker([-0.113049, 51.498568], {
      properties: {
        name: 'point marker',
      },
    }).addTo(map.getLayer('v'));

    info.value = JSON.stringify(marker.toGeoJSON());

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

  .info {
    --guide-color-comps: 230, 20%, 15%;
    --bg-color-secondary: hsl(230, 7%, 95%);
    position: absolute;
    top: 20px;
    left: 20px;
    padding: 15px;
    background-image: radial-gradient(hsla(var(--guide-color-comps), 0.08) 1px, transparent 0), radial-gradient(hsla(var(--guide-color-comps), 0.04) 1px, transparent 0);
    background-size: 32px 32px, 8px 8px;
    background-position: center;
    background-color: var(--bg-color-secondary);
    border-radius: 6px;
    max-height: calc(100% - 60px);
    overflow-y: auto;
  }
</style>
