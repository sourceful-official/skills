---
name: font-control
description: Typography and font input guidance for Riverflow 2.0
---

# Font control

Riverflow 2.0 supports up to 2 fonts per image and up to 300 characters of text.

## Use fontInputs

Provide `fontInputs` with a font URL and the exact text to render:

```
"fontInputs": [
  { "fontUrl": "https://example.com/fonts/BrandSans.otf", "text": "Spring Collection" }
]
```

## Instruction rules

- Quote the exact copy in the instruction.
- Specify placement, size, and alignment.
- Mention font name if it is known, even when using `fontInputs`.

## Example

```
Add the headline "Spring Collection" centered at the top in Brand Sans Bold, 72px,
with tracking +2 and pure white text. Keep the product unchanged.
```

## Two-font layouts

- Use one font for headline, one for body copy.
- Keep body copy short and legible at the target resolution.
