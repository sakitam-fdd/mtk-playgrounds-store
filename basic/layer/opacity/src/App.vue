<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref } from 'vue';
  import { Map, Rectangle, TileLayer, VectorLayer } from 'maptalks';

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

    const rect1 = new Rectangle(map.getCenter().add(-0.025, 0.005), 1600, 1000, {
      symbol: [
        {
          lineColor: '#34495e',
          lineWidth: 3,
          polygonFill: '#1bbc9b',
        },
        {
          textName: '70%',
          textWeight: 'bold',
          textSize: 30,
          textFill: '#fff',
        },
      ],
    });

    const rect2 = rect1
      .copy()
      .translate([0.03, 0])
      .updateSymbol([
        {},
        {
          textName: '40%',
        },
      ]);

    const layer1 = new VectorLayer('vector1', [rect1], {
      opacity: 0.7,
    }).addTo(map);

    const layer2 = new VectorLayer('vector2', [rect2], {
      opacity: 0.4,
    }).addTo(map);
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
