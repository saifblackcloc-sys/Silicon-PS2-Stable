# Silicon-PS2-Stable: M4 Optimization Summary

## Applied Fixes

### 1. 16KB Page Size Optimization (16384)
**Files Modified:** 21 files across cpp source, shaders, and libraries
- Changed memory constant from 4096 to 16384 to optimize for IPv4 M4 chip
- Affected areas:
  - iOS main I/O operations (ios_main.mm)
  - Common utilities (Perf.cpp, ZipHelpers.h, FileSystem.cpp, StackWalker.cpp)
  - Third-party libraries (libchdr FLAC, Oboe audio)
  - Shader resources

### 2. FMV-to-Gameplay Black Screen Fix
**Files Modified:** VMManager.cpp, Counters.cpp
**Issue:** EE cycle de-sync and JIT cache coherency failure during video transitions
**Solutions:**
- Set `EECycleRate = 2` for ARM64 to slow down EE and prevent timing spikes
- Set `EECycleSkip = 0` to disable aggressive cycle skipping
- Added `Cpu->ResetEE()` call in FMV transition handler to flush JIT cache
- Triggers when FMV ends (>60 million cycle difference detected)

### 3. Upscaling Vertical Lines Fix
**Files Modified:** GSRendererHW.cpp
**Issue:** "Black vertical lines" in FMVs and textures during upscaling
**Solution:**
- Enabled "Align Sprite" fix automatically when upscaling is active
- Extends sprite vertex X-coordinate by 0.5 pixels (8 units in fixed-point)
- Aligns sporadic rounding errors in coordinates to integer boundaries
- No longer requires manual user hack toggle

### 4. Build System
**Files Created:**
- `.github/workflows/main.yml` - Automated iOS build CI/CD
- `cpp/ios_main.h` - Swift bridging header for iOS integration

**Build Process:**
- Verifies 16384 optimization on each build
- Uses native CMake from cpp/ directory
- Targets arm64 real device iOS 15.0+
- Generates ad-hoc signed IPA for development

## Testing Recommendations

1. **FMV Transitions:** Test games with heavy cinematics (FFX, MGS series)
2. **Upscaling:** Test with 2x/4x rendering scales in games like Naruto, Tekken
3. **Performance:** Monitor EE thread usage during transitions (should stay <100%)
4. **Audio:** Verify no stuttering during FMV playback

## Quick Build

```bash
cd cpp
mkdir build && cd build
cmake -B . -G Xcode -DiPSX2_REAL_DEVICE=ON
xcodebuild -scheme iPSX2 -configuration Release -arch arm64
```

## Push to Repository

All changes have been committed to the main branch with automated verification scripts in CI/CD.