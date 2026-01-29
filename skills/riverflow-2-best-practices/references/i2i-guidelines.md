---
name: i2i-guidelines
description: Image-to-image instruction patterns and editing constraints
---

# I2I instruction patterns

Use explicit keep/change language to reduce ambiguity.

## Pattern

```
Edit the image by [change].
Keep [elements] unchanged.
Ensure [detail constraints].
```

## Examples

```
Edit the image by replacing the background with a clean white seamless studio backdrop.
Keep the product shape, label text, and perspective unchanged.
Ensure a soft shadow directly below the product.
```

```
Edit the image by changing the cap color to brushed gold.
Keep the bottle material, label typography, and overall lighting unchanged.
```

## Multi-image edits

When using multiple input images, specify the role of each image in the instruction:

- "Use the product from image 1 and the lighting style from image 2."
- "Use the packaging from image 1, but match the background from image 2."
