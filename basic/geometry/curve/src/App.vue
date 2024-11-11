<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref, onUnmounted } from 'vue';
  import { Map, TileLayer, Coordinate, ArcCurve, QuadBezierCurve, CubicBezierCurve, VectorLayer } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const map = new Map(mapRef.value!, {
      center: [-0.11505, 51.4985],
      zoom: 14,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    const c = new Coordinate(-0.11305, 51.4985);

    const arc = new ArcCurve(
      [
        c.add(-0.0202, 0.0081),
        c.add(-0.0269, 0.0069),
        c.add(-0.0369, 0.0032),
        c.add(-0.0314, -0.003),
        c.add(-0.0278, -0.008),
        c.add(-0.022, -0.009),
      ],
      {
        symbol: getSymbol('Arc'),
      },
    );

    const quad = new QuadBezierCurve(
      [
        c.add(-0.0102, 0.0081),
        c.add(-0.0169, 0.0069),
        c.add(-0.0211, 0.0032),
        c.add(-0.0214, -0.0033),
        c.add(-0.0178, -0.0086),
        c.add(-0.012, -0.0095),
      ],
      {
        symbol: getSymbol('Quadratic\nBézier'),
      },
    );

    const cubic = new CubicBezierCurve(
      [
        c.add(-0.0002, 0.0081),
        c.add(-0.0069, 0.0069),
        c.add(-0.0069, 0.0032),
        c.add(-0.0114, -0.0033),
        c.add(-0.0078, -0.0086),
        c.add(-0.002, -0.0095),
      ],
      {
        symbol: getSymbol('Cubic\nBézier'),
      },
    );

    new VectorLayer('vector', [arc, quad, cubic]).addTo(map);

    function getSymbol(title) {
      return [
        {
          lineColor: '#34495e',
          lineWidth: 3,
        },
        {
          markerType: 'ellipse',
          markerWidth: 8,
          markerHeight: 8,
          markerFill: '#f00',
          markerPlacement: 'vertex',
        },
        {
          textName: title,
          textFill: '#f00',
          textWeight: 'bold',
          textHaloColor: '#fff',
          textHaloRadius: 3,
          textSize: 20,
          textWrapCharacter: '\n',
        },
      ];
    }

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
