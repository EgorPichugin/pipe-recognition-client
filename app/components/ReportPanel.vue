<template>
  <div class="report-panel">
    <div class="report-header">
      <div>
        <p class="eyebrow">Analysis Output</p>
        <h2>Recognition Report</h2>
      </div>
      <span class="status-pill" :class="{ active: report }">
        {{ reports.length ? `${reports.length} result${reports.length === 1 ? '' : 's'}` : 'Waiting' }}
      </span>
    </div>

    <div v-if="isLoading" class="loading-state">
      <div class="loading-copy">
        <h3>Analyzing image</h3>
        <p>Waiting for detection, OCR, and confidence evaluation.</p>
      </div>
      <div class="loading-track" aria-hidden="true">
        <span></span>
      </div>
    </div>

    <div v-else-if="reports.length" class="table-shell">
      <table>
        <thead>
          <tr>
            <th scope="col">Image name</th>
            <th scope="col">Category</th>
            <th scope="col">Latitude</th>
            <th scope="col">Longitude</th>
            <th scope="col">Confidence</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="result in reports" :key="result.id">
            <td>{{ result.image_name }}</td>
            <td>{{ result.category }}</td>
            <td>{{ formatCoordinate(result.latitude) }}</td>
            <td>{{ formatCoordinate(result.longitude ?? result.longtitude) }}</td>
            <td>{{ formatConfidence(result.confidence) }}</td>
          </tr>
        </tbody>
      </table>
    </div>

    <div v-else class="empty-state">
      <div class="empty-mark" aria-hidden="true"></div>
      <div>
        <h3>No report yet</h3>
        <p>Upload an image and press Start to generate recognition results.</p>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import type { RecognitionResult } from '~/components/recognition/ImageUploadPanel.vue'

const props = defineProps<{
  reports: RecognitionResult[]
  isLoading: boolean
}>()

const report = computed(() => props.reports.at(-1) ?? null)

const formatCoordinate = (value: number | string | undefined) => {
  const numericValue = Number(value)
  return Number.isFinite(numericValue) ? numericValue.toFixed(8) : 'Unavailable'
}

const formatConfidence = (value: number | string | undefined) => {
  const numericValue = Number(value)
  return Number.isFinite(numericValue)
    ? `${(numericValue * 100).toFixed(2)}%`
    : 'Unavailable'
}
</script>

<style scoped>
.report-panel {
  position: relative;
  display: grid;
  width: 100%;
  height: 100%;
  min-height: 0;
  padding: clamp(1.25rem, 3vw, 2rem);
  grid-template-rows: auto minmax(0, 1fr);
  gap: 1.75rem;
  overflow: hidden;
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 0.5rem;
  background:
    radial-gradient(circle at 82% 12%, rgba(141, 255, 232, 0.12), transparent 18rem),
    radial-gradient(circle at 18% 86%, rgba(255, 208, 113, 0.08), transparent 17rem),
    linear-gradient(135deg, rgba(255, 255, 255, 0.075), rgba(255, 255, 255, 0.018)),
    rgba(6, 10, 13, 0.66);
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.035),
    inset 0 0 100px rgba(119, 229, 216, 0.04),
    0 30px 90px rgba(0, 0, 0, 0.45);
  backdrop-filter: blur(18px);
}

.report-panel::before {
  position: absolute;
  inset: 0;
  pointer-events: none;
  content: "";
  background-image:
    linear-gradient(rgba(255, 255, 255, 0.04) 1px, transparent 1px),
    linear-gradient(90deg, rgba(255, 255, 255, 0.04) 1px, transparent 1px);
  background-size: 42px 42px;
  mask-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.72), transparent 78%);
}

.report-panel > * {
  position: relative;
  z-index: 1;
}

.report-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
}

.eyebrow {
  margin: 0 0 0.45rem;
  color: #70e8d8;
  font-size: 0.72rem;
  font-weight: 850;
  letter-spacing: 0.18em;
  text-transform: uppercase;
}

.report-header h2 {
  margin: 0;
  color: #f6fffd;
  font-size: clamp(1.65rem, 3vw, 2.6rem);
  font-weight: 900;
  line-height: 1;
}

.status-pill {
  flex: 0 0 auto;
  padding: 0.58rem 0.82rem;
  color: #87979e;
  font-size: 0.72rem;
  font-weight: 850;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  border: 1px solid rgba(255, 255, 255, 0.09);
  border-radius: 999px;
  background: rgba(255, 255, 255, 0.04);
}

.status-pill.active {
  color: #d9fff7;
  border-color: rgba(141, 255, 232, 0.28);
  background: rgba(141, 255, 232, 0.08);
}

.table-shell {
  overflow: auto;
  min-height: 0;
  border: 1px solid rgba(151, 255, 235, 0.16);
  border-radius: 0.5rem;
  background:
    linear-gradient(135deg, rgba(141, 255, 232, 0.06), rgba(255, 255, 255, 0.015)),
    rgba(2, 6, 8, 0.52);
  box-shadow: 0 22px 70px rgba(0, 0, 0, 0.34);
}

table {
  min-width: 44rem;
  width: 100%;
  border-collapse: collapse;
}

tr + tr {
  border-top: 1px solid rgba(255, 255, 255, 0.07);
}

tr:hover {
  background: rgba(141, 255, 232, 0.045);
}

th,
td {
  padding: 1.05rem 1.15rem;
  text-align: left;
}

th {
  color: #8ea0a8;
  font-size: 0.78rem;
  font-weight: 850;
  letter-spacing: 0.12em;
  text-transform: uppercase;
}

td {
  color: #f6fffd;
  font-size: 0.95rem;
  font-weight: 800;
  white-space: nowrap;
}

.empty-state {
  display: grid;
  min-height: 0;
  align-content: center;
  justify-items: center;
  gap: 1.15rem;
  padding: 2rem;
  color: #8ea0a8;
  text-align: center;
  border: 1px dashed rgba(151, 255, 235, 0.18);
  border-radius: 0.5rem;
  background:
    linear-gradient(135deg, rgba(255, 255, 255, 0.055), rgba(255, 255, 255, 0.015)),
    rgba(2, 6, 8, 0.32);
}

.loading-state {
  display: grid;
  min-height: 0;
  align-content: center;
  gap: 1.25rem;
  padding: 2rem;
  border: 1px solid rgba(141, 255, 232, 0.2);
  border-radius: 0.5rem;
  background:
    linear-gradient(135deg, rgba(141, 255, 232, 0.08), rgba(255, 208, 113, 0.035)),
    rgba(2, 6, 8, 0.36);
}

.loading-copy h3,
.loading-copy p {
  margin: 0;
}

.loading-copy h3 {
  color: #f6fffd;
  font-size: clamp(1.35rem, 2.4vw, 2rem);
  line-height: 1.1;
}

.loading-copy p {
  max-width: 30rem;
  margin-top: 0.55rem;
  color: #9eafb7;
  line-height: 1.6;
}

.loading-track {
  position: relative;
  height: 0.75rem;
  overflow: hidden;
  border: 1px solid rgba(141, 255, 232, 0.22);
  border-radius: 999px;
  background: rgba(255, 255, 255, 0.055);
  box-shadow: inset 0 0 18px rgba(0, 0, 0, 0.28);
}

.loading-track span {
  position: absolute;
  inset: 0 auto 0 0;
  width: 38%;
  border-radius: inherit;
  background: linear-gradient(90deg, #8dffe8, #ffd073, #ff6e80);
  box-shadow: 0 0 28px rgba(141, 255, 232, 0.28);
  animation: loading-slide 1.15s ease-in-out infinite;
}

@keyframes loading-slide {
  0% {
    transform: translateX(-105%);
  }

  100% {
    transform: translateX(285%);
  }
}

.empty-state h3,
.empty-state p {
  margin: 0;
}

.empty-state h3 {
  color: #f6fffd;
  font-size: 1.2rem;
  line-height: 1.2;
}

.empty-state p {
  max-width: 28rem;
  margin-top: 0.45rem;
  line-height: 1.6;
}

.empty-mark {
  position: relative;
  width: 5rem;
  height: 5rem;
  border: 1px solid rgba(141, 255, 232, 0.32);
  border-radius: 50%;
  background:
    linear-gradient(#8dffe8, #8dffe8) center / 1.8rem 0.16rem no-repeat,
    linear-gradient(#8dffe8, #8dffe8) center / 0.16rem 1.8rem no-repeat,
    rgba(141, 255, 232, 0.07);
  box-shadow:
    inset 0 0 30px rgba(141, 255, 232, 0.1),
    0 0 44px rgba(141, 255, 232, 0.08);
}

@media (max-width: 560px) {
  .report-header {
    align-items: flex-start;
    flex-direction: column;
  }
}
</style>
