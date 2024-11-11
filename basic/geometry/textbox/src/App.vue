<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref, onUnmounted } from 'vue';
  import { Map, TileLayer, TextBox, VectorLayer } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const map = new Map(mapRef.value!, {
      center: [-0.113049, 51.498568],
      zoom: 14,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    const textbox = new TextBox(
      'This is a textbox, with very long content', // content
      [-0.113049, 51.498568], // coordinate
      200, // width
      90, // height
      {
        draggable: true,
        textStyle: {
          wrap: true, // auto wrap text
          padding: [12, 8], // padding of textbox
          verticalAlignment: 'top',
          horizontalAlignment: 'right',
          symbol: {
            textFaceName: 'monospace',
            textFill: '#34495e',
            textHaloFill: '#fff',
            textHaloRadius: 4,
            textSize: 18,
            textWeight: 'bold',
          },
        },
        boxSymbol: {
          // box's symbol
          markerType: 'square',
          markerFill: 'rgb(135,196,240)',
          markerFillOpacity: 0.9,
          markerLineColor: '#34495e',
          markerLineWidth: 1,
        },
      },
    );

    new VectorLayer('vector', textbox).addTo(map);

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
