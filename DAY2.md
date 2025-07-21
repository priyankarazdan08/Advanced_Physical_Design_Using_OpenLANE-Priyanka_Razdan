Day 2 - Good floorplan vs. bad floorplan and introduction to library cells

Utilization factor and aspect ratio

1) Define Width and Height of Core and Die

Width and Height of chips is dependent on size of the logic gates sitting in the chips. 
Launch Flop -> And Gate -> Or Gate -> Flip Flop
Dimensions of standard cells are more important than wires. 

<img width="460" height="309" alt="image" src="https://github.com/user-attachments/assets/0a68b164-ddb7-4054-836e-ab61df2598af" />

minimum area that will be occupied by netlist will be all standard cells back to back ignoring wires. 

The core of a chip is where you place the fundamental logic of a design (this is like on the inside). A die consists of a core is small semiconductor material specimen on which the fundamental circuit is fabricated. 

For determining the height and core of the die. Place all the logical cells inside the core @ 100% utilization. 

Utilization Factor = (Area Occupied by Netlist)/(Total Area of the Core)

<img width="460" height="202" alt="image" src="https://github.com/user-attachments/assets/58eba3f7-44e3-477f-9598-8e71bfcaa03f" />

area occupied by netlist is total size of all gates together and total area of core is how much space the core of the chip has to accomidate. If num and denom are same then utlization factor is 1 = 100% occupied. 

Ideally, aim for 50-60% utlization. 
Aspect Ratio = Height/Width

If aspect ratio is 1- chip is a square shape. 


<img width="460" height="301" alt="image" src="https://github.com/user-attachments/assets/5697c68c-fad3-4514-9e4b-bb3b6947a298" />

Example:
Combinational logic (does some amt of job)-> does big task that the output of it is a huge circuit (50k gates)
Take piece of gate seperately- divide circuit into parts. 

<img width="528" height="170" alt="image" src="https://github.com/user-attachments/assets/bb99a892-735a-4743-ba21-bd0fec399eab" />

2 blcoks implemented seperately. Extend IO pins. 

<img width="528" height="318" alt="image" src="https://github.com/user-attachments/assets/26b2f588-5861-4607-aae3-6477a084af0f" />

outputs of first block will be inputs of second block (will both have 2 diff IPs or modules. Those blcoks will get replicated on netlist to perfrom- small modules can get used multiple times. 


Other IPs are availble: memory, clock-gating cell; comparator; mux. 
Can be instantiated only once. You must know the arrangemnet of IPs in chip. This is called floorplanning. These are called pre-placed cells bc you must know where they go. Automated place ment and routing tools places the remaiing logical cells in the design onto chip. 

must surround pre-placed cells with decoupling capacitors. 

There will be a voltage drop along the circuit before reaching output. If voltage drop is too high might reach an unsafe zone (won't pullup to vdd). (problem with having a large physical difference in a circuit). If you add a decoupling capactior (powered up) it will make sure there isn't a big voltage drop bc the decoupling capactior provides the voltage it needs. The name "decoupled" shows that its decoupled from the circuit. It charges up during discharge when it doesn't need ot be providing charge. 

<img width="654" height="367" alt="image" src="https://github.com/user-attachments/assets/385c3c28-72dc-4bc2-ba3d-b13bc4b89f94" />


If the macro is repeated multiple times on the chip- there will be need for power for each one. Not feasible to put capacitor on each element. 
1 -> vdd
0 -> gnd

Ground bump -> when using an inverter and all capacitors that are charged to vdd all discharge to gnd at the same time. All water taps are being opened at the same time. Might go into an undefined region. 

Voltage dropp -> same thing vice versa.

<img width="654" height="367" alt="image" src="https://github.com/user-attachments/assets/09dc5737-f7e2-4de8-a15c-ecdd629f8299" />

<img width="654" height="367" alt="image" src="https://github.com/user-attachments/assets/1a40f1d1-08bf-4778-ba68-6055c69833fe" />

solution is to grid it and make multiple drains and srcs so that each can take its current from nearest power supply. see logic in img above. 
This is exactly our power planning and why our chip has vss and vdd grid. 

<img width="654" height="367" alt="image" src="https://github.com/user-attachments/assets/394dd45c-7503-43fb-835f-3b46288bff50" />

see chip power planning design above. 

interclock timing means 2 diff clocks running for diff flip floppers. put ff that run on same clocks tied togehter. The connectivity information between the gates is coded using VHDL/Verilog language and is called as the "netlist"

pin placement is dependent on the user. in the example shown all input ports are on the left and output ports are placed on the right. 

<img width="593" height="315" alt="image" src="https://github.com/user-attachments/assets/e5d6ce28-0adf-4e44-abfa-87a234adea41" />

notice that the outputs and inputs are random. try to place each block near its inputs and outputs. need complete logic between the blocks- both front end and back end need to agree of placement of pins and blocks. clocks are the ports that drive all cells simultaneiously. need to least resistant path for clk1 and clk2. same for clk out. thats why they're bigger "boxes" or pins so that hteres less resistance (more area = less resistance). Needs a logical blockage around core to not allow anything where the pins are. Then you are done with floor planning. 


<img width="593" height="466" alt="image" src="https://github.com/user-attachments/assets/24ba1cc7-8b6e-41d7-b168-6470309bf5f1" />
(defualt setting for floorplan)

in config.tcl -> the vmetal and hmetal (amt of "lines"  basically) is always one more than you specified. if you specificed 4 there will be 5. 

Steps to run floorplan using openLANE:
(post-synthesis)
1) run_floorplan
2) go back to terminal you can see that config.tcl has overriden the system defaults.
3) config.tcl was overriden by sky130A_sky130_fd_sc_hd_config.tcl

<img width="590" height="571" alt="image" src="https://github.com/user-attachments/assets/261e0bc7-0ab4-48d2-bbc9-2bb4596f3b7f" />

<img width="590" height="571" alt="image" src="https://github.com/user-attachments/assets/cd1f3698-016d-47ef-ae47-07d7516b9cc9" />

4) in current run folder: cd results/floorplan/
5) less floorplan.def
to see layout check magic
6) magic -T

7) <img width="590" height="571" alt="image" src="https://github.com/user-attachments/assets/32ca5df7-714a-41f2-92f7-892c635a3447" />

ok, the reason it is very long is because you must download magic and then use the command otherwise it won't work. This tab should pop up on the command:

<img width="1361" height="756" alt="image" src="https://github.com/user-attachments/assets/4d5555c9-9f96-4c97-acc0-c62f5306055b" />

Select the layout and move it to the center of the display. Select s on your keyboard (will see it get highlighted) and then press v (will fit layout on screen). To zoom in: left mouse click and right mouse click to form box you want to zoom to then  you press z. 

set io pin as equidistance on the chip. 

press s on keyboard to highlight layer. then if you go into menu and type what you can see waht the layer you selected is made out of. 

standard cells exist in the lower left area. zoom in on the area. 

<img width="761" height="756" alt="image" src="https://github.com/user-attachments/assets/b81b1f48-115d-4b09-b481-b69ee43620a9" />


Netlist bdinging and inital place design:

1) bind netlist with physical cells
   
Library has information about the physucal cell:
-> width hight of cell
-> delay information
-> conditions for the cell.

<img width="699" height="384" alt="image" src="https://github.com/user-attachments/assets/78a80385-c171-4bc1-84b3-f3d1f65ba258" />

2) placements

closer blocks are placed delay reduces. ff1 -> din2 ff2 -> dout2

3) optimize placement
stage we estimate wire length and capacitance and, based on that, insert repeaters. Estimate the capacitance.

signal integrity; depends on distortion of it based on distance from input to ff1
repeaters need buffers that will reproduce og signal and create new one and send it to next one.

<img width="604" height="341" alt="image" src="https://github.com/user-attachments/assets/3fac0a9b-e77c-473a-9896-c85e247e942a" />

connections between different ffs are in differnet layers. for example the blue one will be in a diff layer than the green one so its ok if the path of their signal transfer overlaps. 


Library characterization and modelling:

Spread out gates based on functionality or based on logical connection that we get out of logical sunthesis step. We take care of the distance between gates with buffers as its not a problem as each different type is on differnt layers.

CTS is the next step after placement. ClockTree Synthesis will take care of the clock signal for equal rise and fall time. Very important & related to library characterization step. 
Routing comes next- needs certain flow; depending on characteristics of the ff ur trying to route. 

One thing is common across all stages "Gates or Cells"

<img width="148" height="180" alt="image" src="https://github.com/user-attachments/assets/5be2d170-96e3-48df-a8de-2f64b1cd9acf" />

Placement in OpenLane occurs in 2 stages: 1 is global and the other is detailed placement. There are different tools to do both of those functionailities. 

Global placement is course plament: there is no legalizations happening. Global placement just optimizes placement so it allows overlap and has some illegal placement that can't actually be on a chip. Main objective is to reduce the wire length. 

after floorplan & synthesis:
do run_placement in the openlane environment. If you closed the terminal like me, you probably need to recreate it by running synthesis then floorplan again. This will create a new run folder with todays time. 


After running placement- both phases should be complete and running magic should allow you to see the new placmenet of cells. 

1) make sure you are in your current run folder/results/placement
2) use the same command as before: magic -T home/beaver/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def read picorv32.def

PSA- since we're running a newer version of openlane we are using merged.nom.lef instead of merged.lef. merged.lef wasn't created after running synthesis step. 

<img width="890" height="756" alt="image" src="https://github.com/user-attachments/assets/ae46401c-79b3-4e15-bf44-70223db36e18" />

<img width="890" height="756" alt="image" src="https://github.com/user-attachments/assets/ed12c1b1-88f6-4a79-b589-a3f72780d947" />

after zooming in you can see the different placement in standarad cells. powerplanning post cts. 


Cel Design Flow:

<img width="820" height="461" alt="image" src="https://github.com/user-attachments/assets/68e96e0d-52aa-40c8-9109-8b2855c088ff" />

if you pick up any of the cells in the design- they are referred to as standard cells. Standard cells are placed in a section called libraries:
-> The library has got different gates with different functionalities. Library also contains different sizes of each functionality- referred to as drive strength. Library contains physical characteristics, functionalities, and contains information about threshold voltage of different kinds of standard cells. 

<img width="820" height="461" alt="image" src="https://github.com/user-attachments/assets/0dfa0e8a-d1b9-4856-933c-d3c9c35376be" />

Each standard cell has to go through the typical steps of the cell design flow. Even something as small as an inverter. 

The cell design flow is being divided into 3 parts. 
1) Inputs- The inputs u need to design your standard cell
2) Design Steps- the steps to design it
3) Outputs- the ones you actually use with the eda tools.

The inputs:
PDKS (process design kids): 
-> DRC & LVS rules, SPICE models, library & user-defined specs. 
-> DRC & LVS: Each contains rules for being able to design the cell. Foundry Rules are very important and must be followed in order to create a viable cell. There can be thousands of rules in the typical IC design flow. 


-> Spice models: 
contains parameters & equations for things like threshold voltage & other rules that must be followed in order to fabricate the cell. 

-> Library and User-defined specifications:
Cell-heright is determined by power rail and ground rail. Responsibilty of developer that the cell height is maintained. The cell width is dependent on the timing of formation. resonsibility of library developer. This isn't defined by the foundry rather defined by the user. Another example that is defined by the user is supply voltage- which is provided by the top level developer. Must follow all these rule once set. Another example is metal layers- might be a requirement that the metal layers should be built on specific metal layers(ex: metal1 metal2 metal3). Pin location, Drawn gate-length are other typical example of user-defined specs. 

It is the responsibilty of the dev to take the inputs and come up w a cell that aligns with these rules so that it is possible to create. 

<img width="674" height="375" alt="image" src="https://github.com/user-attachments/assets/d0c907fb-b302-4621-a4ab-2ce9c0226108" />

Design Steps:

-> Circuit Design (mostly based on SPICE simulations):
1) implement function itself
2) model pmos and nmos transisitor in order to meet library requirements. (drains shoudl add up to 0 usualy)

-> Layout Design
1) Get function implemented through a mos transisotr
2) get pmos and nmos netword graph out of design you've implemented

Euler's path is a path traced only once -> need this for pmos and nmos network graph. Next step is to draw a stick diagram out of it.

<img width="674" height="375" alt="image" src="https://github.com/user-attachments/assets/f9213b6c-8891-4b2d-af0c-e74d66c95f00" />

characterization:
get timing, noise, power .libs, functionality of specific circuit. 

<img width="674" height="375" alt="image" src="https://github.com/user-attachments/assets/932fb3d4-6bb0-4d72-b456-ec706bec2a16" />

the extracted spice netlist will give you some informatoin about the standard cell. nmos spice models are nothign but chracteristics of nmos transistor. 
1) Read Model Files
2) Read Extracted Spice Netlist
3) Analyze behavior of buffer
4) Read the subcircuts of the inverter
5) attach the necessary power soruces (vdd, gnd)
6) to apply the stimulus
7) provide necessary output capacitance
8) provide necessary simulation commands
   
These are 8 diff characterization steps. 

put these 8 steps in -> Guna -> gives a model with timing, noise, power .libs, function
= means we have the timing chracterization, power characterization, and noise characterization. 

Timing Characterization:
<img width="674" height="375" alt="image" src="https://github.com/user-attachments/assets/5655952e-2256-4fe5-8e63-ca9520ff92c6" />

Has waveforms from both inverters (red is output of first inverter) which goes as the input to the second inverter (blue)

Low = means values that are close to 0 power supply

Calculation of slew rise: take timing between points 20 percent from high point and 20 percent from low point. 

in_rise_thres = if you want to calculate delay- you need points to calculate the delay. Take 50% point of slew waveform that's input. 
out_rise_thr = 50% of output waveform thats output. 

if you apply fall the def and wording remains the same- except for the fall waveforms. 

Propogation Delay:
time (out thres) - time (in thres)
should give exact delay!


if the threhold points move- the delay will probably be wrong. 
If delay is negative- you've usually had poor choice in choosing rhe points for thresholds in input and output. 

If your circuit isn't designed properly and even if u did pick the right points for thresholds in input and output- you will still get a negative delay which means you didn't optimize your wires and are experiencing huge wire delays. 
Ex: 

<img width="674" height="375" alt="image" src="https://github.com/user-attachments/assets/a9712757-1f1f-4a68-850b-044ad7725371" />

<img width="674" height="375" alt="image" src="https://github.com/user-attachments/assets/061cd274-3aed-4b76-8e0a-7bfe892c0a76" />

Transistion Times:
For rise waveoform-> time(slew_high_rise_thr) - time(slew_low_rise_thr)
For falling waveoform-> time(slew_high_fall_thr) - time(slew_low_fall_thr)

<img width="674" height="375" alt="image" src="https://github.com/user-attachments/assets/52b3bbee-a1ab-46c4-a978-6dcf57be1d8a" />












