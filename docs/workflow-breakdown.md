# Workflow Breakdown

This document explains the ComfyUI inpainting workflow block by block.

## 1. Model Loading

The model loading block is responsible for the neural network used to reconstruct the masked region.

### Load Checkpoint

The checkpoint node loads an SDXL inpainting model. The checkpoint produces three outputs:

| Output | Purpose |
|---|---|
| `MODEL` | neural network used by the sampler |
| `CLIP` | text encoder used for prompts |
| `VAE` | image/latent encoder and decoder |

Different inpainting checkpoints can produce different results. If the output does not match the image style, the checkpoint is one of the first things to test.

## 2. Source Image And Mask

The source image block defines what image is edited and which region is regenerated.

### Load Image

The image is loaded into ComfyUI through the `Load Image` node.

### Mask

The mask is the most important control input. The masked area is the region that will be repainted.

The workflow uses ComfyUI MaskEditor:

1. open the image node context menu;
2. open MaskEditor;
3. paint the text/object/mark region;
4. run the prompt queue.

The cleaner the mask, the easier it is for the model to reconstruct the background.

## 3. Prompting

The workflow uses a positive prompt and a negative prompt.

### Positive Prompt

```text
clean background, high quality, seamless
```

This tells the model to reconstruct the masked region as a clean, high-quality continuation of the surrounding image.

### Negative Prompt

```text
watermark, text, logo, signature, bad quality
```

This pushes the model away from recreating text-like shapes, logos, signatures or low-quality artifacts inside the masked region.

## 4. VAE Encode For Inpainting

`VAE Encode (for Inpainting)` combines:

- source image;
- painted mask;
- VAE from the checkpoint.

It converts the editable image region into latent space so the sampler can regenerate it.

### Mask Expansion

```text
grow_mask_by = 6
```

Mask expansion grows the painted region slightly. This helps avoid hard edges around the removed element and gives the model enough space to blend the reconstructed area.

## 5. KSampler

KSampler performs the actual generation.

Baseline settings:

| Setting | Value | Meaning |
|---|---|---|
| `steps` | `20` | generation iterations |
| `cfg` | `8.0` | prompt strength |
| `sampler_name` | `euler` | sampling algorithm |
| `scheduler` | `normal` | noise schedule |
| `denoise` | `1.00` | full repainting of the masked region |

`denoise = 1.00` means the masked pixels are regenerated from scratch. This is useful when the goal is to fully remove an unwanted element and reconstruct the background.

## 6. Decode And Save

### VAE Decode

The generated latent is decoded back into a normal image.

### Save Image

The final image is saved into the ComfyUI output folder.

Default filename pattern:

```text
ComfyUI_0001.png
```

## Result Evaluation

The result is evaluated by checking:

- whether the unwanted element is gone;
- whether the background looks continuous;
- whether edges around the mask are visible;
- whether new artifacts appeared;
- whether the style matches the source image.
