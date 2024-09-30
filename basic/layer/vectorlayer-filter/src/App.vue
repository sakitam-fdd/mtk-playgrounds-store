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

    const layer = map.getLayer('v');

    // select features and update symbol
    function filter() {
      layer.filter(['>=', 'count', 200]).forEach(function (feature) {
        feature.updateSymbol([
          {
            polygonFill: 'rgb(216,115,149)',
          },
        ]);
      });
    }

    // prepare data
    for (let i = 0; i < 3; i++) {
      new Polygon(
        [
          [-0.123049 + 0.02 * i, 51.503568],
          [-0.136049 + 0.02 * i, 51.503568],
          [-0.136049 + 0.02 * i, 51.488568],
          [-0.123049 + 0.02 * i, 51.488568],
        ],
        {
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
      ).addTo(layer as VectorLayer);

      const gui = new Pane();
      gui
        .addButton({
          title: 'Select >= 200',
        })
        .on('click', () => {
          filter();
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
