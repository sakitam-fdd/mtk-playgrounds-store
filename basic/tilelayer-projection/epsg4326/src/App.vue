<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref, onUnmounted } from 'vue';
  import { Map, TileLayer } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const map = new Map(mapRef.value!, {
      center: [105.08052356963802, 36.04231948670001],
      zoom: 4,
      minZoom: 1,
      maxZoom: 18,
      spatialReference: {
        projection: 'EPSG:4326',
      },
      baseLayer: new TileLayer('base', {
        tileSystem: [1, -1, -180, 90],
        urlTemplate:
          'https://t{s}.tianditu.gov.cn/vec_c/wmts?SERVICE=WMTS&REQUEST=GetTile&VERSION=1.0.0&LAYER=vec&STYLE=default&TILEMATRIXSET=c&FORMAT=tiles&TILEMATRIX={z}&TILEROW={y}&TILECOL={x}&tk=43dc56f9516dfedb64196da436b6dec3',
        subdomains: ['1', '2', '3', '4', '5'],
        attribution: '&copy; <a target="_blank" href="http://www.tianditu.cn">Tianditu</a>',
      }),
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
