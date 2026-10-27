# Local RP2040 test setup

Source: maximweb/cad-mouse-mk2, branch `ekf-firmware`, commit `2d22409`.
This is a separate checkout for testing the alternative firmware in upstream PR #56.
The original project and its recordings remain in the adjacent `cad-mouse-mk2` folder.

## Verified build

The RP2040 release build passed on this Apple Silicon Mac. RAM use was
23,876 / 262,144 bytes (9.1%); flash use was 206,048 / 2,027,520 bytes (10.2%),
with 64 KiB reserved for calibration storage. No upload was performed.
PlatformIO VS Code metadata was generated and the compiled USB identity checked.

The initial automatic compiler selection failed to resolve the installed
compiler. The local `platform_packages` override explicitly selects the Apple
Silicon compiler archive; this machine-specific setting is not a portable
upstream firmware change. Build components were platform-raspberrypi
`5d4561a`, Arduino-Pico `fd65f6d4` (1.60100.0), and compiler 5.160100.260719.

## Configuration

- Default PlatformIO environment: `RP2040` (Seeed XIAO RP2040).
- Eight LEDs, original pin assignments, and the author's motion/filter/calibration defaults.
- USB VID `0x256F`, PID `0xC635`, manufacturer `3Dconnexion` and product
  `Seeed Studio XIAO RP2040`, matching the previous working configuration.
- Monitor at 115200 baud with timestamps and file logging.

## First hardware comparison

1. Close the serial monitor in the original VS Code project.
2. In a new VS Code window, open this entire `cad-mouse-mk2-ekf` folder.
3. Select the `RP2040` environment. Build, then use the normal firmware Upload action.
4. Leave the knob untouched during first-start calibration. The firmware stores
   its calibration in LittleFS automatically; a separate filesystem upload is
   not part of this procedure.
5. Normal calibrated operation uses a white ring. Orange indicates running
   without calibration; a red spinner indicates a sensor error. See the full
   LED blink descriptions in `firmware/README.md` if calibration fails.
6. Keep the USB cable pointing away. Check for motion while untouched, then
   gently test right/left, away/toward, lift/press, forward/back tilt,
   sideways tilt, and clockwise/counterclockwise twist in PrusaSlicer.
7. Also try combined translation and rotation. The default isolation setting
   intentionally suppresses smaller secondary axes, which can change blended motion.

This branch does not contain our earlier CSV logger. Its serial debug switches
are off by default, so a quiet monitor after startup is expected. Evaluate the
initial behavior and LED state before enabling extra diagnostics.

## Returning to the original firmware

Close this project's monitor, open the original `cad-mouse-mk2` project,
keep `USE_EXPERIMENTAL_MOTION_CALIBRATION = false`, and upload its normal
`seeed_xiao_rp2040` environment. The source files and recordings in that project
were not replaced by this checkout.

Compiling confirms that the firmware builds; it does not establish improved
motion accuracy or compatibility on the device. A fresh hardware comparison
is still required.
