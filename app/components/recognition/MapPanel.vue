<template>
  <div class="map-panel">
    <div class="map-header">
      <p class="eyebrow">Geo Overview</p>
      <h2>Recognition Map</h2>
    </div>
    <div class="map-frame">
      <iframe :src="mapUrl" :key="reloadKey" title="Recognition map"></iframe>
    </div>
  </div>
</template>

<script setup lang="ts">
const props = defineProps<{
  reloadKey: number | string
}>()

const baseUrl = 'http://localhost:8000/map'
const mapUrl = computed(() => `${baseUrl}?t=${props.reloadKey}`)
</script>

<style scoped>
.map-panel {
  position: relative;
  display: grid;
  width: 100%;
  padding: clamp(1.25rem, 3vw, 2rem);
  grid-template-rows: auto minmax(0, 1fr);
  gap: 1.25rem;
  overflow: hidden;
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 0.5rem;
  background:
    radial-gradient(circle at 18% 12%, rgba(141, 255, 232, 0.12), transparent 18rem),
    linear-gradient(135deg, rgba(255, 255, 255, 0.075), rgba(255, 255, 255, 0.018)),
    rgba(6, 10, 13, 0.66);
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.035),
    0 30px 90px rgba(0, 0, 0, 0.45);
  backdrop-filter: blur(18px);
}

.eyebrow {
  margin: 0 0 0.45rem;
  color: #70e8d8;
  font-size: 0.72rem;
  font-weight: 850;
  letter-spacing: 0.18em;
  text-transform: uppercase;
}

.map-header h2 {
  margin: 0;
  color: #f6fffd;
  font-size: clamp(1.35rem, 2.4vw, 2rem);
  font-weight: 900;
  line-height: 1;
}

.map-frame {
  position: relative;
  overflow: hidden;
  min-height: 24rem;
  border-radius: 0.5rem;
  border: 1px solid rgba(151, 255, 235, 0.16);
  background: rgba(2, 6, 8, 0.52);
}

.map-frame iframe {
  width: 100%;
  height: 100%;
  min-height: 24rem;
  border: 0;
}
</style>
