<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref } from 'vue';
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
      layers: [new VectorLayer('v', [])],
    });

    addMarkers();

    let pitch = 0,
      d = 'up',
      bearing = 0;
    let paused = false;

    changeView();

    function changeView() {
      if (pitch > 50) {
        d = 'down';
      } else if (pitch < 0) {
        d = 'up';
      }
      if (d === 'down') {
        pitch--;
      } else {
        pitch++;
      }
      map.setPitch(pitch);
      map.setBearing(bearing++);
      if (!paused) {
        requestAnimationFrame(changeView);
      }
    }

    function reset() {
      requestAnimationFrame(function () {
        paused = true;
        pitch = 0;
        bearing = 0;
        map.setPitch(0);
        map.setBearing(0);
      });
    }

    function addMarkers() {
      const center = map.getCenter();
      const m1 = new Marker(center.add(-0.008, -0.008));
      const m2 = new Marker(center.add(0.008, -0.008));
      const m3 = new Marker(center.add(-0.008, 0.008));
      const m4 = new Marker(center.add(0.008, 0.008));
      map.getLayer('v').addGeometry(m1, m2, m3, m4);
    }

    const toolbar = new control.Toolbar({
      items: [
        {
          item: 'pause',
          click: function () {
            paused = true;
          },
        },
        {
          item: 'start',
          click: function () {
            paused = false;
            changeView();
          },
        },
        {
          item: 'reset',
          click: function () {
            reset();
          },
        },
      ],
    }).addTo(map);
  }

  onMounted(() => {
    initMap();
  });
</script>

<style>
  @import 'https://esm.sh/maptalks/dist/maptalks.css';

  html, body, #app {
    width: 100%;
    height: 100%;
    margin: 0;
    padding: 0;
    overflow: hidden;
  }

  .content {
    width: 100%;
    height: 100%;
  }
</style>
