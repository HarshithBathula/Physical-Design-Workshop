# picorv32a — Synthesis Report (OpenLane + Sky130)

## Design
- **Design name:** picorv32a
- **Tool:** Yosys (synthesis) → `sky130_fd_sc_hd` standard-cell library. OpenLane's `run_synthesis` step also invokes OpenSTA to sanity-check timing on the synthesized netlist.
- **Run directory:** `designs/picorv32a/runs/12-08_08-37/`

## Steps Performed

### 1. Run Synthesis
`run_synthesis` was executed inside the OpenLane interactive flow. Yosys mapped the `picorv32a` RTL to the SKY130 high-density standard-cell library (`sky130_fd_sc_hd`).

<!-- screenshot: Yosys synthesis statistics terminal output -->

### 2. Synthesis Statistics

**Yosys statistics for `picorv32a`:**

| Metric                              | Value                |
| ------------------------------------ | --------------------- |
| Number of wires                      | 14596                 |
| Number of wire bits                  | 14978                 |
| Number of public wires               | 1565                  |
| Number of public wire bits           | 1947                  |
| Number of memories                   | 0                      |
| Number of memory bits                | 0                      |
| Number of processes                  | 0                      |
| **Number of cells**                  | **14876**              |
| **Chip area (module `picorv32a`)**   | **147712.918400 µm²**  |

### 3. Flop Ratio

```
Flop Ratio = Number of D Flip-Flops / Total Number of Cells
           = 1613 / 14876
           ≈ 0.1084296853993009
           ≈ 10.84%
```

`sky130_fd_sc_hd__dfxtp_2` is the D flip-flop cell in this library — 1613 instances out of 14876 total cells, so about **10.84%** of the design is sequential logic; the rest is combinational.

### 4. Post-Synthesis Static Timing Analysis (STA)

OpenLane's `run_synthesis` also runs OpenSTA against the synthesized netlist as a sanity check — clock definition, I/O delay and load setup, then a WNS/TNS summary. **Pending your data:** none of the three screenshots include the OpenSTA portion of the log, so this section is left as a placeholder rather than filled with another run's numbers. Paste or upload the STA portion of your terminal output (or the log file itself) and this section gets completed with your own clock setup, timing summary, and warning count.

<!-- screenshot: OpenSTA run and synthesis success log -->

### 5. Result

Synthesis completed successfully — Yosys produced the full statistics report and computed chip area. For the exact `[INFO]` success line and warning count, grab the tail of your `run_synthesis` terminal log and it can be dropped in here precisely.
