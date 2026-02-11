---
name: openrouter
description: OpenRouter integration for Sourceful Riverflow 2.0 (v2 fast/pro).
---

# OpenRouter integration (Riverflow 2.0)
Last verified: 2026-02-03

## Models
- `sourceful/riverflow-v2-fast`
- `sourceful/riverflow-v2-pro`

OpenRouter exposes refSR via `image_config.super_resolution_references` on image-to-image requests; there is no standalone refSR model listed alongside these models.

## Constraints and limits
- Sourceful models on OpenRouter enforce a 4.5MB request size limit; prefer image URLs over base64 data.
- Input image content types: `image/png`, `image/jpeg`, `image/webp`, `image/gif`.
- Number of input images per request varies by provider and model; send text first, then images.
- Output images are returned as base64 data URLs (typically PNG) in `message.images`.

## Endpoint
Use `POST https://openrouter.ai/api/v1/chat/completions` with `modalities: ["image"]` to generate images.

## Image config (Sourceful)
`image_config` supports:
- `aspect_ratio`: `1:1`, `2:3`, `3:2`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`, `16:9`, `21:9`.
- `image_size`: `1K`, `2K`, `4K` (only on models that support it).
- `font_inputs`: up to 2 entries, each with `font_url` and `text`.
- `super_resolution_references`: up to 4 URLs, only applied for image-to-image requests (when you include input images in `messages`).

## TypeScript (OpenRouter SDK, image-to-image + refSR + fonts)
```ts
import { OpenRouter } from "@openrouter/sdk";
import fs from "fs";

const openRouter = new OpenRouter({
  apiKey: process.env.OPENROUTER_API_KEY,
});

const result = await openRouter.chat.send({
  model: "sourceful/riverflow-v2-pro",
  modalities: ["image"],
  messages: [
    {
      role: "user",
      content: [
        {
          type: "text",
          text: "Edit the label to say NEW BLEND in Agu Display font and keep the bottle shape unchanged.",
        },
        {
          type: "image_url",
          imageUrl: {
            url: "https://example.com/input.png",
          },
        },
      ],
    },
  ],
  image_config: {
    aspect_ratio: "4:5",
    font_inputs: [
      {
        font_url: "https://cdn.jsdelivr.net/fontsource/fonts/agu-display/latin-400-normal.woff2",
        text: "NEW BLEND",
      },
    ],
    super_resolution_references: [
      "https://example.com/ref-1.png",
      "https://example.com/ref-2.png",
    ],
  },
  stream: false,
});

const image = result.choices?.[0]?.message?.images?.[0];
const dataUrl = image?.imageUrl?.url;
if (dataUrl) {
  const base64 = dataUrl.split(",")[1];
  fs.writeFileSync("output.png", Buffer.from(base64, "base64"));
}
```

## REST (Python, image-to-image + refSR + fonts)
```python
import base64
import requests

url = "https://openrouter.ai/api/v1/chat/completions"
headers = {
  "Authorization": f"Bearer {OPENROUTER_API_KEY}",
  "Content-Type": "application/json",
}

payload = {
  "model": "sourceful/riverflow-v2-fast",
  "messages": [
    {
      "role": "user",
      "content": [
        {"type": "text", "text": "Enhance the label details and set the text to LIMITED EDITION in Agu Display font."},
        {
          "type": "image_url",
          "image_url": {"url": "https://example.com/input.png"},
        },
      ],
    }
  ],
  "modalities": ["image"],
  "image_config": {
    "aspect_ratio": "4:5",
    "font_inputs": [
      {
        "font_url": "https://cdn.jsdelivr.net/fontsource/fonts/agu-display/latin-400-normal.woff2",
        "text": "LIMITED EDITION",
      }
    ],
    "super_resolution_references": [
      "https://example.com/ref-1.png"
    ],
  },
}

response = requests.post(url, headers=headers, json=payload)
response.raise_for_status()
result = response.json()
image_url = result["choices"][0]["message"]["images"][0]["image_url"]["url"]
_, b64 = image_url.split(",", 1)
with open("output.png", "wb") as f:
  f.write(base64.b64decode(b64))
```
