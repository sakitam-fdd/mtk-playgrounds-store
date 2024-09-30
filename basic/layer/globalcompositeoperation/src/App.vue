<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref } from 'vue';
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

    // globalCompositeOperation on MDN:
    // https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/globalCompositeOperation
    const layer = new VectorLayer('v', {
      globalCompositeOperation: 'difference',
    }).addTo(map);

    // prepare data
    const center = map.getCenter(),
      width = 0.055,
      height = 0.03,
      markers = [],
      colors = ['#f00', '#0f0', '#00f'];
    for (let i = 0; i <= 50; i++) {
      const x = center.x + (Math.random() - 0.5) * width * 0.5;
      const y = center.y + (Math.random() - 0.5) * height * 0.5;
      const color = colors[Math.floor(Math.random() * 3)];
      markers.push(
        new Marker([x, y], {
          symbol: {
            markerType: 'ellipse',
            markerFill: color,
            markerFillOpacity: 1,
            markerLineWidth: 1,
            markerLineColor: color,
            markerWidth: 70,
            markerHeight: 70,
          },
        }),
      );
    }
    layer.addGeometry(markers);
  }

  onMounted(() => {
    initMap();
  });
</script>

<style>
  @import 'https://esm.sh/maptalks/dist/maptalks.css';

  html,
  body,
  #app {
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
