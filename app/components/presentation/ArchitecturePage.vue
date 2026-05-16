<template>
  <main class="architecture-page">
    <div class="architecture-body">
      <div class="architecture-left">
        <section class="architecture-hero" aria-labelledby="architecture-title">
          <div class="hero-copy">
            <p class="eyebrow">Processing Architecture</p>
            <h1 id="architecture-title">From field image to structural report</h1>
            <p class="intro">
              Our pipeline converts a raw inspection photo into a structured client
              report through detection, classification, geolocation extraction, and
              confidence scoring.
            </p>
          </div>
        </section>

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

          <div class="photo-arrow" aria-hidden="true"></div>

          <div class="photo-placeholder detection-placeholder">
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
      </div>

      <section class="steps" aria-label="Architecture workflow">
        <article
          v-for="step in steps"
          :key="step.number"
          class="step-card"
        >
          <div class="step-content">
            <div class="step-number">{{ step.number }}</div>
            <div>
              <h2>{{ step.title }}</h2>
              <p>{{ step.description }}</p>
            </div>
          </div>
        </article>
      </section>
    </div>
  </main>
</template>

<script setup lang="ts">
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
</script>

<style scoped>
.architecture-page {
  position: relative;
  min-height: 100vh;
  overflow: hidden;
  padding: clamp(0.75rem, 1.5vw, 1.25rem) clamp(1.5rem, 4vw, 4.5rem) clamp(1.5rem, 4vw, 4.5rem);
  background:
    radial-gradient(circle at 16% 14%, rgba(48, 179, 158, 0.2), transparent 31rem),
    radial-gradient(circle at 85% 22%, rgba(255, 208, 113, 0.14), transparent 30rem),
    linear-gradient(135deg, #050608 0%, #0c1114 48%, #07090c 100%);
}

.architecture-page::before {
  position: absolute;
  inset: 0;
  pointer-events: none;
  content: "";
  background-image:
    linear-gradient(rgba(255, 255, 255, 0.05) 1px, transparent 1px),
    linear-gradient(90deg, rgba(255, 255, 255, 0.05) 1px, transparent 1px);
  background-size: 72px 72px;
  mask-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.92), transparent 92%);
}

.architecture-hero,
.architecture-left,
.architecture-body,
.photo-stage,
.steps {
  position: relative;
  z-index: 1;
}

.architecture-hero {
  display: flex;
  align-items: flex-end;
  padding-bottom: clamp(1.25rem, 2vw, 2rem);
}

.hero-copy {
  max-width: 58rem;
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

.intro {
  max-width: 46rem;
  margin: 0.8rem 0 0;
  color: #b7c2c8;
  font-size: clamp(1rem, 1.35vw, 1.18rem);
  line-height: 1.7;
}

.architecture-body {
  display: grid;
  grid-template-columns: minmax(0, 0.92fr) minmax(25rem, 0.72fr);
  gap: clamp(2rem, 4vw, 4rem);
  align-items: start;
}

.architecture-left {
  margin-top: clamp(4rem, 9vh, 7rem);
}

.photo-stage {
  display: grid;
  grid-template-columns: minmax(0, 1fr) auto minmax(0, 1fr);
  gap: 1rem;
  align-items: center;
  max-width: 42rem;
}

.photo-placeholder {
  display: grid;
  gap: 0.85rem;
}

.photo-placeholder p {
  margin: 0;
  color: #d9fff7;
  font-size: 0.82rem;
  font-weight: 850;
  letter-spacing: 0.12em;
  text-transform: uppercase;
}

.photo-frame {
  position: relative;
  display: grid;
  min-height: clamp(6.5rem, 10vw, 8.5rem);
  overflow: hidden;
  place-items: center;
  border: 1px solid rgba(151, 255, 235, 0.22);
  border-radius: 0.5rem;
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
  width: clamp(2.5rem, 4vw, 4rem);
  height: 2px;
  background: linear-gradient(90deg, rgba(141, 255, 232, 0.25), #8dffe8);
  box-shadow: 0 0 24px rgba(141, 255, 232, 0.24);
}

.photo-arrow::after {
  position: absolute;
  top: 50%;
  right: -1px;
  bottom: auto;
  width: 0.8rem;
  height: 0.8rem;
  content: "";
  border-top: 2px solid #8dffe8;
  border-right: 2px solid #8dffe8;
  transform: translateY(-50%) rotate(45deg);
}

.steps {
  display: grid;
  grid-template-columns: 1fr;
  gap: 1.35rem;
  margin-top: clamp(2.5rem, 5vh, 4rem);
}

.step-card {
  position: relative;
  min-height: 8.5rem;
  padding: 1.4rem;
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

.step-card:not(:last-child)::after {
  position: absolute;
  bottom: -1.32rem;
  left: 2.42rem;
  width: 0.85rem;
  height: 0.85rem;
  content: "";
  border-top: 2px solid rgba(141, 255, 232, 0.78);
  border-right: 2px solid rgba(141, 255, 232, 0.78);
  transform: rotate(135deg);
}

.step-card:not(:last-child)::before {
  position: absolute;
  bottom: -1.35rem;
  left: 2.8rem;
  width: 2px;
  height: 1.35rem;
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
  width: 3.8rem;
  height: 3.8rem;
  place-items: center;
  color: #051312;
  font-size: 0.9rem;
  font-weight: 900;
  border-radius: 50%;
  background: linear-gradient(135deg, #8dffe8, #ffd073 58%, #ff6e80);
  box-shadow: 0 0 32px rgba(126, 247, 225, 0.16);
}

h2 {
  margin: 0;
  color: #f6fffd;
  font-size: clamp(1.15rem, 1.45vw, 1.45rem);
  line-height: 1.25;
}

.step-card p {
  margin: 0.75rem 0 0;
  color: #9eafb7;
  font-size: 1rem;
  line-height: 1.55;
}

@media (max-width: 980px) {
  .architecture-body {
    grid-template-columns: 1fr;
  }

  .architecture-left {
    margin-top: 0;
  }

  .photo-stage {
    max-width: none;
    grid-template-columns: minmax(0, 1fr) auto minmax(0, 1fr);
    gap: 1.25rem;
  }

  .step-card {
    min-height: auto;
  }

  .steps {
    margin-top: 0;
  }
}

@media (max-width: 640px) {
  .architecture-page {
    padding: 1.2rem;
  }

  .architecture-hero {
    min-height: auto;
    padding: 3rem 0 2rem;
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
}
</style>
