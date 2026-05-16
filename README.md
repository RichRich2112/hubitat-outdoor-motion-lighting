# hubitat-outdoor-motion-lighting: Front Outdoor Motion Lights with Holiday Override

This repository contains a Hubitat Rule Machine automation designed to intelligently manage front outdoor lighting based on motion, ambient time restrictions, manual overrides, and seasonal holiday lighting.

## Overview

The purpose of this main rule is to brighten front outdoor lights when motion is detected at the driveway or front door during overnight hours, and automatically return them to a dim ambient state (or turn them off) after motion stops. 

![Hubitat Rule Layout](RuleMachine.jpg)

Additionally, it features a built-in seasonal override: if specific Christmas light switches are active, the regular motion automation is suppressed to prevent clashing with your holiday display.

> **Note on Architecture:** This rule *only* handles the transition between ambient levels and full brightness when motion occurs. Baseline scheduling (turning the ambient lights on at dusk and off at dawn) is handled by two very simple companion rules detailed below.

---

## Main Rule Configuration

### Trigger Events
* `Motion Driveway, Motion Front Door any motion reports active`

### Logic Breakdown & Actions to Run

```text
IF (NOT Time between Sunset+15 minutes and Sunrise-11 minutes) Exit Rule
IF (vS Front Lights OVERRIDE is on) Exit Rule
// Toggle this one on when the Christmas lights are up.
IF (Plug XMas Zooz ZEN15, Plug XMas Outdoor GE Black, Plug XMas Indoor Aeon Labs any is on) Exit Rule
// Motion detected, turn on the lights.
IF (Motion Front Door motion is active) THEN
    Dim: Lights Front Down, Lights Front Coach: 100
ELSE
    Dim: Lights Front Coach: 100
END-IF
// Wait 5 minutes for no motion.
Wait for Expression: Motion Driveway, Motion Front Door any motion is inactive --> duration: 0:05:00
// Return to ambient light levels after motion ends.
Off: Lights Front Coach
Dim: Lights Front Down: 20 --> fade: 20
