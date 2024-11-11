<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref, onUnmounted } from 'vue';
  import { Map, WMSTileLayer } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const map = new Map(mapRef.value!, {
      center: [-0.113049, 51.498568],
      zoom: 6,
      spatialReference: {
        projection: 'EPSG:4326',
      },
      baseLayer: new WMSTileLayer('wms', {
        tileSystem: [1, -1, -180, 90],
        urlTemplate: 'https://ows.terrestris.de/osm/service',
        crs: 'EPSG:4326',
        layers: 'OSM-WMS',
        styles: '',
        version: '1.3.0',
        format: 'image/png',
        transparent: true,
        uppercase: true,
      }),
      attribution: {
        content: '&copy ows.terrestris.de',
      },
    });

    return () => {
      map.remove();
    };
  }

  onMounted(() => {
    const dispose = initMap();
    onUnmounted(() => {
      dispose();
    });
  });
</script>

<style>
  @import 'https://esm.sh/maptalks/dist/maptalks.css';

  .content {
    width: 100%;
    height: 100%;
  }
</style>
