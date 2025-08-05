## **Day 5 - Final steps for RTL2GDS using tritonRoute and openSTA**

---

### **ROUTING and DRC STAGE**

#### **Core Routing Concept:**
-> Physical routing transforms **logical connectivity** into actual **physical wires** on silicon
Example: **D_in1** must be physically connected to **flip-flop** input with actual wire
-> Challenge lies in finding optimal pathways between **source** and **target** locations

#### **Routing Optimization Goals:**
-> Find best possible connection between two endpoints
-> Minimize total **path length**
-> Reduce number of twists and turns
-> Avoid **zigzag patterns** where possible

#### **Maze Algorithm:**
-> Foundation for modern **routing techniques** despite limitations
-> Still used for **global routing** in some applications

**Preferred:** **L-shaped routes** (single bend)
**Acceptable:** **Z-shaped routes** (two bends)
**Avoid:** Multiple **zigzag patterns**
**Goal:** Shortest possible path with minimal direction changes

#### **For Blockage Handling:**
Physical obstructions act as absolute **routing prohibitions**, so algorithm must navigate around these constraints.
The **Blockages** represent placed components or reserved areas.

---

### **3 Phases for Implementation:**

#### **Phase 1:**
<img width="420" height="360" alt="image" src="https://github.com/user-attachments/assets/b73cc6b7-dfc4-4e25-83e2-ad371e4459a7" />

create **standarized grid overlay** across entire **routing region** (**floorplan**); dimensins shoudl follow industry standards. 

#### **Phase 2: Wave Propagation Labelling Process**
<img width="140" height="110" alt="image" src="https://github.com/user-attachments/assets/4699557d-4412-4686-a564-ca84888ecf16" />

<img width="140" height="110" alt="image" src="https://github.com/user-attachments/assets/1736613a-41bd-4721-be2d-53784411fce8" />

start at **src** (**src** is **1** and **target** is **2**) loc as starting point and label propogate outward until **target** is reached
handle **blockages** as they come up; **wave propogations** shoudl continue underneath them. 

#### **Phase 3:**
several valid paths should exist from **src** to **target**

<img width="140" height="110" alt="image" src="https://github.com/user-attachments/assets/39fed0e9-57d7-4b5a-8b1b-492bf2205b60" />

count number of **bends** in each path; **single-bend** should recieve highest priority
keep priority on **L-shape**- **2bend** is a backup when **l shape** isn't availbable. (this is optimization)

<img width="660" height="360" alt="image" src="https://github.com/user-attachments/assets/c6ca6cc3-cc73-4f01-8994-f2fb4e5830ba" />

img above is **algorithm** implemented on **floorplan**.

---

### **Algorithm Performance Limitations:**

-> All potential **path information** must be maintained simultaneously; means extreme exponential **memory growth** with increasing complexity.

-> **wave propogation** is computationally complex for runtime

so alternatives are:
**line search algorithms** and **steiner tree algorightms**

---

### **DRC Checks**

**Design Rule Checks** represent critical interface between **digital design** and **manufacturing**; this is important for **photolithography** later. 

#### **Rules:**

<img width="250" height="380" alt="image" src="https://github.com/user-attachments/assets/901b33be-c63e-40ca-997d-1a132094b732" />

<img width="250" height="380" alt="image" src="https://github.com/user-attachments/assets/c0df82d5-f957-48b8-b73d-3aed9f9584e6" />

**Width** of any **metal wire** must meet or exceed specified minimum
Can exceed **minimum width**
Cannot go below minimum 
Actual wire shapes often not perfect rectangles after **lithography**

#### **Characterization Methodology:**
1) Test patterns w/ varying **wire widths**
2) minimum reliably printable width found w measuremnet
3) **design rules** incorporate safety margins above characterized minimum
4) process variations acct for across-wafer and wafer-towafer differences

---

### **Lab steps to build power distribution network:**

<img width="440" height="390" alt="image" src="https://github.com/user-attachments/assets/bc52ca3e-2b9d-4d63-a279-87e1cd00803b" />

I currently don't have a **CURRENT_DEF** var.

```bash
gen_pdn
```
(to create **powernet**)

should create the **grid**; can find properties in the **def file**.

<img width="240" height="95" alt="image" src="https://github.com/user-attachments/assets/1a2d8643-19aa-408f-83ac-ce0ad06e55a4" />

heres an example of what the **rails** should mean. 

<img width="370" height="250" alt="image" src="https://github.com/user-attachments/assets/498ffeb0-d2ee-4f97-814f-0d923c808fa7" />

here are some of the components genrated:
-> **standard cell rails**: give direct power delivery to **standard cells** (provide **vdd** and **gnd**)
usually **metal1**

-> **power straps**: vertical power distribution throughout chipl functioning at higher current capcaity than **rails**. connects **power ring** to **standard cell rails**
usually **metal 4** and **5**. 

-> **power ring**: perimeter power distribution; links external **power pads** to internal distribution. 

#### **Physical alignment requirements:**
**vdd** and **gnd** pins must align w/ **pdn rails**
**cell pitch** must match **rail pitch** exactly
misalignment prevents power delivery

#### **Power distribution hierarchy:**
1) **external power pads**: primary power input from package
2) **power ring**: perimater collection and ditribution
3) **power straps**: vertical distribution through chip core
4) **standard cell rails**: direct connection to individual cells
5) **cell power pins**: final power delivery to transistors

---

### **Lab steps to run routing using tritonRoute**

the primary routing command is just:
```bash
run_routing
```

#### **4 routing strategies:**
**0** ~ not great routing (very basic optimization) will gain on runtime. (**30 min**)
**1-3** and **14**. 
For our workshop we are using **0**. 

#### **Routing using TritonRoute**

#### **1) Fast Route (Global Route)** 
~ represented as a **3d graph**.
Accounts for **via connections** between layers
Enables **layer assignment** optimization

No actual wire implementation at this stage
output is: set of **rectangular regions** for each net and inital **metal layer** allocation

#### **2) Detail Route**
Actual responsisblites:
Implement actual **wire connections** within **global routing** constraints
Optimize **wire length** and **via count**
Ensure **DRC compliance**
Minimize **routing congestion**

some more advanced technical feature are:
**MILP based optimization** which is just mathematical optimizastion for optimal path selection to minimize total **routing cost**. 

**Pre-processed route guide compliance** for limited deviation.

**Inter-guide connectivity assurance:**
-> to quickly validate proper connection to all pins. 

<img width="440" height="195" alt="image" src="https://github.com/user-attachments/assets/b6d219df-5381-43fd-aa8b-a55d10d26a16" />

#### **Route Guide Pre-processing- Multi-Step Refinement:**
1) **Splitting phase** to divide into unit-width segments to faciliate subsequent optimization steps
2) **Merging phase** is to combine adjacsnet segments to reduce **routing complexity** and improve effiency.
3) **Briding phase** is to connect via upper **metal layers** for continuous connecitivyt.

<img width="440" height="195" alt="image" src="https://github.com/user-attachments/assets/bbe5d18b-fa85-473f-be8d-105101fccbfa" />

**Intra-Layer exceuction** is when routing occurs simultanious across mutiple regions. This maximizes computational resource utilization.

#### **Panel processing:**
**panel** is verticla strips within each **metal alyer** and the way they proess is that even and odd get indexed and even ones get processed first. evens are done in parallel and odds are done in sequence (even after completion)

#### **Sequential execution (interlayer)**
-> **metal layers** are processed in sequence (**m1->m2->m3**)
-> lower layer must be fuly routed before upper layer begins. 

#### **Access Point Cluster Architecture:** 
Grouped collections of **access points** associated with specific **routing segments**: (like pins or i/o ports things like that)
-> **access points** in same **routing regions**
-> points on same net or segment
-> points on same adjascent layers 
these are js examples of different formation criteria. 

#### **3 diff types of connectivity scenarios:**
1) **Vertical layer connectivity**: connect lower layer routing to upper layer routing
-> this needs electrical connection and there are multiple possible **access points** in electrical overlap region.

2) **Pin Access Connectivity:**
-> connect **standard cells** pins to **routing network**
**access point** must align with both pin and **routing grid**.

3) **Layer Briding Connectivity:**
-> mutli-layer connection managment; this ones more complex bc requires mutliple **vias** (**metal connectors** basically) across several layers.

<img width="260" height="135" alt="image" src="https://github.com/user-attachments/assets/1eb6c9e6-2d95-404f-b84b-0c24f844b98d" />

very breif overview of this equation-> minimal and optimal point between two **apcs**. 

All this is saying is for each **apc** you need to find cost accosiated with it. (**minimum spanning tree** is for optimization)

---

### **Lab steps to configure tritonRoute**

Will need to correct violations manually after running **power planning**. **DRC** should come back w **0 violations** in order for it to be viable. 

in `tmp/routing/fastroute.guide`
this should contian all the nets and metals associated with the net next to it. (can't run it on my **vm** currently)

```bash
cd work/tools/SPEF_EXTRACTOR
python3 main.py [where my lef file path shohuld be] [where my def file path should be] (if i had it...)
```

<img width="500" height="90" alt="image" src="https://github.com/user-attachments/assets/f40ca950-2092-4beb-86cd-0af6b511599e" />

don't have **spef_extractor** in my **tools** folder. 

if you do the command above, idealy new **spef** will be created in `results/routing` of most recent run.




