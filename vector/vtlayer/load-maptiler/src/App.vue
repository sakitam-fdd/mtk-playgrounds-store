<template>
    <div id="map" ref="mapRef" class="container"></div>
</template>

<script setup lang="ts">
    import { onMounted, ref } from 'vue';
    import * as maptalks from "maptalks";

    const mapRef = ref<HTMLDivElement>();

    function initMap() {
        

  const token = "uKYsZQZpm72WlbSgH9B7";

  const map = new maptalks.Map("map", {
    center: [-74.00912099912109, 40.71107610933129],
    zoom: 14,
  });

  const vt = new maptalks.VectorTileLayer("vt", {
    urlTemplate: `https://api.maptiler.com/tiles/v3/{z}/{x}/{y}.pbf?key=${token}`,
  });

  const group = new maptalks.GroupGLLayer("group", [vt]).addTo(map);

    }

    onMounted(() => {
        initMap();
    });
</script>

<style>

  @import 'https://esm.sh/maptalks/dist/maptalks.css';

  .content {
    width: 100%;
    height: 450px;
  }
  
</style>