<template>
  <div id="map" ref="mapRef" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, TileLayer, MapTool, VectorLayer, Marker, control } from 'maptalks';

  const mapRef = ref<HTMLDivElement>();

  class CustomTool extends MapTool {
    onEnable() {
      this._markerLayer = new VectorLayer('CustomTool_layer').addTo(this.getMap());
    }

    onDisable() {
      if (this._markerLayer) {
        this._markerLayer.remove();
      }
    }

    getEvents() {
      return {
        click: this._onClick,
        contextmenu: this._onRightClick,
      };
    }

    _onClick(param) {
      this._markerLayer.addGeometry(new Marker(param.coordinate));
    }

    _onRightClick(param) {
      this._markerLayer.clear();
    }
  }

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

    const customTool = new CustomTool().addTo(map);

    function toolbar(text) {
      const toolbar = new control.Toolbar({
        position: 'top-right',
        items: [
          {
            item: text,
            click: function () {},
          },
        ],
      });
      return toolbar;
    }

    toolbar('<div class="attr">Click to add Marker, right click to clear</div>').addTo(map);

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
