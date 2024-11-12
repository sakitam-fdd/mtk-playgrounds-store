<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, TileLayer, DistanceTool } from 'maptalks';

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

    const distanceTool = new DistanceTool({
      symbol: {
        lineColor: '#34495e',
        lineWidth: 2,
      },
      vertexSymbol: {
        markerType: 'ellipse',
        markerFill: '#1bbc9b',
        markerLineColor: '#000',
        markerLineWidth: 3,
        markerWidth: 10,
        markerHeight: 10,
      },

      labelOptions: {
        textSymbol: {
          textFaceName: 'monospace',
          textFill: '#fff',
          textLineSpacing: 1,
          textHorizontalAlignment: 'right',
          textDx: 15,
          markerLineColor: '#b4b3b3',
          markerFill: '#000',
        },
        boxStyle: {
          padding: [6, 2],
          symbol: {
            markerType: 'square',
            markerFill: '#000',
            markerFillOpacity: 0.9,
            markerLineColor: '#b4b3b3',
          },
        },
      },
      clearButtonSymbol: [
        {
          markerType: 'square',
          markerFill: '#000',
          markerLineColor: '#b4b3b3',
          markerLineWidth: 2,
          markerWidth: 15,
          markerHeight: 15,
          markerDx: 20,
        },
        {
          markerType: 'x',
          markerWidth: 10,
          markerHeight: 10,
          markerLineColor: '#fff',
          markerDx: 20,
        },
      ],
      language: 'en-US',
    }).addTo(map);

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
