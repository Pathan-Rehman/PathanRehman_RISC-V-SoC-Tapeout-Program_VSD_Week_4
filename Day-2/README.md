# VSD Hardware Design Program

## Velocity Saturation and CMOS Inverter VTC

### 📘 Overview

- [SPICE Simulation for Lower Nodes](#spice-simulation-for-lower-nodes)  
  - [Observation 1: Long vs Short Channel NMOS](#observation-1-long-vs-short-channel-nmos)  
  - [Observation 2: Peak Current Comparison](#observation-2-peak-current-comparison)
- [Labs: Sky130 Id–Vgs](#labs-sky130-idvgs)
- [CMOS Voltage Transfer Characteristics](#cmos-voltage-transfer-characteristics)
- [CMOS Inverter Views](#cmos-inverter-views)
- [Load Line Curves for NMOS and PMOS](#load-line-curves-for-nmos-and-pmos)

***

### SPICE Simulation for Lower Nodes

The plots depict NMOS output characteristics for a device with **W = 1.8 μm** and **L = 1.2 μm (W/L = 1.5)**.

**Linear Region:**  
Current $I_d$ increases linearly with $V_{ds}$ for $V_{ds} < (V_{gs} - V_t)$.

**Saturation Region:**  
For $V_{ds} ≥ (V_{gs} - V_t)$ , $I_d$ becomes limited by channel length modulation and $V_{ds}$.

<img width="1307" height="721" alt="image" src="https://github.com/user-attachments/assets/9cc286c8-e015-4f1f-a6d7-200d5b012a01" />


***

### Observation 1: Long vs Short Channel NMOS

<img width="1452" height="723" alt="image" src="https://github.com/user-attachments/assets/aec2998b-c53c-4594-98f3-15a0eabb908d" />
<img width="1294" height="666" alt="image" src="https://github.com/user-attachments/assets/1ecbd416-d2af-4687-97e4-7ec1e189673c" />
<img width="1206" height="582" alt="image" src="https://github.com/user-attachments/assets/57513669-d48a-4330-87a8-1f7af8aa261a" />

Comparison between **long-channel (L = 1.2 μm)** and **short-channel (L = 0.25 μm)** NMOS devices having the same W/L ratio:

- **Long-channel:** $I_d$ follows a quadratic relation with $V_{gs}$.
<img width="1300" height="719" alt="image" src="https://github.com/user-attachments/assets/dfa9c213-ce6a-4b1d-a9a9-8d4e6b7c1000" />

- **Short-channel:** At low $V_{gs}$ , $I_d$ is quadratic, but transitions to a linear relationship at higher $V_{gs}$ due to **velocity saturation**, where carrier velocity stops increasing with field strength.
  
<img width="1396" height="719" alt="image" src="https://github.com/user-attachments/assets/f178754c-3fdf-4794-81d5-f371c77c155e" />

### Operation:

| Device Type | Modes |
|--------------|--------|
| Long Channel (>250 nm) | Cutoff → Resistive → Saturation |
| Short Channel (<250 nm) | Cutoff → Resistive → Velocity Saturation → Saturation |

<img width="1396" height="719" alt="image" src="https://github.com/user-attachments/assets/d4301621-51f2-4dc5-ae96-dbac7151ee10" />

Comparison between long and short channel devices:

| Device | Dimensions (W/L μm) | Type | Peak Current |
|---------|----------------------|------|---------------|
| Left | 1.8 / 1.2 | Long-channel | 410 μA |
| Right | 0.375 / 0.25 | Short-channel | 210 μA |


***

### Labs: Sky130 Id–Vgs

#### Netlist 1 — nfet_idvds_L015_W039.spice

```
.param temp=27
.lib "sky130_fd_pr/models/sky130.lib.spice" tt
XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=0.39 l=0.15
R1 n1 in 55
Vdd vdd 0 1.8V
Vin in 0 1.8V
.op
.dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2
.end
```

Run in ngspice:

```shell
ngspice day2_nfet_idvds_L015_W039.spice
plot -vdd#branch
```

<img width="1369" height="909" alt="image" src="https://github.com/user-attachments/assets/ab72135b-5bdb-4b05-984b-974a0e4e5aef" />


#### Netlist 2 — nfet_idvgs_L015_W039.spice

```
.param temp=27
.lib "sky130_fd_pr/models/sky130.lib.spice" tt
XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=0.39 l=0.15
R1 n1 in 55
Vdd vdd 0 1.8V
Vin in 0 1.8V
.op
.dc Vin 0 1.8 0.1
.end
```

Run in ngspice:

```shell
ngspice day2_nfet_idvgs_L015_W039.spice
plot -vdd#branch
```

<img width="1372" height="911" alt="image" src="https://github.com/user-attachments/assets/21d2f340-afe8-476b-b91a-37968508e306" />


***

### CMOS Voltage Transfer Characteristics

**MOSFET as a Switch**

- **OFF State:**  
  Acts as an open circuit when $|V_{gs}| < |V_{th}|$.
- **ON State:**  
  Behaves as a closed circuit when $|V_{gs}| > |V_{th}|$.

<img width="1173" height="703" alt="image" src="https://github.com/user-attachments/assets/e0cb20c3-5a8c-4bb5-9e59-9c7c703f673e" />

***

### CMOS Inverter Views

**Transistor-Level and Switch-Level Models**

- **Vin = 0:** PMOS ON, NMOS OFF → $V_{out} = V_{dd}$
- **Vin = Vdd:** PMOS OFF, NMOS ON → $V_{out} = 0$

This illustrates the **complementary switching** that enables **low static power** and **sharp transitions** in CMOS inverters.

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/32cc5f79-a337-4eae-81e1-10c2eec9055b" />


**Step 1:**  
Express **PMOS gate voltage** $V_{gsP}$ in terms of **Vin**. Replace node voltages using **Vin**, **Vdd**, **Vss**, and **Vout**.

<img width="1493" height="721" alt="image" src="https://github.com/user-attachments/assets/b3d9ec85-84ce-46ee-982a-a163162cbfba" />

Transform PMOS and NMOS $V_{ds}$ in terms of **Vout**.

<img width="1204" height="655" alt="image" src="https://github.com/user-attachments/assets/192f64dc-ae00-4495-a840-0f4d1daf9150" />

Merge load curves by equating NMOS and PMOS $I_d $.  
Sweep **Vin** and plot **Vout** to obtain the CMOS inverter **VTC** showing the switch behavior from HIGH to LOW.

<img width="1324" height="729" alt="image" src="https://github.com/user-attachments/assets/d9887b47-94e1-4b48-ac2a-d6cee3d18e39" />
