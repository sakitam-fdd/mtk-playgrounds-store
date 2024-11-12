<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, LineString, TileLayer, VectorLayer } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const map = new Map(mapRef.value, {
      center: [-0.113049, 51.498568],
      zoom: 14,
      pitch: 56,
      bearing: 60,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    const line = new LineString(
      [
        [-0.131049, 51.498568],
        [-0.107049, 51.498568],
        [-0.101049, 51.498568],
      ],
      {
        symbol: {
          lineColor: '#1bbc9b',
          lineWidth: 3,
        },
        properties: {
          altitude: [100, 400, 1200],
        },
      },
    );

    // same line without alitutde
    const line0 = new LineString(
      [
        [-0.131049, 51.498568],
        [-0.107049, 51.498568],
      ],
      {
        symbol: {
          lineColor: '#000',
          lineDasharray: [10, 5, 5],
          lineWidth: 3,
        },
      },
    );

    new VectorLayer('vector', [line], {
      enableAltitude: true,
      drawAltitude: {
        polygonFill: '#1bbc9b',
        polygonOpacity: 0.3,
        lineWidth: 0,
      },
    }).addTo(map);

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
