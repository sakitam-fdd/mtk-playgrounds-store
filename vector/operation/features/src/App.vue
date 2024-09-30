<template>
    <div id="map" ref="mapRef" class="container"></div>
</template>

<script setup lang="ts">
    import { onMounted, ref } from 'vue';
    import * as maptalks from "maptalks";
import { GUI } from "mt.gui";

    const mapRef = ref<HTMLDivElement>();

    function initMap() {
        


  const map = new maptalks.Map("map", {
    center: [-110.01171, 53.277809],
    zoom: 16,
    zoomControl: true,
  });

  const vtLayer = new maptalks.VectorTileLayer("vt", {
    urlTemplate: "https://tile.maptalks.com/test/planet-single/{z}/{x}/{y}.mvt",
    features: false,
    style: "{res}/styles/maptalks-common/style.json",
  });

  const groupLayer = new maptalks.GroupGLLayer("group", [vtLayer]).addTo(map);

  map.on("click", (e) => {
    const identifiedData = vtLayer.identify(e.coordinate, { tolerance: 2 });
    const layers = getLayers(identifiedData);
    document.mapRef.value.innerText = !layers ? "无" : layers;
  });

  function setFeatures(value) {
    vtLayer.config({
      features: value,
    });
  }

  function getLayers(identifiedData) {
    if (!identifiedData.length) {
      return "";
    }
    console.log(identifiedData);
    const layers = identifiedData.map((data) => data.data.feature.layer);
    return layers.join(", ");
  }

  const gui = new GUI();

  gui
    .add({
      type: "checkbox",
      label: "features",
      value: false,
    })
    .onChange((value) => {
      setFeatures(value);
    });

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