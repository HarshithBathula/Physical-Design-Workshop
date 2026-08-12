# Design Prep

## Design

- **Design name:** picorv32a
- **Source:** OpenLane example designs
- **Technology node:** SKY130 (130nm)

---

## Steps Performed
Working Directory: 
```bash
cd ~/Desktop/work/tools/openlane_working_dir/openlane
```


### 1. Pull OpenLane Docker Image

```bash
\docker pull efabless/openlane:v0.21
```
### 2. Launch OpenLane in Interactive Mode

From the OpenLane working directory:

```bash
docker
./flow.tcl -interactive
```
