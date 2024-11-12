<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref, onUnmounted } from 'vue';
  import { Map, GeoJSONVectorTileLayer, GroupGLLayer } from 'maptalks-gl';

  const mapRef = ref<HTMLDivElement>();

  function initMap() {
    const map = new Map(mapRef.value, {
      center: [-74.00912099912109, 40.71107610933129],
      zoom: 15,
      bearing: 157,
      pitch: 80,
      lights: {
        directional: {
          direction: [1, 0, -1],
          color: [1, 1, 1],
        },
        ambient: {
          resource: {
            url: {
              front: 'http://examples.maptalks.com/resources/hdr/gradient/front.png',
              back: 'http://examples.maptalks.com/resources/hdr/gradient/back.png',
              left: 'http://examples.maptalks.com/resources/hdr/gradient/left.png',
              right: 'http://examples.maptalks.com/resources/hdr/gradient/right.png',
              top: 'http://examples.maptalks.com/resources/hdr/gradient/top.png',
              bottom: 'http://examples.maptalks.com/resources/hdr/gradient/bottom.png',
            },
            prefilterCubeSize: 32,
          },
          exposure: 1,
          hsv: [0, 0.34, 0],
          orientation: 0,
        },
      },
    });

    const style = [
      {
        filter: true,
        renderPlugin: {
          dataConfig: {
            type: 'point',
          },
          sceneConfig: {
            collision: true,
            fading: false,
            depthFunc: 'always',
          },
          type: 'icon',
        },
        symbol: {
          markerType: 'ellipse',
          markerFill: '#1bbc9b',
          markerHeight: 21,
          markerWidth: 21,
        },
      },
    ];

    const layer = new GeoJSONVectorTileLayer('geo', {
      data: 'http://examples.maptalks.com/resources/geojson/area.geojson',
      style,
    });

    layer.on('dataload', (e) => {
      map.fitExtent(e.extent);
    });

    const groupLayer = new GroupGLLayer('group', [layer], {
      sceneConfig: {
        environment: {
          enable: true,
          mode: 1,
          level: 0,
          brightness: 0,
        },
      },
    });
    groupLayer.addTo(map);

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
    height: 450px;
  }
</style>
