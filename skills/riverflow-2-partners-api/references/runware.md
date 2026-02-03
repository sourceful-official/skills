---
name: runware
description: Runware integration for Sourceful Riverflow 2.0 (pro, fast).
---

# Runware integration (Riverflow 2.0)
Last verified: 2026-02-03

## Models (AIR IDs)
- `sourceful:riverflow-2.0@pro`
- `sourceful:riverflow-2.0@fast`

Runware exposes Riverflow 2.0 Pro and Fast for text-to-image, image-to-image, image editing, and upscale. RefSR is available through `inputs.superResolutionReferenceImages` inside `imageInference`, rather than a standalone model.

## Authentication
Runware accepts auth via `Authorization: Bearer $RUNWARE_API_KEY` or an `authentication` task as the first task in your request.

```json
[
  {
    "taskType": "authentication",
    "apiKey": "RUNWARE_API_KEY"
  },
  {
    "taskType": "imageInference",
    "taskUUID": "0bd8e7e6-0f66-4f2e-9e62-77e9d1f5a2c7",
    "model": "sourceful:riverflow-2.0@fast",
    "positivePrompt": "A clean product photo of a stainless steel water bottle on white.",
    "width": 1024,
    "height": 1024
  }
]
```

## Constraints and limits
- `positivePrompt` is required (2+ characters). Reference images: up to 10. Super resolution references: up to 4. Font inputs: up to 2, supported formats `TTF`, `OTF`, `WOFF`, `WOFF2`.
- Input image requirements for Riverflow 2.0: width and height between 300 and 2048, file size max 20MB.
- Image uploads accept `jpeg`, `jpg`, `png`, `webp`, `bmp`, `gif`; images are stored at max 2048px and deleted after 30 days if not reused.
- Upscale task input image accepts UUID, data URI, base64, or URL; supported formats are PNG/JPG/WEBP; max input size is 1,048,576 pixels.
- `imageInference` output controls: `outputType` supports `URL`, `dataURI`, `base64Data`; `outputFormat` supports `JPG`, `PNG`, `WEBP`; `outputQuality` ranges 20–99.

## Image upload (REST)
Use `imageUpload` to get an `imageUUID` you can reference in `inputs.referenceImages` or `inputs.superResolutionReferenceImages`.

```json
[
  {
    "taskType": "imageUpload",
    "taskUUID": "8d0f4b46-0f51-4f9b-9f8c-1f7a17f0f1cc",
    "image": "https://example.com/input.png"
  }
]
```

## Minimal working example (REST, upload → i2i + refSR + fonts)
```bash
# 1) Upload the base image
curl -X POST \
  -H "Authorization: Bearer $RUNWARE_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.runware.ai/v1 \
  -d '[
    {
      "taskType": "imageUpload",
      "taskUUID": "6f0caa25-06dd-4f1f-a2e5-1b1c9da2b90b",
      "image": "https://example.com/input.png"
    }
  ]'

# 2) Use the returned imageUUID in imageInference
curl -X POST \
  -H "Authorization: Bearer $RUNWARE_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.runware.ai/v1 \
  -d '[
    {
      "taskType": "imageInference",
      "taskUUID": "a91e5a3c-82b5-4f45-8b03-8c8b6e8f84d9",
      "model": "sourceful:riverflow-2.0@pro",
    "positivePrompt": "Refine the label and add the text LIMITED EDITION in Agu Display font.",
      "width": 1024,
      "height": 1280,
      "inputs": {
        "referenceImages": ["<imageUUID-from-upload>"],
        "superResolutionReferenceImages": ["<refUUID-1>", "<refUUID-2>"]
      },
      "providerSettings": {
        "sourceful": {
          "fontInputs": [
            {
              "fontUrl": "https://cdn.jsdelivr.net/fontsource/fonts/agu-display/latin-400-normal.woff2",
              "text": "LIMITED EDITION"
            }
          ]
        }
      },
      "outputType": "URL",
      "outputFormat": "PNG"
    }
  ]'
```

## Response handling
- If `outputType` is `URL`, read `imageURL` from the task response.
- If `outputType` is `dataURI`, read `imageDataURI`.
- If `outputType` is `base64Data`, read `imageBase64Data`.

## JavaScript (Runware SDK)
```ts
import { Runware } from "@runware/sdk-js";

const runware = new Runware({
  apiKey: process.env.RUNWARE_API_KEY,
});

const images = await runware.requestImages({
  model: "sourceful:riverflow-2.0@fast",
  positivePrompt: "A minimalist hero image of a premium notebook on marble. Add the text NEW ARRIVAL in Agu Display font.",
  width: 1280,
  height: 720,
  inputs: {
    referenceImages: ["<imageUUID-from-upload>"],
  },
  providerSettings: {
    sourceful: {
      fontInputs: [
        {
          fontUrl: "https://cdn.jsdelivr.net/fontsource/fonts/agu-display/latin-400-normal.woff2",
          text: "NEW ARRIVAL",
        },
      ],
    },
  },
});
```

## Python (Runware SDK)
```python
import asyncio
from runware import Runware, IImageInference

async def main():
  runware = Runware(api_key=RUNWARE_API_KEY)
  request = IImageInference(
    positivePrompt="A studio-lit product shot of a coffee grinder. Add the text PREMIUM in Agu Display font.",
    model="sourceful:riverflow-2.0@pro",
    width=1024,
    height=1024,
    inputs={
      "referenceImages": ["<imageUUID-from-upload>"],
    },
    providerSettings={
      "sourceful": {
        "fontInputs": [
          {"fontUrl": "https://cdn.jsdelivr.net/fontsource/fonts/agu-display/latin-400-normal.woff2", "text": "PREMIUM"}
        ]
      }
    },
  )
  results = await runware.imageInference(request)
  print(results)

asyncio.run(main())
```
