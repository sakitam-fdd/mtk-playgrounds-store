<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, Marker, TileLayer, VectorLayer } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const map = new Map(mapRef.value, {
      center: [-0.113049, 51.498568],
      zoom: 14,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    const layer = new VectorLayer('vector', {
      enableAltitude: true,
      // draw altitude
      drawAltitude: {
        lineWidth: 1,
        lineColor: '#000',
      },
    }).addTo(map);

    const extent = map.getExtent(),
      min = extent.getMin(),
      w = extent.getWidth(),
      h = extent.getHeight(),
      markers = [];
    for (let i = 0; i < 100; i++) {
      markers.push(
        new Marker([min.x + Math.random() * w, min.y + Math.random() * h], {
          properties: {
            // random altitude
            altitude: Math.random() * 600,
          },
        }),
      );
    }
    layer.addGeometry(markers);

    map.setPitch(60);

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
