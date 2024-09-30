<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref } from 'vue';
  import { Map, Marker, TileLayer, VectorLayer } from 'maptalks';
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
        textName: "Layer is added.",
        textWeight: "bold",
        textSize: 50,
        textFill: "#1bbc9b",
        textHaloFill: "#fff",
        textHaloRadius: 5,
      },
    });

    const layer = new VectorLayer("vector", [marker]).addTo(map);

    function add() {
      map.addLayer(layer);
    }

    function remove() {
      map.removeLayer(layer);
    }

    const gui = new Pane();
    gui
      .addButton({
        title: 'Add Layer',
      })
      .on('click', () => {
        map.addLayer(layer);
      });

      gui
        .addButton({
          title: 'Remove Layer',
        })
        .on('click', () => {
          map.removeLayer(layer);
        });
  }

  onMounted(() => {
    initMap();
  });
</script>

<style>
  @import 'https://esm.sh/maptalks/dist/maptalks.css';

  html,
  body,
  #app {
    width: 100%;
    height: 100%;
    margin: 0;
    padding: 0;
    overflow: hidden;
  }

  .content {
    width: 100%;
    height: 100%;
  }
</style>
