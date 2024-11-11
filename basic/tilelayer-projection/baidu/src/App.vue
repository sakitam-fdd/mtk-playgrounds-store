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
      zoom: 5,
      minZoom: 1,
      maxZoom: 19,
      spatialReference: {
        projection: 'baidu',
      },
      baseLayer: new TileLayer('base', {
        urlTemplate:
          'https://gss{s}.bdstatic.com/8bo_dTSlRsgBo1vgoIiO_jowehsv/tile/?qt=tile&x={x}&y={y}&z={z}&styles=pl&scaler=1&udt=20170927',
        subdomains: ['0', '1', '2', '3'],
        attribution: '&copy; <a target="_blank" href="http://map.baidu.com">Baidu</a>',
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
