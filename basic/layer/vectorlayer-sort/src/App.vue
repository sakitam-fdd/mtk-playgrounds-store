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
      layers: [new VectorLayer('v')],
    });

    const rect3 = new Rectangle(map.getCenter().sub(0.025, 0.0035), 1200, 1000, {
      symbol: [
        {
          lineColor: '#34495e',
          lineWidth: 3,
          polygonFill: '#1bbc9b',
          polygonOpacity: 1,
        },
        {
          textName: '3',
          textWeight: 'bold',
          textSize: 30,
          textFill: '#fff',
        },
      ],
    });

    const rect2 = rect3
      .copy()
      .translate([0.006, 0.006])
      .updateSymbol([
        {
          polygonFill: 'rgb(216,115,149)',
        },
        {
          textName: '2',
        },
      ]);

    const rect1 = rect2
      .copy()
      .translate([0.006, 0.006])
      .updateSymbol([
        {
          polygonFill: 'rgb(135,196,240)',
        },
        {
          textName: '1',
        },
      ]);

    // sort to 3,2,1
    function sort1() {
      rect3.bringToFront();
      rect1.bringToBack();
    }

    // sort to 1,2,3
    function sort2() {
      rect1.setZIndex(3);
      rect2.setZIndex(2);
      rect3.setZIndex(1);
    }

    (map.getLayer('v') as VectorLayer).addGeometry(rect3, rect2, rect1);

    const gui = new Pane();
    gui
      .addButton({
        title: 'Sort to 3,2,1',
      })
      .on('click', () => {
        sort1();
      });

    gui
      .addButton({
        title: 'Sort to 1,2,3',
      })
      .on('click', () => {
        sort2();
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
