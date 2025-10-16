## Lab Introduction

This lab introduces **SPICE simulation of MOSFETs (using the Sky130 open-source PDK)**, from understanding the basic MOSFET model parameters to creating, customizing, and running a SPICE netlist, including technology model files and simulation commands. The aim is to:

- Set up a basic NMOS MOSFET circuit with proper biasing and protection.
- Define exact netlist and technology model file for SPICE.
- Demonstrate parameter sweeps for $V_{GS}$ and $V_{DS}$ using ngspice.
- Observe and analyze drain current ($I_D$) characteristics across different operating regions.

## Lab Steps

### 1. Clone the Workshop Repository

Start by cloning the GitHub repository containing all the necessary Sky130 PDK files and example SPICE netlists.

```bash
cd ~
git clone https://github.com/kunalg123/sky130CircuitDesignWorkshop.git
```
<img width="824" height="507" alt="image" src="https://github.com/user-attachments/assets/8e64ddc7-6012-4101-9f44-bd664d6555ac" />

### 2. Explore Repository Structure

Navigate through relevant directories:

```bash
cd Sky130CircuitDesignWorkshop/design
ls
```
<img width="812" height="609" alt="image" src="https://github.com/user-attachments/assets/765af6ad-cbdd-4f11-a5b3-31e8eaf27fa7" />

List files in the Sky130 directory to identify `cells`, `models`, and related files:

```bash
cd ../Sky130
ls
```
<img width="817" height="487" alt="image" src="https://github.com/user-attachments/assets/f5d9d684-87c8-452a-a728-7f5961c0549b" />

Explore cells and models:

```bash
cd cells
ls
```

You will find subdirectories like `nfet01v8` and `pfet01v8`—used throughout the workshop.

### 3. Inspect Pre-characterized Cell Sizes

Navigate to `nfet01v8` and explore its corners:

```bash
cd nfet01v8/typical
ls
cat corner.spec.spice
```

Observe available $W$ and $L$ values, e.g.:
- $W = 1.26\; \mu m$, $L = 0.15\; \mu m$
- $W = 1.68\; \mu m$, $L = 0.15\; \mu m$

**Note:** Only these pre-characterized combinations are valid for simulation; avoid arbitrary sizing.

### 4. Inspect Model Libraries

The SPICE library is in `models/sky130.lib.spice`:

```bash
cd ../../models
ls
cat sky130.lib.spice
```

Confirm that it includes references to NFET and PFET model files for all corners (TT, SS, SF, FF).

```bash
*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description



XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=5 l=2

R1 n1 in 55

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op
.dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2

.control

run
display
setplot dc1
.endc

.end
```

## Plot the waveforms in ngspice

```bash
ngspice day1_nfet_idvds_L2_W5.spice
plot -vdd#branch
```
## Id vs Vds for different Vgs -sky130 NMOS (W=5um, L=2um)

<img width="1357" height="878" alt="image" src="https://github.com/user-attachments/assets/e65e8437-f0a1-42ef-8bb6-0e1933bf2f20" />
