<template>
  <div class="content">
    <div id="map" ref="mapRef" class="content-map"></div>
    <div id="map1" ref="map1Ref" class="content-map"></div>
  </div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, TileLayer, VectorLayer, Marker, Rectangle, Geometry } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();
  const map1Ref = ref<HTMLDivElement>();
  function initMap() {
    const c = [-0.113049, 51.498568];
    const map = new Map(mapRef.value!, {
      center: c,
      zoom: 13,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
      layers: [new VectorLayer('v')],
    });

    const marker = new Marker(c);
    const rect = new Rectangle(c, 1000, 800);
    map.getLayer('v').addGeometry(marker, rect);

    const map1 = new Map(map1Ref.value!, {
      center: c,
      zoom: 13,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });
    const newLayer = new VectorLayer('v').addTo(map1);
    // copy geometry by JSON
    Geometry.fromJSON(rect.toJSON()).addTo(newLayer);

    return () => {
      map.remove();
      map1.remove();
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
    display: flex;
  }

  .content-map {
    width: 50%;
    height: 100%;
    border: 1px solid;
  }
</style>
