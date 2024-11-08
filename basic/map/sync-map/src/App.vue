<template>
  <div id="map0" ref="map0Ref" class="content"></div>
  <div id="map1" ref="map1Ref" class="content"></div>
</template>

<script setup lang="ts">
  import { onMounted, onUnmounted, ref } from 'vue';
  import { Map, TileLayer, control } from 'maptalks';

  const map0Ref = ref<HTMLDivElement>();
  const map1Ref = ref<HTMLDivElement>();

  function initMap() {
    const map0 = new Map(map0Ref.value!, {
      center: [-0.113049, 51.498568],
      zoom: 14,
      zoomControl: true,
      baseLayer: new TileLayer('base', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    const map1 = new Map(map1Ref.value!, {
      center: map0.getCenter(),
      zoom: map0.getZoom(),
      draggable: false, // disable draggble
      scrollWheelZoom: false, // disable scroll wheel zoom
      doubleClickZoom: false, // disable doubleclick
      baseLayer: new TileLayer('base1', {
        urlTemplate: 'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
        subdomains: ['a', 'b', 'c', 'd'],
        attribution:
          "&copy; <a href='http://osm.org'>OpenStreetMap</a> contributors, &copy; <a href='https://carto.com/'>CARTO</a>",
      }),
    });

    map0.on('moving moveend', function (e) {
      map1.setCenter(e.target.getCenter());
    });

    map0.on('zooming zoomend', function (e) {
      map1.setCenterAndZoom(e.target.getCenter(), e.target.getZoom());
    });

    map0.on('pitch', function (e) {
      map1.setPitch(e.target.getPitch());
    });

    map0.on('rotate', function (e) {
      map1.setBearing(e.target.getBearing());
    });

    new control.Toolbar({
      position: 'top-right',
      items: [
        {
          item: 'move me',
          click: function () {},
        },
      ],
    }).addTo(map0);

    return () => {
      map0?.remove?.();
      map1?.remove?.();
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
    width: calc(50% - 2px);
    height: calc(100% - 2px);
    float: left;
    border: 1px solid;
  }

  #map1 {
    float: right;
  }
</style>
