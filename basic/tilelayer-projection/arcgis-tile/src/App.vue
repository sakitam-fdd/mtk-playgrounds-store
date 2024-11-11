<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref, onUnmounted } from 'vue';
  import { Map, SpatialReference, TileLayer } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();
  let map: Map;

  function initMap() {
    const arcUrl = 'https://server.arcgisonline.com/arcgis/rest/services/World_Imagery/MapServer';

    SpatialReference.loadArcgis(arcUrl + '?f=pjson', function (err, conf) {
      if (err) {
        throw new Error(err);
      }
      const ref = conf.spatialReference;
      ref.projection = 'EPSG:3857';
      ref.fullExtent = null;
      map = new Map(mapRef.value!, {
        center: [121, 0],
        zoom: 1,
        minZoom: 1,
        maxZoom: 16,
        spatialReference: ref,
        baseLayer: new TileLayer('base', {
          tileSystem: conf.tileSystem,
          tileSize: conf.tileSize,
          urlTemplate: arcUrl + '/tile/{z}/{y}/{x}',
          attribution: '&copy; <a target="_blank" href="' + arcUrl + '"">ArcGIS</a>',
        }),
      });
    });
  }

  onMounted(() => {
    initMap();
  });

  onUnmounted(() => {
    if (map) {
      map.remove();
    }
  });
</script>

<style>
  @import 'https://esm.sh/maptalks/dist/maptalks.css';

  .content {
    width: 100%;
    height: 100%;
  }
</style>
