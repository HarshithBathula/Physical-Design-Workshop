
# Environment Setup

## Objective
Set up a local VirtualBox VM with a pre-built Ubuntu VDI containing OpenLane and SKY130 PDK.

---

## 1. Install VirtualBox

| Property | Value |
|----------|-------|
| Tool | Oracle VirtualBox |
| Version | 7.2.14 |
| Source | [virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads) |

**Dependencies resolved:**
- Installed Microsoft Visual C++ 2019 Redistributable (`vc_redist.x64.exe`)
- Restarted host machine before proceeding
- Python bindings warning clicked "Yes" (optional, no impact)

---

## 2. Create Virtual Machine

| Setting | Value |
|---------|-------|
| Name | `vsdworkshop` |
| Type | Linux |
| Version | Ubuntu 18.04 LTS (64-bit) |
| Base Memory | **4096 MB** |
| Processors | **4 CPUs** |

---
## 3. Attach Storage

Selected **"Use an existing "openlane.vdi"virtual hard disk file"**:

## VirtualBox Configuration

### 1. Bi-directional Clipboard
Enabled **Shared Clipboard** and **Drag'n'Drop** to `Bidirectional` in VM settings to allow copy-paste between host and guest.

> **Settings → General → Advanced → Shared Clipboard: Bidirectional**

### 2. Network Adapter (Optional)
If you need internet inside the VM (e.g., to clone a GitHub repository or install packages), ensure the network adapter is enabled:

> **Settings → Network → Adapter 1 → Enable Network Adapter → Attached to: NAT**

**Note:** Internet is only required if you plan to push documentation directly from the VM terminal. The OpenLane flow itself runs entirely offline once the VDI is loaded.



## 4. OPENLANE


