<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, Marker, TileLayer, VectorLayer, control } from 'maptalks';

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
      layers: [new VectorLayer('v', null)],
    });

    function up() {
      map.panBy([0, -200]);
    }

    function down() {
      map.panBy([0, 200]);
    }

    function left() {
      map.panBy([-200, 0]);
    }

    function right() {
      map.panBy([200, 0]);
    }

    function toCoordinate() {
      const symbol = {
        markerType: 'x',
        markerLineColor: '#f00',
        markerLineWidth: 4,
        markerWidth: 20,
        markerHeight: 20,
      };
      const coordinate = map.getCenter().add(0.008, 0.008);
      map
        .getLayer('v')
        .clear()
        .addGeometry(new Marker(coordinate, { symbol: symbol }));
      map.panTo(coordinate);
    }

    const toolbar = new control.Toolbar({
      items: [
        {
          item: '↑',
          click: up,
        },
        {
          item: '↓',
          click: down,
        },
        {
          item: '←',
          click: left,
        },
        {
          item: '→',
          click: right,
        },
        {
          item: 'pan to',
          click: toCoordinate,
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
