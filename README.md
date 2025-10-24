
# 🧩 **From RTL to Placement using OpenROAD Flow**

The OpenROAD project makes it possible to perform a **complete digital backend flow** using open-source tools and process design kits.
This documentation walks through the setup and execution of the **OpenROAD Flow** on the `gcd` (Greatest Common Divisor) design — beginning from the **Register Transfer Level (RTL)** and proceeding up to **placement**, along with methods to view and verify the layout results.

---

## 🌐 1. The Journey from Code to Silicon

Every integrated circuit begins as logic — equations, modules, and connections written in a hardware description language such as **Verilog**.
The **physical design flow** translates that abstract logic into a **real, placeable structure** made up of transistors and wires.

In our case, the process includes:

* Converting Verilog into a gate-level netlist (synthesis)
* Mapping logic into silicon space (floorplanning)
* Physically arranging standard cells (placement)

At the end of this journey, you don’t just have a design — you have a **visual blueprint** that could be fabricated on a wafer.

---

## 🧱 2. Setting up the OpenROAD Environment

### Cloning the Repository

The OpenROAD flow scripts bring together all the required build configurations and design examples.
Clone the entire project, including its submodules:

```bash
git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts.git
cd OpenROAD-flow-scripts
```

### Installing Dependencies

To make sure all necessary libraries and compilers are present, run the dependency installer script with the `-all` option:

```bash
sudo ./etc/DependencyInstaller.sh -all
```

This will pull and build everything required — from Boost and Eigen to Tcl and Python bindings.
The process is lengthy, but it ensures the environment is consistent and future build steps will succeed.

### Building OpenROAD

After dependencies are set up, compile the OpenROAD toolchain locally:

```bash
./build_openroad.sh --local
```

Once this completes, you’ll have a fully operational OpenROAD executable inside your environment.

<img width="1210" height="773" alt="Image" src="https://github.com/user-attachments/assets/dd195657-e577-47aa-bc13-0dfc0131f666" />

---

## ⚠️ 3. Common Installation Issues & Their Fixes

Several library-related issues may arise during the build process. Below are the major ones and how they were resolved:

| Error                                                          | Cause                         | Fix                                                                                     |
| :------------------------------------------------------------- | :---------------------------- | :-------------------------------------------------------------------------------------- |
| `Could NOT find GTest`                                         | Google Test library missing   | `sudo apt-get install libgtest-dev`                                                     |
| `Could not find package configuration file provided by spdlog` | Logging library not installed | `sudo apt-get install libspdlog-dev`                                                    |
| `yaml-cpp`, `or-tools`, or similar                             | Unavailable headers/libraries | Rerun `sudo ./etc/DependencyInstaller.sh -all` or manually install all build essentials |

A complete manual installation (if the script fails) can also be done using:

```bash
sudo apt install build-essential cmake python3-pip clang flex bison libboost-all-dev libeigen3-dev swig
```

---

## 🧠 4. Executing the Flow

With everything built and configured, the next step is to **run the flow** for our design.

### Launching the Flow up to Placement

Inside the flow directory, execute:

```bash
cd flow
make DESIGN_CONFIG=./designs/sky130hd/gcd/config.mk place
```

This single command will:

1. Perform logic synthesis
2. Define the chip outline (floorplanning)
3. Place standard cells based on timing and connectivity

The process stops automatically before clock tree synthesis, as per the target stage (`place`).

After completion, layout files will be available in:

```
results/sky130hd/gcd/base/
```
<img width="1210" height="773" alt="Image" src="https://github.com/user-attachments/assets/6c724bd0-33ae-4146-8dbb-2c05695d6e44" />

---

## 🖥️ 5. Visualizing the Physical Layout

The results of the OpenROAD flow can be explored using two visualization tools — **OpenROAD GUI** and **KLayout**.
Before either is used, the environment variables must be set up properly:

```bash
cd ~/OpenROAD-flow-scripts
source ./env.sh
```

### Method 1: OpenROAD GUI

The GUI allows interactive viewing of design data (.odb files).
To open the design:

```bash
cd flow
openroad -gui
```

<img width="1210" height="773" alt="Image" src="https://github.com/user-attachments/assets/d7db8472-ddfd-4440-86d2-e231e466ca80" />

Then, through the GUI:

* Navigate to `File → Open`
* Load `results/sky130hd/gcd/base/2_floorplan.odb` for the floorplan view

  <img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/0eeb9cba-c7b2-481f-87f8-0c4f37819417" />
  <img width="1210" height="773" alt="Image" src="https://github.com/user-attachments/assets/7236ff90-000a-48fb-9794-a49170e77a92" />

* Similarly, open `3_place.odb` for the placement visualization
You can explore the die area, cell rows, and standard cell placements within the window.
  
 <img width="1213" height="770" alt="Image" src="https://github.com/user-attachments/assets/8419dfbe-3b00-40c0-81ce-9995a7dc6b80" />
 <img width="1210" height="773" alt="Image" src="https://github.com/user-attachments/assets/de91de38-b064-48fb-b2fb-b1aa492fc567" />

---

### Method 2: Viewing through KLayout

KLayout works with `.def` format files. Since OpenROAD produces `.odb` outputs, they need conversion.

#### Converting ODB to DEF

```tcl
openroad
read_lef platforms/sky130hd/lef/sky130_fd_sc_hd.tlef
read_lef platforms/sky130hd/lef/sky130_fd_sc_hd_merged.lef
read_db results/sky130hd/gcd/base/2_floorplan.odb
write_def results/sky130hd/gcd/base/2_floorplan.def
read_db results/sky130hd/gcd/base/3_place.odb
write_def results/sky130hd/gcd/base/3_place.def
exit
```
#### Verify files are generated

```bash
ls -lh results/sky130hd/gcd/base/2_floorplan.def results/sky130hd/gcd/base/3_place.def
```

<img width="1213" height="79" alt="Image" src="https://github.com/user-attachments/assets/15ac5e97-a994-409c-a665-2d011afe688d" />

#### Opening in KLayout

```bash
klayout -l platforms/sky130hd/lef/sky130_fd_sc_hd.tlef -l platforms/sky130hd/lef/sky130_fd_sc_hd_merged.lef results/sky130hd/gcd/base/2_floorplan.def
```
This view provides a lightweight alternative to the GUI, excellent for verifying placement alignment and macro geometry.

<img width="1210" height="773" alt="Image" src="https://github.com/user-attachments/assets/da18407c-904c-460c-894c-9256582ffd17" />

---

## 📊 6. Notes and Tips

* Always source `env.sh` when launching a new terminal.
* For missing macros or warnings in KLayout, ensure both `.tlef` and `.lef` files are loaded.
* The `results` folder will contain `.odb`, `.def`, and intermediate report files for analysis.

---

## 🔍 7. Results Snapshot

| Stage         | File Type      | Description                             |
| :------------ | :------------- | :-------------------------------------- |
| Floorplanning | `.odb`, `.def` | Defines die area and block placements   |
| Placement     | `.odb`, `.def` | All standard cells placed and legalized |
| Visualization | `.lef`, `.def` | Used for GUI and KLayout inspection     |

---

## 🎯 8. Final Thoughts

Through this exercise, you completed a **partial OpenROAD physical design flow** — transitioning a design from **RTL to its physical placement** using open-source infrastructure.

This step forms the **foundation of modern chip implementation**, bridging digital logic and real-world geometry.
Each step — synthesis, floorplanning, placement — brings the circuit closer to becoming a tangible silicon design.

By mastering this open-source workflow, you’ve effectively experienced the **first milestone of backend VLSI design automation**.

---

## 🧾 Summary

* Installed and built OpenROAD-flow-scripts
* Resolved dependency issues
* Ran the `gcd` design flow up to placement
* Visualized results in both GUI and KLayout
* Validated the structure and layout geometry successfully

