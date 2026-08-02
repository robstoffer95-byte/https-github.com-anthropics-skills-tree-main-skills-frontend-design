# CLAUDE.md

Project guidance for Claude Code when working in this repository.

## WSS Animation Standards

These standards govern all animation and canvas/WebGL work in this project. Follow
them for every visual/animation change — do not deviate without explicit approval.

### Rendering quality

- **Never upscale a low-resolution canvas or texture.** Render at the source
  resolution or higher; never stretch a small buffer to fill a larger display.
- **Use responsive device-pixel rendering.** Size canvases to
  `clientWidth/Height × window.devicePixelRatio` and reset the transform on
  resize so output stays crisp on HiDPI and mobile screens.

### Testing

- **Test every animation at 1920×1080 and at mobile width.** Both viewports must
  be verified before a change is considered done.
- **Verify animations with screenshots, console checks, and FPS measurements.**
  - Capture screenshots at each tested viewport.
  - Check the browser console for errors and warnings.
  - Measure and record frame rate (target smooth 60fps).

### Art direction

- **Premium, cinematic streetwear direction.** Keep the aesthetic elevated and
  intentional.
- **No generic neon, random surfboards, or cartoon visuals.** Avoid clichéd or
  off-brand imagery.
- **Preserve established branding and layout.** Keep existing brand identity,
  color, type, and structural layout intact.

### Process

- **Diagnose the existing implementation before replacing it.** Understand the
  current approach and why it behaves as it does before rewriting or swapping it.
