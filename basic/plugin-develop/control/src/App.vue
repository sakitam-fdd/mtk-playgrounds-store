<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, TileLayer, control, DomUtil } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  const options = {
    position: 'top-right',
    content: 'My Control',
  };

  class MyControl extends control.Control {
    buildOn(map) {
      const dom = DomUtil.createEl('div', 'my-control');
      dom.innerText = this.options['content'];
      return dom;
    }
  }

  MyControl.mergeOptions(options);

  function initMap() {
    const map = new Map(mapRef.value, {
      center: [-0.113049, 51.503568],
      zoom: 14,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    const control = new MyControl({
      content: "Hello, MyControl!",
    });
    map.addControl(control);

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

  .my-control {
    background: #051127;
    border: 5px solid #fff;
    border-radius: 5px;
    color: #fff;
    height: 50px;
    line-height: 50px;
    text-align: center;
    font-size: 30px;
    padding: 0 8px;
  }
</style>
