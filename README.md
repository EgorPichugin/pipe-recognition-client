# Pipe Recognition Client

Nuxt client for uploading structural field images, sending them to a recognition API, and displaying the returned report data in a dark-mode interface.

## Features

- Image upload with preview
- Recognition request to `http://localhost:8000/recognize/upload`
- Report table for image name, category, coordinates, and confidence
- Architecture page explaining the processing flow

## Run Locally

```bash
npm install
npm run dev
```

Open `http://localhost:3000`.

## Pages

- `/` - image recognition workflow
- `/architecture` - client-facing architecture overview
- `/presentation` - reserved presentation page
