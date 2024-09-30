<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref } from 'vue';
  import { Map, Rectangle, TileLayer, VectorLayer } from 'maptalks';
  import { Pane } from 'tweakpane';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const map = new Map(mapRef.value, {
      center: [-0.113049, 51.498568],
      zoom: 14,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
      layers: [new VectorLayer('1', []), new VectorLayer('2', [])],
    });

    function bringToFront() {
      // bringToFront
      map.getLayer('1').bringToFront();
    }

    function sendToBack() {
      // bringToBack
      map.getLayer('1').bringToBack();
    }

    const rect2 = new Rectangle(map.getCenter().add(-0.02, 0), 1600, 1000, {
      symbol: [
        {
          lineColor: '#34495e',
          lineWidth: 3,
          polygonFill: '#1bbc9b',
          polygonOpacity: 1,
        },
        {
          textName: 'Layer 2',
          textWeight: 'bold',
          textSize: 30,
          textFill: '#fff',
        },
      ],
    });

    (map.getLayer('2') as VectorLayer).addGeometry(rect2);

    const rect1 = rect2
      .copy()
      .translate([0.006, 0.006])
      .updateSymbol([
        {
          polygonFill: 'rgb(216,115,149)',
        },
        {
          textName: 'Layer 1',
        },
      ]);

    (map.getLayer('1') as VectorLayer).addGeometry(rect1);

    const gui = new Pane();
    gui
      .addButton({
        title: 'Bring layer 1 to front',
      })
      .on('click', () => {
        bringToFront();
      });

    gui
      .addButton({
        title: 'Send layer 1 to back',
      })
      .on('click', () => {
        sendToBack();
      });
  }

  onMounted(() => {
    initMap();
  });
</script>

<style>
  @import 'https://esm.sh/maptalks/dist/maptalks.css';

  html, body, #app {
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
