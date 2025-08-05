## **Day 3 - Design library cell using Magic Layout and ngspice characterization**

---

### **Review Floorplan Files and Steps to View Floorplan**

Go to latest run:
```bash
1) cd results
2) cd floorplan
3) cd floorplan.def
4) magic -T
```

<img width="500" height="400" alt="image" src="https://github.com/user-attachments/assets/67e0fbb2-8e0c-4e73-99f4-da38e84c695f" />

This is old- from **day 2** but notice how each pin is equidistant after the **floorplan**. 

Now go back to configuration folder- open `floorplan.tcl`: should contain default configs about **floorplan**. 

<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/c85aa82b-36d8-4a6d-a66b-8916adf30ca8" />

<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/0fa616e0-7712-4e81-bfe7-4d208e2aa798" />

(newer version of **openlane** means some syntax is different so no double colon needed here)

<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/b0500d5b-7b05-444f-8d55-c26886a1e2f0" />

Now run **magic** command again back in terminal in `results/floorplan` & see what comes up:

<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/9e080c6b-9c24-49e9-9c51-7d2580e5bc47" />

no longer equidistant and cells are placed on top of each other. 

---

### **Spice Simulations**

#### **1) Create SPICE Deck:** 
connectivity info, tap points, etc (basically a **netlist**)

<img width="400" height="450" alt="image" src="https://github.com/user-attachments/assets/02bef6a0-d8b5-4256-a5da-f054333362da" />

#### **2) Define component values** 
(values for **pmos** and **nmos**) **pmos** should be wider than **nmos** usualy but in the case we're using now for simplicity we are keeping them the same width for now. Assume values for inputs/outputs (2.5 v for now) calculation will be talked abt later

#### **3) Identify 'nodes'** 
**node** means a point where it is between 2 components.

#### **4) Name 'nodes'** 
<img width="350" height="300" alt="image" src="https://github.com/user-attachments/assets/715dd235-205a-4865-85db-5fdd617974a2" />

<img width="300" height="130" alt="image" src="https://github.com/user-attachments/assets/44e23974-e082-4c1b-bbbb-72631d4c1266" />
Example above is a **spice deck**!

line underlined: defines connectivity of **pmos** (m1)
**drain-gate-subtrate-src**
Ex:
**M1** drain is connected to **out** node
**M1** gate is connected to **in** node
**M1** substrate is connected to **vdd** node
**M1** src is connected to **vdd** node. 
**M1** is a **pmos**
**W** = width of M1
**L** = length of M1

Ex of output load capacitor:
**cload** is name
connected between **out** and **0** 
value of it is **10f**

Ex supply voltage:
name is **vdd**
connected between **vdd** and **0**
value is **2.5V**

#### **Simulation Commands:**
```
.op
.dc Vin 0 2.5 0.05
```
means sweeping the gate input from 0-2.5 in steps of 0.05
calculating voltage from gate to output! 

<img width="300" height="200" alt="image" src="https://github.com/user-attachments/assets/424d571e-5ec8-49ba-b60b-f79a3c653bea" />

-> descibe **model file**; this is file it takes all its descriptions of the model

<img width="300" height="50" alt="image" src="https://github.com/user-attachments/assets/34a0e106-0b22-46b8-8ba2-fe29a9edec00" />

**SPICE waveform:** Wn=Wp=0.375u=,Ln,p=0.25u device (Wn/Ln=Wp/Lp = 1.5)

<img width="400" height="220" alt="image" src="https://github.com/user-attachments/assets/0f97f0fd-0639-4d0b-9f66-41da7d2f9f3b" />

source circuit file
excecute circuit
**dc1** transfer characteristics
**plout** out vs. in

<img width="400" height="300" alt="image" src="https://github.com/user-attachments/assets/2563a73c-7022-46f7-b10b-e8edb76f6f31" />

slihglty shifted towards the left; 

Shapes of waveforms are the same for when wdith of **pmos** is increased. 
**Cmos** is a robust device- whenever ur input is 0 ur output is high
1) **switching threshold**, **Vm** is point where **Vin = Vout**
2) if you draw a 45 degree line across the graph- the place where it intersects is **Vm**
3) usually around **0.9V**
4) Area where both are turned on/saturated (**pmos** and **nmos**)
5) high possibilty current flows from power to ground. 

<img width="550" height="230" alt="image" src="https://github.com/user-attachments/assets/a44a6f23-afd3-419b-a944-e981293fbd6d" />

**Vgs = Vds** 
**IdsP = -IdsN**
directions of current from **pmo** and **nmos** are inverse

<img width="300" height="230" alt="image" src="https://github.com/user-attachments/assets/be9a6b48-9b5e-48f1-bcc2-d6413e676a2b" />

Ex: **pulse 0 2.5 0 10p 10p in 2n** 
starts from 0 ends at 2.5v 
shift at 0 (starts at 0.0)
rise time of **10picosec**
fall time of **10picosec**
complete cycle is **2nanosec**

continue cycle of calculations to figure out robustness of **cmos inverter**. 

---

### **Inception of Layout ∩ CMOS fabrication process**

In the normal **VM** envt:
```bash
clone ts https://github.com/nickson-jose/vsdstdcelldesign
```

<img width="600" height="180" alt="image" src="https://github.com/user-attachments/assets/5a28b7d5-af5f-4455-ac3b-815833d13cb5" />

it'll download into **openlane** & you'll see it if you list the contents.
if you `cd` into the new github you can see all the files that were availble online. 

<img width="600" height="60" alt="image" src="https://github.com/user-attachments/assets/29ad4b95-7136-4ee8-a739-241f9f6d4bb8" />

<img width="800" height="450" alt="image" src="https://github.com/user-attachments/assets/b8718142-8867-4c5e-aea1-3964a174ba0e" />

---

#### **Setup for Mac Users:**
```bash
cd /home/beaver/Desktop/work/tools/openlane_working_dir/

wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz

tar xfz drc_tests.tgz

cd drc_tests

ls -al
```

view with **VIM**, not **GVIM** which is not installed
```bash
vi .magicrc
```

<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/1456c14a-70a5-46fd-b7e2-4e7fe17ff57a" />

make sure you: 
```bash
~/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign$ cp sky130_inv.mag ~/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/drc_tests
```

now inside **drc** files that was downloaded through the **mac silicon patch** run the code the instructor gave:
inside **VM**:

```bash
beaver@openlanevm:~/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/drc_tests$ magic -T sky130A.tech sky130_inv.mag &
```

Basically before the **tech file** was either corrupted or not suited for **apple silicon**. Once you load the patch a new `sky130A.tech` file will come with it in **drc_tests**. So, now that I have the right technology you can just copy the **mag file** and everything should work properly. 

---

### **16-mask CMOS process**

#### **1) Selecting a substrate**
The layout u create get fabricated onto the substrate u pick.
**Well-doping** is what u use to fabricate **pmos** and **nmos**

#### **2) create active region for transistors.**
-> places where u actually see **pmos** and **nmos**
create small buckets thats called **active regions**; pockets should have a small gap between them.

place **40 nm** of **Silicon Dioxide**
place **80 nm** of **Silicon Nitrate** onto layer of **silicon dioxide**
place **1um** of **photoresist**

<img width="450" height="240" alt="image" src="https://github.com/user-attachments/assets/f448322e-d5e3-4e6d-841c-5a6f21e68e16" />

<img width="450" height="240" alt="image" src="https://github.com/user-attachments/assets/bb1529cc-4b66-4690-8652-abf316f50742" />

The way to protect the weels is through **photolithography**; protected with **masks** on top of the **photoresist**. That way the wells are safe. 

Etch of **silicon nitrade**; then remove the **photoresist** itself (chemically removed). Placed in **oxidation furnace** so the other oxide in other areas will grow. 

<img width="700" height="360" alt="image" src="https://github.com/user-attachments/assets/bbd684a0-34ce-42b9-bfaf-7d4a536e471f" />

Creates isolation regions so that the wells have clearly defined isolation areas.
**Field oxide** is grown. This process is called "**LOCOS**" or "**Local Oxidation of Silicon**"

Next etch of **silicon nitrate**. 

#### **3) N-Well and P-Well formation**

<img width="160" height="300" alt="image" src="https://github.com/user-attachments/assets/854f74f6-85aa-4f3b-9e87-64eda373f579" />

**Boron** creates a **p-well** and it's part of the **p-substrates ion implantation**. Needs high energy bc it has to pass through the **silicon oxide** layer and enter the **p-well** in the substrate. Needs at least **200 keV**. It does damage the oxide layer a little bit. 

Similar steps for **n-well** as well. 
Use **phosphorous** as material -for **n-well**; energy needed is **400keV** whihc is higher bc **phosphorous** is a bit heavier than **boron**. 

Place the whole thing into a **high temp furnace**- will cause **drive-in diffusion**. Will create 2 distinct wells. 

<img width="550" height="310" alt="image" src="https://github.com/user-attachments/assets/5ef49164-654d-46c7-abaf-f68e31415868" />

next we have to create a gate. Create 2 diff gates for **pmos** and **nmos**. 

1) Use another **mask (4)** on top of the **n-well** (first put on **photoresist**) then apply **UV light**. **N-well** stays protected.
2) After **UV** **mask** goes away
3) Want **boron** (low energy) to slightly dope the top of the **pwell**
4) put the **mask(5)** ontop of **p-well** (once its doped the way u wnt it) obv put on **photoresist** before the **mask**. Then apply **UV light**.
5) Apply **Arsenic** to dope a slight level of **n-well**.

<img width="550" height="310" alt="image" src="https://github.com/user-attachments/assets/194760b0-aa83-4797-a9ce-c28c4584415a" />

<img width="550" height="310" alt="image" src="https://github.com/user-attachments/assets/bf030357-296a-4800-ba89-833567f7d2bd" />

Original oxide etched/stripped using dilute **hydrofluoric(HF)** solution. Then re-grown again to give high quality oxide (~**10 nm** thin)

1) Deposit a ~**0.4 um** **polysilicon** layer using normal deposition techniques.
2) Dope it with more impurities (**N-type** (**phosphorous** or **arsenic**) ion implants for low gate resistance)

<img width="550" height="310" alt="image" src="https://github.com/user-attachments/assets/1849f50e-3166-445f-9d5c-7d13b478b504" />

3) Deposit **photoresist** then add the **Mask (6)**
4) etch away the excess **photoresist** and the **mask** is etched away.
5) Then through the **UV** the rest of the **mask** not under the **photoresist** is etched away as well

<img width="550" height="310" alt="image" src="https://github.com/user-attachments/assets/a6ea8312-f303-4358-97f6-6e569ba392b2" />

#### **Lightly doped drain (LDD) formation:**
**P+**, **P-**, **N**
**N-well** where ur trying to fabricate the **pmos**
**P+** is src and drain are **p-type**
**P-** is the **lightly doped drain** formation that we're trying to do now. 

Similary for **nmos** (being fabricated in the **pwell**):
**N+** is the src and drain that is **n-type**
**N-** ~ one we're going to form now
**P** is what you already see. 

<img width="550" height="310" alt="image" src="https://github.com/user-attachments/assets/42a4b90a-347e-4544-9963-a1507708494b" />

#### **Why we need LDD formation:**

-> **hot electron effect:**
High energy carriers break **Si-Si** bonds
Energies might be so high that it breaks **3.2eV** barrier built with **Si** conduction band & might enter **SiO2** band which will create issues.

-> **Short channel effect:**
For shorter channels the drain field penetrates channel. The gate basically can't control the src or the drain current. 

 1) place **photoresist** on top of the **n-well** side & add a **mask** on top of that **(7)**
 2) **UV light** will be directed on the **CMOS** (standard steps will remain the same)
 3) Place **phosphorous** (or other **n-type** impurity) and implant it in the **pwell** to create a **n-implant**. To create an **n-** coping concentration. Very lihgtly doped.

<img width="400" height="220" alt="image" src="https://github.com/user-attachments/assets/b21d5a6a-346c-4a96-9052-b1ed779b1bdf" />

4) Repeat the process covering the **p-well** with the **photoresist** and **mask8**
5) next dope **n-well** with **p-impurity**: **boron**

<img width="400" height="220" alt="image" src="https://github.com/user-attachments/assets/b39dedf0-c13b-4867-9cbd-d0cea101ccc9" />

**Plasma anisotropic etching**.
removes all oxide from each part of structure. Helps prevent the most imporant oxide from getting etched away. 

#### **How does Src and Drain formation happen.**

**Channeling** is an effect where you do a lot of **ion implantation** if the vector velocity of the ions matches the velocity of the crystaline structure of the **p-type substrate**. The atoms might go deep- without even getting blocked. So we add thin **screen oxide** to avoid **channeling** during implants. 

<img width="500" height="290" alt="image" src="https://github.com/user-attachments/assets/cf4f028d-6838-4ad9-a24a-1c142745c16f" />

1) Add **mask 9** **nwell**
2) add **arsenic** (**n-type** impurity) **75 keV**
3) **p-** implant in **nwell** and **n+** implant in **p-well**
4) add **mask 10** on top of **p-well**
5) add **boron** in **n-well** (**p-type** impurity for **p-** plant)

<img width="500" height="270" alt="image" src="https://github.com/user-attachments/assets/abe659f8-1403-4714-bd92-974b5d8a5aa5" />

**High temperature annelling**. 

#### **7) Steps to form contacts and interconnects (local)**

Etch thin oxide in **Hydrofloruic** soltion. Gate and Src drain are open for contact. 
Deposit **titanium** on wafer surface, using **sputtering**. **Titanium** has very low resistivity which is why we're using it. 

Take a **Ti** metal and hit it with **Argon** and allow it to deposit on a substrate. 

<img width="420" height="230" alt="image" src="https://github.com/user-attachments/assets/184b6e20-0391-4c7a-ac9a-fa2834366c37" />

**Titanium** gets placed over the substrate. 
low resistance **TiSi2** on top
Next, Waver heated at about **650-700 C** in **N**, ambient for **60 sec**. 
**TiN** is the result and placed on top of the **TiSi2**

<img width="420" height="230" alt="image" src="https://github.com/user-attachments/assets/19edb246-d747-4a34-994f-f3aefda5b2e4" />

Place **photoresist** then **Mask11** on top of some drains 

**Mask** is removed and then **TiN** (light blue) is etched off using **RCA cleaning**. 
Remove **photoresist** now. Left with the some drains having **TiN** left on. 

Deposit **1 um** of **SiO2**, doped with **phosphorous** or **boron** deposited on wafer surface. **Phosphorous** acts as a protection layer (barrier) against the sodium ions. **Boron** is used to reduce the temperature. The wafer will be exposed to higher temps or certain processes that need higher temperature.

Polish the wafer- **chemical mechanical polishing (CMP)** technique fro planarizing the water surace. A little complex, but won't get into that for now. 

Next step is to create contact poles,
Create pins and for connections down the subtrates. 

<img width="450" height="240" alt="image" src="https://github.com/user-attachments/assets/fa173a87-d654-439e-b69c-c244942c975c" />

**Mask/photoresist** placement depends on where you want to drill the contact poles. Anywhere you want to drill them is where you'll create a small break in **photoresist/mask**. Above shows the vids example. 

Deposit thin layer of ~**10nm TiN**
Acts as a good barrier layer between the bottom and top interconnects. 

Blanket **tungsten (W)** layer deposition- help create a good contact from bottom to top. 

Again **CMP** technique and remove extra **tungsten** for planarizing wafer surface. 
Use **alumninum** layer at the top level and add **mask 13/photoresist** & etch out any **alumnimum** not directly under the **photoresist**. 

Add another hgihter level metal!!!

Deposit **SiO2** and use **CMP** to polish; then go through normal **photolithography** steps to drill the contact holes (**mask 14**). 

Depost a small **TiN** layer between and then deposit **tungsten** again as contacts holes. 

<img width="450" height="240" alt="image" src="https://github.com/user-attachments/assets/d642cc72-3ab1-4761-b719-b1f17362a342" />

this is the same as the first layer of interconnects that I already wrote before. 

**Mask15** to add more layers of **aluminum** on top of the contact holes. As you go from bottom to top the thickness of the interconnects increases. Deposity **Si2N4** to protect the chip as a whole. 

<img width="450" height="240" alt="image" src="https://github.com/user-attachments/assets/510be848-fb58-4480-9d84-c50c47cd2ba3" />

This is what your final **cmos** should look like during fabrication. You still need to drill holes on top of the **Si3N4** in order for there to be contact throughout your **cmos**. 

---

### **Lab Steps to create STD Cell Layout and Extract SPICE Netlist**

Image credits: **Nikson-Jose/VSDSTDcellDesign Github Repo**
<img width="300" height="210" alt="image" src="https://github.com/user-attachments/assets/3b9e7f31-24a1-47c7-a1e6-77afa5cd56c4" />

On the right side all the colors are the layers the you can choose. 
Ex:
**Metal1** ~ light purple
**Metal2** ~ pinkish color
**N-Well** ~ Solid dark dashed lines

Hover over a color ~ it'll tell you what the layer means at the top. 
When selecting a layer (use `s`), move to the **tkcon** window and simply type `what`. The `what` will explain what the highlighted portion. 

If you want to see if there are ocnnections between 2 different parts of a circuit, press `s` 3 times.

<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/4a2384f7-0d55-4650-b939-5fdf137520cd" />

**LEF** only contains material about where to place a cell. So, in the abstract you can see that it only has info about placement and not connectivity.

When selecting- white dotted line means **node error**. Click **DRC** tab and then the tool will zoom in on what the error actually is. Then the tools will write the error in the **tkcon** window. 

Enter the following command in **tkcon** (the window that pops up when running **magic**) in order to recieve the **SPICE netlist** from the layout. 

```
-> extract all
-> ext2spice cthresh 0 rthresth 0 
-> ext2spice
```

if you go back and open the `Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign` (whereever your **magic** was) you should see a new file called `sky130_inv.spice`. whihc is the **netlist** that was just extract. 

<img width="650" height="250" alt="image" src="https://github.com/user-attachments/assets/18d4b7b8-3868-4125-8c82-eb05b7bb8b69" />

<img width="680" height="400" alt="image" src="https://github.com/user-attachments/assets/ed16e003-208d-4363-a6a9-1b4d5bc1cae4" />
This image shows the new **spice extracted** file. 

```bash
-> nano sky130_inv.spice
```
This shows the contents of **spice file** (will need to **sudo**)

<img width="750" height="500" alt="image" src="https://github.com/user-attachments/assets/d9331c5a-9418-4953-a64c-73f43a9d6aa6" />

**pshort** means **pmos** and **nshort** means **nmos**

In order of **Drain, Gate, Src, Substrate**. 

<img width="270" height="300" alt="image" src="https://github.com/user-attachments/assets/786c1a02-a424-43c1-85ca-ec4e5a55a9fc" />

This is what the extracted **spice file** is telling us. 

<img width="620" height="520" alt="image" src="https://github.com/user-attachments/assets/38c38da3-1657-49e9-97b2-ad785b8e9451" />

---

### **Lab Introduction to Magic tools options and DRC rules**

The goal is the find problems in the lab and fixing the errors. 
**CIF** ~ **Caltech Intermediate Format** is a human readable **ASCII** format. 
**CIF** and **GDS** are used interchangably. 

Documentation @ **opencircuitdesign.com/magic/** or **../technologyfiles/**

They also detail the rules about verification. **Skywater 130 pdk** is **open src** created by **google**. 

#### **Lab introduction to sky130 pdk's and steps to download labs**

documentation @ **skywater-pdk-130.org/readthedocs.build/en/136**

**opencircuitdesign.com/open_pdks/archive/drc_tests.tgz**

#### **Lab Introduction to Magic and steps to load Sky130 tech-rules:**

```bash
-> magic -d XR in drc_tests
-> file then open met3.mag
```

<img width="750" height="600" alt="image" src="https://github.com/user-attachments/assets/7805854d-599a-4ed8-a1a7-a6834c9432a0" />

```bash
-> cif see VIA2 in tkcon
```

<img width="290" height="170" alt="image" src="https://github.com/user-attachments/assets/98c1dace-0d92-441d-97a4-e063d7ecf995" />

<img width="480" height="350" alt="image" src="https://github.com/user-attachments/assets/e0820ac9-d7b2-4187-b917-45d7fe243feb" />

First find the error, so `nano` the `sky130A.tech` file. 
**DRC** rule starts on line **4702**.

<img width="700" height="580" alt="image" src="https://github.com/user-attachments/assets/58985ff5-1155-4a34-abce-095337cd1106" />

Then look for **poly.9**

<img width="700" height="580" alt="image" src="https://github.com/user-attachments/assets/7e6f14df-24f1-4496-9ea2-021bedf062e6" />

Update **tech file** in spacing line and replace **\*nsd** to **allpolynonres**. 

Then in order to update **magic** just run **DRC check** in the **tkcon** window and you don't have to relaunch **magic**. 

Every **n-well** must have an **n-tap** layer in contact. 

<img width="700" height="580" alt="image" src="https://github.com/user-attachments/assets/4552a684-5235-42f1-8f07-bad6a7b63e4d" />

Added **variant (full)** & **variant \*** to the top and bottom of new **nwell_untapped** rule. Need to check if it works using the same **nwell** load command. 

<img width="350" height="270" alt="image" src="https://github.com/user-attachments/assets/8ec98738-6f7a-46a1-b801-46c3332ed39f" />

when the **tech file** was modified the spacing between **npolyres** and all types of diffusion were not considered which is by definition- an error
-> easy fix is changing the **\*nsd** to **alldiff**.

**temp layers** have unique features that can be used as building blocks for other layers.








