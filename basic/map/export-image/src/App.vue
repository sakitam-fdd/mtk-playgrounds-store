<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, Marker, TileLayer, VectorLayer } from 'maptalks';
  import { Pane } from 'tweakpane';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const map = new Map(mapRef.value!, {
      center: [-0.113049, 51.498568],
      zoom: 14,
      baseLayer: new TileLayer('base', {
        // crossOrigin : 'anonymous', // required if renderer is canvas
        // renderer : 'canvas',
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    new VectorLayer('v', new Marker(map.getCenter())).addTo(map);

    // Export map to an image
    // External image(tiles, marker images) hosts need to support CORS
    function save() {
      const data = map.toDataURL({
        mimeType: 'image/jpeg', // or 'image/png'
        save: true, // to pop a save dialog
        fileName: 'map', // file name
      });
    }

    const gui = new Pane();
    gui
      .addButton({
        title: 'Export image',
      })
      .on('click', () => {
        save();
      });

    return () => {
      gui.dispose();
      map.remove();
    };
  }

  onMounted(() => {
    const dispose = initMap();
    onUnmounted(() => {
      dispose?.();
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
