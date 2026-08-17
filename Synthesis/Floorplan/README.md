# Floorplanning — picorv32a (OpenLane + Sky130)

RTL-to-GDS flow, stage 2 of 6. Floorplan of the picorv32a RISC-V core using OpenLane 1 with the SkyWater 130nm PDK.

**Tools:** OpenROAD 0.9.0 (floorplan, I/O placement, PDN) · Magic (layout viewer)
**PDK / library:** `sky130A` / `sky130_fd_sc_hd`
**Run:** `designs/picorv32a/runs/14-08_21-33/`

## Contents

- [1. Design Preparation](#1-design-preparation)
- [2. Floorplan Configuration](#2-floorplan-configuration)
- [3. Run Floorplan](#3-run-floorplan)
- [4. Floorplan Results — DEF](#4-floorplan-results--def)
- [5. Verification Against Synthesis](#5-verification-against-synthesis)
- [6. Layout Inspection in Magic](#6-layout-inspection-in-magic)
- [7. Result](#7-result)

---

## 1. Design Preparation

```tcl
package require openlane 0.9
prep -design picorv32a
```

<img width="1536" height="712" alt="design preparation" src="https://github.com/user-attachments/assets/23010630-471a-4d38-a90c-5428f61b4652" />


The prep step resolves configuration, merges LEF files, and creates the run directory. Two details matter later:

**Available metal layers** — extracted from the tech LEF:

```
[INFO]: The number of available metal layers is 6
[INFO]: The available metal layers are li1 met1 met2 met3 met4 met5
```

**LEF merge counts** — `mergeLef.py` merges the standard-cell LEF with the extra Efabless cells:

| LEF file | MACROs found |
| --- | --- |
| `sky130_fd_sc_hd.lef` | 437 |
| `sky130_ef_sc_hd__fill_12.lef` | 1 |
| `sky130_ef_sc_hd__decap_12.lef` | 1 |
| `sky130_ef_sc_hd__fakediode_2.lef` | 1 |
| **Total** | **440** |

OpenROAD later reports exactly 440 library cells when reading `merged.lef` — confirming the merge dropped nothing.

---

## 2. Floorplan Configuration

### Where the variables are documented

```bash
cd ~/Desktop/work/tools/openlane_working_dir/openlane/configuration
ls -ltr
less README.md
```

`README.md` lists every OpenLane variable with a description, organized by flow stage (Floorplanning, Placement, CTS, …).

### Defaults in `configuration/floorplan.tcl`

<img width="1536" height="712" alt="floorplan_tcl" src="https://github.com/user-attachments/assets/af82ee2a-a944-4c6b-b0bb-46f3d4bf2aed" />


<img width="1536" height="712" alt="floorplan_tcl_2" src="https://github.com/user-attachments/assets/996c6e26-98fe-40da-9f7f-74f2c26f79c4" />


<details>
<summary><b>Full variable table</b></summary>

| Variable | Default | Meaning |
| --- | --- | --- |
| `FP_IO_VMETAL` | 3 | Metal layer index for vertical I/O pins |
| `FP_IO_HMETAL` | 4 | Metal layer index for horizontal I/O pins |
| `FP_SIZING` | relative | Die sized relative to core utilization |
| `FP_CORE_UTIL` | 50 | Core utilization target (%) |
| `FP_CORE_MARGIN` | 0 | Core-to-die margin |
| `FP_ASPECT_RATIO` | 1 | Core height / width |
| `FP_PDN_VOFFSET` / `VPITCH` | 16.32 / 153.6 | Vertical power stripe offset and pitch |
| `FP_PDN_HOFFSET` / `HPITCH` | 16.65 / 153.18 | Horizontal power stripe offset and pitch |
| `FP_PDN_AUTO_ADJUST` | 1 | Auto-adjust PDN to the die |
| `FP_PDN_CORE_RING` | 0 | No core power ring |
| `FP_PDN_ENABLE_RAILS` | 1 | Generate standard-cell power rails |
| `FP_PDN_CHECK_NODES` | 1 | Check PDN for unconnected nodes |
| `FP_IO_MODE` | 1 | 0 = matching mode, 1 = random-equidistant mode |
| `FP_IO_HLENGTH` / `VLENGTH` | 4 / 4 | I/O pin length |
| `FP_IO_VEXTEND` / `HEXTEND` | -1 / -1 | Pin extension beyond boundary |
| `FP_IO_VTHICKNESS_MULT` / `HTHICKNESS_MULT` | 2 / 2 | Pin thickness multiplier |
| `BOTTOM_MARGIN_MULT` / `TOP_MARGIN_MULT` | 4 / 4 | Vertical margins (× site height) |
| `LEFT_MARGIN_MULT` / `RIGHT_MARGIN_MULT` | 12 / 12 | Horizontal margins (× site width) |
| `FP_HORIZONTAL_HALO` / `VERTICAL_HALO` | 10 / 10 | Macro halo |
| `DESIGN_IS_CORE` | 1 | Design is the core (not a padframe) |

</details>

### Configuration priority

Lowest to highest precedence:

```
1. configuration/floorplan.tcl              (OpenLane global defaults)
2. designs/picorv32a/config.tcl             (design-specific settings)
3. sky130A_sky130_fd_sc_hd_config.tcl       (PDK / std-cell-library overrides)
```

To see what actually resolved for a run:

```bash
less designs/picorv32a/runs/14-08_21-33/config.tcl
```

> [!NOTE]
> `FP_IO_VMETAL` / `FP_IO_HMETAL` are **inputs**, not values echoed to the log. There is also a known off-by-one — OpenLane resolves the integer to a layer name by index, so the metal the pins land on isn't necessarily the number written here. The reliable check is the DEF:
> ```bash
> grep -A2 "^PINS" results/floorplan/picorv32a.floorplan.def | head -20
> ```
> Both variables are deprecated in current OpenLane in favour of `FP_IO_HLAYER` / `FP_IO_VLAYER`, which take the layer name directly.

---

## 3. Run Floorplan

```tcl
run_floorplan
```

This invokes several OpenROAD sub-steps: die/core sizing, I/O pin placement, tap-cell insertion, and PDN generation.

<img width="1536" height="712" alt="Floorplan executed" src="https://github.com/user-attachments/assets/56e04f74-db12-4311-a95a-36b630fc2b16" />


### PDN generation

The log shows a series of `PSM-0030` warnings while building the power grid:

```
[WARNING PSM-0030] Vsrc location at (145.520um, 10.880um) and size =10.000um,
is not located on a power stripe. Moving to closest stripe at (145.800um, 107.180um).
```

These are **not errors**. Power-source locations generated from the pitch/offset settings don't always land exactly on a stripe, so OpenROAD snaps each to the nearest stripe. Followed by:

```
[INFO PSM-0031] Number of nodes on net VGND = 19223.
[INFO PSM-0037] G matrix created sucessfully.
[INFO PSM-0040] Connection between all PDN nodes established in net VGND.
[INFO]: PDN generation was successful.
```

`PSM-0040` is the one that matters — every PDN node on `VGND` is connected, which is what `FP_PDN_CHECK_NODES 1` verifies.

### I/O placement mode

```
Random pin placement
RandomMode Even
```

This is `FP_IO_MODE 1` behaving as documented — "random equidistant". Pins are distributed evenly around the die boundary rather than matched to a specified order.

---

## 4. Floorplan Results — DEF

```bash
cd designs/picorv32a/runs/14-08_21-33/results/floorplan
ls -ltr
less picorv32a.floorplan.def
```

<img width="1536" height="712" alt="Diearea_floorplan" src="https://github.com/user-attachments/assets/c0d3069a-6fe7-4d83-aa25-eefcc569d892" />


```
VERSION 5.8 ;
DESIGN picorv32a ;
UNITS DISTANCE MICRONS 1000 ;
DIEAREA ( 0 0 ) ( 660685 671405 ) ;
ROW ROW_0 unithd 5520 10880 FS DO 1412 BY 1 STEP 460 0 ;
ROW ROW_1 unithd 5520 13600 N  DO 1412 BY 1 STEP 460 0 ;
...
```

### Die area calculation

`UNITS DISTANCE MICRONS 1000` means 1000 DEF units = 1 µm.

```
Lower-left  (x, y) = (0, 0)
Upper-right (x, y) = (660685, 671405)  →  (660.685 µm, 671.405 µm)

Die width  = 660.685 µm
Die height = 671.405 µm
Die area   = 660.685 × 671.405 ≈ 443,587.21 µm²
```

### Row structure

```
ROW ROW_0 unithd 5520 10880 FS DO 1412 BY 1 STEP 460 0 ;
```

| Field | Meaning |
| --- | --- |
| `unithd` | Site name for the `sky130_fd_sc_hd` library |
| `5520 10880` | Row origin in DEF units (5.52 µm, 10.88 µm) |
| `FS` / `N` | Row orientation, alternating so power rails abut and share between adjacent rows |
| `DO 1412 … STEP 460 0` | 1412 sites per row at 0.46 µm site pitch |

Row width check: 1412 × 0.46 µm ≈ 649.5 µm — consistent with a 660.685 µm die once the left/right margins (`MARGIN_MULT` = 12) are accounted for.

---

## 5. Verification Against Synthesis

```
OpenROAD 0.9.0
Notice 0: Reading LEF file: .../tmp/merged.lef
Notice 0:     Created 13 technology layers
Notice 0:     Created 25 technology vias
Notice 0:     Created 440 library cells
Notice 0: Reading DEF file: .../tmp/floorplan/3-verilog2def_openroad.def
Notice 0: Design: picorv32a
Notice 0:     Created 409 pins.
Notice 0:     Created 14876 components and 115597 component-terminals.
Notice 0:     Created 14978 nets and 56051 connections.
```

| Quantity | Synthesis | Floorplan | Match |
| --- | --- | --- | --- |
| Cells / components | 14876 | 14876 | ✅ |
| Nets (wires) | 14978 | 14978 | ✅ |
| Library cells | 437 + 3 merged | 440 | ✅ |

Counts carry through exactly — a clean handoff from synthesis to floorplanning with no cells lost or added.

### Utilization sanity check

Synthesis reported a total cell area of **147,712.92 µm²**:

```
147,712.92 / 443,587.21 ≈ 0.333  →  ~33.3% of the die
```

Core utilization measures against the *core* area, slightly smaller than the die once margins apply — so effective core utilization sits a little above this. Consistent with `FP_CORE_UTIL 35` rather than the tool default of 50, indicating `config.tcl` overrides the default.

---

## 6. Layout Inspection in Magic

```bash
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech \
  lef read ../../tmp/merged.lef \
  def read picorv32a.floorplan.def &
```

### Magic navigation

| Action | Key / command |
| --- | --- |
| Set zoom box | Left-click (lower-left), right-click (upper-right) |
| Zoom into box | `z` |
| Zoom out | `shift + z` |
| Select object under cursor | `s` |
| Zoom to fit selection | `v` |
| Identify selected cell | `what` (tkcon console) |

### Full die view

<img width="1536" height="713" alt="Layout_view" src="https://github.com/user-attachments/assets/c4a4717d-202f-4f59-8ef9-af90396a20ad" />


The complete die with standard-cell rows visible as vertical striping. Note that after floorplanning the cells are **not yet placed** at final positions — the rows define where they *can* go.

### I/O pin placement

<img width="1536" height="713" alt="closeview_io_pins" src="https://github.com/user-attachments/assets/6b7aa1c3-9e7f-4366-8f07-efb9dd6953d4" />


Zooming to the die boundary shows I/O pins (`mem_addr[30]`, `trace_data[24]`, …) at equal spacing along the edge — visual confirmation of `FP_IO_MODE 1`. The adjacent vertical column is filled with `sky130_fd_sc_hd__decap_3` decoupling capacitor cells (`PHY_194`, `PHY_192`, `PHY_186`, …).

### Cell-level view

<img width="1536" height="713" alt="Layout_closure_view" src="https://github.com/user-attachments/assets/208d3471-91ef-4c29-bdb3-5999e8ce0a60" />

Zooming further resolves individual standard cells — `sky130_fd_sc_hd__mux4_1` and flip-flop instances — with `mem_la_write` and `trace_data[29]` pin geometries crossing the view in met3/met4 (pink).

### Identifying a cell

Hover over a cell, press `s`, then in the tkcon console:

```tcl
% what
Selected subcell(s)
    Instance "PHY_xxxx" of cell "sky130_fd_sc_hd__decap_3"
```

---

## 7. Result

- Die area established at 660.685 µm × 671.405 µm (≈ 443,587 µm²)
- Placement rows generated with the `unithd` site, alternating `N`/`FS` orientation
- I/O pins placed in random-equidistant mode around the die boundary
- Power distribution network generated, all `VGND` nodes verified connected
- Component and net counts verified identical to the synthesis netlist


