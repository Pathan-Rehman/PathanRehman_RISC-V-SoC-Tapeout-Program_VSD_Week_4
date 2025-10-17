## Static Behavior Evaluation: CMOS Inverter Robustness and Noise Margin

### 📘 Contents

- [Introduction to Noise Margin](#introduction-to-noise-margin)  
- [Noise Margin Definition — VTC and Undefined Region](#noise-margin-definition--vtc-and-undefined-region)  
- [Noise Margin Summary — Handling Input Bumps](#noise-margin-summary--handling-input-bumps)  
- [Sky130 Noise Margin Labs](#sky130-noise-margin-labs)

***

### Introduction to Noise Margin

Noise margin measures the **maximum noise voltage** a CMOS circuit can withstand without logic failure.  
<img width="949" height="559" alt="image" src="https://github.com/user-attachments/assets/7c916c35-bad4-4bf9-99d5-205bc32ea82c" />


- **Ideal characteristic:** Infinite slope at $V_{in}=V_{dd}/2$ (abrupt switching).  
- **Actual characteristic:** Finite slope with a gradual transition region.

***

### Noise Margin Definition — VTC and Undefined Region

Noise margins are derived from the **Voltage Transfer Characteristic (VTC)** curve of a CMOS inverter.  
<img width="982" height="582" alt="image" src="https://github.com/user-attachments/assets/c2d2a3a3-a0d6-4b40-8bd7-7029419d3f59" />


From the plot:

- The slope $=−1$ at two key points:
  - $V_{IL}$ (Input Low Threshold)
  - $V_{IH}$ (Input High Threshold)

**Voltage Levels:**
- $V_{OH},V_{OL}$: Output logic high/low  
- $V_{IL},V_{IH}$: Input thresholds at slope $=−1$

**Noise Margins:**
- $NM_H=V_{OH}−V_{IH}$ (high-level tolerance)  
- $NM_L=V_{IL}−V_{OL}$ (low-level tolerance)

**Undefined Region:**
- Between $V_{IL}$ and $V_{IH}$ lies the unstable region — logic is indeterminate.
- Designers maximize $NM_H$ and $NM_L$ for robust circuits.

Noise margins ensure reliable operation, even with voltage fluctuations or external interference.

***

### Noise Margin Summary — Handling Input Bumps

This figure depicts how noise margins protect against transient voltage “bumps.”  
<img width="1163" height="554" alt="image" src="https://github.com/user-attachments/assets/cff49747-d1a0-440f-b752-0ecd2a40ff21" />


**Input Thresholds:**
- $V_{IL}$ ≈ 10% $V_{dd}$ — treated as logic ‘0’  
- $V_{IH}$ ≈ 90% $V_{dd}$ — treated as logic ‘1’

**Output Thresholds:**
- $V_{OL}$ ≈ 0V → logic ‘0’  
- $V_{OH}$ ≈ $V_{dd}$ → logic ‘1’

**Noise Scenarios:**

- **Case (a):** Bump between $V_{OL}$ and $V_{IL}$ → remains logic ‘0’  
- **Case (b):** Bump between $V_{IL}$ and $V_{IH}$ → undefined logic  
- **Case (c):** Bump between $V_{IH}$ and $V_{OH}$ → interpreted as logic ‘1’

Signals must stay within the **NML/NMH** boundaries to ensure valid logic levels, avoiding the undefined region.

***

### Sky130 Noise Margin Labs

#### day4_inv_noisemargin_wp1_wn036.spice

```
.param temp=27
.lib "sky130_fd_pr/models/sky130.lib.spice" tt
XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=1 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15
Cload out 0 50f
Vdd vdd 0 1.8
Vin in 0 1.8
.op
.dc Vin 0 1.8 0.01
.control
run
setplot dc1
display
.endc
.end
```

Run simulation and plot:

```shell
ngspice day4_inv_noisemargin_wp1_wn036.spice
plot out vs in
```

<img width="1385" height="895" alt="image" src="https://github.com/user-attachments/assets/bdd854ea-a3fc-42f9-b386-b2e6648fd20a" />


***

#### How to Calculate Noise Margin from SPICE

In an ngspice VTC plot:

1. **Select PMOS slope (left transition):**  
   Terminal outputs: $x_0=V_{IL}$ , $y_0=V_{OH}$  
2. **Select NMOS slope (right transition):**  
   Terminal outputs: $x_1=V_{IH}$ , $y_1=V_{OL}$  

**Noise Margin Calculations:**

- $NM_H=V_{OH}−V_{IH}=y_0−x_1$  
- $NM_L=V_{IL}−V_{OL}=x_0−y_1$
