<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref, onUnmounted } from 'vue';
  import { Map, TileLayer, Rectangle, Circle, Sector, Ellipse, VectorLayer } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

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

    const center = map.getCenter();

    const rectangle = new Rectangle(center.add(-0.018, 0.012), 800, 700, {
      symbol: {
        lineColor: '#34495e',
        lineWidth: 2,
        polygonFill: '#34495e',
        polygonOpacity: 0.4,
      },
    });
    const circle = new Circle(center.add(0.002, 0.008), 500, {
      symbol: {
        lineColor: '#34495e',
        lineWidth: 2,
        polygonFill: '#1bbc9b',
        polygonOpacity: 0.4,
      },
    });
    const sector = new Sector(center.add(-0.013, -0.001), 900, 240, 300, {
      symbol: {
        lineColor: '#34495e',
        lineWidth: 2,
        polygonFill: 'rgb(135,196,240)',
        polygonOpacity: 0.4,
      },
    });

    const ellipse = new Ellipse(center.add(0.003, -0.005), 1000, 600, {
      symbol: {
        lineColor: '#34495e',
        lineWidth: 2,
        polygonFill: 'rgb(216,115,149)',
        polygonOpacity: 0.4,
      },
    });

    new VectorLayer('vector').addGeometry([rectangle, circle, sector, ellipse]).addTo(map);

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
