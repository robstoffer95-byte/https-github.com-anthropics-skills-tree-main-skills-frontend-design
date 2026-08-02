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

## Reference: three.js `Renderer` (WebGPU / WebGL 2)

The unified three.js `Renderer` (`src/renderers/common/Renderer.js`) is the base
class for WebGPU and WebGL 2 backends. Use this as the canonical API reference for
all animation/3D work. Anything driving pixels through a canvas in this project
should go through a `Renderer` configured to satisfy the WSS standards above.

### Construction

`new Renderer(backend, parameters)` — `backend` is the target (WebGPU or WebGL 2);
`parameters` (`Renderer~Options`) configures the default framebuffer.

Key options:

- `alpha` (default `true`) — transparent vs. opaque default framebuffer.
- `depth` (default `true`) / `stencil` (default `false`) — depth/stencil buffers.
- `antialias` (default `false`) — enable MSAA; `samples` overrides sample count
  (4 when `antialias` is on, else `0`).
- `outputBufferType` (default `HalfFloatType`) — keep `HalfFloatType` for best
  quality; `UnsignedByteType` saves memory/bandwidth at a quality cost.
- `logarithmicDepthBuffer` / `reversedDepthBuffer` (default `false`) — depth
  precision strategies.
- `getFallback` — callback providing a fallback backend if the primary can't init.
- `multiview` (default `false`) — use multiview during WebXR when supported.

### Sizing & pixel ratio — enforces "responsive device-pixel rendering"

This is where the WSS **no-upscaling / device-pixel** rule is implemented:

- `setSize(width, height, updateStyle = true)` — set logical-pixel size.
- `setPixelRatio(value = 1)` — set device pixel ratio; pass
  `window.devicePixelRatio` so the drawing buffer matches physical pixels and
  output stays crisp on HiDPI/mobile. Never render small and stretch up.
- `setDrawingBufferSize(width, height, pixelRatio)` — set width/height/ratio at
  once (`bufferSize = logicalSize × pixelRatio`).
- `getSize(target)` — logical size (ignores pixel ratio).
- `getDrawingBufferSize(target)` — physical size (honors pixel ratio).
- `getPixelRatio()` — current pixel ratio.

On every resize, re-apply `setSize` + `setPixelRatio` (or `setDrawingBufferSize`).

### Render loop

- `setAnimationLoop(callback)` — **always** drive animation with this, not a raw
  `requestAnimationFrame`, for correct init and WebXR compatibility. Inside the
  loop the renderer is guaranteed initialized.
- `render(scene, camera)` — render to the default framebuffer or the bound render
  target. Requires an initialized renderer; for on-demand rendering call `init()`
  first.
- `init()` → `Promise<this>` — initialize before rendering outside the loop;
  `initialized` / `hasInitialized()` report status.
- `compileAsync(scene, camera, targetScene)` (aliased by `compile`) — precompile
  materials to avoid first-use shader-compilation stutter. Configure the target
  scene's lighting/environment before calling.
- `dispose()` — free all internal resources when the renderer is retired.

### Clearing

- `autoClear` (default `true`) clears before each `render`; `autoClearColor` /
  `autoClearDepth` / `autoClearStencil` gate individual buffers.
- Manual (ignores `autoClear`): `clear(color, depth, stencil)`,
  `clearColor()`, `clearDepth()`, `clearStencil()`.
- Clear values: `setClearColor(color, alpha = 1)`, `setClearAlpha`,
  `setClearDepth`, `setClearStencil`; getters `getClearColor(target)`,
  `getClearAlpha`, `getClearDepth`, `getClearStencil`.

### Color, tone mapping & output quality

- `outputColorSpace` (default `SRGBColorSpace`), `currentColorSpace`.
- `toneMapping` (default `NoToneMapping`), `toneMappingExposure` (default `1`),
  `currentToneMapping`.
- `samples` / `currentSamples` — MSAA sample counts.
- `needsFrameBufferTarget` / `isOutputTarget` — the renderer allocates an internal
  render target when tone mapping or color-space conversion is needed.

### Render targets & viewport

- `setRenderTarget(target, activeCubeFace = 0, activeMipmapLevel = 0)` /
  `getRenderTarget()` — `null` resets to the default framebuffer (canvas).
- `setOutputRenderTarget` / `getOutputRenderTarget`, `initRenderTarget`.
- `setViewport(x, y, width, height, minDepth = 0, maxDepth = 1)` /
  `getViewport(target)` (logical pixels; `minDepth`/`maxDepth` are WebGPU-only).
- `setScissor(...)` / `getScissor(target)`, `setScissorTest(bool)` /
  `getScissorTest()`.

### Textures, copy & readback

- `initTexture(texture)` — preload/upload a texture to avoid first-render lag.
- `copyFramebufferToTexture(framebufferTexture, rectangle)`.
- `copyTextureToTexture(src, dst, srcRegion, dstPosition, srcLevel, dstLevel)`.
- `readRenderTargetPixelsAsync(renderTarget, x, y, width, height, textureIndex, faceIndex)`.

### Compute (WebGPU)

- `compute(computeNodes, dispatchSize)` / `computeAsync(...)` — run compute
  node(s); `dispatchSize` is a count, `[x, y, z]`, or an indirect attribute.
- `getArrayBufferAsync(attribute, target, offset, count)` — read a storage buffer
  back GPU→CPU.

### Debugging & FPS support — enforces "verify with console + FPS"

- `info` (`Info`) — GPU memory and per-frame render statistics; use it (alongside
  browser FPS tooling) to record the frame-rate measurements the WSS standards
  require.
- `debug`, `inspector`, `shadowMap`, `hasFeature(name)` / `hasCompatibility(name)`,
  `getMaxAnisotropy()`.
- `onDeviceLost`, `onError` — install handlers to surface device-loss and
  uncaptured backend errors in-app instead of only the console.

### Notes

- `coordinateSystem` is backend-dependent (`WebGLCoordinateSystem` vs.
  `WebGPUCoordinateSystem`).
- `highPrecision` uses CPU 64-bit model-view/normal matrices — **not** compatible
  with `InstancedMesh` / `SkinnedMesh`.
- Prefer non-deprecated calls: the `*Async` clear/compile/feature variants and
  `getColorBufferType` are deprecated; use the synchronous forms and
  `getOutputBufferType()`.
- `xr` (`XRManager`) — WebXR entry point.
