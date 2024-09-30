<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref } from 'vue';
  import { Map, TileLayer, ImageLayer } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const map = new Map(mapRef.value, {
      center: [-0.09270712, 51.50615],
      zoom: 14,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    const imageLayer = new ImageLayer('images', [
      {
        url: 'http://examples.maptalks.com/resources/images/1.png',
        extent: [-0.11854216406254636, 51.50043810048564, -0.09081885168461667, 51.50994770979011],
        opacity: 1,
      },
      {
        url: 'http://examples.maptalks.com/resources/images/2.png',
        extent: [-0.10343596289067136, 51.50797115663946, -0.07897421667485105, 51.51876102463089],
        opacity: 1,
      },
    ]);

    map.addLayer(imageLayer);
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
