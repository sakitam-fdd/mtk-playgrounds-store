<template>
    <div id="map" ref="mapRef" class="container"></div>
</template>

<script setup lang="ts">
    import { onMounted, ref } from 'vue';
    import * as maptalks from "maptalks";

    const mapRef = ref<HTMLDivElement>();

    function initMap() {
        

  const map = new maptalks.Map("map", {
    center: [-3.707467, 40.409928],
    zoom: 16,
  });

  const json = {
    type: "VectorTileLayer",
    id: "vt",
    options: {
      urlTemplate:
        "https://tile.maptalks.com/test/planet-single/{z}/{x}/{y}.mvt",
      style: "{res}/styles/maptalks-common/style.json",
    },
  };

  const vtLayer = maptalks.VectorTileLayer.fromJSON(json);

  const groupLayer = new maptalks.GroupGLLayer("group", [vtLayer]).addTo(map);

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