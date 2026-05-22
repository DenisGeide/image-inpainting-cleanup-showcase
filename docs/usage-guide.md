# Usage Guide

This guide explains the practical run flow for the inpainting pipeline.

## 1. Open ComfyUI

Start ComfyUI and load the inpainting workflow.

## 2. Select An Inpainting Checkpoint

Use an SDXL checkpoint designed for inpainting. The exact checkpoint can be changed depending on the image style and desired result.

## 3. Load The Source Image

Use the `Load Image` node and select the image that should be cleaned.

## 4. Create The Mask

Open MaskEditor from the image node and paint the region that should be reconstructed.

Good masks usually:

- cover the full unwanted element;
- include a small margin around the element;
- avoid covering too much unrelated detail;
- follow the shape of the object/text when possible.

## 5. Check Prompts

Baseline positive prompt:

```text
clean background, high quality, seamless
```

Baseline negative prompt:

```text
watermark, text, logo, signature, bad quality
```

## 6. Run The Queue

Run `Queue Prompt` and inspect the saved output.

## 7. Iterate

If the result is weak, the most useful changes are:

- adjust the mask shape;
- increase/decrease mask expansion;
- try another inpainting checkpoint;
- change the positive prompt to match the background;
- reduce visible seams by masking a slightly larger area;
- run several seeds and compare results.

## Common Issues

| Problem | Likely Cause | Practical Fix |
|---|---|---|
| visible edge around the removed area | mask too tight | increase mask area or `grow_mask_by` |
| text-like artifacts remain | negative prompt too weak / mask too small | expand mask and keep text/logo terms in negative prompt |
| background does not match | checkpoint or prompt mismatch | try another checkpoint or more specific background prompt |
| image gets changed too much | mask too large | reduce mask area |
| blurry patch | low quality generation or weak context | try a different seed/checkpoint or cleaner mask |
