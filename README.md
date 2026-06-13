# glfw-wayland-minecraft

**GLFW fork with native Wayland support for Minecraft** — based on [LWJGL-CI/glfw](https://github.com/LWJGL-CI/glfw) (the same GLFW that Minecraft bundles), with Wayland compatibility patches applied on top.

---

## Compatibility

| Minecraft | LWJGL | Native Wayland |
|-----------|-------|----------------|
| 26.1+ | 3.4.1+ | ✅ via `-DMC_DEBUG_PREFER_WAYLAND` + this lib |
| 1.21.x | 3.3.4+ | ✅ this lib alone |
| 1.18 – 1.20 | 3.3.1+ | ✅ this lib alone |
| 1.14 – 1.17 | 3.2.x | ✅ this lib alone |
| 1.13 | 3.1.6 | ✅ this lib alone |
| 1.12.2 and below | LWJGL2 | ❌ GLFW not used — use XWayland |

---

## Usage

### Minecraft 1.13 – 1.21

Add to **JVM arguments**:
```
-Dorg.lwjgl.glfw.libname=/path/to/libglfw.so
```

### Minecraft 26.1+

Starting from 26.1, Mojang ships their own native Wayland backend (currently behind debug flags). This library is still required to **prevent a startup crash** — without it, 26.1+ will not launch on Wayland at all.

Add to **JVM arguments**:
```
-DMC_DEBUG_ENABLED -DMC_DEBUG_PREFER_WAYLAND -Dorg.lwjgl.glfw.libname=/path/to/libglfw.so
```

> `-DMC_DEBUG_PREFER_WAYLAND` activates Mojang's own Wayland backend. This is separate from GLFW — it is expected to become a stable flag in future releases as Mojang finalizes their implementation.

---

**Prism / MultiMC:** Instance → Edit → Settings → Java → JVM Arguments

**NVIDIA GPU?** Also set in environment:
```
__GL_THREADED_OPTIMIZATIONS=0
```

---

## Applied patches

Based on patches from [BoyOrigin/glfw-wayland](https://github.com/BoyOrigin/glfw-wayland), adapted where needed for the LWJGL-CI base:

| # | Patch | Status | Notes |
|---|-------|--------|-------|
| 1 | Key Modifiers Fix | ✅ Applied | Prevents ghost characters when pressing modifier keys |
| 2 | Fix duplicate scroll events | ⏭ Skipped | Obsolete — LWJGL-CI rewrote scroll handling via `WL_POINTER_FRAME` |
| 3 | Implement glfwSetCursorPos Wayland | ⏭ Skipped | Obsolete — LWJGL-CI implements this via `wp_pointer_warp_v1` |
| 4 | Fix window size on unset fullscreen | ✅ Applied | Correctly fires resize event when leaving fullscreen |
| 5 | Avoid error on startup | ✅ Applied (adapted) | Old Minecraft calls `glfwSetWindowIcon` on startup — converts fatal GLFW error to stderr warning. **Required for 26.1+ to launch at all.** |

---

## Building from source

```bash
# Dependencies
sudo apt-get install -y build-essential cmake ninja-build git curl pkg-config \
    libwayland-dev wayland-protocols libxkbcommon-dev \
    libx11-dev libxrandr-dev libxinerama-dev \
    libxcursor-dev libxi-dev libxext-dev libgl-dev

# Clone
git clone https://github.com/nsydykovich/glfw-wayland-minecraft.git
cd glfw-wayland-minecraft

# Build
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

---

## Based on

- [LWJGL-CI/glfw](https://github.com/LWJGL-CI/glfw) — upstream base (the GLFW Minecraft bundles)
- [BoyOrigin/glfw-wayland](https://github.com/BoyOrigin/glfw-wayland) — original Wayland patch set

---

## Credits

Repository assembled and patches adapted by **[Claude](https://claude.ai)** (Anthropic) together with [@nsydykovich](https://github.com/nsydykovich).
