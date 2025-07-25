# Advanced_Physical_Design_Using_OpenLANE-Priyanka_Razdan

## **Day 1: Inception of open-src EDA, openLANE and Sky130 PDK**

### **How to talk to computers:**

<img width="438" height="430" alt="LEONARDO" src="https://github.com/user-attachments/assets/62bb2fcf-57e0-4d7c-a294-eb7cd533761b" />

### **Processor Chip:**
The pins around the processor chip are the interfaces with the chips: **FTDI**, **QSPI1-Flash**, **12C0-EEPROM**, **VCC/GND**, **SDRAM**, etc. 

<img width="504" height="445" alt="Pasted Graphic 2" src="https://github.com/user-attachments/assets/553a8d36-4226-4487-813f-7c0837b76f83" />

**Package: QFN-48** -> Quad Flat No-leads
Chip is sitting in the middle of the package. 

<img width="504" height="445" alt="vdd3v3" src="https://github.com/user-attachments/assets/e0b6eeb6-ac1f-4e71-bc5c-0d2aad0e1ec1" />

If you were to open the chip -> various components.
**Pads** for transporting signal inside the chip (inside blue area)
**Core** of the chip is inside the black. Where all the digital logic sits (analog, digital, and gates)
The **die** is the name for the full encasing of the chip. 

<img width="504" height="445" alt="11" src="https://github.com/user-attachments/assets/fa60f07e-6048-4606-bfac-af4d52e891c1" />

**RISCV SoC**, **SRAM**, **PLL**, **adc0**, **adc1**, **dac**, **SPI** -> the small parts inside the chip

**PIL**, **adc0**, **adc1**, **dac**, **SRAM** -> foundry IPs
**RISC SoC** & **SPI** -> Macros

**Foundry** is basically a big factory with big machines (lithography) and the engineer has to communicate with the foundry with some medium or some interface. 

**Macros** are pure digital logic, unlike **IPs** which need intelligence.

In order to communicate with chips, must communicate with foundry. 
In order to communicate with foundry, need interface files.

---

### **RISC-V Instruction Set Architecture (ISA)** -> Language of Computer
Compile C into assembly to machine language program, then bits get excecuted in specific layout (flow)

**Hardware description language (HDL)** -> bridge between **RISC V** & Layout. Essentially the implementation of code. 

<img width="692" height="394" alt="Hardware" src="https://github.com/user-attachments/assets/1073e9f5-ffab-40e4-ac1e-e7f04f6b56e9" />

**Applications/Software** -> **System Software** -> **Hardware** (chip layout)
In system software: compiler & assembler
-> Handles IO Operations
-> Allocate memory 
-> Low Level system functions
**C/C++/Java** -> **Compiler** -> **\*.exe file** (instructions) -> **Assembler** -> **Binary** (ml)

**\*.exe file** (instructions) -> abstract interference **ISA** ('architecture' of computer)

From abstract instructions -> **RTL** implementation of instruction set-> synthesized setlist of below **RTL** (gate level) -> **Hardware**.

---

## **SoC design and OpenLane**

### **Digital ASIC design**
-> **RTL IPs** (GitHub, librecores, opencores)
-> **EDA Tools** (Qflow, open road, openLane)
-> **PDK DATA** (?)

Designing open source asic is a dream.

**PDK: Process Design Kit**
A collection of files used to model a fabrication process for the **EDA tools** used to design an IC.
-> Rules: **DRC**, **LVS**, **PEX**
-> Device Models
-> etc.

**Google** & **skwater130** released open source pdks for the first time ever may 30th.

**ASIC Flow Objective: RTL to GDSll**
-> Automated **PnR** and/or Physical implementation.

### **Simplied RTL -> GDSll**

<img width="601" height="342" alt="Synthesis" src="https://github.com/user-attachments/assets/7d9c513d-1dc2-4da5-aeff-15823e6a7ced" />

This is simplified **RTL** to **GDSll** Flow
-> **Synthesis**
-> **Floor/Power Planning**
-> **Placement**
-> **ClockTree Synthesis**
-> **Routing** 
-> **Sign Off**

**Synthesis:** This step converts **RTL** to a circuit out of components from the **standard cell library**. **Gate level netlist** is equivalent to the **RTL**. 
-> **Standard Cells** have regular layouts. 
-> Each has different views/models
-> **Electrical**, **HDL**, **SPICE**,
-> Layout (Abstract and Detailed)

### **Floor and Power Planning**
Plan Silicon area
-> **Chip Floor Planning:** Partition the chip die between different system building blocks and place the **I/O Pads**. 
-> **Macro Floor-Planning:** Define the Dimensions, pin locations, and rows.
-> **Power Planning:** Chip is powered by multiple **VDD** and **GND** pins and the pins are connected throughout the chip in vertical and horizontal strips. These structures help reduce the power resistance. 

### **Placement**
Place the get-Level Netlist subs on rows. And aligned with the sites. Place the ones that communicate together. 
**Global and Detailed Placement** is done in 2 steps. The first step is to first find the optimal (illegal steps) the second step is to place them correctly which is the **detailed placement**. This usually comes with minimal changes. 

<img width="609" height="343" alt="Clock Tree" src="https://github.com/user-attachments/assets/190b38b3-f3f9-4cda-9d00-65f7e0e9578f" />

**Clock Tree Synthesis (CTS):**
The network looks like a tree. The **clock src** is the roots and the elements are the end leaves. 

Delivers the clock to all sequential elements; has minimum skew; good shape. 

<img width="609" height="343" alt="Routing" src="https://github.com/user-attachments/assets/b19b623b-63b1-4884-a1b5-2c15637ed126" />

**Routing:**
-> Needs to find a pattern of horixontall and vertical wires to implement the nets that connects the cells together. The router uses available metal layers as defined by **PDK**.
The **PDK** defines: 
-> Thickness
-> Pitch
-> Minimum Width
-> **VS** - used to connect wire segments on their fragmented layers together. 

As defined by **SkyWater PDK**: 
Lowest Layers is the local interconnect layer.
**Titanium Nitride Layer**
Next 5 are all **illumnium layers**.

As the routing grid is huge routing uses a **divide and conquer** approach. 
-> **Global routing** first- using course grained grids to generate routing guides.
-> **Detailed Routing**- uses the routing guides and the fine grain grids to implement the actual wiring. 

### **Sign Off:**
-> **Physical Verifications**
-> **DRC** makes sure final layout aligns with all the rules
-> **LVC** makes sure final matches gate level netlist. 

-> **STA** makes sure it fulfills all timing constraints and the circuit will run. 

When using open src tools: 
-> Tools Qualification; Tools Calibration; Missing Tools;

**Openlane** was released as an open src to help with the flow that didn't exist before. 
It started as an open src flow for a true open src tape-out experiment. 

<img width="568" height="314" alt="image" src="https://github.com/user-attachments/assets/c265f3e3-484b-46d7-9009-b3dc4c9d85bf" />

**striVe** is a family of open everything SoCs.
-> open **pdk**, open **eda**, open **rtl**
-> **striVe 1-6**

**Main goal:**
-> produce a clean **GDSll** with no humann intervention
-> clean means no **LVS** biolations, no **DRC** violations, ideally no timing violations (still in progress)
**OpenLANE** is tuned for **SkyWater 130 PDK**
-> functional out of th ebox; instrucitons to build and run natively is ideally the future of **openLANE**
-> flow can be used to harden macros and chips; harden means to generate **gds tool** or final layout
-> **autonomous** or **interative**
-> **automonous** means confiugre the flow, then push button then final **gds tool** will appear
-> **interactive** means going through commands step by step; more for expeerimentation
-> **Design Space Exploratoin**
-> find the best set of flow configurations; depending on each chip.
-> large number of design example exist as it is open src and more should be added in the future. 

<img width="568" height="314" alt="image" src="https://github.com/user-attachments/assets/d269a9e7-dbdf-422a-903a-ab415d87d5b8" />

Flow starts with design **RTL** and ends with the final layout. to function, flow needs **pdk**. 

**RTL** into logic circuits using generic components. **Synthesis exploration** report shows best strategy through data. **Design Exploration** used to generate report to show different design metrics shows violations generated by each design. Useful to find best config. 

**Regression Training:**
-> Run exploration on several designs using best configs.
-> Run **openLANE** on ~70 designs and compare the results to the best known ones. 

**DFT:** 
-> Scan Insertion; Automatic Test Pattern Generation (**ATPG**); Test Patterns Compaction; Fault Coverage; Fault Simulation;

After this is physical implementation:
Used with **openROAD app**: 
**PnR:** Floor/Power Planning; End Decoupling Capacitors and Tap cells insertion; Global and Detailed Placement; **CTS**; Global and Detailed Routing

**Logic Equiablence Checking:**
Compare netlist to getlevel netlist to make sure they're equiavalent, otherwise something is wrong. 

When a metal wire segment is fabricated, it can act as an **antenna**- recieves charges that can harm transistor gates that can be damaged during fabrication. Length of wires on transistor gates must be limited (job of router). If router fails - 2 solutions
1) briding attaches a high layer intermediary (requires routing awarenesS)
2) Add **antenna diode cell** to leak away charges; antenna diodes are provided by **SCL**.

**OpenLANE** uses a preventive approach:
1) adds a fake **antenna diode** next to every cell input after placement
2) run the **antenna checker** (**magic**) on the routed layout
3) if the checker reports a violation on the cell input pin, replace the fake diode cell with a real one. 

**Magic** is used for **DRC** (design ruels checking) and **SPICE** extraction from layout.
**Magiv** and **Netgen** are used for **LVS**
-> Extracted **SPICE** by **magic** vs. **verilog netlist**

---

## **Get Familiar to open-src EDA tools:**

### **OpenLANE Directory structure in detail** 

```bash
1) cd work/tools/ (open dir)
2) ls -ltr (to list everything in chronological order)
3) cd openlane_working_dir
4) ls -ltr
5) cd openlane
```

<img width="753" height="170" alt="image" src="https://github.com/user-attachments/assets/adb1202a-2e4b-4e10-8425-ae3e7c2aecd0" />

<img width="787" height="187" alt="image" src="https://github.com/user-attachments/assets/f3ae9381-8984-4493-82fc-d2e59493f6d3" />

**PDKs** are the project design kit~ ours is **skywater 130**
**openlane** is built around this specific **pdk**. If you open the **pdk** dir, you can see the **sky-water-pdk** which has all the info about our **pdk**. **sky130A** is our **pdk** that contains the files about our tools & tech assciated with out **pdk**. if you open **sky130A** you'll see **libs.ref** and **libs.tech**. **ref** contains the files abt the tools and **tech** contains the files about the tech. 

if `cd lib` in **sky130_fd_sc_hd**: 
**tt** -> typical
**ss** -> slow slow
**ff** -> fast fast
more files about **pdk**. 

use `cd ..` mutliple times then use `cd openlane` to open **openlane**

<img width="753" height="290" alt="image" src="https://github.com/user-attachments/assets/c18554f8-b872-4152-a942-132a72128fb3" />

### **For mac users:** 
use `bash <(curl -s https://raw.githubusercontent.com/ZimengXiong/bASICs-openlane-apple-silicon-vm/refs/heads/main/patch-1.sh)` in your normal terminal then close the terminal tab. Instead of **docker** use the **openlane Launch** on desktop (sry if u don't have one :() and you're already in the **openlane container**. 

Then do `./flow.tcl -interactive` (as the video stated and keep going) keep in mind **picorv32a** now exists one folder deeper and you need go through **designs** and **ci** before reaching it. 

<img width="787" height="400" alt="image" src="https://github.com/user-attachments/assets/74a4b933-3692-4606-bf89-e4808aeb9e2d" />

if you want to exit the **openlane flow** envt you need to write the command `exit` otherwise you stay in it. if you don't do the previous step you would be int he **openlane container** as shown above and can't perform many commands unique to **openlane**. 

<img width="787" height="508" alt="image" src="https://github.com/user-attachments/assets/97e93e5d-c2df-48a6-94ac-12ce47282460" />

go back to the vid and begin to his commands.
```bash
1) package require openlane
2) prep -design designs/ci/picorv32a
```

**src**- is where **verilog file** for **rtl** is present
**config.tcl**- all the files are picked from here; contains time period (overrides setting)

what the **prep step** does is it creates/merged new **lef files** in order to prep for the design. to make sure you did the step right, open a new tab in terminal (make sure it's not in the **openlane** envt) and check in `openlane/designs/ci/picorv32a/runs` and make sure theres a new file called **runs**. 

<img width="792" height="621" alt="image" src="https://github.com/user-attachments/assets/a371936a-bf6b-4c8f-ada6-27aa0b2c04c3" />

```bash
1) cd runs
2) ls -ltr (might need to cd into most recent run folder inside runs if performed prep multiple times)
3) cd tmp
```
you will notice **lef files** inside of **tmp** that were prepared from ur **prep step**. Won't be inside anything inside **synthesis** bc nothign has been run yet. (need to access from **results** folder) There will be **reports** and **logs** created to every step in the future.

**cmds.file** takes account of all the commands u used. 

go back to ur **openlane** envt:
```bash
1) run_synthesis
```

**efabless/openlane** on **github** would have everything you need to know. 
<img width="792" height="621" alt="image" src="https://github.com/user-attachments/assets/c12452c8-ec05-4cdf-af7e-996068008b8a" />

<img width="614" height="280" alt="image" src="https://github.com/user-attachments/assets/ae9d079d-45cb-430b-b867-3c7225daa0ab" />

These commands must be run in this order otherwise the commands will fail. You can't run **routing** without first running **cts**. 

<img width="590" height="533" alt="image" src="https://github.com/user-attachments/assets/af2c6477-83e6-4faa-8d22-33680b7feb10" />

If you go back to the normal terminal and open the `run/temp/synthesis` folder you'll no longer see that its empty. Since we completed the **synthesis step** we can see that it's populated with files. 

if you want to check the **reports**. `cd` back into **temp** and you can see the **reports** created for all the steps including **synthesis** (which should be only one populated). 
after `less` you can exit the screen by typing `q`.
 
