<template>
  <section class="upload-panel" aria-labelledby="upload-title">
    <div class="panel-glow panel-glow-left"></div>

    <div class="panel-header">
      <StructuralBrand />
      <NuxtLink class="architecture-link" to="/architecture">
        Architecture
      </NuxtLink>
    </div>

    <div class="copy-block">
      <p class="eyebrow">Pipe Recognition</p>
      <h1 id="upload-title">Image intake for field analysis</h1>
      <p class="intro">
        Upload an inspection image and start the recognition flow when the
        frame is ready for processing.
      </p>
    </div>

    <label class="dropzone" for="image-upload">
      <input
        id="image-upload"
        type="file"
        accept="image/*"
        multiple
        @change="handleImageSelection"
      >

      <span v-if="selectedPreviews.length" class="preview-grid">
        <span
          v-for="preview in selectedPreviews"
          :key="preview.url"
          class="preview-tile"
        >
          <img
            :src="preview.url"
            :alt="`Selected image preview: ${preview.name}`"
          >
          <span>{{ preview.name }}</span>
        </span>
      </span>

      <span v-else class="upload-state">
        <span class="upload-icon" aria-hidden="true"></span>
        <span class="upload-title">Select images</span>
        <span class="upload-meta">Choose multiple JPG, PNG, WEBP inspection frames</span>
      </span>
    </label>

    <div class="action-row">
      <div class="file-chip" :class="{ active: selectedFiles.length }">
        {{ uploadMessage || selectedImageSummary || 'No images selected' }}
      </div>
      <button
        class="start-button"
        type="button"
        :disabled="!selectedFiles.length || isUploading"
        @click="uploadImages"
      >
        {{ isUploading ? 'Sending' : 'Start' }}
      </button>
    </div>
  </section>
</template>

<script setup lang="ts">
import StructuralBrand from '~/components/recognition/StructuralBrand.vue'

export interface RecognitionResult {
  id: number
  image_name: string
  category: number
  latitude: number
  longitude?: number
  longtitude?: number
  confidence: number
  status: string
}

const emit = defineEmits<{
  recognized: [result: RecognitionResult]
  loading: [isLoading: boolean]
  reset: []
}>()

const selectedFiles = ref<File[]>([])
const selectedImageSummary = ref('')
const selectedPreviews = ref<Array<{ name: string, url: string }>>([])
const isUploading = ref(false)
const uploadMessage = ref('')

const handleImageSelection = (event: Event) => {
  const input = event.target as HTMLInputElement
  const files = Array.from(input.files ?? []).filter((file) =>
    file.type.startsWith('image/'),
  )

  revokePreviewUrls()

  selectedFiles.value = files
  selectedImageSummary.value = formatImageSummary(files)
  selectedPreviews.value = files.map((file) => ({
    name: file.name,
    url: URL.createObjectURL(file),
  }))
  uploadMessage.value = ''
  emit('reset')
}

const uploadImages = async () => {
  if (!selectedFiles.value.length) {
    return
  }

  isUploading.value = true
  emit('loading', true)

  try {
    uploadMessage.value = `Sending batch of ${selectedFiles.value.length} image${selectedFiles.value.length === 1 ? '' : 's'}...`

    const results = await uploadImageBatch(selectedFiles.value)

    for (const result of results) {
      emit('recognized', result)
    }

    uploadMessage.value = `Uploaded ${results.length} image${results.length === 1 ? '' : 's'} successfully`
  } catch (error) {
    uploadMessage.value =
      error instanceof Error ? error.message : 'Upload failed'
  } finally {
    isUploading.value = false
    emit('loading', false)
  }
}

const uploadImageBatch = async (files: File[]) => {
  const formData = new FormData()

  for (const file of files) {
    formData.append('ids', generateImageId().toString())
    formData.append('images', file)
  }

  const response = await fetch('http://localhost:8000/recognize/upload/batch', {
    method: 'POST',
    headers: {
      accept: 'application/json',
    },
    body: formData,
  })

  if (!response.ok) {
    throw new Error(`Batch upload failed with status ${response.status}`)
  }

  return normalizeRecognitionResponse(await response.json())
}

const generateImageId = () => Date.now() + Math.floor(Math.random() * 1000)

const normalizeRecognitionResponse = (response: unknown): RecognitionResult[] => {
  if (Array.isArray(response)) {
    return response as RecognitionResult[]
  }

  if (
    response &&
    typeof response === 'object' &&
    'results' in response &&
    Array.isArray((response as { results: unknown }).results)
  ) {
    return (response as { results: RecognitionResult[] }).results
  }

  return [response as RecognitionResult]
}

const formatImageSummary = (files: File[]) => {
  if (!files.length) {
    return ''
  }

  return `${files.length} image${files.length === 1 ? '' : 's'} selected`
}

onBeforeUnmount(() => {
  revokePreviewUrls()
})

const revokePreviewUrls = () => {
  for (const preview of selectedPreviews.value) {
    URL.revokeObjectURL(preview.url)
  }

  selectedPreviews.value = []
}
</script>

<style scoped>
.upload-panel {
  position: relative;
  z-index: 2;
  display: flex;
  min-width: 0;
  min-height: 100vh;
  padding: clamp(1.5rem, 4vw, 4.5rem);
  flex-direction: column;
  justify-content: space-between;
  gap: 2rem;
  border-right: 1px solid rgba(255, 255, 255, 0.1);
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

.panel-glow-left {
  right: 8%;
  bottom: 6%;
  background: conic-gradient(
    from 180deg,
    rgba(61, 201, 176, 0),
    rgba(61, 201, 176, 0.3),
    rgba(224, 83, 96, 0.2),
    rgba(61, 201, 176, 0)
  );
}

.panel-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
}

.architecture-link {
  flex: 0 0 auto;
  min-height: 2.75rem;
  padding: 0.78rem 1rem;
  color: #d9fff7;
  font-size: 0.78rem;
  font-weight: 850;
  letter-spacing: 0.12em;
  text-align: center;
  text-decoration: none;
  text-transform: uppercase;
  border: 1px solid rgba(151, 255, 235, 0.24);
  border-radius: 0.5rem;
  background:
    linear-gradient(135deg, rgba(141, 255, 232, 0.16), rgba(255, 208, 115, 0.08)),
    rgba(7, 15, 18, 0.74);
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.04),
    0 16px 38px rgba(0, 0, 0, 0.28);
  transition:
    border-color 160ms ease,
    color 160ms ease,
    transform 160ms ease;
}

.architecture-link:hover {
  color: #ffffff;
  border-color: rgba(151, 255, 235, 0.54);
  transform: translateY(-1px);
}

.copy-block {
  max-width: 42rem;
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
  max-width: 22ch;
  margin: 0;
  color: #ffffff;
  font-size: clamp(1.75rem, 2.8vw, 3.25rem);
  line-height: 1.08;
  text-wrap: balance;
}

.intro {
  max-width: 34rem;
  margin: 1.5rem 0 0;
  color: #b7c2c8;
  font-size: clamp(1rem, 1.45vw, 1.18rem);
  line-height: 1.7;
}

.dropzone {
  position: relative;
  display: grid;
  min-height: clamp(18rem, 34vh, 25rem);
  overflow: hidden;
  cursor: pointer;
  place-items: center;
  border: 1px solid rgba(151, 255, 235, 0.24);
  border-radius: 0.5rem;
  background:
    linear-gradient(135deg, rgba(255, 255, 255, 0.08), rgba(255, 255, 255, 0.018)),
    rgba(8, 14, 17, 0.74);
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.04),
    0 30px 80px rgba(0, 0, 0, 0.42);
  transition:
    border-color 180ms ease,
    transform 180ms ease,
    box-shadow 180ms ease;
}

.dropzone:hover {
  border-color: rgba(151, 255, 235, 0.54);
  transform: translateY(-2px);
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.06),
    0 38px 96px rgba(0, 0, 0, 0.5),
    0 0 50px rgba(87, 229, 204, 0.12);
}

.dropzone::before {
  position: absolute;
  inset: 1rem;
  pointer-events: none;
  content: "";
  border: 1px dashed rgba(255, 255, 255, 0.16);
  border-radius: 0.4rem;
}

.dropzone input {
  position: absolute;
  width: 1px;
  height: 1px;
  overflow: hidden;
  clip: rect(0 0 0 0);
}

.upload-state {
  display: grid;
  justify-items: center;
  gap: 0.8rem;
  padding: 2rem;
  text-align: center;
}

.upload-icon {
  width: 4.5rem;
  height: 4.5rem;
  border: 1px solid rgba(126, 247, 225, 0.42);
  border-radius: 50%;
  background:
    linear-gradient(#8dffe8, #8dffe8) center 1.35rem / 1.45rem 0.18rem no-repeat,
    linear-gradient(#8dffe8, #8dffe8) center 1.35rem / 0.18rem 1.45rem no-repeat,
    rgba(100, 255, 227, 0.08);
  box-shadow:
    inset 0 0 28px rgba(126, 247, 225, 0.13),
    0 0 35px rgba(126, 247, 225, 0.1);
}

.upload-title {
  color: #f6fffd;
  font-size: 1.35rem;
  font-weight: 800;
}

.upload-meta {
  color: #8e9da4;
  font-size: 0.95rem;
}

.preview-grid {
  display: grid;
  width: 100%;
  max-height: 20rem;
  overflow: auto;
  padding: 1.65rem;
  grid-template-columns: repeat(auto-fill, minmax(7rem, 1fr));
  gap: 0.85rem;
}

.preview-tile {
  display: grid;
  min-width: 0;
  gap: 0.45rem;
}

.preview-tile img {
  width: 100%;
  aspect-ratio: 1;
  object-fit: cover;
  border: 1px solid rgba(151, 255, 235, 0.22);
  border-radius: 0.45rem;
  background: rgba(2, 6, 8, 0.72);
  box-shadow:
    0 14px 32px rgba(0, 0, 0, 0.32),
    0 0 28px rgba(126, 247, 225, 0.08);
}

.preview-tile span {
  overflow: hidden;
  color: #9eafb7;
  font-size: 0.72rem;
  font-weight: 700;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.action-row {
  display: grid;
  grid-template-columns: minmax(0, 1fr) auto;
  gap: 1rem;
  align-items: center;
}

.file-chip {
  min-width: 0;
  overflow: hidden;
  padding: 1rem 1.1rem;
  color: #819096;
  text-overflow: ellipsis;
  white-space: nowrap;
  border: 1px solid rgba(255, 255, 255, 0.08);
  border-radius: 0.5rem;
  background: rgba(255, 255, 255, 0.045);
}

.file-chip.active {
  color: #d9fff7;
  border-color: rgba(120, 255, 231, 0.22);
}

.start-button {
  min-width: 9.5rem;
  height: 3.45rem;
  color: #051312;
  font-weight: 900;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  cursor: pointer;
  border: 0;
  border-radius: 0.5rem;
  background: linear-gradient(135deg, #8dffe8, #ffd073 54%, #ff6e80);
  box-shadow:
    0 18px 45px rgba(255, 112, 128, 0.18),
    0 10px 40px rgba(130, 255, 232, 0.18);
  transition:
    filter 160ms ease,
    transform 160ms ease,
    opacity 160ms ease;
}

.start-button:not(:disabled):hover {
  filter: brightness(1.08);
  transform: translateY(-1px);
}

.start-button:disabled {
  cursor: not-allowed;
  opacity: 0.42;
  filter: grayscale(0.65);
}

@media (max-width: 900px) {
  .upload-panel {
    min-height: 50vh;
    border-right: 0;
    border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  }

  h1 {
    max-width: 22ch;
  }
}

@media (max-width: 560px) {
  .upload-panel {
    padding: 1.2rem;
  }

  .panel-header {
    align-items: flex-start;
    flex-direction: column;
  }

  .architecture-link {
    width: 100%;
  }

  .action-row {
    grid-template-columns: 1fr;
  }

  .start-button {
    width: 100%;
  }
}
</style>
