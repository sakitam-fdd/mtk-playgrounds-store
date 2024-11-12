<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, TileLayer, VectorLayer, Marker, LineString, Polygon, Coordinate } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const center = new Coordinate(-0.113049, 51.498568);
    const map = new Map(mapRef.value!, {
      center: center,
      zoom: 14,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    const marker = new Marker(center.add(-0.018, 0.007).toArray(), {
      draggable: true,
      symbol: {
        textFaceName: '"microsoft yahei",arial,sans-serif',
        textName: 'Try to Drag Us',
        textFill: '#34495e',
        textHorizontalAlignment: 'right',
        textSize: 40,
      },
    });
    const polyline = new LineString(
      [center.add(-0.018, 0.005).toArray(), center.add(0.006, 0.005).toArray()],
      {
        draggable: true,
        symbol: {
          lineColor: '#1bbc9b',
          lineWidth: 5,
        },
      },
    );
    const polygon = new Polygon(
      [
        center.add(-0.018, 0.004).toArray(),
        center.add(0.006, 0.004).toArray(),
        center.add(0.006, -0.001).toArray(),
        center.add(-0.018, -0.001).toArray(),
        center.add(-0.018, 0.004).toArray(),
      ],
      {
        draggable: true,
        symbol: {
          lineColor: '#34495e',
          lineWidth: 2,
          polygonFill: 'rgb(135,196,240)',
          polygonOpacity: 0.6,
        },
      },
    );

    const geometries = [marker, polyline, polygon];

    const layer = new VectorLayer('vector').addGeometry(geometries).addTo(map);

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
