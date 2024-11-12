<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, Marker, TileLayer, ui, VectorLayer } from 'maptalks';
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
      center: [100.63299495279648, 30.895363667711848],
      zoom: 3,
      pitch: 0,
      attribution: true,
      zoomControl: true,
      baseLayer: baseLayer,
    });

    const layer = new VectorLayer('layer').addTo(map);
    const point = new Marker(map.getCenter());
    layer.addGeometry(point);

    const uiMarker = new ui.UIMarker(map.getCenter(), {
      content: '<div class="text_marker">maptalks</div>',
      dy: -100,
    }).addTo(map);

    const pane = new Pane();

    pane
      .addBlade({
        view: 'slider',
        label: 'Layer opacity',
        min: 0,
        max: 1,
        step: 0.1,
        value: 1,
      })
      .on('change', ({ value }) => {
        baseLayer.options.opacity = parseFloat(value);
        layer.options.opacity = parseFloat(value);
      });

    const params = {
      visible: true,
      centercross: false,
    };

    pane.addBinding(params, 'visible').on('change', ({ value }) => {
      baseLayer.options.visible = value;
      layer.options.visible = value;
      uiMarker.options.visible = value;
    });
    pane.addBinding(params, 'centercross').on('change', ({ value }) => {
      map.options.centerCross = value;
    });

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
