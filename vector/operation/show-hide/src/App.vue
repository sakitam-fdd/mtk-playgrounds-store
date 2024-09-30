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
    center: [114.31, 30.52],
    zoom: 15,
  });

  const vtLayer = new maptalks.VectorTileLayer("vt", {
    urlTemplate: "https://tile.maptalks.com/test/planet-single/{z}/{x}/{y}.mvt",
    style: "{res}/styles/maptalks-common/style.json",
  });

  const groupLayer = new maptalks.GroupGLLayer("group", [vtLayer]).addTo(map);

  function toggleLayerVisible(visible) {
    if (visible) {
      vtLayer.show();
    } else {
      vtLayer.hide();
    }
  }

  const gui = new GUI();

  gui
    .add({
      type: "checkbox",
      label: "显示图层",
      value: true,
    })
    .onChange((value) => {
      toggleLayerVisible(value);
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