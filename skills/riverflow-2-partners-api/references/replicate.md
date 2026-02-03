---
name: replicate
description: Replicate integration for Sourceful Riverflow 2.0 (pro, fast, refSR).
---

# Replicate integration (Riverflow 2.0)

## Models
- `sourceful/riverflow-2.0-pro` supports text-to-image, image-to-image, font control, and reference-based super resolution. It offers higher quality and supports 1K, 2K, and 4K outputs.
- `sourceful/riverflow-2.0-fast` is optimized for speed and supports text-to-image, image-to-image, font control, transparency, 1K and 2K output, and control inputs like `instruction`, `resolution`, `aspectRatio`, `fontInputs`, `enhancePrompt`, `maxIterations`, `safetyChecker`, and `imageUrls` for i2i.
- `sourceful/riverflow-2.0-refsr` is a standalone refSR model that accepts `imageUrl` plus `superResolutionReferences` (up to 4) with optional idempotency keying.

## TypeScript (Replicate SDK)
```ts
import Replicate from "replicate";

const replicate = new Replicate({
  auth: process.env.REPLICATE_API_TOKEN,
});

const [output] = await replicate.run("sourceful/riverflow-2.0-fast", {
  input: {
    instruction: "Generate a minimal studio shot of a ceramic mug on white.",
    resolution: "2K",
    aspectRatio: "4:5",
    enhancePrompt: false,
  },
});
```

## Python (Replicate SDK)
```python
import replicate

output = replicate.run(
  "sourceful/riverflow-2.0-fast",
  input={
    "instruction": "Create a clean hero image of a backpack on a neutral background.",
    "resolution": "1K",
    "aspectRatio": "16:9",
  },
)
```

## REST (HTTP API)
```bash
curl -X POST \
  -H "Authorization: Bearer $REPLICATE_API_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.replicate.com/v1/models/sourceful/riverflow-2.0-fast/predictions \
  -d '{
    "input": {
      "instruction": "Photoreal product shot of a glass bottle with softbox lighting.",
      "resolution": "2K",
      "aspectRatio": "3:2"
    }
  }'
```

If you need to target a specific model version, use `POST https://api.replicate.com/v1/predictions` with a `version` ID instead of the model endpoint.

## RefSR example (REST)
```bash
curl -X POST \
  -H "Authorization: Bearer $REPLICATE_API_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.replicate.com/v1/models/sourceful/riverflow-2.0-refsr/predictions \
  -d '{
    "input": {
      "imageUrl": "https://example.com/low-res.png",
      "superResolutionReferences": [
        "https://example.com/ref-1.png",
        "https://example.com/ref-2.png"
      ]
    }
  }'
```
