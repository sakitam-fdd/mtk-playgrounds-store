<template>
  <div id="map" ref="mapRef" class="content"></div>
  <div class="info" v-html="info"></div>
</template>

<script setup lang="ts">
  import { onMounted, ref, onUnmounted } from 'vue';
  import { Map, TileLayer, Marker, VectorLayer } from 'maptalks';
  import { Pane } from 'tweakpane';

  const mapRef = ref<HTMLDivElement>();
  const info = ref('');
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

    const marker = new Marker(map.getCenter(), {
      symbol: [
        {
          markerType: 'square',
          markerFill: 'rgba(216,115,149,0.8)',
          markerWidth: 120,
          markerHeight: 120,
        },
        {
          textName: 'Click\non Me',
          textSize: 18,
        },
      ],
    }).addTo(layer);

    addListen();

    function addListen() {
      // mousemove and touchmove is annoying, so not listening to it.
      marker.on('mousedown mouseup click dblclick contextmenu touchstart touchend', onEvent);
    }

    function removeListen() {
      // mousemove and touchmove is annoying, so not listening to it.
      marker.off('mousedown mouseup click dblclick contextmenu touchstart touchend', onEvent);
    }

    const events = [];

    function onEvent(param) {
      events.push(param);
      let content = '';
      for (let i = events.length - 1; i >= 0; i--) {
        content +=
          events[i].type +
          ' on ' +
          events[i].coordinate
            .toArray()
            .map(function (c) {
              return c.toFixed(5);
            })
            .join() +
          '<br>';
      }
      info.value = '<div>' + content + '</div>';
      // return false to stop event propagation
      return false;
    }

    const gui = new Pane();
    gui
      .addButton({
        title: 'Listen',
      })
      .on('click', () => {
        addListen();
      });

    gui
      .addButton({
        title: 'Unlisten',
      })
      .on('click', () => {
        removeListen();
      });

    return () => {
      gui.dispose();
      removeListen();
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
    max-height: calc(100% - 60px);
    overflow-y: auto;
  }
</style>
