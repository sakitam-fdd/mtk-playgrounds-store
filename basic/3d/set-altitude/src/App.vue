<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, Marker, Polygon, LineString, TileLayer, ui, VectorLayer } from 'maptalks';
  import { Pane } from 'tweakpane';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const baseLayer = new TileLayer('base', {
      urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
      subdomains: ['a', 'b', 'c', 'd'],
      attribution:
        "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
    });

    const map = new Map(mapRef.value, {
      center: [114.26012989831725, 30.616193225646924],
      zoom: 18,
      pitch: 60,
      attribution: true,
      zoomControl: true,
      baseLayer: baseLayer,
    });

    const offset = 0.001;
    const center = map.getCenter();
    let c1: any = center.add(-offset, 0);
    let c2: any = center.add(offset, 0);

    let c3: any = center.add(offset / 2, offset / 2);

    let c11 = c1.add(0, offset);
    let c22 = c2.add(0, offset);

    c1 = c1.toArray();
    c2 = c2.toArray();
    c3 = c3.toArray();
    c11 = c11.toArray();
    c22 = c22.toArray();

    const layer = new VectorLayer('layer', {
      enableAltitude: true,
    }).addTo(map);

    const polygon = new Polygon([[c11, c22, c3]]);
    layer.addGeometry(polygon);
    const line = new LineString([c1, c2]);
    layer.addGeometry(line);

    const point = new Marker(c1);
    layer.addGeometry(point);

    const uiMarker = new ui.UIMarker(c2, {
      content: '<div class="text-marker">maptalks</div>',
      verticalAlignment: 'top',
    });
    uiMarker.addTo(map);

    const pane = new Pane();

    pane.addBlade({
      view: 'slider',
      label: 'altitude',
      min: 0,
      max: 100,
      step: 1,
      value: 0,
    }).on('change', ({ value }) => {
      [point, uiMarker, line, polygon].forEach((geo) => {
        geo.setAltitude(value);
      });
    });

    return () => {
      map.remove();
      pane.dispose();
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
