# Meson appleframeworks iOS sysroot bug

## Problem

When cross-compiling for iOS using `appleframeworks` dependency, Meson injects `-F` flags pointing to the macOS SDK instead of respecting the iOS sysroot specified in `c_args`/`objc_args`. This causes the build to link against macOS frameworks instead of iOS frameworks.

## Reproducing the bug

1. Ensure you're on macOS with Xcode installed
2. Run:
   ```bash
   meson setup build --cross-file ios.cross
   ```
3. Inspect `build/build.ninja` and look for the `FRAMEWORK_PATH` or `-F` flags in the Foundation dependency compilation
4. Observe that `-F /Applications/Xcode.app/.../MacOSX.sdk/...` appears before the iOS sysroot, overriding it

## Expected behavior

The `-F` framework search paths should either:
- Not be injected by appleframeworks (let the sysroot handle framework discovery)
- Be iOS SDK paths, not macOS SDK paths
- Respect the sysroot already specified in the compiler flags

## Files

- `meson.build` - Minimal Meson project using appleframeworks
- `main.m` - Minimal Objective-C program
- `ios.cross` - iOS cross-compilation configuration
