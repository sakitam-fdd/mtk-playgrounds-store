<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref, onUnmounted } from 'vue';
  import { Map, TileLayer, Marker, LineString, Polygon, GeometryCollection, VectorLayer } from 'maptalks';
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

    const point = new Marker([-0.113049 - 0.018, 51.498568 + 0.003], {
      symbol: {
        textFaceName: 'sans-serif',
        textName: 'MapTalks',
        textFill: '#34495e',
        textHorizontalAlignment: 'right',
        textSize: 40,
      },
      properties: {
        foo: 'marker',
      },
    });
    const line = new LineString(
      [
        [-0.131049, 51.499568],
        [-0.107049, 51.499568],
      ],
      {
        symbol: {
          lineColor: '#1bbc9b',
          lineWidth: 3,
        },
        properties: {
          foo: 'linestring',
        },
      },
    );
    const polygon = new Polygon(
      [
        [-0.131049, 51.498568],
        [-0.107049, 51.498568],
        [-0.107049, 51.493568],
        [-0.131049, 51.493568],
        [-0.131049, 51.498568],
      ],
      {
        symbol: {
          lineColor: '#34495e',
          lineWidth: 2,
          polygonFill: 'rgb(135,196,240)',
          polygonOpacity: 0.6,
        },
        properties: {
          foo: 'polygon',
        },
      },
    );

    const collection = new GeometryCollection([line, polygon, point], {
      visible: true,
      editable: true,
      cursor: null,
      shadowBlur: 0,
      shadowColor: 'black',
      draggable: false,
      dragShadow: false,
      drawOnAxis: null,
    });

    new VectorLayer('vector', collection).addTo(map);

    // filter
    function filter() {
      // condition can be a mapbox filter or a function
      const filtered = collection.filter(['==', 'foo', 'polygon']);
      filtered.forEach(function (polygon) {
        polygon.updateSymbol({
          polygonFill: '#f00',
        });
      });
    }

    const gui = new Pane();
    gui
      .addButton({
        title: 'filter "foo == polygon"',
      })
      .on('click', () => {
        filter();
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
