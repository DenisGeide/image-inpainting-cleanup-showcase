# Prompt And Mask Notes

Inpainting quality depends more on the mask and context than on a long prompt.

## Positive Prompt Strategy

The positive prompt should describe what should appear inside the masked area.

Baseline:

```text
clean background, high quality, seamless
```

Useful additions:

```text
smooth background
matching texture
natural lighting
same material
clean surface
```

## Negative Prompt Strategy

The negative prompt should describe what should not return.

Baseline:

```text
watermark, text, logo, signature, bad quality
```

Useful additions:

```text
letters
caption
distorted text
low quality
blur
artifacts
hard edges
```

## Mask Strategy

The mask controls the edit more strongly than the prompt.

Good mask behavior:

- covers the full unwanted element;
- includes the edge/shadow/glow of the element;
- leaves enough surrounding image context;
- avoids covering important subject details;
- gives the model room to blend with the background.

## Denoise Strategy

`denoise = 1.00` fully regenerates the masked region.

This is useful for:

- removing text;
- removing logos/signatures;
- reconstructing backgrounds;
- replacing a patch completely.

Lower denoise values can preserve more original pixels, but they may leave traces of the removed element.
