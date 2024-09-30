<template>
  <input type="range" id="swipe" />

  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref } from 'vue';
  import { Map, TileLayer } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const layer = new TileLayer('light', {
      urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
      subdomains: ['a', 'b', 'c', 'd'],
      attribution:
        "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      // force layer to render when map is zooming and moving
      forceRenderOnMoving: true,
      forceRenderOnZooming: true,
    });

    const map = new Map(mapRef.value, {
      center: [121.4, 37.5],
      zoom: 13,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
      }),
      layers: [layer],
    });

    const swipe = document.getElementById('swipe');

    const renderer = layer.getRenderer();
    const canvasGetter = renderer.getCanvasImage;
    // override renderer's default method to get layer canvas image
    renderer.getCanvasImage = function () {
      const dpr = map.getDevicePixelRatio();
      // original layer canvas image
      const layerImage = canvasGetter.call(renderer);
      if (!layerImage || !layerImage.image) {
        return layerImage;
      }
      // drawn width after layer is erased by swipper
      const ctx = renderer.context;
      const width = renderer.canvas.width * (swipe.value / 100);
      const height = ctx.canvas.height;

      // copy drawn rect of original layer canvas
      const drawnRect = document.createElement('canvas');
      drawnRect.width = width;
      drawnRect.height = ctx.canvas.height;
      drawnRect.getContext('2d').drawImage(layerImage.image, 0, 0);

      // clear the erased part
      ctx.clearRect(0, 0, ctx.canvas.width, ctx.canvas.height);
      // draw a white background to cover the bottom layers when zooming
      ctx.beginPath();
      ctx.rect(0, 0, width / dpr, height / dpr);
      ctx.fillStyle = '#fff';
      ctx.fill();

      // draw the drawn part on layer's canvas
      ctx.drawImage(drawnRect, 0, 0, width / dpr, height / dpr);
      layerImage.image = ctx.canvas;
      return layerImage;
    };

    swipe.addEventListener('input', function () {
      // let layer redraw self in the next frame
      layer.getRenderer().setToRedraw();
    });
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

  #swipe {
    background-color: transparent;
    width: 100%;
    border: none;
    height: 10px;
    cursor: col-resize;
    margin-bottom: 20px;
  }

  .content {
    width: 100%;
    height: 100%;
  }
</style>
