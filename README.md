# Photoplethysmography (PPG) Signal Acquisition and Conditioning

## Project Overview

This project presents the design, simulation, and analysis of a Photoplethysmography (PPG) signal acquisition and conditioning system.

The system was developed for the BM3110/EN3533 Electronic Instrumentation course at the University of Moratuwa.

The complete system was designed and simulated using LTspice and consists of:

- LED optical emitter and current driver
- Photodiode signal model
- Transimpedance amplifier (TIA)
- High-pass filtering
- Signal amplification
- Low-pass filtering
- PI feedback controller
- Conditioned PPG output

## Author

Samiru Nuwanaka

## Objectives

The main objectives of this project are:

- To understand the operating principle of a PPG system.
- To design a circuit capable of detecting optical variations associated with blood-volume changes.
- To convert photodiode current into a measurable voltage.
- To amplify the small pulsatile component.
- To remove the large DC component and baseline variations.
- To attenuate high-frequency noise.
- To simulate the complete system using LTspice.
- To analyse waveforms at different stages of the circuit.
- To implement PI feedback for automatic LED-current regulation.
- To consider practical PCB implementation issues.

## System Architecture

    LED Driver → Photodiode Model → TIA → High-Pass Filter
                                          ↓
                                  Signal Amplifier
                                          ↓
                                    PI Controller
                                          ↓
                                      PPG Output
                                          ↓
                                    LED Feedback

## Main Components

| Component | Value / Type | Function |
|---|---|---|
| LED | NSCW100 White LED | Optical illumination |
| MOSFET | BSS123 | LED current switching |
| Operational Amplifier | TS912 | Amplification, buffering, filtering and PI control |
| TIA Feedback Resistor | 10 kΩ | Current-to-voltage conversion |
| TIA Feedback Capacitor | 1 µF | Low-pass filtering |
| AC Coupling Capacitor | 10 µF | DC blocking |
| Bias Resistor | 1 MΩ | DC biasing |
| Receiver Amplifier Gain | 11 | Pulsatile signal amplification |
| Integrated Amplifier Gain | 8 | Signal amplification |
| PI Proportional Gain | -0.47 | Proportional correction |
| PI Integrator | 1 MΩ + 470 nF | Slow DC correction |
| Supply | +5 V / +2.5 V | Circuit power and bias |

## Circuit Stages

### 1. LED Emitter

The LED is driven using a TS912 op-amp and BSS123 N-channel MOSFET.

A 10 Ω sense resistor is used to monitor the LED current.

The simulated LED current varies approximately between:

- 12 mA at the low input level
- 40 mA at the high input level

The LED drive signal uses a 1-second period.

### 2. Photodiode Model

The photodiode output is modelled using three current sources:

- `I1 = PULSE(0, 30 µA, 1 s, 5 s, 10 s)`
- `I2 = SINE(0, 10 µA, 100 Hz)`
- `I3 = PULSE(0, 10 µA, 0, 1 µs, 1 µs, 100 ms, 1 s)`

These represent:

- **I1:** DC / slowly varying optical component
- **I2:** 100 Hz interference
- **I3:** Cardiac pulsatile component

The cardiac component has a frequency of:

`1 Hz = 60 BPM`

### 3. Transimpedance Amplifier

The TIA converts the photodiode current into voltage.

The approximate relationship is:

`Vout = -IPD × Rf`

For:

`Rf = 10 kΩ`

the conversion gain is approximately:

`10 mV / µA`

The feedback capacitor is:

`Cf = 1 µF`

The approximate upper cutoff frequency is:

`fH = 1 / (2πRfCf)`

`fH ≈ 15.9 Hz`

This helps attenuate the modelled 100 Hz interference.

### 4. High-Pass Filter

The receiver uses a 10 µF AC coupling capacitor and a 1 MΩ bias resistor.

The lower cutoff frequency is:

`fL = 1 / (2πRC)`

`fL ≈ 0.016 Hz`

This is well below the 1 Hz cardiac signal, allowing the pulsatile PPG component to pass while removing DC and very slow baseline variations.

For the integrated sensor stage, a 1 µF coupling capacitor gives:

`fL ≈ 0.159 Hz`

### 5. Non-Inverting Amplifier

The standalone receiver amplifier uses:

`Rf = 10 kΩ`

`Rg = 1 kΩ`

Therefore:

`Av = 1 + Rf/Rg`

`Av = 1 + 10k/1k`

`Av = 11`

The simulated gain is approximately 11.1, which agrees closely with the theoretical value.

### 6. PI Controller

A proportional-integral controller is included to automatically regulate the LED drive and maintain a stable DC operating point.

The proportional gain is:

`KP = -470 kΩ / 1 MΩ`

`KP = -0.47`

The integrator consists of:

`R = 1 MΩ`

`C = 470 nF`

Therefore:

`τ = RC`

`τ = 0.47 s`

The PI controller provides:

- DC operating-point stabilisation
- Compensation for slow optical variations
- Reduced baseline drift
- Automatic LED-current correction

## Simulation Results

### Final PPG Output

| Parameter | Result |
|---|---:|
| Maximum voltage | 2.53 V |
| Minimum voltage | 2.43 V |
| Peak-to-peak amplitude | 100 mV |
| DC bias | 2.50 V |
| Pulse frequency | 1 Hz |
| Pulse period | 1 s |
| Estimated heart rate | 60 BPM |
| Modelled noise | 100 Hz |

The heart rate is calculated from:

`HR = 60 / T`

For a pulse period of 1 second:

`HR = 60 / 1`

`HR = 60 BPM`

## Filter Characteristics

The approximate signal-conditioning bandwidth is:

`0.016 Hz < f < 15.9 Hz`

for the standalone receiver.

| Frequency | Purpose |
|---:|---|
| 0.016 Hz | Lower cutoff |
| 1 Hz | Modelled cardiac pulse |
| 15.9 Hz | Upper cutoff |
| 100 Hz | Modelled interference |

The theoretical attenuation at 100 Hz is approximately:

`Attenuation = 20 log10(15.9 / 100)`

`Attenuation ≈ -16 dB`

## Key Results

### Emitter Stage

`LED current ≈ 12 mA to 40 mA`

### TIA Stage

`fH ≈ 15.9 Hz`

### Amplifier Stage

Theoretical:

`Av = 11`

Simulated:

`Av ≈ 11.1`

### Integrated Sensor Output

`Vmin ≈ -15 mV`

`Vmax ≈ +15 mV`

`Vpp ≈ 30 mV`

### Final PI-Controlled Output

`Vmax ≈ 2.53 V`

`Vmin ≈ 2.43 V`

`VDC ≈ 2.50 V`

`Vpp ≈ 100 mV`

Modelled pulse frequency:

`1 Hz`

Corresponding heart rate:

`60 BPM`

## Theoretical vs Simulated Values

| Parameter | Theoretical | Simulated | Agreement |
|---|---:|---:|---|
| Non-inverting gain | 11 | ≈11.1 | Excellent |
| Integrated gain | 8 | ≈8 | Excellent |
| PI output gain | 11 | ≈11 | Excellent |
| Receiver HPF cutoff | 0.016 Hz | ≈0.016 Hz | Excellent |
| Integrated HPF cutoff | 0.159 Hz | ≈0.159 Hz | Excellent |
| TIA LPF cutoff | 15.9 Hz | ≈15.9 Hz | Excellent |
| PI proportional gain | -0.47 | ≈-0.47 | Excellent |
| PI integrator time constant | 0.47 s | ≈0.47 s | Excellent |

## Noise Rejection

The simulated receiver contains a 100 Hz interference component.

The TIA low-pass characteristic has an upper cutoff of approximately 15.9 Hz.

The theoretical attenuation at 100 Hz is:

`Attenuation = 20 log10(15.9 / 100)`

`Attenuation ≈ -16 dB`

The filtering therefore reduces high-frequency interference while preserving the 1 Hz cardiac component.

## PI Controller Behaviour

The PI controller maintains a stable operating point by correcting slow changes in the LED/sensor system.

### Controller Parameters

`Proportional gain:`

`KP = -0.47`

`Integrator:`

`R = 1 MΩ`

`C = 470 nF`

`Integrator time constant:`

`τ = 0.47 s`

The feedback output settles to approximately:

`5.0702 V`

The feedback input remains close to:

`1.2899 V`

with a small residual AC component.

This indicates that the controller reaches a steady-state correction during the simulation.

## LTspice Simulation

The transient simulation command used was:

`.tran 0 1s 0 100m`

The following signals were analysed:

- LED current
- MOSFET gate voltage
- TIA output
- Non-inverting amplifier output
- Receiver integrator output
- Integrated emitter-sensor output
- Final PPG output
- PI controller feedback output
- PI controller feedback input

## Repository Structure

    PPG-Signal-Acquisition-and-Conditioning/
    │
    ├── README.md
    │
    ├── LTspice/
    │   ├── Emitter/
    │   │   └── emitter.asc
    │   ├── Receiver/
    │   │   └── receiver.asc
    │   ├── Emitter_with_Sensor/
    │   │   └── emitter_sensor.asc
    │   └── PI_Controller/
    │       └── ppg_pi_controller.asc
    │
    ├── Figures/
    │   ├── campus_logo.png
    │   ├── absorbance_spectrum.png
    │   ├── Basic block diagram.png
    │   ├── The picture of Emitter.png
    │   ├── The LTC Spice model of the receiver.png
    │   ├── The PI Controller.png
    │   └── Output.png
    │
    ├── Results/
    │   ├── Emitter/
    │   ├── Receiver/
    │   ├── Emitter_with_Sensor/
    │   └── PI_Controller/
    │
    └── Report/
        └── PPG_Report.tex

## Practical PCB Considerations

### Component Placement

The photodetector section carries a relatively small signal and should be located close to the first amplification stage.

Long PCB traces should be avoided because they can introduce:

- Additional noise
- Parasitic capacitance
- Electromagnetic interference

### Power Supply Noise

The operational amplifiers should have suitable supply-decoupling capacitors placed close to their supply pins.

Both low-frequency and high-frequency supply noise should be considered.

### Grounding

A suitable grounding strategy is important because the PPG signal can be significantly smaller than surrounding interference.

Large current paths and noisy switching currents should be kept away from sensitive analogue-ground regions.

### Ambient Light

The photodetector can respond to unwanted ambient light.

Possible solutions include:

- Optical shielding
- Black enclosure around the sensor
- Proper sensor positioning
- Ambient-light cancellation

### Motion Artefacts

Movement of the sensor relative to the skin can introduce significant artefacts.

The sensor should therefore be mechanically stabilised while avoiding excessive pressure on the measurement site.

### PCB Layout

Sensitive analogue traces should be:

- Short
- Direct
- Away from digital signals
- Away from switching power supplies
- Away from high-current LED paths

### Component Tolerances

Actual cutoff frequencies depend on resistor and capacitor tolerances.

Appropriate component tolerances should therefore be selected when accurate filter characteristics are required.

## Limitations

The simulated circuit does not fully reproduce all effects present in a real PPG measurement.

The main limitations are:

- The photodiode is modelled using ideal current sources.
- Photodiode dark current is not included.
- Photodiode junction capacitance is not included.
- Temperature effects are not included.
- Skin optical properties are simplified.
- Motion artefacts are not realistically modelled.
- Ambient-light variations are simplified.
- PCB parasitic effects are not included.
- Real component tolerances are not fully represented.

The LTspice simulation also indicated convergence warnings related to floating nodes and source-stepping issues.

Therefore, successful simulation does not guarantee identical performance in hardware.

Experimental validation is required after PCB implementation.

## Future Improvements

Possible improvements include:

- Higher-order active band-pass filtering
- Improved 50/100 Hz interference rejection
- Low-noise transimpedance amplification
- Automatic gain control
- Ambient-light cancellation
- Motion-artefact rejection
- Digital filtering
- Real photodiode SPICE modelling
- Hardware prototype development
- PCB implementation
- Experimental heart-rate measurement
- Comparison with a commercial PPG sensor

## Conclusion

This project demonstrates the design and simulation of a complete PPG signal acquisition and conditioning system.

The system combines:

1. LED optical excitation
2. MOSFET LED-current control
3. Photodiode current modelling
4. Transimpedance amplification
5. High-pass filtering
6. Signal amplification
7. Low-pass filtering
8. PI feedback control

The final simulated PPG output is centred around approximately **2.5 V** and has a peak-to-peak amplitude of approximately **100 mV**.

The modelled cardiac pulse occurs at **1 Hz**, corresponding to an estimated heart rate of **60 BPM**.

The theoretical and simulated amplifier gains and filter cutoff frequencies show good agreement.

Overall, the project demonstrates the importance of amplification, filtering, biasing, feedback control, and careful analogue design in biomedical instrumentation and provides a foundation for developing a practical PPG-based heart-rate monitoring system.

## References

1. J. G. Webster, *Medical Instrumentation: Application and Design*, 4th ed., Wiley, 2010.

2. J. Allen, “Photoplethysmography and its application in clinical physiological measurement,” *Physiological Measurement*, vol. 28, no. 3, pp. R1–R39, 2007.

3. Analog Devices, *LTspice Simulation Software*.

4. R. S. Khandpur, *Handbook of Biomedical Instrumentation*, 3rd ed., McGraw-Hill Education, 2014.

5. STMicroelectronics, *TS912 – Rail-to-Rail CMOS Dual Operational Amplifier*, Datasheet.

6. ON Semiconductor, *BSS123 – N-Channel Logic Level Enhancement Mode Field Effect Transistor*, Datasheet.

## Keywords

`PPG` `Photoplethysmography` `Biomedical Instrumentation` `LTspice` `Signal Conditioning` `Transimpedance Amplifier` `TIA` `TS912` `BSS123` `LED Driver` `Photodiode` `High-Pass Filter` `Low-Pass Filter` `PI Controller` `Heart Rate Monitoring` `60 BPM` `University of Moratuwa`
