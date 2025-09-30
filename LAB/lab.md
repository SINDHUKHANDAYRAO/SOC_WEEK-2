

# **Week 2 Lab Report**

## **BabySoC Fundamentals & Functional Modelling**

---

## **1. Objective**

The aim of this lab is to develop a solid understanding of **System-on-Chip (SoC) fundamentals** and to practice functional modelling of the **BabySoC** design using simulation tools — specifically **Icarus Verilog (iverilog)** and **GTKWave**.

By the end of this lab, I'm able to:

* Explain SoC architecture and module interactions.
* Compile BabySoC Verilog modules using Icarus Verilog.
* Simulate and generate `.vcd` waveform files.
* Analyse simulation results in GTKWave.
* Document observations with waveform screenshots.

---

## **2. Tools Required**

| Tool           | Purpose                                         |
| -------------- | ----------------------------------------------- |
| Icarus Verilog | Compile Verilog code                            |
| GTKWave        | View waveform files generated during simulation |
| Git            | Clone BabySoC repository                        |
| Make           | Automate build and simulation processes         |

---

## **3. Methodology**

### **Step 1 — Environment Setup**

```bash
sudo apt install make python python3 python3-pip git iverilog gtkwave docker.io
sudo chmod 666 /var/run/docker.sock
pip3 install pyyaml click sandpiper-saas
```

---

### **Step 2 — Clone BabySoC Repository**

```bash
cd ~/VLSI
git clone https://github.com/manili/VSDBabySoC.git
cd VSDBabySoC
```

**Repository Structure:**

```
VSDBabySoC/
├── src/
│   ├── include/
│   │   ├── sandpiper.vh
│   │   └── other header files...
│   ├── module/
│   │   ├── vsdbabysoc.v      # Top-level SoC module
│   │   ├── rvmyth.v          # RISC-V core
│   │   ├── avsdpll.v         # PLL module
│   │   ├── avsddac.v         # DAC module
│   │   └── testbench.v       # Testbench
├── output/
└── compiled_tlv/
```

---

### **Step 3 — Understanding the Modules**

**vsdbabysoc.v**
Top-level SoC module integrating all cores.
Inputs: `reset`, `VCO_IN`, `ENb_CP`, `ENb_VCO`, `REF`, `VREFH`
Outputs: `OUT`, `RV_TO_DAC[9:0]`, `CLK`

**rvmyth.v**
RISC-V core processor producing 10-bit digital outputs.
Inputs: `CLK`, `reset`
Outputs: `OUT[9:0]`

**avsdpll.v**
Phase-locked loop generating clock signals.
Inputs: `VCO_IN`, `ENb_CP`, `ENb_VCO`, `REF`
Outputs: `CLK`

**avsddac.v**
DAC converting digital outputs to analog signals.
Inputs: `D[9:0]`, `VREFH`
Outputs: `OUT`

**testbench.v**
Testbench driving simulation, clock generation, and waveform dumping.

---

### **Step 4 — Pre-Synthesis Simulation**

Run:

```bash
make pre_synth_sim
```

Output:
Waveform `.vcd` file at:

```
output/pre_synth_sim/pre_synth_sim.vcd
```

View in GTKWave:

```bash
gtkwave output/pre_synth_sim/pre_synth_sim.vcd
```

**Observed Signals:**

* **`CLK`**: Clock signal from PLL.
* **`reset`**: Processor reset signal.
* **`OUT`**: DAC analog output (digital in simulation).
* **`RV_TO_DAC[9:0]`**: Processor output bus.
* **`dac.OUT`**: Real datatype analog output.

**Waveform Screenshot:**
[Link to Screenshot — Pre-Synthesis Simulation](pre.png)

**Observation:**
Reset initializes the processor and clears outputs. PLL generates a stable clock. Data flows from RISC-V core → DAC producing analog output.

---

### **Step 5 — Post-Synthesis Simulation**

#### **Synthesis**

```bash
make synth
```

Output:

```
output/synth/vsdbabysoc.synth.v
```

#### **Post-Synthesis Simulation**

```bash
make post_synth_sim
```

Output:

```
output/post_synth_sim/post_synth_sim.vcd
```

View:

```bash
gtkwave output/post_synth_sim/post_synth_sim.vcd
```

**Waveform Screenshot:**
[Link to Screenshot — Post-Synthesis Simulation](post.png)

**Observation:**
Post-synthesis simulation verifies the gate-level netlist matches the RTL design. Timing and dataflow are consistent with pre-synthesis results. Minor differences may appear due to synthesis optimizations.

---

## **4. Simulation Observations**

| Simulation Type | Key Observations                                                                                      |
| --------------- | ----------------------------------------------------------------------------------------------------- |
| Pre-Synthesis   | Clock generation correct, reset functionality works, processor outputs expected digital waveform.     |
| Post-Synthesis  | RTL behavior preserved, timing constraints met, gate-level simulation verifies correct functionality. |

---

## **5. Importance of Functional Modelling**

Functional modelling is crucial to:

* Validate SoC functionality before committing to physical design.
* Identify design errors early.
* Ensure timing constraints and functional correctness.

**Pre-synthesis** validates RTL logic, while **post-synthesis** verifies physical gate-level design.

---

## **6. References**

* [VSDBabySoC GitHub Repository](https://github.com/manili/VSDBabySoC.git)
* [RISC-V Core (rvmyth)](https://github.com/kunalg123/rvmyth/)
* [PLL Module](https://github.com/lakshmi-sathi/avsdpll_1v8.git)
* [DAC Module](https://github.com/vsdip/rvmyth_avsddac_interface.git)
* [Lab Reference](https://github.com/hemanthkumardm/SFAL-VSD-SoC/Journey/tree/main/12.%20VSDBabySoC%20Project)

---

## **7. Deliverables**

* **Simulation logs:** Added the logs
* **GTKWave Screenshots:** added in this document
* **Explanation:** Included above with each screenshot.

---

### **Final Note**

This lab demonstrates the importance of functional modelling in SoC design. BabySoC’s modular approach allows a hands-on understanding of processor design, clock generation, and analog conversion.


