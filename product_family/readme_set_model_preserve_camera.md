# set_model_preserve_camera Function Guide

This document explains only the `set_model_preserve_camera(model)` function used in `product_detail_popup.html`.

## Purpose

`set_model_preserve_camera(model)` updates the 3D model with `setModel(...)` while preserving the current camera state.

It is designed to handle SDK timing issues where camera position can drift after model reload.

## Function Signature

```javascript
async function set_model_preserve_camera(model)
```

## Input

- `model` (object): The model payload passed to `v3.main.setModel(model)`.

Example payload:

```javascript
{
  model: "Didsbury Bed 2 Drawer",
  materials: {
    UPHOLSTERY: { code: "SILVER GREY" },
    BEDDING: { code: "BASEBEDDING" },
    LEGS: { code: "CHROME" },
    HEADBOARDBACK: { code: "BASE" },
    PLASTICPARTS: { code: "BASE" }
  }
}
```

## What The Function Does

1. Captures current camera values before switching model:

- `alpha`
- `beta`
- `radius`
- `target.x`
- `target.y`
- `target.z`

2. Runs `setModel(...)` in a queued/serialized flow so updates do not overlap.

3. Restores camera after load in multiple phases:

- on `READY_MODEL`
- immediate timer (`0ms`)
- delayed timers (`350ms`, `900ms`)
- short lock interval for late internal auto-fit behavior

4. Stops late camera forcing if user starts interacting (drag/zoom/touch/wheel), so controls remain responsive.

## Why Queueing Is Important

Rapid model changes can overlap and cause camera restore conflicts.

This function keeps an internal promise queue:

```javascript
set_model_preserve_camera._queue = (
  set_model_preserve_camera._queue || Promise.resolve()
).then(runner, runner);
```

This ensures one model switch completes before the next one starts.

## Internal State Used By The Function

The function stores internal state on itself:

- `_cache`: last known camera snapshot
- `_queue`: promise chain for serialized updates
- `_lock_timer`: interval id for temporary stabilization
- `_mark_user_input`: input callback
- `_last_input_at`: latest user interaction timestamp
- `_input_bound`: avoids re-binding event listeners

## Usage

Call this function instead of direct `v3.main.setModel(...)`:

```javascript
set_model_preserve_camera(model);
```

Current usage in this sample is inside `global_model_status.update_model()`.

## Notes

- This function is intentionally defensive because model load and camera reset timings can differ per model.
- If your SDK/build behaves differently, tune delay/lock timings inside this function.
- Keep this as the single model-switch entry point to avoid bypassing camera preservation.

## Troubleshooting

If camera still jumps occasionally:

1. Increase lock duration slightly.
2. Increase the late restore delay (for example from `900ms` to `1100ms`).
3. Verify all model switches use only `set_model_preserve_camera(...)`.
4. Avoid direct `v3.main.setModel(...)` in other code paths.
