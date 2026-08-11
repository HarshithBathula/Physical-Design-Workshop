# Physical-Design-Workshop
VSD Physical Design Workshop - SKY130 RTL to GDSII

A hands-on implementation of an RTL-to-GDSII physical design flow using **OpenLane** and the open-source **SKY130 PDK**.

---

## Environment

| Component | Details |
|-----------|---------|
| **Host OS** | Windows |
| **Virtualization** | Oracle VirtualBox 7.2.14 |
| **Guest OS** | Ubuntu 18.04 LTS |
| **PDK** | SKY130 |
| **Flow** | OpenLane |
| **Standard Cell Library** | sky130_fd_sc_hd |

### Setup Challenges Faced
| Issue | Solution |
|-------|----------|
| VC++ Redistributable missing | Downloaded & installed from Microsoft |
| Python bindings warning | Clicked "Yes" (optional dependency) |
| No bootable medium | Attached VDI to SATA controller |
| No network in VM | Enabled NAT adapter |

---

## RTL-to-GDSII Flow


## What I Am Learning

- RTL-to-GDSII implementation, end to end
- Logic synthesis and technology mapping to SKY130 standard cells
- Floorplanning: core area, die area, utilization, I/O placement
- Power distribution: rings, straps, and standard-cell power connections
- Standard-cell placement and congestion analysis
- Clock tree synthesis and skew optimization
- Reading and interpreting tool logs (OpenROAD, Magic) to debug the flow
- Linux-based EDA workflows and Docker-based tool environments
- Tcl-based flow automation

---

## Tools & Technologies

| Tool | Purpose |
|------|---------|
| **OpenLane** | Automated RTL-to-GDSII flow orchestration |
| **SKY130 PDK** | Google/SkyWater's open-source 130nm process design kit |
| **Yosys** | RTL synthesis (Verilog → gate-level netlist) |
| **OpenROAD** | Floorplanning, placement, CTS, routing, STA |
| **Magic** | VLSI layout viewing, DRC, GDSII generation |
| **Netgen** | LVS (Layout vs. Schematic) verification |
| **Docker** | Containerized OpenLane environment |
| **Tcl** | Flow scripting and automation |
