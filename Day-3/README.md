## CMOS Switching Threshold and Dynamic Simulations

### 📘 Contents

- [Voltage Transfer Characteristics and SPICE Simulations](#voltage-transfer-characteristics-and-spice-simulations)  
- [Labs Sky130 SPICE Simulation for CMOS](#labs-sky130-spice-simulation-for-cmos)  
  - [Comparison: DC vs Transient Simulations](#comparison-dc-vs-transient-simulations)  
- [Static Behavior Evaluation — CMOS Inverter Robustness and Switching Threshold](#static-behavior-evaluation--cmos-inverter-robustness-and-switching-threshold)

***

### Voltage Transfer Characteristics and SPICE Simulations

#### CMOS Inverter SPICE Deck

This diagram should show the **connections** and **component layout** of a CMOS inverter.  
<img width="1350" height="646" alt="image" src="https://github.com/user-attachments/assets/120c5d2f-13f5-4202-af30-bcd27c95b88d" />


Key aspects to define:

- **Connectivity:** PMOS (M1), NMOS (M2), Vdd, Vss, Vin, and Vout nodes.  
- **Values:** Transistor sizes (W/L), supply voltage (e.g., 2.5V), load capacitor (Cload = 10fF).  
- **Node naming:** Assign unique labels like `in`, `out`, `vdd`, `vss`.

***

#### Example SPICE Deck

<img width="1297" height="612" alt="image" src="https://github.com/user-attachments/assets/d286a397-f07e-46ac-bd30-bc4e3c11e559" />


Main elements of the SPICE deck:

1. **Model and Netlist:**
   - `M1 out in vdd vdd pmos W=0.375u L=0.25u`
   - `M2 out in 0 0 nmos W=0.375u L=0.25u`
   - `cload out 0 10f`

2. **Voltage Sources:**
   - `Vdd vdd 0 2.5`
   - `Vin in 0 2.5`

3. **Simulation Commands:**  
     `.op` and `.dc Vin 0 2.5 0.05`

4. **Model Inclusion:**  
     `.include tsmc_025um_model.mod`

5. **End Statement:**  
     `.end`

***

### Labs Sky130 SPICE Simulation for CMOS

#### VTC Sweep — day3_inv_vtc_Wp084_Wn036.spice

Run the simulation and plot:

```shell
ngspice day3_inv_vtc_Wp084_Wn036.spice
plot out vs in
```

<img width="1377" height="902" alt="image" src="https://github.com/user-attachments/assets/73af0977-bce4-477e-9a16-ed416f4d4b9a" />


#### Transient Analysis — day3_inv_tran_Wp084_Wn036.spice

Run in ngspice:

```shell
ngspice day3_inv_tran_Wp084_Wn036.spice
plot out vs time in
```

<img width="1376" height="899" alt="image" src="https://github.com/user-attachments/assets/8b9b35e3-a3fd-4d43-ad63-9bb0c98fb53b" />


**Rise/Fall Delay Calculation:**

- Rise delay = time(50% of rising edge) − time(50% of falling edge)  
- Fall delay = time(50% of falling edge) − time(50% of rising edge)

***

### Comparison: DC vs Transient Simulations

| Aspect | Voltage Transfer Characteristics (DC Sweep) | Transient Response (Time Domain) |
|--------|---------------------------------------------|----------------------------------|
| Type | DC Sweep | Dynamic Time Simulation |
| Input | Steady 0–1.8V Sweep | Pulse 0–1.8V (2ns width, 4ns period) |
| Command | `.dc Vin 0 1.8 0.01` | `.tran 1n 10n` |
| Purpose | Examine static VTC | Analyze switching speed and delay |
| Output | Vout vs Vin curve | Vout & Vin vs Time |
| Key Metric | Threshold voltage, noise margin | Rise/Fall delay, propagation delay |

***

### Static Behavior Evaluation — CMOS Inverter Robustness and Switching Threshold

#### Switching Threshold Voltage ($V_m$)

- Defined as the point where $V_{in} = V_{out}$.  
- Both NMOS and PMOS operate in **saturation**, yielding maximum gain.  

<img width="1476" height="705" alt="image" src="https://github.com/user-attachments/assets/ef1efd17-387a-4041-ae05-78bf90987a9a" />


**Example Configurations:**

| Case | Parameters | $V_m$ |
|------|-------------|-------|
| Left | $W_n=W_p=0.375μm$, $L_n=L_p=0.25μm$, $(W/L)_n=(W/L)_p=1.5$ | ≈ 0.98V |
| Right | $W_n=0.375μm$, $W_p=0.9375μm$, $(W/L)_p=3.75$, $(W/L)_n=1.5$ | ≈ 1.2V |

***

**Regions of Operation along VTC:**

- PMOS Linear / NMOS OFF  
- PMOS Linear / NMOS Saturation  
- PMOS Saturation / NMOS Saturation (at $V_m$)  
- PMOS Saturation / NMOS Linear  
- PMOS OFF / NMOS Linear  

<img width="1395" height="715" alt="image" src="https://github.com/user-attachments/assets/42ba2749-dc09-430b-859a-c1dfb4fb7145" />


At $V_m$, currents satisfy $I_{dsp}=-I_{dsn}$ where:
- For NMOS: $V_{gsn}=V_m$  
- For PMOS: $V_{gsp}=V_m−V_{dd}$  

<img width="1461" height="718" alt="image" src="https://github.com/user-attachments/assets/aa54d76d-ff87-4ac7-8494-f935b3a20dc0" />


Equating $I_{dsp}+I_{dsn}=0$, we can express $V_m$ based on the width-to-length ratios and mobility factors.  
<img width="1478" height="726" alt="image" src="https://github.com/user-attachments/assets/4785ac2c-703b-49ac-8227-42b6812e73ac" />


This balance allows computation of $(W_p/L_p)/(W_n/L_n)$ for a specific $V_m$ when $V_{in}=V_{out}=V_m$.

<img width="884" height="310" alt="image" src="https://github.com/user-attachments/assets/8557eb57-84be-469f-86a8-6e7cd989bba7" />


***

### Delay and Duty Cycle Effects

The table below shows the impact of the $W_p/W_n$ ratio on key performance metrics:

- Higher $W_p/L_p$ improves rise time but shifts $V_m$ upward.
- Balanced case ($W_p/L_p≈2×W_n/L_n$):  
  - $V_m ≈ 1.2V$  
  - Rise ≈ Fall delay ≈ 80ps

<img width="1403" height="719" alt="image" src="https://github.com/user-attachments/assets/9ea736db-b3eb-4b38-88de-e8991790a314" />


If rise and fall delays differ due to $R_{on}$ mismatch, duty cycle distortion occurs.  
Clock buffers may require **duty cycle correction** to maintain uniform switching.

<img width="1289" height="708" alt="image" src="https://github.com/user-attachments/assets/a7da1791-b853-47c0-91dd-2559619e56c7" />


***
