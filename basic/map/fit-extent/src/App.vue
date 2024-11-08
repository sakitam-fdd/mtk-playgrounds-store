<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, Polygon, TileLayer, VectorLayer } from 'maptalks';
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
      layers: [new VectorLayer('v', [])],
    });

    const center = map.getCenter();
    const polygon = new Polygon(
      [center.add(-0.005, 0.005), center.add(0.005, 0.005), center.add(0.005, -0.005), center.add(-0.005, -0.005)],
      {
        symbol: {
          polygonFill: '#fff',
          polygonOpacity: 0.5,
        },
      },
    );
    (map.getLayer('v') as VectorLayer).addGeometry(polygon);

    const gui = new Pane();
    gui
      .addButton({
        title: 'Fit to extent',
      })
      .on('click', () => {
        // fit map's extent to polygon's
        // 0 is the zoom offset
        map.fitExtent(polygon.getExtent(), 0);
      });

    return () => {
      gui.dispose();
      map.remove();
    };
  }

  onMounted(() => {
    const dispose = initMap();
    onUnmounted(() => {
      dispose?.();
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
