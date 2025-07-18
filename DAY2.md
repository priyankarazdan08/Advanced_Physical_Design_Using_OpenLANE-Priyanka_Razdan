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

<img width="1440" height="861" alt="image" src="https://github.com/user-attachments/assets/bbd876f0-eb96-4554-86a5-783acff4ade8" />



Select the layout and move it to the center of the display. Select s on your keyboard (will see it get highlighted) and then press v (will fit layout on screen). To zoom in: left mouse click and right mouse click to form box you want to zoom to then  you press z. 

set io pin as equidistance on the chip. 

press s on keyboard to highlight layer. then if you go into menu and type what you can see waht the layer you selected is made out of. 

standard cells exist in the lower left area. zoom in on the area. 







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
CTS is the next step after placement. ClockTree Synthesis will take care of the clock signal for equal rise and fall time. 
Routing comes next- needs certain flow; depending on characteristics of he ff ur trying to route. 


