<template>
  <main class="workspace-shell">
    <ImageUploadPanel
      @loading="isReportLoading = $event"
      @recognized="onRecognized"
      @reset="onReset"
    />

    <section class="report-shell" aria-label="Analysis report">
      <div class="panel-glow panel-glow-right"></div>
      <ReportPanel :is-loading="isReportLoading" :reports="reports" />
      <MapPanel :reload-key="mapReloadKey" />
    </section>
  </main>
</template>

<script setup lang="ts">
import ImageUploadPanel from '~/components/recognition/ImageUploadPanel.vue'
import type { RecognitionResult } from '~/components/recognition/ImageUploadPanel.vue'
import ReportPanel from '~/components/ReportPanel.vue'
import MapPanel from '~/components/recognition/MapPanel.vue'

const reports = ref<RecognitionResult[]>([])
const isReportLoading = ref(false)
const mapReloadKey = ref(0)

function onRecognized(result: RecognitionResult) {
  reports.value.push(result)
  mapReloadKey.value = Date.now()
}

function onReset() {
  reports.value = []
  mapReloadKey.value = Date.now()
}
</script>

<style scoped>
.workspace-shell {
  position: relative;
  display: grid;
  grid-template-columns: minmax(0, 1fr) minmax(0, 1fr);
  min-height: 92vh;
  overflow: hidden;
  background:
    radial-gradient(circle at 16% 14%, rgba(48, 179, 158, 0.2), transparent 31rem),
    radial-gradient(circle at 84% 16%, rgba(220, 76, 92, 0.15), transparent 29rem),
    linear-gradient(135deg, #050608 0%, #0c1114 45%, #07090c 100%);
}

.workspace-shell::before {
  position: absolute;
  inset: 0;
  z-index: 0;
  pointer-events: none;
  content: "";
  background-image:
    linear-gradient(rgba(255, 255, 255, 0.055) 1px, transparent 1px),
    linear-gradient(90deg, rgba(255, 255, 255, 0.055) 1px, transparent 1px);
  background-size: 72px 72px;
  mask-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.9), transparent 86%);
}

.workspace-shell::after {
  position: absolute;
  top: 7vh;
  bottom: 7vh;
  left: 50%;
  z-index: 1;
  width: 1px;
  content: "";
  background: linear-gradient(
    to bottom,
    transparent,
    rgba(145, 241, 224, 0.72),
    rgba(255, 255, 255, 0.1),
    transparent
  );
  box-shadow: 0 0 44px rgba(120, 255, 231, 0.34);
}

.report-shell {
  position: relative;
  z-index: 2;
  display: grid;
  grid-template-rows: minmax(22rem, 1fr) minmax(20rem, 1fr);
  min-width: 0;
  min-height: 92vh;
  padding: clamp(1rem, 3vw, 3rem);
  gap: 1rem;
}

.panel-glow {
  position: absolute;
  inset: auto;
  z-index: -1;
  width: 30rem;
  height: 30rem;
  pointer-events: none;
  filter: blur(12px);
  opacity: 0.48;
}

.panel-glow-right {
  top: 9%;
  left: 8%;
  background: conic-gradient(
    from 60deg,
    rgba(255, 208, 113, 0),
    rgba(255, 208, 113, 0.18),
    rgba(86, 178, 216, 0.28),
    rgba(255, 208, 113, 0)
  );
}

@media (max-width: 900px) {
  .workspace-shell {
    grid-template-columns: 1fr;
  }

  .workspace-shell::after {
    top: 50%;
    right: 1.5rem;
    bottom: auto;
    left: 1.5rem;
    width: auto;
    height: 1px;
  }

  .report-shell {
    grid-template-rows: minmax(22rem, auto) minmax(20rem, auto);
    min-height: 50vh;
  }
}

@media (max-width: 560px) {
  .report-shell {
    padding: 1.2rem;
  }
}
</style>
