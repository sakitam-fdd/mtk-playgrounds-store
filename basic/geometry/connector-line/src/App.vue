<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref, onUnmounted } from 'vue';
  import { Map, TileLayer, Marker, ConnectorLine, ArcConnectorLine, VectorLayer } from 'maptalks';

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
    });

    const layer = new VectorLayer('vector').addTo(map);

    // blue circle
    const src = new Marker([-0.128449, 51.503568], {
      symbol: {
        markerType: 'ellipse',
        markerFill: 'rgb(135,196,240)',
        markerFillOpacity: 0.8,
        markerLineColor: '#fff',
        markerLineWidth: 3,
        markerWidth: 120,
        markerHeight: 120,
      },
    });

    // red circle
    const dst = new Marker([-0.102149, 51.503568], {
      draggable: true,
      symbol: [
        {
          markerType: 'ellipse',
          markerFill: 'rgb(216,115,149)',
          markerFillOpacity: 0.8,
          markerLineColor: '#fff',
          markerLineWidth: 3,
          markerWidth: 70,
          markerHeight: 70,
        },
        {
          textName: 'Drag\nMe',
          textSize: 18,
          textFill: '#fff',
          textWrapCharacter: '\n',
        },
      ],
    });

    // connector line
    const line = new ConnectorLine(src, dst, {
      showOn: 'always', //'moving', 'click', 'mouseover', 'always'
      arrowStyle: 'classic',
      arrowPlacement: 'vertex-last', // 'vertex-last', //vertex-first, vertex-last, vertex-firstlast, point
      symbol: {
        lineColor: '#34495e',
        lineWidth: 2,
      },
    });

    layer.addGeometry(src, dst, line);

    const src2 = src.copy().translate(0, -0.01);
    const dst2 = dst.copy().translate(0, -0.01);
    // Arc Connector Line
    const line2 = new ArcConnectorLine(src2, dst2, {
      arcDegree: 90,
      showOn: 'always',
      symbol: {
        lineColor: '#34495e',
        lineWidth: 2,
      },
    });

    layer.addGeometry(src2, dst2, line2);

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
