<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref } from 'vue';
  import { Map, TileLayer } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const baseLayer = new TileLayer('base', {
      urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
      subdomains: ['a', 'b', 'c', 'd'],
      attribution:
        "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
    });

    // generate tile url
    baseLayer.getTileUrl = function (x, y, z) {
      // replace with your own
      // e.g. return a url pointing to your sqlite database
      return TileLayer.prototype.getTileUrl.call(this, x, y, z);
    };

    baseLayer.on('renderercreate', function (e) {
      // load tile image
      // img(Image): an Image object
      // url(String): the url of the tile
      e.renderer.loadTileImage = function (img, url) {
        // mocking getting image's base64
        // replace it by your own, e.g. load from sqlite database
        const remoteImage = new Image();
        remoteImage.crossOrigin = 'anonymous';
        remoteImage.onload = function () {
          const base64 = getBase64Image(remoteImage);
          img.src = base64;
        };
        remoteImage.src = url;
      };
    });

    function getBase64Image(img) {
      const canvas = document.createElement('canvas');
      canvas.width = img.width;
      canvas.height = img.height;

      const ctx = canvas.getContext('2d');
      ctx.drawImage(img, 0, 0);

      const dataURL = canvas.toDataURL('image/png');
      return dataURL;
    }

    const map = new Map(mapRef.value!, {
      center: [-0.113049, 51.498568],
      zoom: 11,
      baseLayer: baseLayer,
    });
  }

  onMounted(() => {
    initMap();
  });
</script>

<style>
  @import 'https://esm.sh/maptalks/dist/maptalks.css';

  html, body, #app {
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
