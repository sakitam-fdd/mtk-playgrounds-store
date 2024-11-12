<template>
  <div class="content">
    <div id="map" ref="mapRef" class="content-map"></div>
    <div id="map1" ref="map1Ref" class="content-map"></div>
  </div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, TileLayer, VectorLayer, Marker } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();
  const map1Ref = ref<HTMLDivElement>();
  function initMap() {
    const map = new Map(mapRef.value!, {
      center: [-0.113049, 51.498568],
      zoom: 13,
      attribution: true,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
      layers: [new VectorLayer('v', [new Marker([-0.113049, 51.498568])])],
    });

    const mapJSON = map.toJSON();
    Map.fromJSON(map1Ref.value!, mapJSON);

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
    display: flex;
  }

  .content-map {
    width: 50%;
    height: 100%;
    border: 1px solid;
  }
</style>
