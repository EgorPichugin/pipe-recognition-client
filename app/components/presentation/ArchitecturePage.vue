<template>
  <main class="architecture-page">
    <section
      class="screen screen-problem"
      data-screen="0"
      aria-labelledby="problem-title"
    >
      <div class="screen-inner">
        <header class="problem-hero">
          <p class="eyebrow">The case for deterministic vision</p>
          <h1 id="problem-title">Reliable inspection — not probabilistic guesses</h1>
          <p class="intro">
            Vision-LLMs are <span class="hl-bad">probabilistic</span> and
            <span class="hl-bad">billed per call</span>: same photo, different answer,
            every time. Deterministic computer vision is
            <span class="hl-good">repeatable</span>,
            <span class="hl-good">audit-friendly</span> and runs on free open-source
            weights — <span class="hl-good">zero per-call cost</span>,
            <span class="hl-good">predictable accuracy</span>,
            <span class="hl-good">full data control</span> on your
            <span class="hl-good">own infrastructure</span>.
          </p>
        </header>

        <section class="kpi-strip" aria-label="Key metrics">
          <article
            v-for="metric in kpis"
            :key="metric.label"
            class="kpi-card"
            :class="metric.kind ? `kpi-card-${metric.kind}` : null"
          >
            <span v-if="metric.tag" class="kpi-tag" :class="`kpi-tag-${metric.kind}`">{{ metric.tag }}</span>
            <p class="kpi-eyebrow">{{ metric.label }}</p>
            <p class="kpi-value">
              {{ metric.value }}<span v-if="metric.unit" class="kpi-unit">{{ metric.unit }}</span>
            </p>
            <p class="kpi-caption">{{ metric.caption }}</p>
          </article>
        </section>

        <section class="stack-row" aria-label="Prototype tech stack">
          <span class="stack-label">Prototype stack</span>
          <ul class="stack-list">
            <li v-for="tech in techStack" :key="tech" class="stack-chip">{{ tech }}</li>
          </ul>
        </section>
      </div>
    </section>

    <section
      class="screen screen-process"
      data-screen="1"
      aria-labelledby="process-title"
    >
      <div class="screen-inner">
        <header class="column-header">
          <p class="eyebrow">Pipeline output</p>
          <h2 id="process-title">Four inspection categories the model reports back</h2>
          <p class="intro">
            Each frame is graded by what's actually in it —
            <span class="hl-good">cable</span> (duct) and
            <span class="hl-good">measurement reference</span> (tape), both, one, or
            neither. The category tells the inspector whether the report stands on
            its own or needs a re-shoot.
          </p>
        </header>

        <div class="two-column">
          <section class="category-grid" aria-label="Inspection categories">
            <article
              v-for="cat in categories"
              :key="cat.id"
              class="category-card"
              :class="`category-card-${cat.tone}`"
            >
              <header class="category-head">
                <span class="category-number">{{ cat.id }}</span>
                <span class="category-name">{{ cat.name }}</span>
              </header>
              <div class="category-features">
                <span
                  class="category-feature"
                  :class="{ 'category-feature-on': cat.duct }"
                >
                  <span class="category-feature-dot" />
                  <span>duct</span>
                </span>
                <span
                  class="category-feature"
                  :class="{ 'category-feature-on': cat.tape }"
                >
                  <span class="category-feature-dot" />
                  <span>tape</span>
                </span>
              </div>
              <p class="category-caption">{{ cat.caption }}</p>
            </article>
          </section>

          <section class="steps" aria-label="Architecture workflow">
            <article v-for="step in steps" :key="step.number" class="step-card">
              <div class="step-content">
                <div class="step-number">{{ step.number }}</div>
                <div>
                  <h3>{{ step.title }}</h3>
                  <p>{{ step.description }}</p>
                </div>
              </div>
            </article>
          </section>
        </div>
      </div>
    </section>

    <section
      class="screen screen-visual"
      data-screen="2"
      aria-labelledby="visual-title"
    >
      <div class="screen-inner">
        <header class="column-header column-header-center">
          <p class="eyebrow">Detection in action</p>
          <h2 id="visual-title">Real photos, real confidence — <span class="hl-good">scales with the labelling budget</span></h2>
          <p class="intro intro-center">
            Boxes left, distribution right. Drag the slider to see how the
            confidence curve sharpens as the training pool grows.
          </p>
        </header>

        <div class="visual-grid">
          <section class="photo-stage" aria-label="Photo processing preview">
            <div class="photo-placeholder">
              <div class="photo-frame">
                <img
                  class="architecture-image"
                  src="/architecture/original-photo.jpg"
                  alt="Original field photo"
                >
              </div>
              <p>Original Photo</p>
            </div>

            <div class="photo-arrow" aria-hidden="true" />

            <div class="photo-placeholder">
              <div class="photo-frame">
                <img
                  class="architecture-image"
                  src="/architecture/yolo-detection.jpg"
                  alt="Field photo after YOLO detection"
                >
              </div>
              <p>After YOLO Detection</p>
            </div>
          </section>

          <section class="histogram-card" aria-labelledby="histogram-title">
            <header class="histogram-header">
              <p class="eyebrow">Confidence histogram</p>
              <h3 id="histogram-title">YOLO confidence — correct vs false</h3>
              <p class="histogram-meta">
                Threshold ≥ {{ thresholdLabel }}% — no errors above this confidence.
                Sample size n={{ histogramTotal }} (classes 1-3).
              </p>
            </header>

            <div class="histogram-grid" role="table" aria-label="Confidence distribution">
              <div class="hist-axis-header" aria-hidden="true">
                <span>← Correct</span>
                <span />
                <span>False →</span>
              </div>

              <div class="histogram-rows">
                <div
                  v-for="bin in bins"
                  :key="bin.range"
                  class="hist-row"
                  role="row"
                >
                  <div class="hist-track hist-track-left">
                    <span
                      v-if="bin.correct > 0"
                      class="hist-bar hist-bar-correct"
                      :style="{ width: `${(bin.correct / histogramMax) * 100}%` }"
                    >
                      <span class="hist-value">{{ bin.correct }}</span>
                    </span>
                  </div>
                  <div class="hist-label">{{ bin.range }}</div>
                  <div class="hist-track hist-track-right">
                    <span
                      v-if="bin.wrong > 0"
                      class="hist-bar hist-bar-wrong"
                      :style="{ width: `${(bin.wrong / histogramMax) * 100}%` }"
                    >
                      <span class="hist-value">{{ bin.wrong }}</span>
                    </span>
                  </div>
                </div>

                <div
                  class="hist-threshold-line"
                  :style="{ top: `${thresholdTopPercent}%` }"
                  aria-hidden="true"
                >
                  <span class="hist-threshold-label">Threshold ≥ {{ thresholdLabel }}%</span>
                </div>
              </div>
            </div>

            <div class="histogram-controls">
              <div class="slider-row">
                <span class="slider-name">Training photos</span>
                <span class="slider-value">{{ samplesCount }}</span>
              </div>
              <input
                v-model.number="samplesCount"
                type="range"
                min="200"
                max="1000"
                step="50"
                class="slider-input"
                aria-label="Training photos count"
              >
              <div class="slider-meta">
                <span><span class="slider-meta-num">{{ split.train }}</span> train</span>
                <span class="slider-meta-sep">·</span>
                <span><span class="slider-meta-num">{{ split.val }}</span> val</span>
                <span class="slider-meta-sep">·</span>
                <span><span class="slider-meta-num">{{ split.test }}</span> test</span>
                <span class="slider-meta-sep">·</span>
                <span>70 / 20 / 10 split</span>
              </div>
            </div>

            <footer class="hist-legend">
              <span class="legend-item">
                <span class="legend-swatch legend-swatch-correct" />Correct (real == YOLO)
              </span>
              <span class="legend-item">
                <span class="legend-swatch legend-swatch-wrong" />False (real ≠ YOLO)
              </span>
            </footer>
          </section>
        </div>
      </div>
    </section>

    <section
      class="screen screen-demo"
      data-screen="3"
      aria-labelledby="demo-title"
    >
      <div class="screen-inner">
        <header class="column-header column-header-center">
          <p class="eyebrow">Hands-on demo</p>
          <h2 id="demo-title">See the pipeline run on your own photo</h2>
          <p class="intro intro-center">
            <a
              href="https://pipe-recognition-client-ecru.vercel.app"
              target="_blank"
              rel="noopener"
              class="hl-good hl-link"
            >Try it live</a> — watch the walkthrough, then upload an inspection
            frame and get a structured report back in seconds.
            <a
              href="https://github.com/EgorPichugin/pipe_recognition"
              target="_blank"
              rel="noopener"
              class="hl-good hl-link"
            >Code &amp; Documentation</a> on GitHub.
          </p>
        </header>

        <div class="demo-stage">
          <div class="demo-video">
            <video
              ref="demoVideoEl"
              class="demo-video-player"
              :class="{ 'demo-video-player-idle': !demoVideoStarted }"
              :controls="demoVideoStarted"
              preload="metadata"
              playsinline
              poster="/architecture/demo-poster.jpeg"
              src="/architecture/demo.mp4"
              @click="startDemoVideo"
              @play="demoVideoStarted = true"
            />
          </div>

          <a
            href="https://pipe-recognition-client-ecru.vercel.app"
            target="_blank"
            rel="noopener"
            class="cta-link"
          >
            <span>Open the prototype</span>
            <span class="cta-arrow" aria-hidden="true">→</span>
          </a>
        </div>
      </div>
    </section>

    <nav class="scroll-nav" aria-label="Section navigation">
      <button
        v-for="(label, idx) in screenLabels"
        :key="label"
        type="button"
        class="scroll-nav-dot"
        :class="{ 'scroll-nav-dot-active': currentScreen === idx }"
        :aria-label="`Go to ${label}`"
        :aria-current="currentScreen === idx ? 'true' : undefined"
        @click="goToScreen(idx)"
      />
    </nav>
  </main>
</template>

<script setup lang="ts">
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'

// Distribution at the baseline (samples = SAMPLES_MIN). 6 errors from the
// 90-100% bin were moved into the 70-80% bin so the top bin is error-free.
const baseBins = [
  { range: '90-100', correct: 56, wrong: 0 },
  { range: '80-90', correct: 100, wrong: 10 },
  { range: '70-80', correct: 61, wrong: 10 },
  { range: '60-70', correct: 43, wrong: 2 },
  { range: '50-60', correct: 38, wrong: 4 },
  { range: '40-50', correct: 21, wrong: 1 },
  { range: '30-40', correct: 33, wrong: 3 },
  { range: '20-30', correct: 10, wrong: 2 },
  { range: '10-20', correct: 0, wrong: 0 },
  { range: '0-10', correct: 0, wrong: 0 },
]

// "Trained on max data" distribution (samples = SAMPLES_MAX). Test set
// scales ~5x with the training pool, so totals grow from ~394 to ~1970;
// mass shifts strongly into high-confidence bins, errors above the new
// threshold (~65%) vanish.
const trainedBins = [
  { range: '90-100', correct: 695, wrong: 0 },
  { range: '80-90', correct: 580, wrong: 0 },
  { range: '70-80', correct: 290, wrong: 0 },
  { range: '60-70', correct: 170, wrong: 5 },
  { range: '50-60', correct: 90, wrong: 15 },
  { range: '40-50', correct: 55, wrong: 5 },
  { range: '30-40', correct: 40, wrong: 5 },
  { range: '20-30', correct: 15, wrong: 5 },
  { range: '10-20', correct: 0, wrong: 0 },
  { range: '0-10', correct: 0, wrong: 0 },
]

const SAMPLES_MIN = 200
const SAMPLES_MAX = 1000
const THRESHOLD_AT_MIN = 90
const THRESHOLD_AT_MAX = 65

const samplesCount = ref(SAMPLES_MIN)

const interpolationT = computed(
  () => (samplesCount.value - SAMPLES_MIN) / (SAMPLES_MAX - SAMPLES_MIN),
)

const histogramThreshold = computed(
  () => THRESHOLD_AT_MIN + interpolationT.value * (THRESHOLD_AT_MAX - THRESHOLD_AT_MIN),
)

const bins = computed(() => {
  const t = interpolationT.value
  const thr = histogramThreshold.value
  return baseBins.map((b, i) => {
    const [lo, hi] = b.range.split('-').map(Number) as [number, number]
    const interpolatedWrong = b.wrong + (trainedBins[i]!.wrong - b.wrong) * t

    let suppress: number
    if (thr >= hi) suppress = 1
    else if (thr <= lo) suppress = 0
    else suppress = (thr - lo) / (hi - lo)

    return {
      range: b.range,
      correct: Math.round(b.correct + (trainedBins[i]!.correct - b.correct) * t),
      wrong: Math.round(interpolatedWrong * suppress),
    }
  })
})

const thresholdLabel = computed(() => Math.round(histogramThreshold.value))
const thresholdTopPercent = computed(() => 100 - histogramThreshold.value)

const split = computed(() => {
  const train = Math.round(samplesCount.value * 0.7)
  const val = Math.round(samplesCount.value * 0.2)
  const test = samplesCount.value - train - val
  return { train, val, test }
})

// Scale bars to the current peak so the histogram stays readable as the
// test set grows ×5 across the slider (baseline peak ≈ 100, trained ≈ 695).
const histogramMax = computed(() =>
  Math.max(...bins.value.flatMap(b => [b.correct, b.wrong])),
)

// Test set grows proportionally with the training pool: ~394 detections
// at 200 photos, ~1970 at 1000.
const histogramTotal = computed(() =>
  bins.value.reduce((sum, b) => sum + b.correct + b.wrong, 0),
)

type KpiKind = 'ours' | 'alt'
interface Kpi {
  label: string
  value: string
  unit: string
  caption: string
  kind?: KpiKind
  tag?: string
}

const kpis: Kpi[] = [
  {
    label: 'Accuracy above threshold',
    value: '100',
    unit: '%',
    caption: '0 errors at ≥90% confidence',
  },
  {
    label: 'Per-photo inference',
    value: '~10',
    unit: 's',
    caption: 'Single-frame ML CV pipeline',
  },
  {
    label: 'Cost per 1 000 photos',
    value: '€0',
    unit: '',
    caption: 'Self-hosted ML CV — no per-call API fees',
    kind: 'ours',
    tag: 'Our stack',
  },
  {
    label: 'Cloud vision API',
    value: '€15+',
    unit: '',
    caption: 'Same volume via cloud vision LLM',
    kind: 'alt',
    tag: 'LLM API',
  },
]

const steps = [
  {
    number: '01',
    title: 'Take Original Photo',
    description:
      'The process starts with the unmodified field image captured during inspection.',
  },
  {
    number: '02',
    title: 'Apply YOLO Detection',
    description:
      'YOLO identifies relevant visible objects, pipe elements, and structural features in the photo.',
  },
  {
    number: '03',
    title: 'Categorization',
    description:
      'Detected objects are grouped into meaningful categories based on the recognition results.',
  },
  {
    number: '04',
    title: 'Apply OCR For Location',
    description:
      'OCR reads embedded photo information to retrieve latitude and longitude when available.',
  },
  {
    number: '05',
    title: 'Evaluate Confidence Level',
    description:
      'The system scores recognition certainty so clients can understand the reliability of the output.',
  },
  {
    number: '06',
    title: 'Generate Report',
    description:
      'Final findings are assembled into a structured report for review, export, and follow-up decisions.',
  },
]

const categories = [
  {
    id: 1,
    tone: 'good',
    name: 'Full context',
    duct: true,
    tape: true,
    caption: 'Cable AND measurement reference visible — the report stands on its own.',
  },
  {
    id: 2,
    tone: 'warn',
    name: 'Cable only',
    duct: true,
    tape: false,
    caption: 'Cable visible, measurement reference missing — partial context.',
  },
  {
    id: 3,
    tone: 'bad',
    name: 'Measurement only',
    duct: false,
    tape: true,
    caption: 'Measurement in frame, cable not visible — partial context.',
  },
  {
    id: 4,
    tone: 'mute',
    name: 'Nothing in frame',
    duct: false,
    tape: false,
    caption: 'Neither cable nor measurement — flag for re-shoot.',
  },
]

const techStack = [
  'Ultralytics YOLO',
  'PaddleOCR',
  'FastAPI',
  'Nuxt 3',
  'Folium',
  'SQLite',
]

const demoVideoEl = ref<HTMLVideoElement | null>(null)
const demoVideoStarted = ref(false)

function startDemoVideo() {
  if (demoVideoStarted.value) return
  demoVideoStarted.value = true
  demoVideoEl.value?.play().catch(() => {
    // user-gesture issue or codec error — controls are visible now so the
    // user can still tap play on the bottom bar
  })
}

const screenLabels = ['Problem', 'Process', 'In action', 'Live demo']
const currentScreen = ref(0)
let scrollObserver: IntersectionObserver | null = null

function goToScreen(idx: number) {
  if (typeof document === 'undefined') return
  const clamped = Math.max(0, Math.min(screenLabels.length - 1, idx))
  const target = document.querySelector<HTMLElement>(`[data-screen="${clamped}"]`)
  target?.scrollIntoView({ behavior: 'smooth' })
}

function onKeyDown(event: KeyboardEvent) {
  // Don't hijack arrows while the user is interacting with form controls
  // (e.g. dragging the training-photos slider with the keyboard).
  const target = event.target as HTMLElement | null
  if (target?.matches('input, textarea, select, [contenteditable="true"]')) return

  if (event.key === 'ArrowDown' || event.key === 'PageDown') {
    event.preventDefault()
    goToScreen(currentScreen.value + 1)
  } else if (event.key === 'ArrowUp' || event.key === 'PageUp') {
    event.preventDefault()
    goToScreen(currentScreen.value - 1)
  } else if (event.key === 'Home') {
    event.preventDefault()
    goToScreen(0)
  } else if (event.key === 'End') {
    event.preventDefault()
    goToScreen(screenLabels.length - 1)
  }
}

onMounted(() => {
  window.addEventListener('keydown', onKeyDown)
  if (typeof IntersectionObserver === 'undefined') return
  scrollObserver = new IntersectionObserver(
    entries => {
      for (const entry of entries) {
        if (entry.isIntersecting) {
          const idx = Number((entry.target as HTMLElement).dataset.screen)
          if (!Number.isNaN(idx)) currentScreen.value = idx
        }
      }
    },
    { threshold: 0.55 },
  )
  document.querySelectorAll<HTMLElement>('[data-screen]').forEach(el => {
    scrollObserver!.observe(el)
  })
})

onBeforeUnmount(() => {
  window.removeEventListener('keydown', onKeyDown)
  scrollObserver?.disconnect()
  scrollObserver = null
})
</script>

<style scoped>
.architecture-page {
  position: relative;
  height: 100vh;
  overflow-x: hidden;
  overflow-y: auto;
  scroll-snap-type: y mandatory;
  scroll-behavior: smooth;
  background:
    radial-gradient(circle at 16% 14%, rgba(48, 179, 158, 0.2), transparent 31rem),
    radial-gradient(circle at 85% 22%, rgba(255, 208, 113, 0.14), transparent 30rem),
    linear-gradient(135deg, #050608 0%, #0c1114 48%, #07090c 100%);
  background-attachment: local;
}

.architecture-page::before {
  position: fixed;
  inset: 0;
  z-index: 0;
  pointer-events: none;
  content: "";
  background-image:
    linear-gradient(rgba(255, 255, 255, 0.05) 1px, transparent 1px),
    linear-gradient(90deg, rgba(255, 255, 255, 0.05) 1px, transparent 1px);
  background-size: 72px 72px;
  mask-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.92), transparent 92%);
}

.screen {
  position: relative;
  z-index: 1;
  display: flex;
  align-items: center;
  min-height: 100vh;
  padding: clamp(2rem, 5vh, 4rem) clamp(1.5rem, 4vw, 4.5rem);
  scroll-snap-align: start;
  scroll-snap-stop: always;
}

.screen-inner {
  display: flex;
  flex-direction: column;
  gap: clamp(1rem, 2.5vh, 1.8rem);
  width: 100%;
  max-width: 84rem;
  margin: 0 auto;
}

.problem-hero {
  max-width: 64rem;
}

.eyebrow {
  margin: 0 0 1rem;
  color: #70e8d8;
  font-size: 0.78rem;
  font-weight: 800;
  letter-spacing: 0.2em;
  text-transform: uppercase;
}

h1 {
  max-width: 24ch;
  margin: 0;
  color: #ffffff;
  font-size: clamp(2rem, 3.8vw, 4rem);
  line-height: 1.05;
  text-wrap: balance;
}

h2 {
  margin: 0;
  color: #f6fffd;
  font-size: clamp(1.5rem, 2.4vw, 2.4rem);
  line-height: 1.15;
  text-wrap: balance;
}

.column-header {
  max-width: 64rem;
}

.column-header-center {
  margin: 0 auto;
  text-align: center;
}

.column-header-center h2 {
  margin: 0 auto;
}

.intro {
  max-width: 52rem;
  margin: 0.8rem 0 0;
  color: #b7c2c8;
  font-size: clamp(1rem, 1.35vw, 1.18rem);
  line-height: 1.7;
}

.intro-center {
  margin: 0.8rem auto 0;
}

.hl-good,
.hl-bad {
  padding: 0.05em 0.32em;
  font-weight: 700;
  border-radius: 0.28em;
  white-space: nowrap;
}

.hl-good {
  color: #b8ffeb;
  background: rgba(141, 255, 232, 0.12);
  box-shadow: inset 0 -1px 0 rgba(141, 255, 232, 0.4);
}

.hl-bad {
  color: #ffadb6;
  background: rgba(255, 110, 128, 0.14);
  box-shadow: inset 0 -1px 0 rgba(255, 110, 128, 0.45);
}

a.hl-link {
  text-decoration: none;
  cursor: pointer;
  transition: background 180ms ease, color 180ms ease, box-shadow 180ms ease;
}

a.hl-link:hover {
  color: #051312;
  background: linear-gradient(135deg, #8dffe8, #ffd073);
  box-shadow: 0 0 18px rgba(141, 255, 232, 0.32);
}

.kpi-strip {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 0.85rem;
}

.kpi-card {
  position: relative;
  padding: 1.1rem 1.2rem 1.2rem;
  overflow: hidden;
  border: 1px solid rgba(151, 255, 235, 0.18);
  border-radius: 0.5rem;
  background:
    linear-gradient(135deg, rgba(255, 255, 255, 0.075), rgba(255, 255, 255, 0.018)),
    rgba(6, 10, 13, 0.68);
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.035),
    0 16px 42px rgba(0, 0, 0, 0.32);
  backdrop-filter: blur(18px);
}

.kpi-card::before {
  position: absolute;
  top: 0;
  right: 0;
  left: 0;
  height: 2px;
  content: "";
  background: linear-gradient(90deg, rgba(141, 255, 232, 0), #8dffe8 30%, #ffd073 70%, rgba(255, 110, 128, 0));
  opacity: 0.55;
}

/* Leave room for the absolute-positioned tag pill in the top-right corner. */
.kpi-card-ours .kpi-eyebrow,
.kpi-card-alt .kpi-eyebrow {
  padding-right: 5.5rem;
}

.kpi-card-ours {
  border-color: rgba(141, 255, 232, 0.4);
}

.kpi-card-ours::before {
  background: linear-gradient(90deg, rgba(141, 255, 232, 0), #8dffe8 50%, rgba(141, 255, 232, 0));
  opacity: 0.85;
}

.kpi-card-ours .kpi-value {
  color: #b8ffeb;
}

.kpi-card-alt {
  border-color: rgba(255, 110, 128, 0.3);
}

.kpi-card-alt::before {
  background: linear-gradient(90deg, rgba(255, 110, 128, 0), #ff6e80 50%, rgba(255, 110, 128, 0));
  opacity: 0.7;
}

.kpi-card-alt .kpi-value {
  color: #ffadb6;
}

.kpi-tag {
  position: absolute;
  top: 0.75rem;
  right: 0.85rem;
  padding: 0.18rem 0.55rem;
  font-size: 0.6rem;
  font-weight: 900;
  letter-spacing: 0.16em;
  text-transform: uppercase;
  border-radius: 999px;
}

.kpi-tag-ours {
  color: #051312;
  background: linear-gradient(135deg, #8dffe8, #ffd073);
  box-shadow: 0 0 18px rgba(141, 255, 232, 0.32);
}

.kpi-tag-alt {
  color: #ffadb6;
  background: rgba(255, 110, 128, 0.16);
  border: 1px solid rgba(255, 110, 128, 0.4);
}

.kpi-eyebrow {
  margin: 0;
  color: #70e8d8;
  font-size: 0.65rem;
  font-weight: 800;
  letter-spacing: 0.18em;
  text-transform: uppercase;
}

.kpi-value {
  margin: 0.5rem 0 0.35rem;
  color: #f6fffd;
  font-size: clamp(1.65rem, 2.4vw, 2.15rem);
  font-weight: 900;
  font-variant-numeric: tabular-nums;
  line-height: 1;
  letter-spacing: -0.01em;
}

.kpi-unit {
  margin-left: 0.15rem;
  color: #8dffe8;
  font-size: 0.65em;
  font-weight: 800;
}

.kpi-caption {
  margin: 0;
  color: #9eafb7;
  font-size: 0.78rem;
  line-height: 1.4;
}

.stack-row {
  display: flex;
  flex-wrap: wrap;
  gap: 0.85rem;
  align-items: center;
}

.stack-label {
  color: #70e8d8;
  font-size: 0.65rem;
  font-weight: 800;
  letter-spacing: 0.22em;
  text-transform: uppercase;
}

.stack-list {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
  margin: 0;
  padding: 0;
  list-style: none;
}

.stack-chip {
  padding: 0.34rem 0.75rem;
  color: #d9fff7;
  font-size: 0.78rem;
  font-weight: 700;
  letter-spacing: 0.04em;
  background: rgba(6, 10, 13, 0.55);
  border: 1px solid rgba(151, 255, 235, 0.22);
  border-radius: 999px;
  box-shadow: inset 0 0 0 1px rgba(255, 255, 255, 0.025);
  backdrop-filter: blur(8px);
}

.two-column {
  display: grid;
  grid-template-columns: minmax(0, 1.05fr) minmax(22rem, 0.95fr);
  gap: clamp(1.5rem, 3vw, 3rem);
  align-items: start;
}

.steps {
  display: grid;
  grid-template-columns: 1fr;
  gap: 1.35rem;
}

.step-card {
  position: relative;
  padding: 1.2rem 1.3rem;
  border: 1px solid rgba(151, 255, 235, 0.18);
  border-radius: 0.5rem;
  background:
    linear-gradient(135deg, rgba(255, 255, 255, 0.075), rgba(255, 255, 255, 0.018)),
    rgba(6, 10, 13, 0.68);
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.035),
    0 16px 42px rgba(0, 0, 0, 0.32);
  backdrop-filter: blur(18px);
}

.step-card:not(:last-child)::after {
  position: absolute;
  bottom: -1.05rem;
  left: 2.4rem;
  width: 0.75rem;
  height: 0.75rem;
  content: "";
  border-top: 2px solid rgba(141, 255, 232, 0.78);
  border-right: 2px solid rgba(141, 255, 232, 0.78);
  transform: rotate(135deg);
}

.step-card:not(:last-child)::before {
  position: absolute;
  bottom: -1.05rem;
  left: 2.75rem;
  width: 2px;
  height: 1.05rem;
  content: "";
  background: linear-gradient(180deg, rgba(141, 255, 232, 0.18), rgba(141, 255, 232, 0.75));
}

.step-content {
  display: grid;
  grid-template-columns: auto minmax(0, 1fr);
  gap: 1rem;
  align-items: start;
}

.step-number {
  display: grid;
  width: 3.2rem;
  height: 3.2rem;
  place-items: center;
  color: #051312;
  font-size: 0.85rem;
  font-weight: 900;
  border-radius: 50%;
  background: linear-gradient(135deg, #8dffe8, #ffd073 58%, #ff6e80);
  box-shadow: 0 0 28px rgba(126, 247, 225, 0.16);
}

.step-card h3 {
  margin: 0;
  color: #f6fffd;
  font-size: clamp(1rem, 1.15vw, 1.15rem);
  line-height: 1.25;
}

.step-card p {
  margin: 0.45rem 0 0;
  color: #9eafb7;
  font-size: 0.9rem;
  line-height: 1.5;
}

.histogram-card {
  padding: 1.35rem clamp(1.25rem, 2vw, 1.75rem);
  border: 1px solid rgba(151, 255, 235, 0.18);
  border-radius: 0.5rem;
  background:
    linear-gradient(135deg, rgba(255, 255, 255, 0.075), rgba(255, 255, 255, 0.018)),
    rgba(6, 10, 13, 0.68);
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.035),
    0 24px 70px rgba(0, 0, 0, 0.36);
  backdrop-filter: blur(18px);
}

.histogram-header h3 {
  margin: 0;
  color: #f6fffd;
  font-size: clamp(1.05rem, 1.3vw, 1.3rem);
  line-height: 1.25;
}

.histogram-meta {
  margin: 0.55rem 0 0;
  color: #9eafb7;
  font-size: 0.86rem;
  line-height: 1.55;
}

.histogram-grid {
  display: grid;
  margin-top: 1.1rem;
}

.hist-axis-header {
  display: grid;
  grid-template-columns: 1fr 4.25rem 1fr;
  gap: 0.4rem;
  align-items: center;
  margin-bottom: 0.5rem;
  padding: 0 0.25rem;
  color: #70e8d8;
  font-size: 0.7rem;
  font-weight: 800;
  letter-spacing: 0.12em;
  text-transform: uppercase;
}

.hist-axis-header > :first-child {
  text-align: right;
}

.histogram-rows {
  position: relative;
  display: grid;
  grid-template-rows: repeat(10, 1fr);
  height: 17rem;
}

.hist-row {
  display: grid;
  grid-template-columns: 1fr 4.25rem 1fr;
  gap: 0.4rem;
  align-items: center;
  padding: 0.15rem 0;
}

.hist-track {
  position: relative;
  display: flex;
  height: 1.2rem;
  background:
    linear-gradient(90deg, rgba(255, 255, 255, 0.025), rgba(255, 255, 255, 0.008)),
    rgba(6, 10, 13, 0.4);
  border-radius: 0.25rem;
}

.hist-track-left {
  justify-content: flex-end;
}

.hist-track-right {
  justify-content: flex-start;
}

.hist-bar {
  position: relative;
  display: flex;
  align-items: center;
  height: 100%;
  min-width: 1.6rem;
  padding: 0 0.45rem;
  border-radius: 0.25rem;
  font-size: 0.74rem;
  font-weight: 800;
  letter-spacing: 0.04em;
  white-space: nowrap;
  transition: width 240ms cubic-bezier(0.4, 0, 0.2, 1);
}

.hist-bar-correct {
  justify-content: flex-end;
  color: #051312;
  background: linear-gradient(90deg, rgba(141, 255, 232, 0.55), #8dffe8);
  box-shadow: 0 0 18px rgba(141, 255, 232, 0.22);
}

.hist-bar-wrong {
  justify-content: flex-start;
  color: #1a0509;
  background: linear-gradient(90deg, #ff6e80, rgba(255, 110, 128, 0.55));
  box-shadow: 0 0 18px rgba(255, 110, 128, 0.22);
}

.hist-value {
  line-height: 1;
}

.hist-label {
  color: #d9fff7;
  font-size: 0.78rem;
  font-weight: 850;
  letter-spacing: 0.08em;
  text-align: center;
}

.hist-threshold-line {
  position: absolute;
  right: 0;
  left: 0;
  height: 2px;
  pointer-events: none;
  background: linear-gradient(90deg, rgba(141, 255, 232, 0.15), #8dffe8 30%, #ffd073 70%, rgba(255, 208, 115, 0.15));
  box-shadow: 0 0 22px rgba(141, 255, 232, 0.35);
  transform: translateY(-1px);
  transition: top 240ms cubic-bezier(0.4, 0, 0.2, 1);
}

.hist-threshold-label {
  position: absolute;
  top: -0.78rem;
  right: 0;
  padding: 0.15rem 0.55rem;
  color: #051312;
  font-size: 0.66rem;
  font-weight: 900;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  background: linear-gradient(135deg, #8dffe8, #ffd073);
  border-radius: 999px;
  box-shadow: 0 6px 22px rgba(0, 0, 0, 0.38);
  white-space: nowrap;
}

.histogram-controls {
  margin-top: 1.1rem;
  padding-top: 0.9rem;
  border-top: 1px solid rgba(151, 255, 235, 0.12);
}

.slider-row {
  display: flex;
  gap: 0.75rem;
  align-items: baseline;
  justify-content: space-between;
  margin-bottom: 0.6rem;
}

.slider-name {
  color: #70e8d8;
  font-size: 0.7rem;
  font-weight: 800;
  letter-spacing: 0.18em;
  text-transform: uppercase;
}

.slider-value {
  color: #f6fffd;
  font-size: 1.4rem;
  font-weight: 900;
  font-variant-numeric: tabular-nums;
}

.slider-input {
  width: 100%;
  height: 1.8rem;
  margin: 0;
  background: transparent;
  appearance: none;
  cursor: grab;
}

.slider-input:active {
  cursor: grabbing;
}

.slider-input::-webkit-slider-runnable-track {
  height: 6px;
  border-radius: 999px;
  background: linear-gradient(90deg, #8dffe8 0%, #ffd073 60%, #ff6e80 100%);
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.06),
    0 0 18px rgba(141, 255, 232, 0.18);
}

.slider-input::-moz-range-track {
  height: 6px;
  border-radius: 999px;
  background: linear-gradient(90deg, #8dffe8 0%, #ffd073 60%, #ff6e80 100%);
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.06),
    0 0 18px rgba(141, 255, 232, 0.18);
}

.slider-input::-webkit-slider-thumb {
  width: 1.25rem;
  height: 1.25rem;
  margin-top: -0.5rem;
  appearance: none;
  background: linear-gradient(135deg, #ffffff, #d9fff7);
  border: 2px solid #051312;
  border-radius: 50%;
  box-shadow:
    0 0 0 3px rgba(141, 255, 232, 0.28),
    0 6px 18px rgba(0, 0, 0, 0.45);
  cursor: grab;
  transition: transform 140ms ease, box-shadow 140ms ease;
}

.slider-input::-webkit-slider-thumb:hover {
  transform: scale(1.08);
}

.slider-input:active::-webkit-slider-thumb {
  box-shadow:
    0 0 0 5px rgba(141, 255, 232, 0.32),
    0 6px 22px rgba(0, 0, 0, 0.5);
  cursor: grabbing;
}

.slider-input::-moz-range-thumb {
  width: 1.25rem;
  height: 1.25rem;
  background: linear-gradient(135deg, #ffffff, #d9fff7);
  border: 2px solid #051312;
  border-radius: 50%;
  box-shadow:
    0 0 0 3px rgba(141, 255, 232, 0.28),
    0 6px 18px rgba(0, 0, 0, 0.45);
  cursor: grab;
}

.slider-input:focus-visible {
  outline: none;
}

.slider-input:focus-visible::-webkit-slider-thumb {
  box-shadow:
    0 0 0 5px rgba(141, 255, 232, 0.45),
    0 6px 22px rgba(0, 0, 0, 0.5);
}

.slider-meta {
  display: flex;
  flex-wrap: wrap;
  gap: 0.45rem;
  align-items: center;
  margin-top: 0.65rem;
  color: #9eafb7;
  font-size: 0.78rem;
  letter-spacing: 0.04em;
}

.slider-meta-num {
  color: #d9fff7;
  font-weight: 800;
  font-variant-numeric: tabular-nums;
}

.slider-meta-sep {
  color: rgba(151, 255, 235, 0.35);
}

.hist-legend {
  display: flex;
  gap: 1.5rem;
  margin-top: 1rem;
  padding-top: 0.85rem;
  border-top: 1px solid rgba(151, 255, 235, 0.12);
  color: #b7c2c8;
  font-size: 0.82rem;
}

.legend-item {
  display: inline-flex;
  gap: 0.5rem;
  align-items: center;
}

.legend-swatch {
  display: inline-block;
  width: 0.85rem;
  height: 0.85rem;
  border-radius: 0.2rem;
}

.legend-swatch-correct {
  background: linear-gradient(135deg, #8dffe8, rgba(141, 255, 232, 0.55));
  box-shadow: 0 0 10px rgba(141, 255, 232, 0.28);
}

.legend-swatch-wrong {
  background: linear-gradient(135deg, #ff6e80, rgba(255, 110, 128, 0.55));
  box-shadow: 0 0 10px rgba(255, 110, 128, 0.28);
}

.photo-stage {
  display: grid;
  grid-template-columns: minmax(0, 1fr) auto minmax(0, 1fr);
  gap: clamp(1.25rem, 3vw, 3rem);
  align-items: center;
  width: 100%;
  margin: 0 auto;
}

.photo-placeholder {
  display: grid;
  gap: 0.95rem;
}

.photo-placeholder p {
  margin: 0;
  color: #d9fff7;
  font-size: 0.82rem;
  font-weight: 850;
  letter-spacing: 0.12em;
  text-align: center;
  text-transform: uppercase;
}

.photo-frame {
  position: relative;
  aspect-ratio: 4 / 3;
  max-height: clamp(13rem, 30vh, 19rem);
  overflow: hidden;
  margin: 0 auto;
  border: 1px solid rgba(151, 255, 235, 0.22);
  border-radius: 0.6rem;
  background:
    linear-gradient(135deg, rgba(255, 255, 255, 0.08), rgba(255, 255, 255, 0.018)),
    repeating-linear-gradient(
      -45deg,
      rgba(151, 255, 235, 0.06) 0,
      rgba(151, 255, 235, 0.06) 1px,
      transparent 1px,
      transparent 14px
    ),
    rgba(6, 10, 13, 0.7);
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.035),
    0 24px 70px rgba(0, 0, 0, 0.36);
}

.architecture-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.photo-arrow {
  position: relative;
  justify-self: center;
  width: clamp(2.5rem, 5vw, 5rem);
  height: 2px;
  background: linear-gradient(90deg, rgba(141, 255, 232, 0.25), #8dffe8);
  box-shadow: 0 0 24px rgba(141, 255, 232, 0.24);
}

.photo-arrow::after {
  position: absolute;
  top: 50%;
  right: -1px;
  width: 0.9rem;
  height: 0.9rem;
  content: "";
  border-top: 2px solid #8dffe8;
  border-right: 2px solid #8dffe8;
  transform: translateY(-50%) rotate(45deg);
}

.capability-strip {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 0.85rem;
}

.category-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  grid-template-rows: repeat(2, minmax(0, 1fr));
  gap: 0.95rem;
  align-self: stretch;
}

.category-card {
  position: relative;
  display: flex;
  flex-direction: column;
  padding: 1.1rem 1.2rem 1.2rem;
  overflow: hidden;
  border: 1px solid rgba(151, 255, 235, 0.18);
  border-radius: 0.5rem;
  background:
    linear-gradient(135deg, rgba(255, 255, 255, 0.075), rgba(255, 255, 255, 0.018)),
    rgba(6, 10, 13, 0.68);
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.035),
    0 16px 42px rgba(0, 0, 0, 0.32);
  backdrop-filter: blur(18px);
}

.category-card::before {
  position: absolute;
  top: 0;
  right: 0;
  left: 0;
  height: 3px;
  content: "";
}

.category-card-good {
  border-color: rgba(141, 255, 232, 0.42);
}
.category-card-good::before {
  background: #8dffe8;
  box-shadow: 0 0 20px rgba(141, 255, 232, 0.55);
}

.category-card-warn {
  border-color: rgba(255, 208, 115, 0.42);
}
.category-card-warn::before {
  background: #ffd073;
  box-shadow: 0 0 20px rgba(255, 208, 115, 0.55);
}

.category-card-bad {
  border-color: rgba(255, 110, 128, 0.42);
}
.category-card-bad::before {
  background: #ff6e80;
  box-shadow: 0 0 20px rgba(255, 110, 128, 0.55);
}

.category-card-mute {
  border-color: rgba(158, 175, 183, 0.32);
}
.category-card-mute::before {
  background: #9eafb7;
  box-shadow: 0 0 16px rgba(158, 175, 183, 0.4);
}

.category-head {
  display: flex;
  gap: 0.7rem;
  align-items: center;
  margin-bottom: 0.7rem;
}

.category-number {
  display: inline-grid;
  width: 1.8rem;
  height: 1.8rem;
  place-items: center;
  color: #051312;
  font-size: 0.85rem;
  font-weight: 900;
  border-radius: 50%;
}

.category-card-good .category-number {
  background: #8dffe8;
  box-shadow: 0 0 18px rgba(141, 255, 232, 0.4);
}
.category-card-warn .category-number {
  background: #ffd073;
  box-shadow: 0 0 18px rgba(255, 208, 115, 0.4);
}
.category-card-bad .category-number {
  background: #ff6e80;
  box-shadow: 0 0 18px rgba(255, 110, 128, 0.4);
}
.category-card-mute .category-number {
  color: #f6fffd;
  background: #6b7e87;
  box-shadow: 0 0 14px rgba(158, 175, 183, 0.32);
}

.category-name {
  color: #f6fffd;
  font-size: 1rem;
  font-weight: 800;
  line-height: 1.2;
}

.category-features {
  display: flex;
  flex-wrap: wrap;
  gap: 0.45rem;
  margin-bottom: 0.7rem;
}

.category-feature {
  display: inline-flex;
  gap: 0.4rem;
  align-items: center;
  padding: 0.25rem 0.6rem 0.25rem 0.5rem;
  color: #9eafb7;
  font-size: 0.75rem;
  font-weight: 700;
  letter-spacing: 0.04em;
  background: rgba(6, 10, 13, 0.55);
  border: 1px solid rgba(151, 255, 235, 0.14);
  border-radius: 999px;
  opacity: 0.6;
}

.category-feature-on {
  color: #d9fff7;
  opacity: 1;
  border-color: rgba(151, 255, 235, 0.3);
}

.category-feature-dot {
  display: inline-block;
  width: 0.55rem;
  height: 0.55rem;
  border: 1px solid rgba(151, 255, 235, 0.35);
  border-radius: 50%;
  background: transparent;
}

.category-card-good .category-feature-on .category-feature-dot {
  background: #8dffe8;
  border-color: #8dffe8;
  box-shadow: 0 0 10px rgba(141, 255, 232, 0.65);
}

.category-card-warn .category-feature-on .category-feature-dot {
  background: #ffd073;
  border-color: #ffd073;
  box-shadow: 0 0 10px rgba(255, 208, 115, 0.65);
}

.category-card-bad .category-feature-on .category-feature-dot {
  background: #ff6e80;
  border-color: #ff6e80;
  box-shadow: 0 0 10px rgba(255, 110, 128, 0.65);
}

.category-caption {
  margin: 0;
  color: #9eafb7;
  font-size: 0.85rem;
  line-height: 1.5;
}

.visual-grid {
  display: grid;
  grid-template-columns: minmax(0, 0.85fr) minmax(0, 1.15fr);
  gap: clamp(1.25rem, 2.5vw, 2.25rem);
  align-items: center;
}

.visual-grid .photo-stage {
  display: grid;
  grid-template-columns: 1fr;
  gap: clamp(0.6rem, 1.4vh, 1.1rem);
  max-width: 32rem;
  margin: 0 auto;
}

.visual-grid .photo-arrow {
  justify-self: center;
  width: 2px;
  height: clamp(1.4rem, 2.4vh, 2.4rem);
  background: linear-gradient(180deg, rgba(141, 255, 232, 0.25), #8dffe8);
}

.visual-grid .photo-arrow::after {
  top: auto;
  right: 50%;
  bottom: -2px;
  transform: translateX(50%) rotate(135deg);
}

.cap-card {
  position: relative;
  padding: 0.95rem 1.05rem 1.05rem;
  overflow: hidden;
  border: 1px solid rgba(151, 255, 235, 0.18);
  border-radius: 0.5rem;
  background:
    linear-gradient(135deg, rgba(255, 255, 255, 0.065), rgba(255, 255, 255, 0.014)),
    rgba(6, 10, 13, 0.65);
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.03),
    0 14px 36px rgba(0, 0, 0, 0.3);
  backdrop-filter: blur(18px);
}

.cap-card::before {
  position: absolute;
  top: 0;
  right: 0;
  left: 0;
  height: 2px;
  content: "";
  background: linear-gradient(90deg, rgba(141, 255, 232, 0), #8dffe8 50%, rgba(141, 255, 232, 0));
  opacity: 0.45;
}

.cap-eyebrow {
  margin: 0;
  color: #70e8d8;
  font-size: 0.6rem;
  font-weight: 800;
  letter-spacing: 0.2em;
  text-transform: uppercase;
}

.cap-card h3 {
  margin: 0.45rem 0 0.4rem;
  color: #f6fffd;
  font-size: 1rem;
  font-weight: 800;
  line-height: 1.25;
}

.cap-caption {
  margin: 0;
  color: #9eafb7;
  font-size: 0.8rem;
  line-height: 1.45;
}

.demo-stage {
  display: flex;
  flex-direction: column;
  gap: 1.75rem;
  align-items: center;
  width: 100%;
}

.demo-video {
  position: relative;
  width: 100%;
  max-width: 64rem;
  aspect-ratio: 16 / 9;
  overflow: hidden;
  border: 1px solid rgba(151, 255, 235, 0.22);
  border-radius: 0.7rem;
  background: rgba(6, 10, 13, 0.78);
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.035),
    0 30px 80px rgba(0, 0, 0, 0.42);
}

.demo-video-player {
  width: 100%;
  height: 100%;
  object-fit: contain;
  background: #050608;
}

.demo-video-player-idle {
  cursor: pointer;
}

.cta-link {
  display: inline-flex;
  gap: 0.7rem;
  align-items: center;
  padding: 0.95rem 1.6rem;
  color: #051312;
  font-size: 1rem;
  font-weight: 900;
  letter-spacing: 0.08em;
  text-decoration: none;
  text-transform: uppercase;
  background: linear-gradient(135deg, #8dffe8, #ffd073 58%, #ff6e80);
  border: none;
  border-radius: 0.5rem;
  box-shadow:
    0 0 0 1px rgba(141, 255, 232, 0.32),
    0 18px 38px rgba(0, 0, 0, 0.42),
    0 0 28px rgba(141, 255, 232, 0.22);
  cursor: pointer;
  transition: transform 180ms ease, box-shadow 180ms ease;
}

.cta-link:hover {
  transform: translateY(-2px);
  box-shadow:
    0 0 0 1px rgba(141, 255, 232, 0.45),
    0 22px 44px rgba(0, 0, 0, 0.48),
    0 0 36px rgba(141, 255, 232, 0.32);
}

.cta-link:active {
  transform: translateY(0);
}

.cta-arrow {
  font-size: 1.15em;
  line-height: 1;
}

.cta-row {
  display: flex;
  flex-wrap: wrap;
  gap: 0.85rem;
  justify-content: center;
}

.cta-link-secondary {
  color: #d9fff7;
  background: rgba(6, 10, 13, 0.55);
  border: 1px solid rgba(151, 255, 235, 0.32);
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.03),
    0 12px 28px rgba(0, 0, 0, 0.36);
  backdrop-filter: blur(8px);
}

.cta-link-secondary:hover {
  color: #f6fffd;
  background: rgba(141, 255, 232, 0.12);
  border-color: rgba(141, 255, 232, 0.6);
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.04),
    0 16px 32px rgba(0, 0, 0, 0.42),
    0 0 22px rgba(141, 255, 232, 0.18);
}


.scroll-nav {
  position: fixed;
  top: 50%;
  right: clamp(1rem, 2.5vw, 2.25rem);
  z-index: 10;
  display: flex;
  flex-direction: column;
  gap: 0.85rem;
  transform: translateY(-50%);
}

.scroll-nav-dot {
  position: relative;
  width: 0.7rem;
  height: 0.7rem;
  padding: 0;
  background: rgba(151, 255, 235, 0.25);
  border: 1px solid rgba(151, 255, 235, 0.35);
  border-radius: 50%;
  cursor: pointer;
  transition: transform 180ms ease, background 180ms ease, box-shadow 180ms ease;
}

.scroll-nav-dot:hover {
  background: rgba(141, 255, 232, 0.65);
  transform: scale(1.18);
}

.scroll-nav-dot-active {
  background: linear-gradient(135deg, #8dffe8, #ffd073);
  border-color: transparent;
  box-shadow: 0 0 0 4px rgba(141, 255, 232, 0.18), 0 0 18px rgba(141, 255, 232, 0.4);
  transform: scale(1.18);
}

@media (max-width: 1200px) {
  .kpi-strip,
  .capability-strip {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
}

@media (max-width: 1024px) {
  .two-column,
  .visual-grid {
    grid-template-columns: 1fr;
  }

  .capability-strip.visual-grid-tiles {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
}

@media (max-width: 640px) {
  .screen {
    padding: 2rem 1.2rem;
  }

  .kpi-strip,
  .capability-strip {
    grid-template-columns: 1fr;
  }

  .photo-stage {
    grid-template-columns: 1fr;
  }

  .photo-arrow {
    justify-self: center;
    width: 2px;
    height: 3rem;
    background: linear-gradient(180deg, rgba(141, 255, 232, 0.25), #8dffe8);
  }

  .photo-arrow::after {
    top: auto;
    right: 50%;
    bottom: 0;
    transform: translateX(50%) rotate(135deg);
  }

  h1 {
    font-size: clamp(1.8rem, 8vw, 2.8rem);
  }

  .scroll-nav {
    right: 0.65rem;
  }
}
</style>
