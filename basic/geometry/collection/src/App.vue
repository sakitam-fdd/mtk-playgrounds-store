<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref, onUnmounted } from 'vue';
  import { Map, TileLayer, Coordinate, Marker, LineString, Polygon, VectorLayer, GeometryCollection } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const c = new Coordinate(-0.113049, 51.498568);
    const map = new Map(mapRef.value!, {
      center: c,
      zoom: 14,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    const marker = new Marker(c.add(-0.018, 0.007), {
      symbol: {
        textFaceName: 'sans-serif',
        textName: 'MapTalks',
        textFill: '#34495e',
        textHorizontalAlignment: 'right',
        textSize: 40,
      },
    });
    const line = new LineString([c.add(-0.018, 0.005), c.add(0.006, 0.005)], {
      symbol: {
        lineColor: '#1bbc9b',
        lineWidth: 3,
      },
    });
    const polygon = new Polygon(
      [c.add(-0.018, 0.004), c.add(0.006, 0.004), c.add(0.006, -0.001), c.add(-0.018, -0.001), c.add(-0.018, 0.004)],
      {
        symbol: {
          lineColor: '#34495e',
          lineWidth: 2,
          polygonFill: 'rgb(135,196,240)',
          polygonOpacity: 0.6,
        },
      },
    );

    const collection = new GeometryCollection([marker, line, polygon]);

    new VectorLayer('vector', collection).addTo(map);

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
