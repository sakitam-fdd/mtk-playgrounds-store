<template>
  <div id="map" ref="mapRef" class="content"></div>
  <div class="info" v-html="info"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted,ref } from 'vue';
  import * as maptalks from 'maptalks';

  const mapRef = ref<HTMLDivElement>();
  const info = ref('');

  function initMap() {
    const map = new maptalks.Map('map', {
      center: [-0.113049, 51.498568],
      zoom: 14,
      centerCross: true,
      baseLayer: new maptalks.TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    map.on('moving moveend zoomend', update);

    update();

    function update() {
      const projection = map.getProjection();
      const center = map.getCenter(),
        prj = projection.project(center),
        containerPoint = map.coordinateToContainerPoint(center).round();

      info.value = [
          'Center : [' + center.x.toFixed(5) + ', ' + center.y.toFixed(5) + ']',
          'Projected Coordinate : [' + prj.x.toFixed(5) + ', ' + prj.y.toFixed(5) + ']',
          'ContainerPoint is the screen position in px from northwest of the map container.',
          'ContainerPoint : [' + containerPoint.x + ', ' + containerPoint.y + ']',
        ].join('<br>');
    }

    return () => {
      map.remove();
    }
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

  .info {
    --guide-color-comps: 230, 20%, 15%;
    --bg-color-secondary: hsl(230, 7%, 95%);
    position: absolute;
    top: 20px;
    left: 20px;
    padding: 15px;
    background-image: radial-gradient(hsla(var(--guide-color-comps), 0.08) 1px, transparent 0), radial-gradient(hsla(var(--guide-color-comps), 0.04) 1px, transparent 0);
    background-size: 32px 32px, 8px 8px;
    background-position: center;
    background-color: var(--bg-color-secondary);
    border-radius: 6px;
  }
</style>
