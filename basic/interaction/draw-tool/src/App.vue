<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, TileLayer, VectorLayer, DrawTool, control } from 'maptalks';

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

    const layer = new VectorLayer('vector').addTo(map);

    const drawTool = new DrawTool({
      mode: 'Point',
    })
      .addTo(map)
      .disable();

    drawTool.on('drawend', function (param) {
      console.info(param.geometry);
      layer.addGeometry(param.geometry);
    });

    const items = [
      'Point',
      'LineString',
      'Polygon',
      'Circle',
      'Ellipse',
      'Rectangle',
      'FreeHandLineString',
      'FreeHandPolygon',
    ].map(function (value) {
      return {
        item: value,
        click: function () {
          drawTool.setMode(value).enable();
        },
      };
    });

    const toolbar = new control.Toolbar({
      items: [
        {
          item: 'Shape',
          children: items,
        },
        {
          item: 'Disable',
          click: function () {
            drawTool.disable();
          },
        },
        {
          item: 'Clear',
          click: function () {
            layer.clear();
          },
        },
      ],
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
