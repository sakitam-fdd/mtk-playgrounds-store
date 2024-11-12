<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, TileLayer, Point, ui } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  const options = {
    content: '',
    // override parent's animationOnHide option
    animationOnHide: false,
  };

  class MyUI extends ui.UIComponent {
    constructor(coordinate, options) {
      super(options);
      this._coordinate = coordinate;
    }

    buildOn(map) {
      const dom = document.createElement('div');
      dom.className = 'my-ui';
      dom.innerText = this.options['content'];
      return dom;
    }

    getOffset() {
      const size = this.getSize();
      // move anchor to center of UI
      return new Point(-size.width / 2, -size.height / 2);
    }

    getEvents() {
      return {
        zoomend: this._flash,
      };
    }

    onRemove() {
      if (this._flashTimeout) {
        clearTimeout(this._flashTimeout);
      }
    }

    _flash() {
      // flash after zooming.
      this.hide();
      this._flashTimeout = setTimeout(() => {
        this.show(this._coordinate);
      }, 200);
    }
  }

  MyUI.mergeOptions(options);

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

    const ui = new MyUI(map.getCenter(), {
      content: 'Hello, MyUI',
    });
    ui.addTo(map).show();

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

  .my-ui {
    background: #051127;
    border: 5px solid #fff;
    border-radius: 5px;
    color: #fff;
    width: 200px;
    height: 50px;
    line-height: 50px;
    text-align: center;
  }
</style>
