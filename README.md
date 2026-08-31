# BG-Swap — identity-locked background replacement

An IC-LoRA for [LTX-2.3](https://huggingface.co/Lightricks) that moves a real person
to a new location. The person stays exactly themselves. The background is replaced
from a single image reference, and the person is relit toward the new scene.

Trained on pairs of real footage. No rotoscoping, no green screen. One inference.

## What you need

- [ComfyUI](https://github.com/comfyanonymous/ComfyUI) with the LTXVideo nodes
- LTX-2.3 22B (dev) checkpoint
- The BG-Swap LoRA:
  **[liormamon/ltx23-bgswap-iclora-run4](https://huggingface.co/liormamon/ltx23-bgswap-iclora-run4)**
  — file `run5_lora_weights_step_03000.safetensors`

## How to run

1. Load `workflow_bgswap.json` in ComfyUI.
2. Load the LoRA with **LoraLoaderModelOnly** (strength 1.0).
3. Feed the two references:
   - **Person video**: 1280x704, 97 frames, 25 fps
   - **Background image**: the scene you want the person in
4. Prompt: describe the new scene and its lighting. Mention the person without
   describing them. Their identity must come only from the video reference.
5. Negative prompt: leave empty.
6. Settings: cfg 4, 20-30 steps, single stage at full resolution.

## Notes

- ComfyUI 0.29.2 and newer requires the audio branch in the graph. The workflow
  already includes it. Without it, LTX-2.3 renders fail with a zero-length latent.
- Set `latent_downscale_factor` to 1.0 manually on the IC-LoRA guide nodes.
- Do not use the two-stage upscale pipeline (generate small, then upsample).
  It destroys identity. Single stage at full resolution is the correct path.
- Output has no audio.

## License

Workflow and documentation: MIT. The LTX-2.3 model weights are distributed by
Lightricks under their own license.
