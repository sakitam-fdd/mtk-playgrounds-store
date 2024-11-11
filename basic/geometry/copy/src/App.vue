<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref, onUnmounted } from 'vue';
  import { Map, TileLayer, Rectangle, VectorLayer } from 'maptalks';
  import { Pane } from 'tweakpane';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const map = new Map(mapRef.value!, {
      center: [-0.113049, 51.49856],
      zoom: 14,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    const layer = new VectorLayer('vector').addTo(map);
    const copyLayer = new VectorLayer('copy').addTo(map);

    const rect = new Rectangle([-0.121049, 51.50656], 800, 600, {
      symbol: {
        lineColor: '#fff',
        lineWidth: 2,
        polygonFill: 'rgb(216,115,149)',
        polygonOpacity: 0.7,
      },
    }).addTo(layer);

    let counter = 1;

    function copy() {
      // copy with translation of [0.003, -0.003]
      rect
        .copy()
        .translate(0.003 * counter, -0.003 * counter)
        .addTo(copyLayer);
      counter++;
    }

    function clear() {
      counter = 1;
      copyLayer.clear();
    }

    const gui = new Pane();
    gui
      .addButton({
        title: 'Copy',
      })
      .on('click', () => {
        copy();
      });

    gui
      .addButton({
        title: 'Clear',
      })
      .on('click', () => {
        clear();
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
