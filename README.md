# Battery Management System (BMS) for UBC Subbots

Custom 4-cell LiPo Battery Management System designed for the Subbots AUV.
Supports high-current discharge, full battery protection, and regulated power distribution for onboard electronics.

⸻

## Overview
	•	Designed for a 4S LiPo (Blue Robotics)
	•	Nominal: 14.8 V
	•	Max: 16.8 V
	•	Supports up to 80 A peak discharge
	•	Uses BQ76920 as the analog front end (AFE)
	•	Uses nRF54L15 as the microcontroller
	•	Generates regulated 12 V, 9 V, and 5 V rails using high-current buck regulators
	•	Low-side FET switching for discharge control
	•	Complete cell balancing, protection, and monitoring

⸻

### Battery Front End (BQ76920)
	•	Measures cell voltages (4S)
	•	Computes pack current using a Kelvin-routed shunt resistor
	•	Provides:
	•	Over-current (OCD) protection
	•	Short-circuit (SCD) protection
	•	Under-voltage and over-voltage protection
	•	Temperature monitoring (TS1)
	•	Cell balancing
	•	SRP/SRN routed as Kelvin sense lines with RC filtering
	•	PACK− and BAT− separated through the shunt resistor only

⸻

### Microcontroller (nRF54L15)
	•	Handles I²C communication with BQ76920
	•	ALERT pin used for interrupt-based event handling
	•	SWD interface exposed
	•	Powered from the regulated 3.3 V rail

⸻

## Power Regulation

### LM2596 (Pre-Regulator)
	•	Drops pack voltage down to ~5 V
	•	Used strictly to power the logic and control section of the AOZ2261NQI-11 regulators

### AOZ2261NQI-11 Buck Regulators
	•	Three independent outputs:
	•	12 V rail
	•	9 V rail
	•	5 V rail
	•	Each regulator includes:
	•	Separate AGND and PGND regions
	•	A single-point net tie back to PACK−
	•	Recommended output LC filtering
	•	Local decoupling at the VCC pin
	•	Designed to supply high peak currents needed by the AUV subsystems

⸻

## Grounding Strategy
	•	PACK− is the main high-current ground (GNDPWR)
	•	BAT− exists only for the shunt resistor and BQ76920 sense inputs
	•	AGND islands for analog and control circuits
	•	All grounds meet at a single point via net-tie:
	•	BAT− → shunt → PACK− → global ground reference
	•	Switching grounds (PGND) kept physically isolated from sensitive analog routing

⸻

## Connectors
	•	XT90 for main power input/output
	•	JST-XH for cell balance connector
	•	Multiple JST-XH headers for 12 V, 9 V, and 5 V outputs
	•	External thermistor header
	•	SWD programming header

⸻

## Protection and Sensing
	•	Low-value shunt resistor sized for 80 A peak thresholds
	•	OCD/SCD thresholds configured per BQ76920 limits
	•	RC network around SRP/SRN matches TI application notes
	•	Thermistor network matches BQ76920 TS1 recommendations
	•	Gate drive protection network included for discharge FET

⸻

### PCB Considerations
	•	LX nodes on AOZ regulators kept short and constrained
	•	Feedback resistors routed away from switching nets
	•	BQ76920 input filters placed as close as possible to pins
	•	Shunt resistor placed directly between PACK− and BAT− with symmetric Kelvin routing
	•	Output connectors isolated from analog area
	•	Power planes separated into:
	•	GNDPWR (high-current)
	•	GNDA (AFE, MCU, feedback)
	•	Tied at a single net-tie
