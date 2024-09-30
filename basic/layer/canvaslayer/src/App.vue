<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref } from 'vue';
  import { Map, TileLayer, CanvasLayer } from 'maptalks';

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

    const canvasLayer = new CanvasLayer('c', {
      forceRenderOnMoving: true,
      forceRenderOnZooming: true,
    });

    canvasLayer.prepareToDraw = function (/* context */) {
      return ['foo', 'bar'];
    };

    // param1 and param2 are prepareToDraw's return values.
    canvasLayer.draw = function (context, view, param1, param2) {
      const size = map.getSize();
      const str = param1 + ',' + param2;
      context.fillStyle = '#f00';
      context.font = 'bolder 50px sans-serif';
      const len = context.measureText(str);
      context.fillText(str, size.width / 2 - len.width / 2, size.height / 2);
      this.completeRender();
    };

    //draw when map is interacting
    canvasLayer.drawOnInteracting = function (context, view, param1, param2) {
      this.draw(context, view, param1, param2);
    };

    map.addLayer(canvasLayer);
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
