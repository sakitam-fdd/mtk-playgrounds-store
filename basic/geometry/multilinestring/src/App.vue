<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref, onUnmounted } from 'vue';
  import { Map, TileLayer, MultiLineString, VectorLayer } from 'maptalks';

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

    const multiline = new MultiLineString(
      [
        [
          [-0.131049, 51.503568],
          [-0.107049, 51.503568],
        ],
        [
          [-0.131049, 51.498568],
          [-0.107049, 51.498568],
        ],
        [
          [-0.131049, 51.493568],
          [-0.107049, 51.493568],
        ],
      ],
      {
        arrowStyle: null, // arrow-style : now we only have classic
        arrowPlacement: 'vertex-last', // arrow's placement: vertex-first, vertex-last, vertex-firstlast, point
        visible: true,
        editable: true,
        cursor: null,
        shadowBlur: 0,
        shadowColor: 'black',
        draggable: false,
        dragShadow: false, // display a shadow during dragging
        drawOnAxis: null, // force dragging stick on a axis, can be: x, y
        symbol: {
          lineColor: '#1bbc9b',
          lineWidth: 3,
        },
      },
    );

    new VectorLayer('vector', multiline).addTo(map);

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
