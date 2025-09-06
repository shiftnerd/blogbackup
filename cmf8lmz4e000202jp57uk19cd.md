---
title: "Troubleshooting an Ender 5 After My Move: A Step-by-Step Journey"
datePublished: Sat Sep 06 2025 18:30:18 GMT+0000 (Coordinated Universal Time)
cuid: cmf8lmz4e000202jp57uk19cd
slug: troubleshooting-an-ender-5-after-my-move-a-step-by-step-journey
tags: 3d-printing, troubleshooting, ender-5, klipper

---

Should I have just bought a Bambu….probably, but where’s the fun in that?

Bringing an Ender 5 back to life after years in storage is rarely as simple as powering it on. When I pulled mine out of the basement, I ran into a cascade of issues that ranged from power cuts to firmware headaches to calibration quirks. This guide walks through the problems I encountered and how I solved them, so if you are reviving an older machine, you know what to expect.

If you want to skip ahead and try a known working setup for a stock Ender 5(2020), my [Klipper config for an Ender 5 with an SKR Mini E3 V2 board](https://github.com/shiftnerd/Ender5-SKRMini-e3-v2-Klipper) is available on GitHub.

---

## Step 1: Power Cuts Mid-Print

**Symptom:** The printer would power on and start a job, but shut off suddenly.

**What I Tried:**

* I adjusted the power supply voltage after testing and saw that it was about .3 volts under voltage.
    

**Solution:**  
The issue was a loose power cable going into the control board. Once reseated and tightened, the printer stopped cutting out and held steady through prints.

---

## Step 2: Firmware Flashing and the Switch to Klipper

**Reason for Switching:**  
My old Marlin configuration did not have proper values for the Ender 5, and all the current community suggestions I could find were Klipper-based. Rather than endlessly reflashing Marlin firmware with small tweaks, I chose to move fully to Klipper for flexibility and easier config changes.

**Symptom:** Flashing Klipper onto the SKR Mini E3 V2 was not straightforward. The printer would either refuse to home, lock up, or throw errors. To make it more puzzling, the screen stayed completely blank until the correct firmware was finally loaded, which made it feel like nothing was working.

**What I Tried:**

* Built and flashed firmware multiple times with different settings.
    
* Adjusted stepper driver definitions and microstep values.
    
* Rebuilt configs line by line until they matched my hardware.
    

**Solution:**  
After several failed flashes and config rebuilds, I finally landed on a stable build that behaved the way it should. Once Klipper booted cleanly, the blank screen behavior made sense: it was simply waiting for proper firmware to be loaded. If you are struggling with this step, my [working config](https://github.com/shiftnerd/Ender5-SKRMini-e3-v2-Klipper) is a good starting point.

---

## Step 3: Z-Axis Not Climbing Correctly

**Symptom:** Layers were compressing, as if the printer was moving up less than it should. A 0.2 mm layer height behaved more like 0.1 mm.

**What I Tried:**

* Verified microstep and stepper settings.
    
* Compared actual travel to expected travel with manual moves.
    

**Solution:**  
The rotation distance for the Z-axis was off in the config. Once corrected, the printer moved the proper amount per layer and printed stacked cleanly.

---

## Step 4: BLTouch Deployment Errors

**Symptom:** The BLTouch probe would fail to deploy on the second row of bed mesh probing. Sometimes the bed was so close that the pin could not fully extend.

**What I Tried:**

* Adjusted probe offsets.
    
* Changed mesh boundaries.
    
* Ran multiple probe calibrations.
    

**Solution:**  
Carefully tuning offsets and bed mesh ranges stabilized probing. This prevented the “Move out of range” error and allowed consistent mesh leveling.

---

## Step 5: Stringy Extrusion

**Symptom:** Prints showed excessive stringing and oozing, especially when print speed changed.

**What I Tried:**

* Adjusted retraction settings.
    
* Tested filament path for blockages.
    

**Solution:**  
Fine-tuning **pressure advance** and **smooth time** reduced the stringing significantly. Extrusion became more consistent across different speeds.

---

## Step 6: Ringing on Surfaces

**Symptom:** Test cubes showed ringing artifacts on vertical walls.

**What I Tried:**

* Tested different input shaper types (MZV and EI) and frequencies.
    
* Printed calibration patterns to visually compare results.
    

**Solution:**  
The real breakthrough came from tightening loose frame bolts that had worked themselves free during storage. This reduced resonance dramatically. With the input shaper tuned afterwards, the ringing artifacts were minimized.

---

## Step 7: Acceleration Tradeoff

**Symptom:** Even with the input shaper tuned, some faint ringing persisted.

**What I Tried:**

* Reviewed Klipper logs and configs.
    
* Considered belt tension and pulley alignment.
    

**Solution:**  
I realized the **max\_accel** value was still set at 3000 from before storage. Lowering it reduced ringing, although it meant slightly slower print times. The tradeoff was worth it for smoother walls.

---

## Final Results

By the end of the process, the Ender 5 was running stable and producing clean prints again. The path to get there involved:

* Fixing power delivery by reseating a cable
    
* Flashing and tuning Klipper firmware
    
* Correcting Z-axis rotation distance
    
* Stabilizing BLTouch probing
    
* Tuning extrusion with pressure advance
    
* Tightening the frame and applying the input shaper
    
* Balancing acceleration for quality
    

---

## Key Lessons

* Storage can loosen bolts and connections, so check hardware first.
    
* Moving to Klipper saved time versus reflashing Marlin repeatedly.
    
* A blank screen at first can be normal when flashing firmware.
    
* Do not underestimate mechanical issues. A loose bolt can look like a software problem.
    
* Keep your config versioned and saved. Future you will thank present you. Luckily klipper does this by default.
    

---

Reviving this Ender 5 took longer than expected, but now it is better than ever. If you are working on a similar setup and stuck, my [Ender 5 SKR Mini E3 V2 Klipper config](https://github.com/shiftnerd/Ender5-SKRMini-e3-v2-Klipper) may help get you moving faster.

## Quick Fix Reference

| Symptom | Likely Cause | Fix |  |  |  |
| --- | --- | --- | --- | --- | --- |
| Printer shuts off mid-print | Loose power cable to the board | Reseat and tighten cable |  |  |  |
| The screen stays blank after flashing | Incorrect or missing firmware | Reflash; confirm proper Klipper build |  |  |  |
| Layers are squished or not stacking | Z-axis rotation distance is incorrect | Update rotation distance in config |  |  |  |
| BLTouch fails to deploy on mesh probing | Probe offset or bed too close | Adjust probe offsets and mesh ranges |  |  |  |
| Excessive stringing | Retraction or pressure advance is misconfigured | Tune **pressure advance** and **smooth time** |  |  |  |
| Ringing/ghosting on vertical walls | Loose frame bolts or input shaper not tuned | Tighten bolts, run input shaper calibration |  |  |  |
| Residual faint ringing | Acceleration too high | Lower **max\_accel** for smoother walls |  |  |  |