<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref } from 'vue';
  import { Map, ParticleLayer, TileLayer, VectorLayer, Marker, Circle } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const map = new Map(mapRef.value, {
      center: [-0.113049, 51.498568],
      zoom: 14,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
      layers: [new VectorLayer('v')],
    });

    // An animated particle circle
    const particles = new ParticleLayer('c', {
      forceRenderOnMoving: true,
    });

    const center = map.getCenter();
    // circle's radius in meters
    const radius = 1000;

    particles.getParticles = function (t) {
      const point = map.coordinateToContainerPoint(center);
      // particle's angle at current time
      const angle = (((t / 16) % 360) * Math.PI) / 180;
      // convert distance in meter to pixel length
      const pxLen = map.distanceToPixel(radius, radius);
      const r = pxLen.width;
      // caculate pixel offset from circle's center
      const x = r * Math.cos(angle),
        y = r * Math.sin(angle);
      return [
        {
          point: point.add(x, y),
          r: 4,
          color: 'rgb(135,196,240)',
        },
      ];
    };

    map.addLayer(particles);

    new Marker(center, {
      symbol: {
        markerType: 'cross',
        markerWidth: 10,
        markerHeight: 10,
        markerLineWidth: 2,
      },
    }).addTo(map.getLayer('v') as VectorLayer);

    new Circle(center, 1000, {
      symbol: {
        lineColor: '#fff',
        lineWidth: 6,
        lineOpacity: 0.2,
        polygonOpacity: 0,
      },
    }).addTo(map.getLayer('v') as VectorLayer);
  }

  onMounted(() => {
    initMap();
  });
</script>

<style>
  @import 'https://esm.sh/maptalks/dist/maptalks.css';

  html,
  body,
  #app {
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
