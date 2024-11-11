<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref, onUnmounted } from 'vue';
  import { Map, TileLayer, Marker, VectorLayer } from 'maptalks';
  import { Pane } from 'tweakpane';

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

    const marker = new Marker(map.getCenter(), {
      symbol: {
        textFaceName: 'sans-serif',
        textName: 'FLASH\nME',
        textFill: '#34495e',
        textSize: 40,
        textHaloColor: 'white',
        textHaloRadius: 8,
      },
    });

    new VectorLayer('vector', marker).addTo(map);

    function flash() {
      marker.flash(
        200, // flash interval in ms
        5, // count
        function () {
          // callback when flash end
          alert('flash ended');
        },
      );
    }

    const gui = new Pane();
    gui
      .addButton({
        title: 'Flash',
      })
      .on('click', () => {
        flash();
      });

    return () => {
      gui.dispose();
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
