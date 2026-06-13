# glfw-wayland-minecraft

**GLFW fork with native Wayland support for Minecraft** — based on [LWJGL-CI/glfw](https://github.com/LWJGL-CI/glfw) (the same GLFW that Minecraft bundles), with Wayland compatibility patches applied on top.

Minecraft works on Wayland via XWayland out of the box, but this fork gives you **true native Wayland** — better mouse input, no XWayland overhead, proper Wayland protocols.

---

## Compatibility

| Minecraft | LWJGL | Status |
|-----------|-------|--------|
| 1.21+ | 3.3.4+ | ✅ Native target |
| 1.18 – 1.20 | 3.3.1+ | ✅ |
| 1.14 – 1.17 | 3.2.x | ✅ |
| **1.13** | 3.1.6 | ✅ Oldest supported |
| 1.12.2 and below | LWJGL2 | ❌ GLFW not used at all — use XWayland |

> Pre-1.13 Minecraft uses LWJGL2 which has no GLFW. For those versions, XWayland is the only option.

---

## Usage

Add to your **JVM arguments** in your launcher (Prism, MultiMC, ATLauncher, etc.):

```
-Dorg.lwjgl.glfw.libname=/path/to/libglfw.so
```

**Prism Launcher:** Instance → Edit → Settings → Java → JVM Arguments

**NVIDIA GPU?** Also set in your environment:
```
__GL_THREADED_OPTIMIZATIONS=0
```

To verify Wayland is active, add `-Dorg.lwjgl.util.Debug=true` and check the launch log.

---

## Applied patches

Based on patches from [BoyOrigin/glfw-wayland](https://github.com/BoyOrigin/glfw-wayland), adapted where needed for the LWJGL-CI base:

| # | Patch | Status | Notes |
|---|-------|--------|-------|
| 1 | Key Modifiers Fix | ✅ Applied | Prevents ghost characters when pressing modifier keys on Wayland |
| 2 | Fix duplicate scroll events | ⏭ Skipped | Obsolete — LWJGL-CI already rewrote scroll handling via `WL_POINTER_FRAME` |
| 3 | Implement glfwSetCursorPos Wayland | ⏭ Skipped | Obsolete — LWJGL-CI implements this better via `wp_pointer_warp_v1` |
| 4 | Fix window size on unset fullscreen | ✅ Applied | Correctly fires resize event when leaving fullscreen |
| 5 | Avoid error on startup | ✅ Applied (adapted) | Old Minecraft calls `glfwSetWindowIcon` on startup — converts fatal GLFW error to a stderr warning so it doesn't crash |

---

## Building from source

**Requires:** Ubuntu/Debian with a Wayland compositor.

```bash
# 1. Dependencies
sudo apt-get install -y build-essential cmake ninja-build git curl pkg-config \
    libwayland-dev wayland-protocols libxkbcommon-dev \
    libx11-dev libxrandr-dev libxinerama-dev \
    libxcursor-dev libxi-dev libxext-dev libgl-dev

# 2. Clone
git clone https://github.com/nsydykovich/glfw-wayland-minecraft.git
cd glfw-wayland-minecraft

# 3. Build
mkdir build && cd build
cmake .. \
    -GNinja \
    -DCMAKE_BUILD_TYPE=Release \
    -DBUILD_SHARED_LIBS=ON \
    -DGLFW_BUILD_WAYLAND=ON \
    -DGLFW_BUILD_X11=ON \
    -DGLFW_BUILD_EXAMPLES=OFF \
    -DGLFW_BUILD_TESTS=OFF \
    -DGLFW_BUILD_DOCS=OFF
ninja

# Result: build/src/libglfw.so
```

Both **Wayland and X11** backends are compiled in — the library auto-selects based on your active session at runtime.

---

## Based on

- [LWJGL-CI/glfw](https://github.com/LWJGL-CI/glfw) — upstream base (the GLFW version Minecraft bundles)
- [BoyOrigin/glfw-wayland](https://github.com/BoyOrigin/glfw-wayland) — original Wayland patch set

---

## Credits

Repository assembled and patches adapted by **[Claude](https://claude.ai)** (Anthropic) together with [@nsydykovich](https://github.com/nsydykovich).
