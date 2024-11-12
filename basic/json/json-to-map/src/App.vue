<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const mapJSON = {
      version: '1.0',
      options: {
        center: {
          x: -0.113049,
          y: 51.49856800000001,
        },
        zoom: 13,
      },
      baseLayer: {
        type: 'TileLayer',
        id: 'base',
        options: {
          urlTemplate: 'http://{s}.tile.osm.org/{z}/{x}/{y}.png',
          subdomains: ['a', 'b', 'c'],
        },
      },
      layers: [
        {
          type: 'VectorLayer',
          id: 'v',
          geometries: [
            {
              feature: {
                type: 'Feature',
                geometry: {
                  type: 'Point',
                  coordinates: [-0.113049, 51.498568],
                },
              },
            },
          ],
        },
      ],
    };

    const map = Map.fromJSON(mapRef.value!, mapJSON);

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
