<template>
  <div id="map" ref="mapRef" class="content"></div>
  <div class="info">{{ mapStatus.join('\n') }}</div>
</template>

<script setup lang="ts">
  import { onMounted, ref } from 'vue';
  import { Map, Marker, TileLayer, VectorLayer, control } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();
  const mapStatus = ref<string[]>([]);

  function initMap() {
    const map = new Map(mapRef.value!, {
      center: [-0.113049, 51.498568],
      zoom: 14,
      centerCross: true,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    map.on('zoomend moving moveend', getStatus);

    getStatus();

    function getStatus() {
      const extent = map.getExtent(),
        ex = [
          '{',
          'xmin:' + extent.xmin.toFixed(5),
          ', ymin:' + extent.ymin.toFixed(5),
          ', xmax:' + extent.xmax.toFixed(5),
          ', ymax:' + extent.xmax.toFixed(5),
          '}',
        ].join('');
      const center = map.getCenter();
      mapStatus.value = [
        'Center : [' + [center.x.toFixed(5), center.y.toFixed(5)].join() + ']',
        'Extent : ' + ex,
        'Size : ' + map.getSize().toArray().join(),
        'Zoom : ' + map.getZoom(),
        'MinZoom : ' + map.getMinZoom(),
        'MaxZoom : ' + map.getMaxZoom(),
        'Projection : ' + map.getProjection().code,
      ];
    }
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

  .info {
    white-space: break-spaces;
  }
</style>
