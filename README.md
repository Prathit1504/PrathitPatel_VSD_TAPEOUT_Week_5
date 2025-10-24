# PrathitPatel_VSD_TAPEOUT_Week_5
# Week 5 - OpenROAD Flow Setup and Floorplan + Placement

This document explain the setting up the OpenROAD Flow Scripts environment and execute the Floorplan and Placement stages of the physical design flow.

-----

## 📚 Contents

  - [Steps for Week 5 Task (Floorplan & Placement)](#steps-for-week-5-task-floorplan--placement)
      - [1. Clone the OpenROAD Flow Scripts Repository](#1-clone-the-openroad-flow-scripts-repository)
      - [2. Setup, Build and Verifying OpenROAD](#2-setup-build-and-verifying-openroad)
      - [3. Run the Flow to the **Floorplan** Stage](#3-run-the-flow-to-the-floorplan-stage)
      - [4. Visualize the Floorplan (Deliverable 1)](#4-visualize-the-floorplan-deliverable-1)
      - [5. Run the Flow to the **Placement** Stage](#5-run-the-flow-to-the-placement-stage)
      - [6. Visualize the Placement (Deliverable 2)](#6-visualize-the-placement-deliverable-2)

-----

## Steps for Week 5 Task (Floorplan & Placement)


### 1\. Clone the OpenROAD Flow Scripts Repository

This is the main repository for the flow. The `--recursive` flag is crucial because the OpenROAD-flow-scripts repository uses Git submodules.

```bash
git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts
cd OpenROAD-flow-scripts
```

<img width="1920" height="983" alt="Screenshot from 2025-10-21 12-58-02" src="https://github.com/user-attachments/assets/5758727e-cfa0-4951-97a8-b41e36a42b53" />

### 2\. Setup, Build and Verifying OpenROAD

Run the following commands in order from your `OpenROAD-flow-scripts` directory. This single sequence will install dependencies, compile the tools, and verify that they are working.

```bash
# 1. Install system-wide dependencies
sudo ./setup.sh

# 2. Compile OpenROAD and its tools
./build_openroad.sh --local

# 3. Load the new tool paths into your terminal
source ./env.sh

# 4. Verify the tools are working
yosys -help
openroad -help
```
<img width="1920" height="983" alt="Screenshot from 2025-10-21 19-36-12" src="https://github.com/user-attachments/assets/abfcaa0e-2dd3-46ca-a113-6bc178af519a" />

<img width="1920" height="983" alt="Screenshot from 2025-10-21 19-17-49" src="https://github.com/user-attachments/assets/4dafa9ff-69a7-4eb1-8e17-9a300115eac9" />

  * `sudo ./setup.sh`: This command runs the setup script with **administrator (`sudo`) privileges**. It reads a list of required libraries and tools (like `build-essential`, `python3-dev`, `libboost-all-dev`, etc.) and installs them onto the Linux system using package manager (like `apt`). These are the necessary prerequisites for compiling and running OpenROAD.

  * `./build_openroad.sh --local`: This is the main compilation command. It executes a script that builds the OpenROAD application (and its bundled tools like Yosys) from the source code cloned. The `--local` flag tells the script to build the tools inside the current project directory (under `OpenROAD-flow-scripts/tools/`) rather than trying to install them system-wide.

  * `source ./env.sh`: This command is crucial for **setting up the environment**. It executes the `env.sh` script in the *current* terminal session, which "exports" environment variables (like the `PATH`) to tell the shell where to find the new `openroad` and `yosys` executables. 

  * `yosys -help` & `openroad -help`: These are **verification commands**. By asking for the `--help` menu, we can test two things:

    1.  That the `PATH` was set correctly.
    2.  That the binaries were compiled successfully and are executable.

If we can see the help text print out for both, then the installation is successful.
    
### 4\. Run the Flow to the **Floorplan** Stage

Now, navigate into the `flow` directory and run the `make` command to stop at the floorplan stage. This will use the default `gcd` design.

```bash
cd flow
make floorplan
```

**Deliverable:** Take a screenshot of your terminal after this command finishes. This is your **"Floorplan completion log"**.

### 5\. Visualize the Floorplan (Deliverable 1)

Run the following command to open the floorplan layout in the OpenROAD GUI.

```bash
make gui_floorplan
```

**Deliverable:** Take a screenshot of the GUI window showing the core area and I/O pins. This is your **"Floorplan view"** image.

### 6\. Run the Flow to the **Placement** Stage

This command will continue from the floorplan and run the standard cell placement.

```bash
make placement
```

**Deliverable:** Take a screenshot of your terminal after this command finishes. This is your **"Placement completion log"**.

### 7\. Visualize the Placement (Deliverable 2)

Finally, run this command to open the placement layout in the GUI.

```bash
make gui_placement
```

**Deliverable:** Zoom in to see the standard cells arranged in rows. Take a screenshot. This is your **"Placement layout"** image.

✅ **Week 5 Deliverables Complete\!** You now have all the required logs and layout images for your submission.

