<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref } from 'vue';
  import { Map, Polygon, TileLayer, VectorLayer } from 'maptalks';
  import { Pane } from 'tweakpane';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const map = new Map(mapRef.value, {
      center: [121.4854, 31.2285],
      zoom: 14,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    const layer = new VectorLayer('vector').addTo(map);

    // get id of 100
    function get100() {
      getById(100);
    }

    // get id of 200
    function get200() {
      getById(200);
    }

    function getById(id) {
      layer.getGeometryById(id).updateSymbol([
        {
          polygonFill: '#f00',
        },
      ]);
    }

    for (let i = 0; i < 3; i++) {
      new Polygon(
        [
          [121.455542 + 0.02 * i, 31.233812],
          [121.468542 + 0.02 * i, 31.233812],
          [121.468542 + 0.02 * i, 31.222812],
          [121.455542 + 0.02 * i, 31.222812],
        ],
        {
          id: (i + 1) * 100,
          properties: {
            count: (i + 1) * 100,
          },
          symbol: [
            {
              polygonFill: '#747474',
              polygonOpacity: 0.5,
              lineColor: '#000',
              lineWidth: 2,
            },
            {
              textName: '{count}',
              textSize: 40,
              textFill: '#fff',
            },
          ],
        },
      ).addTo(layer);

      const gui = new Pane();
      gui
        .addButton({
          title: 'Get ID of 100',
        })
        .on('click', () => {
          get100();
        });

      gui
        .addButton({
          title: 'Get ID of 200',
        })
        .on('click', () => {
          get200();
        });
    }
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
