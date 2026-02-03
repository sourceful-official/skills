---
name: runware
description: Runware integration for Sourceful Riverflow 2.0 (pro, fast).
---

# Runware integration (Riverflow 2.0)

## Models (AIR IDs)
- `sourceful:riverflow-2.0@pro`
- `sourceful:riverflow-2.0@fast`

Runware exposes Riverflow 2.0 Pro and Fast for text-to-image, image-to-image, image editing, and upscale. RefSR is available through `inputs.superResolutionReferenceImages` inside `imageInference`, rather than a standalone model.

## REST (HTTP)
Runware accepts a JSON array of tasks at `https://api.runware.ai/v1`. You can authenticate with an `Authorization: Bearer` header or include auth as the first task.

```json
[
  {
    "taskType": "imageInference",
    "taskUUID": "8b4a8b65-0c2f-4b62-9f23-202dd1f1d6a1",
    "model": "sourceful:riverflow-2.0@fast",
    "positivePrompt": "A clean product photo of a stainless steel water bottle on white.",
    "width": 1024,
    "height": 1024
  }
]
```

To add Sourceful-only controls:
- `inputs.referenceImages` for image-to-image references.
- `inputs.superResolutionReferenceImages` for refSR references.
- `providerSettings.sourceful.fontInputs` for font control with `fontUrl` and `text`.

## JavaScript (Runware SDK)
```ts
import { Runware } from "@runware/sdk-js";

const runware = new Runware({
  apiKey: process.env.RUNWARE_API_KEY,
});

const images = await runware.requestImages({
  model: "sourceful:riverflow-2.0@fast",
  positivePrompt: "A minimalist hero image of a premium notebook on marble.",
  width: 1280,
  height: 720,
});
```

## Python (Runware SDK)
```python
import asyncio
from runware import Runware, IImageInference

async def main():
  runware = Runware(api_key=RUNWARE_API_KEY)
  request = IImageInference(
    positivePrompt="A studio-lit product shot of a coffee grinder.",
    model="sourceful:riverflow-2.0@pro",
    width=1024,
    height=1024,
  )
  results = await runware.imageInference(request)
  print(results)

asyncio.run(main())
```
