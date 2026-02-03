---
name: openrouter
description: OpenRouter integration for Sourceful Riverflow 2.0 (v2 fast/pro).
---

# OpenRouter integration (Riverflow 2.0)

## Models
- `sourceful/riverflow-v2-fast`
- `sourceful/riverflow-v2-pro`

OpenRouter exposes refSR via `image_config.super_resolution_references` on image-to-image requests; there is no standalone refSR model listed alongside these models.

## Endpoint
Use `POST https://openrouter.ai/api/v1/chat/completions` with `modalities: ["image", "text"]` to generate images.

## Image config (Sourceful)
`image_config` supports:
- `aspect_ratio`: `1:1`, `2:3`, `3:2`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`, `16:9`, `21:9`
- `image_size`: `1K`, `2K`, `4K`
- `font_inputs`: up to 2 entries, each with `font_url` and `text`
- `super_resolution_references`: up to 4 URLs, only applied for image-to-image requests (when you include input images in `messages`)

Images are returned on the assistant message under `message.images` as base64 data URLs.

## TypeScript (OpenRouter SDK)
```ts
import { OpenRouter } from "@openrouter/sdk";

const openRouter = new OpenRouter({
  apiKey: process.env.OPENROUTER_API_KEY,
});

const result = await openRouter.chat.send({
  model: "sourceful/riverflow-v2-pro",
  modalities: ["image", "text"],
  messages: [
    {
      role: "user",
      content: "Generate a premium product photo on a white seamless background.",
    },
  ],
  image_config: {
    aspect_ratio: "16:9",
    image_size: "2K",
    font_inputs: [
      {
        font_url: "https://example.com/fonts/inter-bold.ttf",
        text: "NOW AVAILABLE",
      },
    ],
  },
  stream: false,
});

const images = result.choices?.[0]?.message?.images ?? [];
```

## REST (Python requests)
```python
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
      "content": "Create a hero image for a new coffee blend, clean studio lighting.",
    }
  ],
  "modalities": ["image", "text"],
  "image_config": {
    "aspect_ratio": "4:5",
    "image_size": "1K",
    "font_inputs": [
      {
        "font_url": "https://example.com/fonts/inter-regular.ttf",
        "text": "NEW BLEND"
      }
    ]
  },
}

response = requests.post(url, headers=headers, json=payload)
result = response.json()
images = result.get("choices", [])[0]["message"].get("images", [])
```
