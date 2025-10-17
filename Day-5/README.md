## CMOS Power Supply and Device Variation Robustness Evaluation

### 📘 Contents

- [Static Behavior Evaluation — CMOS Inverter Robustness — Power Supply Variation](#static-behaviour-evaluation--cmos-inverter-robustness--power-supply-variation)  
  - [Sky130 Supply Variation Labs](#sky130-supply-variation-labs)  
- [Static Behavior Evaluation — CMOS Inverter Robustness — Device Variation](#static-behaviour-evaluation--cmos-inverter-robustness--device-variation)  
  - [Etching Variation](#etching-variation)  
  - [Oxide Thickness Variation](#oxide-thickness-variation)  
  - [Transistor Strength Definitions](#transistor-strength-definitions)  
  - [Sky130 Device Variation Labs](#sky130-device-variation-labs)

***

### Static Behaviour Evaluation — CMOS Inverter Robustness — Power Supply Variation

Power supply scaling impacts the static behavior of CMOS inverters by shifting the switching threshold $V_m$, affecting noise margins and robustness.

SPICE simulations compare inverters at $V_{dd}=2.5V$ and scaled down to $V_{dd}=1V$, keeping transistor sizes constant with $W_p=0.9375\,μm$ and $W_n=0.375\,μm$.

<img width="1272" height="711" alt="image" src="https://github.com/user-attachments/assets/d3cd79ce-c30c-4442-82f4-8e880ef2992a" />


- As $V_{dd}$ decreases, $V_m$ moves closer to the midpoint of the supply range; noise margins shrink.
- Lower $V_{dd}$ reduces noise immunity, making circuits more sensitive to noise and supply fluctuations.
- Reduced $V_{dd}$ lowers static and dynamic power but compromises noise robustness.
- Higher $V_{dd}$ improves noise margin but increases power consumption. 

This plot demonstrates the VTC shift and noise margin reduction as $V_{dd}$ scales down.

<img width="1107" height="707" alt="image" src="https://github.com/user-attachments/assets/2caf2eb1-cdea-475c-8c78-85e7aac59b79" />


**Advantages of using $0.5V$ supply:**

- Approximately 50% improvement in gain.
- Around 90% reduction in energy consumption, highlighting efficiency improvements.

**Disadvantages:**

- Reduced drive strength causes slower switching and possible performance degradation.

***

### Sky130 Supply Variation Labs

<details><summary><strong>day5_inv_supplyvariation_Wp1_Wn036.spice</strong></summary>

```
*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description

XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=1 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15

Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 1.8V

.control

let powersupply = 1.8
alter Vdd = powersupply
let voltagesupplyvariation = 0
dowhile voltagesupplyvariation < 6
    dc Vin 0 1.8 0.01
    let powersupply = powersupply - 0.2
    alter Vdd = powersupply
    let voltagesupplyvariation = voltagesupplyvariation + 1
end

plot dc1.out vs in dc2.out vs in dc3.out vs in dc4.out vs in dc5.out vs in dc6.out vs in xlabel "input voltage(V)" ylabel "output voltage(V)" title "Inverter dc characteristics as a function of supply voltage"

.endc

.end
```
</details>

Run in ngspice:

```shell
ngspice day5_inv_supplyvariation_Wp1_Wn036.spice
```

<img width="1376" height="897" alt="image" src="https://github.com/user-attachments/assets/8622141a-d049-4ecf-816b-c0435b55fb99" />


***

### Gain Calculation from SPICE VTC Plot

To calculate gain from the inverter VTC:

1. Click on PMOS slope (left side of transition), terminal shows $x_0=V_{in}$ and $y_0=V_{out}$  
2. Click on NMOS slope (right side), terminal shows $x_1=V_{in}$ and $y_1=V_{out}$  

Gain is computed as:  
$Gain=\frac{y_0 - y_1}{x_0 - x_1}$

***

### Static Behaviour Evaluation — CMOS Inverter Robustness — Device Variation

#### Device Variation and CMOS Inverter Robustness

Device variation affects inverter robustness through:

- Etching Variation  
- Oxide Thickness Variation

***

#### Etching Variation

- Etching shapes physical transistor dimensions $W$ and $L$.
- Deviations may occur between **designed** and **actual** widths and lengths:
  - P-diffusion region defines PMOS gate width.
  - N-diffusion region defines NMOS gate width.
  - Poly-silicon thickness defines gate length related to technology node.
- Metal layers and contacts are also affected.

Etching variations cause changes in device current $I_d$ (which depends on $W$ and $L$), impacting:

- Switching threshold $V_m$
- Noise margins
- Overall robustness and performance

<img width="1122" height="635" alt="image" src="https://github.com/user-attachments/assets/b1d94f3d-d097-4b72-9908-ca9c1c6ee813" />


<img width="1371" height="691" alt="image" src="https://github.com/user-attachments/assets/70150474-f27b-4b86-b086-0d02c2ac3486" />


***

#### Oxide Thickness Variation

- Fabricated gate oxide thickness often varies from the design value.
- Since $I_d$ depends on oxide capacitance $C_{ox}$, oxide thickness variations affect device current and inverter performance.

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/6a6c0938-4ae4-474a-875e-93d63aa1627c" />


Together, etching and oxide thickness variations significantly influence inverter robustness.

***

**Width sweep to analyze PMOS and NMOS variation:**  
<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/0ee63247-3399-4a04-915b-ec366e4f5f14" />


***

### Transistor Strength Definitions

- **Strong PMOS:** Wider width → lower resistance → stronger pull-up path charging output capacitor.  
- **Weak NMOS:** Narrower width → higher resistance → weaker pull-down.  
- **Weak PMOS:** Narrower width → higher resistance.  
- **Strong NMOS:** Wider width → lower resistance → stronger pull-down path.

***

### Sky130 Device Variation Labs

<details><summary><strong>day5_inv_supplyvariation_Wp1_Wn036.spice</strong></summary>

```
*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description

XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=7 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.42 l=0.15

Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands
.op

.dc Vin 0 1.8 0.01

.control
run
setplot dc1
display
.endc

.end
```
</details>

Run in ngspice:

```shell
ngspice day5_inv_supplyvariation_Wp1_Wn036.spice
plot out vs in
```

<img width="1372" height="904" alt="image" src="https://github.com/user-attachments/assets/a51511bb-c74b-4745-b16f-1ba2ea5e96cb" />


Larger PMOS width than NMOS width yields a stronger pull-up path, causing the output to remain high longer compared to NMOS pull-down curve.
