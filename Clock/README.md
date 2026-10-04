# Clock Module

## Overview

The clock module is responsible for generating and controlling the timing signals used by the CPU.
A CPU contains multiple units that need to operate in a coordinated sequence. The clock provides timing pulses that allow these units to perform their operations at the appropriate time and in the required order.
I built the clock module first because the remaining CPU modules will eventually depend on the clock for synchronized operation and testing.
This module is primarily based on the clock section of **Ben Eater's 8-bit breadboard computer**. I followed his design closely while implementing the circuit myself, with the goal of understanding why the components are connected in the way they are rather than simply reproducing the wiring.
---

## Objective

The main objective of this module was not only to generate a clock signal, but also to understand the circuits involved in generating and controlling that signal.
Through this module, I wanted to understand:

- How a 555 timer can be used for timing and pulse generation
- How resistor-capacitor networks affect timing
- How astable and monostable configurations behave
- How bistable switching can be used for clock control
- How automatic and manual clock operation can be selected
- How the clock will eventually synchronize the different units of the CPU

---

## Reference Design

This module is primarily based on **Ben Eater's 8-bit breadboard computer**.
I followed the overall clock design and implementation shown in his videos, but the physical construction, testing, debugging and observations documented here are from my own build.
While building the circuit, I focused on understanding why components were connected to particular pins and points in the circuit. I also used IC datasheets to understand pin functions and the purpose of individual connections.
A minor modification was made during the astable stage because the potentiometer I had was not behaving reliably. The modification is documented below.

---

## Module Structure

The clock module was developed in three main stages:

```text
Astable 555 Timer
        ↓
Monostable 555 Timer
        ↓
Clock Control / Bistable Stage
        ↓
Automatic or Manual Clock Output
```

Each stage was built and tested separately before being combined into the completed clock module.

---

## Circuit Schematic

The following schematic represents the overall clock-module design used as the basis for my physical implementation.

![Clock Module Schematic](images/clock_module_schematic.png)

---

## Components Used

| Component | Value / Type |
|---|---|
| 555 Timer | NE555 |
| Resistors | 1 MΩ, 330 kΩ, 10 kΩ, 1 kΩ |
| Ceramic Capacitors | 0.01 µF (103), 0.1 µF (104) |
| Electrolytic Capacitor | 1 µF |
| Logic Gates | AND, OR, NOT gates |
| LEDs | Visual indication of circuit states and pulses |
| Switches | Manual triggering and clock-mode control |
| Breadboards | Circuit implementation |
| Power Supply | 5 V breadboard power supply module |

---

# Implementation

## 1. Astable 555 Timer

The first stage of the clock module was an astable 555 timer.

In this configuration, the NE555 continuously switches between its output states, producing a repeating pulse. In my implementation, the output was observed using an LED so that the generated timing could be seen directly.

### My Implementation

I used an **NE555 timer** with a 5 V supply.

The timing section uses resistors and a capacitor to determine the oscillation rate. I observed that changing the resistance or capacitance changes the timing behavior of the circuit.

The circuit helped me understand the practical effect of the RC time constant:

```text
τ = R × C
```

The resistor and capacitor values affect the charging and discharging time of the capacitor and therefore influence the timing of the output pulses.

### Observation

The output LED continuously blinked at a particular interval, providing a visual indication of the generated clock pulses.

### Modification: Potentiometer

The reference implementation uses a potentiometer for adjustable timing.

The potentiometer I used did not behave reliably. Its resistance did not remain stable at a selected position, and the measured value kept changing.

Because of this, I removed the potentiometer and continued the circuit using fixed resistors. This removed the variable adjustment capability, but the clock-generation function remained usable.

### Video

[Watch the Astable 555 Timer working](videos/astable_working.mp4)

---

## 2. Monostable 555 Timer

The second stage uses the NE555 timer in a monostable configuration.

Unlike the astable stage, which continuously generates pulses, the monostable configuration can be triggered to produce a controlled output pulse.

### My Implementation

A switch is used to trigger the circuit manually.

When triggered, the output changes state for a period determined by the timing components before returning to its normal state. This provides a manually controlled pulse that is useful for testing and later debugging the CPU one clock transition at a time.

### Switch Debouncing

While working with the manual trigger, I also became aware of **switch bouncing**.

A mechanical switch can produce multiple rapid electrical transitions during one press. In a clock circuit, this can potentially result in unwanted additional pulses.

This is especially important during debugging because a single intended clock step should ideally correspond to a single clean transition.

### Debugging: Incorrect Resistor Value

Initially, I did not have the required **1 MΩ resistor**, so I temporarily used a **10 kΩ resistor**.

The resulting output was much shorter than expected, and the LED appeared to turn on and off almost immediately.

To investigate the problem, I recreated the circuit in **Tinkercad** and experimented with the component values.

This helped me understand how strongly the RC timing network affects the output pulse duration and why using a much smaller resistance changes the timing significantly.

No photograph was recorded during the construction of this stage.

---

## 3. Clock Control / Bistable Stage

The final stage provides control over how the clock operates.

My implementation uses an NE555 timer together with AND, OR and NOT logic gates and a toggle switch to control the clock path.

### Operating Modes

The completed clock module provides two main operating modes:

**Automatic mode**

The clock can continuously generate pulses for normal CPU operation.

**Manual mode**

Individual clock pulses can be controlled manually, which is useful when testing and debugging the CPU.

### Purpose

The ability to switch between automatic and manual operation will be important once the other CPU modules are connected.

During normal operation, the CPU can run using continuous clock pulses. During development and debugging, manual clocking allows individual transitions to be observed step by step.

### Debugging: Missing Power Connection Between Breadboards

While building this stage, I initially failed to pass the required power connection to another breadboard.

This resulted in unexpected LED behavior and an additional light indication during the clock pulses.

After checking the power connections and correcting the missing connection, the unexpected behavior was resolved.

### Implementation Photos

![Bistable Clock Module Overview](images/bistable_clock_module_overview.png)

![Bistable Clock Module Close-up](images/bistable_clock_module_closeup.jpg)

### Video

[Watch the Bistable / Clock Control stage working](videos/bistable_clock_working.mp4)

---

# Testing & Verification

I tested the different stages separately before integrating them into the complete clock module.

Since I do not currently have an oscilloscope, I used the tools available to me, particularly a **multimeter**, together with LEDs and direct observation of the circuit behavior.

### Astable Stage

The astable stage was tested by observing the continuous blinking of the output LED. The repeated switching demonstrated that the circuit was generating a continuous timing signal.

### Monostable Stage

The monostable stage was tested using the manual trigger switch. The output pulse and its duration were observed through the LED, and the effect of resistor and capacitor values was also investigated through Tinkercad simulation.

### Clock Control Stage

The final stage was tested by switching between the available automatic and manual operating modes and observing the resulting output behavior.

---

# Problems & Debugging

Building the clock module gave me several opportunities to practice practical hardware debugging.

## 1. Faulty USB Port on Power Supply Module

At the beginning of the project, the USB input of my breadboard power supply module was not supplying power to the circuit.

Initially, I considered several possible causes, including:

- A problem with my laptop's USB-C port
- A problem with the USB-A to USB-C cable
- A possible short circuit in the circuit
- A fault in the power supply module itself

I used a multimeter to check the power connections and investigate whether the expected electrical connections were present.

After further testing, I used the DC barrel jack on the same power supply module. The barrel jack worked correctly, which showed that the module itself was functioning but the USB-A input was faulty.

I therefore switched to the barrel jack and the problem was resolved.

### Lesson Learned

This was one of my first practical experiences of isolating a hardware fault by testing different parts of the system instead of assuming that the problem was in my circuit.

### Photo

![Power Supply Module](images/power_supply_module_usb_issue.jpg)

---

## 2. Unstable Potentiometer

The potentiometer used in the astable circuit was not behaving reliably. Its resistance was continuously changing instead of remaining stable at the selected position.

Because this prevented reliable control of the pulse rate, I removed the potentiometer and used fixed resistors.

### Lesson Learned

A component itself can be the source of a circuit problem even when the circuit connections are correct. Checking individual components is therefore an important part of hardware debugging.

---

## 3. Incorrect Resistor in Monostable Circuit

I initially did not have the required 1 MΩ resistor and temporarily used a 10 kΩ resistor.

The resulting output pulse was much shorter than expected, causing the LED to turn on and off almost immediately.

I recreated the circuit in Tinkercad and experimented with the component values. This helped me understand how the RC timing network affects the output pulse duration.

### Lesson Learned

The values of resistors and capacitors directly determine the timing behavior of the circuit. Component values are therefore an essential part of circuit design rather than arbitrary values.

---

## 4. Missing Power Connection Between Breadboards

While building the clock-control stage, I initially did not connect the power supply correctly to another breadboard.

This produced unexpected LED behavior. After checking the power distribution and correcting the missing connection, the circuit behaved as expected.

### Lesson Learned

When multiple breadboards are interconnected, power and ground distribution must be checked just as carefully as signal connections.

---

# What I Learned

Before beginning this project, I had very little practical understanding of the 555 timer.

Building the clock module gave me hands-on experience with several concepts that I had previously encountered only theoretically.

Through this module, I learned about:

- Practical operation of the NE555 timer
- IC pin configurations and datasheets
- Astable and monostable configurations
- Bistable switching and clock control
- Capacitor charging and discharging
- RC time constants
- The effect of resistor and capacitor values on timing
- AND, OR and NOT logic gates
- SR latch behavior
- Breadboard wiring
- Power distribution
- Hardware debugging using a multimeter

One of the most useful outcomes was gaining a more practical understanding of the **RC time constant**. Building the circuits showed me how capacitor charging and discharging actually affects the output of a physical circuit.

The project also helped me understand the working of an **SR latch** in a more refined way.

---

# Connection With Digital Electronics

I built this module while studying **Digital Electronics** during my second year.

The concepts from the course and the practical work on this project reinforced each other.

Topics such as:

- Logic gates
- SR latches
- Flip-flops
- K-maps
- IC internal structure
- Datasheets
- Digital logic

became easier to understand when I could see their role in an actual hardware system.

At the same time, working on the physical circuit gave me better intuition for concepts such as timing, switching and the behavior of real components.

This connection between theoretical study and physical implementation is one of the most valuable aspects of the project for me.

---

# Development Timeline

I started working on the clock module at the **end of July 2026** and completed it during **September 2026**.

The development progressed in the following order:

```text
Astable 555
      ↓
Monostable 555
      ↓
Clock Control / Bistable Stage
      ↓
Completed Clock Module
```

The **monostable stage took the most time**, while the **astable stage was the easiest** for me.

---

# Final Implementation

The completed clock module allows the CPU clock to operate in both automatic and manual modes.

Automatic operation will be useful when running the CPU continuously.

Manual operation will be particularly useful later when debugging the CPU, because individual clock pulses can be generated and the behavior of the different modules can be observed step by step.

### Final Photographs

![Completed Clock Module - Overview](images/bistable_clock_module_overview.png)

![Completed Clock Module - Close-up](images/bistable_clock_module_closeup.jpg)

### Demonstrations

- [Astable 555 Timer working](videos/astable_working.mp4)
- [Bistable / Clock Control stage working](videos/bistable_clock_working.mp4)

---

# Reference

This module is primarily based on the clock section of **Ben Eater's 8-bit breadboard computer** and his accompanying video series.

Ben Eater's work was used as the primary reference for understanding the architecture and implementation of the clock module.

The physical construction, testing, modifications, debugging experiences and observations documented here are from my own implementation.

