<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref } from 'vue';
  import { Map, Coordinate, TileLayer, VectorLayer, Marker, LineString, Polygon } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const center = new Coordinate(-0.113049, 51.498568);
    const map = new Map(mapRef.value, {
      center: center,
      zoom: 14,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    const marker = new Marker(
      center, // .add(-0.018,0.007).toArray(),
      {
        symbol: {
          textFaceName: '"microsoft yahei",arial,sans-serif',
          textName: 'MapTalks',
          textFill: '#34495e',
          textHorizontalAlignment: 'right',
          textSize: 40,
        },
      },
    );
    const polyline = new LineString(
      [
        center, // .add(-0.018,0.005).toArray(),
        center.add(0.006, 0.005).toArray(),
      ],
      {
        symbol: {
          lineColor: '#1bbc9b',
          lineWidth: 3,
        },
      },
    );
    const polygon = new Polygon(
      [
        center.add(-0.018, 0.004).toArray(),
        center.add(0.006, 0.004).toArray(),
        center.add(0.006, -0.001).toArray(),
        center.add(-0.018, -0.001).toArray(),
        center.add(-0.018, 0.004).toArray(),
      ],
      {
        symbol: {
          lineColor: '#34495e',
          lineWidth: 2,
          polygonFill: 'rgb(135,196,240)',
          polygonOpacity: 0.6,
        },
      },
    );

    const layer = new VectorLayer('vector').addGeometry([marker, polyline, polygon]).addTo(map);
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
