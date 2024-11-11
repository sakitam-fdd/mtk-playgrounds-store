<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref } from 'vue';
  import { Map, SpatialReference, TileLayer } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const url =
      'https://t0.tianditu.gov.cn/vec_c/wmts?request=GetCapabilities&service=wmts&tk=43dc56f9516dfedb64196da436b6dec3';

    SpatialReference.loadWMTS(url, (err, conf) => {
      if (err) {
        throw new Error(err);
      }
      const params = conf[0];
      params.urlTemplate += '&tk=43dc56f9516dfedb64196da436b6dec3';
      let spatialReference = params.spatialReference;
      const tileLayer = new TileLayer('tilelayer', params);
      spatialReference = params.spatialReference;

      new Map(mapRef.value!, {
        center: [114.3404041441181, 30.548730054693106],
        zoom: 10,
        spatialReference: spatialReference,
        baseLayer: tileLayer,
      });
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
    height: 100%;
  }
</style>
