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
    center: [-74.00912099912109, 40.71107610933129],
    zoom: 16,
    lights: {
      directional: {
        direction: [1, 0, -1],
        color: [1, 1, 1],
      },
      ambient: {
        resource: {
          url: {
            front: "{res}/hdr/gradient/front.png",
            back: "{res}/hdr/gradient/back.png",
            left: "{res}/hdr/gradient/left.png",
            right: "{res}/hdr/gradient/right.png",
            top: "{res}/hdr/gradient/top.png",
            bottom: "{res}/hdr/gradient/bottom.png",
          },
          prefilterCubeSize: 32,
        },
        exposure: 1,
        hsv: [0, 0.34, 0],
        orientation: 0,
      },
    },
  });

  const style = {
    style: [
      {
        filter: true,
        renderPlugin: {
          dataConfig: {
            type: "fill",
          },
          sceneConfig: {},
          type: "fill",
        },
        symbol: {
          polygonFill: "#89c2be",
          polygonOpacity: 1,
        },
      },
      {
        filter: true,
        renderPlugin: {
          dataConfig: {
            type: "line",
          },
          sceneConfig: {},
          type: "line",
        },
        symbol: {
          lineColor: "#E2E2E2",
          lineWidth: 2,
        },
      },
    ],
  };

  const geo = new maptalks.GeoJSONVectorTileLayer("geo", {
    data: "{res}/geojson/area.geojson",
    style,
  });

  geo.on("dataload", (e) => {
    map.fitExtent(e.extent);
  });

  function updateXinFeatureStyle() {
    geo.highlight([{ id: 12, color: "#efc69e" }]);
  }

  function updateNotXinFeatureStyle() {
    geo.highlight([
      {
        name: "非新洲区",
        filter: (feature) => feature.properties.name !== "新洲区",
        color: "#ef9e9f",
      },
    ]);
  }

  const groupLayer = new maptalks.GroupGLLayer("group", [geo], {
    sceneConfig: {
      environment: {
        enable: true,
        mode: 1,
        level: 0,
        brightness: 0,
      },
    },
  });
  groupLayer.addTo(map);

  const gui = new GUI();

  gui
    .add({
      type: "button",
      text: "更新新洲区样式",
    })
    .onClick(() => {
      updateXinFeatureStyle();
    });

  gui
    .add({
      type: "button",
      text: "更新非新洲区样式",
    })
    .onClick(() => {
      updateNotXinFeatureStyle();
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