# CHUNKINS v0.4.0 Windows CPU-path playtest

This report uses the official `feverdream-chunkins-win64.zip` release asset at
tag `v0.4.0` (`55d25a2eb7275e28d61c3178f0bca1b7c6e9dd47`). Its SHA-256 is
`23A2DFD4670F27A1A72C8E6778BC91B38025F2566360797656FC53B4FD3F5551`.

The playtest used the shipped `fd-daemon.exe` and `fd-game.exe` on the real
loopback renderer path, with the built-in automated forward-and-hop gameplay
harness. This was not a manual keyboard/controller session. Each recorded run
required a non-uniform 320x180 framebuffer, a collected relic, a live Lua game
state, and a zero exit code.

## Environment

- Windows 11 Pro 10.0.26200 (build 26200)
- Intel Core i7-1355U, 12 logical processors
- Intel Iris Xe Graphics
- 63.7 GB visible RAM
- CPU renderer path, 320x180 internal framebuffer

## Measured runs

| World | Frames | Renderer result | Score timing | Lives | Result |
| --- | ---: | ---: | --- | ---: | --- |
| 2, Crate Heights | 60 | 1.1 fps | 1 by frame 30 | 3 | real frame, game logic live |
| 4, Thief's Hollow | 60 | 0.9 fps | 1 by frame 30 | 3 | real frame, game logic live |
| 8, The Magpie King | 30 | 2.0 fps | 0 at frame 15, 1 by frame 30 | 4 | real frame, game logic live |

Earlier clean 60-frame passes of worlds 1, 4, and 8 completed in 16.66-17.49
seconds wall-clock (about 3.4-3.6 fps including process startup). The later
logged runs exposed meaningful CPU-load sensitivity down to 0.9 fps.

## Concrete observations

1. **The CPU path is functional but performance is highly load-sensitive.**
   Every logged run produced a non-blank framebuffer and live Lua state, but
   end-to-end throughput varied from 0.9 to 3.6 fps on the same machine. The
   low end makes timing and jump feedback difficult to read. A user-facing
   low-resolution/preset selector and a frame-time warning would make the
   fallback path much less confusing.

2. **The centered chase camera lets characters visually collapse into one
   silhouette.** The world 1 frame shows a second squirrel/character directly
   inside the player's screen-space silhouette. World 8 repeats the problem:
   the Magpie King's dark wings sit directly behind the player, making boss
   distance and contact timing hard to judge. A small lateral camera offset,
   minimum actor separation, or boss outline would improve readability.

3. **Crate Heights communicates the route better than Thief's Hollow.** In
   world 2 the near crates provide an obvious next jump, but the forward-hop
   run still reported `highest stand 0.00`, so the collected relic does not
   prove that raised-platform collision or traversal was exercised. The world
   4 central structure blocks much more of the forward view, and its dark
   opening has little contrast against the character. A checkpoint or route
   marker on the first raised surface would reduce trial-and-error at low fps.

4. **The Magpie King encounter needs stronger depth cues.** The boss frame is
   visually distinctive and world 8 correctly grants four lives, but the
   overlapping boss/player silhouettes make the swoop/bonk distance ambiguous.
   A ground shadow that tracks the boss independently, a brief dazed flash, and
   a camera pullback during swoops would make the three-phase loop readable
   without changing its difficulty.

## What I would want in a future world

A canopy chase built around alternating branch-height routes, with a visible
checkpoint after each major jump and an optional time trial. The route should
force one deliberate camera/readability challenge, but provide high-contrast
landing markers so difficulty comes from movement rather than uncertain depth.

## Evidence

- `world1_gametest.png`
- `world2_gametest.png`
- `world4_gametest.png`
- `world8_gametest.png`
- `world2_stdout_retry.log`
- `world4_stdout.log`
- `world8_stdout.log`

