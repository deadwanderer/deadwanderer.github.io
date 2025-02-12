+++
date = '2025-02-12T12:53:55-05:00'
draft = false
title = 'Odin_test'
+++
## A test

So, what does it look like when we type stuff here?

```odin
package cube

import "core:fmt"
import sdl "vendor:sdl3"

main :: proc() {
	_ = sdl.Init({.VIDEO})
	defer sdl.Quit()

	device := sdl.CreateGPUDevice({.SPIRV, .DXIL}, true, nil)
	defer sdl.DestroyGPUDevice(device)
	window := sdl.CreateWindow("Cube", 1920, 1080, {.RESIZABLE, .HIDDEN})
	defer sdl.DestroyWindow(window)
	success := sdl.ClaimWindowForGPUDevice(device, window)
	defer sdl.ReleaseWindowFromGPUDevice(device, window)

	running := true
	sdl.ShowWindow(window)

	for running {
		ev: sdl.Event
		for sdl.PollEvent(&ev) {
			#partial switch ev.type {
			case .QUIT:
				{
					running = false
				}
			case .KEY_DOWN:
				{
					#partial switch ev.key.scancode {
					case .ESCAPE:
						{
							running = false
						}
					}
				}
			}
		}
```

Next, we'll insert another codeblock.

```odin
		cmdbuf := sdl.AcquireGPUCommandBuffer(device)
		swap_tex: ^sdl.GPUTexture
		if sdl.WaitAndAcquireGPUSwapchainTexture(cmdbuf, window, &swap_tex, nil, nil) {
			render_pass := sdl.BeginGPURenderPass(
				cmdbuf,
				&sdl.GPUColorTargetInfo {
					texture = swap_tex,
					clear_color = {0.2, 0.2, 0.2, 1},
					load_op = .CLEAR,
					store_op = .STORE,
					cycle = false,
				},
				1,
				nil,
			)

			sdl.EndGPURenderPass(render_pass)

		}
		success = sdl.SubmitGPUCommandBuffer(cmdbuf)
	}
}
```