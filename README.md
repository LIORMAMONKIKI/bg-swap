# BG-Swap — identity-locked background replacement

An IC-LoRA for [LTX-2.3](https://huggingface.co/Lightricks) that moves a real person
to a new location. The person stays exactly themselves. The background is replaced
from a single image reference, and the person is relit toward the new scene.

Trained on pairs of real footage. No rotoscoping, no green screen. One inference.

Project page with results and method: https://liormamon.com/ic-lora/

## What you need

- [ComfyUI](https://github.com/comfyanonymous/ComfyUI) with the LTXVideo nodes and VideoHelperSuite
- LTX-2.3 22B (dev) checkpoint `ltx-2.3-22b-dev.safetensors` and the Gemma 3 12B text encoder
- Lightricks' distilled LoRA `ltx-2.3-22b-distilled-lora-384-1.1.safetensors` (from the LTX-2.3 release)
- The BG-Swap LoRA:
  **[liormamon/ltx23-bgswap-iclora-run4](https://huggingface.co/liormamon/ltx23-bgswap-iclora-run4)**
  — file `run5_lora_weights_step_03000.safetensors`

## How to run

1. Load `workflow_bgswap.json` in ComfyUI (API format; it is Lightricks' official
   `LTX-2.3_V2V_ICLoRA_Single_Stage_Distilled` template with a second reference guide for the background image).
2. The LoRA chain is already wired: dev checkpoint → distilled LoRA at 0.5 → BG-Swap LoRA at 1.0, both via **LoraLoaderModelOnly**.
3. Feed the two references:
   - **Person video**: 1280x704, 97 frames, 25 fps
   - **Background image**: the scene you want the person in
4. Prompt: describe the new scene and its lighting. Mention the person without
   describing them. Their identity must come only from the video reference.
5. Negative prompt: leave empty.
6. Settings (already in the file, from the official recipe): cfg 1, `euler_ancestral_cfg_pp`, the 8 distilled sigmas
   `1.0, 0.99375, 0.9875, 0.98125, 0.975, 0.909375, 0.725, 0.421875, 0.0`, single stage at full resolution 1280x704x97, seed 42.
   Do not raise cfg above 1: with cfg 4 the person flickers (measured 26 see-through frames vs 0).

## Notes

- ComfyUI 0.29.2 and newer requires the audio branch in the graph. The workflow
  already includes it. Without it, LTX-2.3 renders fail with a zero-length latent.
- Set `latent_downscale_factor` to 1.0 manually on both IC-LoRA guide nodes (the curated IC-LoRA loader cannot load a custom LoRA, so the plain loader is used and this value is not set for you).
- Do not use the two-stage upscale pipeline (generate small, then upsample).
  It destroys identity. Single stage at full resolution is the correct path.
- Output has no audio.

## License

Workflow and documentation: MIT. The LTX-2.3 model weights are distributed by
Lightricks under their own license.
