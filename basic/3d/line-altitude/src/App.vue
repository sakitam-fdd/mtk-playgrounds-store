<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, LineString, TileLayer, VectorLayer } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const map = new Map(mapRef.value, {
      center: [-0.113049, 51.503568],
      zoom: 14,
      pitch: 56,
      bearing: 30,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    // line with one altitude
    const line1 = new LineString(
      [
        [-0.131049, 51.498568],
        [-0.107049, 51.498568],
        [-0.093049, 51.498568],
      ],
      {
        symbol: {
          lineColor: '#1bbc9b',
          lineWidth: 3,
          textName: '{altitude}',
          textPlacement: 'vertex',
        },
        properties: {
          altitude: 200, //altitude for all vertexes
        },
      },
    );

    // line with seperate alitutdes
    const line2 = new LineString(
      [
        [-0.131049, 51.498568],
        [-0.107049, 51.498568],
        [-0.093049, 51.498568],
      ],
      {
        properties: {
          altitude: [400, 600, 1200], //seperate altitude for each vertex
        },
        symbol: {
          lineColor: 'rgb(135,196,240)',
          lineWidth: 3,
          textName: '{altitude}',
          textPlacement: 'vertex',
        },
      },
    );

    // line without alitutde
    const line0 = new LineString(
      [
        [-0.131049, 51.498568],
        [-0.107049, 51.498568],
        [-0.093049, 51.498568],
      ],
      {
        symbol: {
          lineColor: '#000',
          lineDasharray: [10, 5, 5],
          lineWidth: 3,
          textName: '0',
          textPlacement: 'vertex',
        },
      },
    );

    new VectorLayer('vector', [line0, line1, line2], {
      enableAltitude: true,
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
