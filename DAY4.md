
When making the standard cells you need to follow specific guidlines

1) Input and Output both must lie on the intersection of vertical and horizontal tracks. 
2) The fit of the standard cell should be in all multiples of the track pitch the height should be reminisent of the vertical pitch.

<img width="804" height="694" alt="image" src="https://github.com/user-attachments/assets/723b106e-1475-4e2b-bf6c-d618afa03da0" />

open ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd$ 
less tracks.info

<img width="804" height="694" alt="image" src="https://github.com/user-attachments/assets/79a26df4-923e-443d-97cf-ce0061fa6248" />

Tracks are used during the routing step. Routes can go over the tracks which are the layers. Routes are basically metal traces. 

li1 X 0.23 0.46
li1 Y 0.17 0.34

spaced 0.46 around x
and 0.34 around y.

In magic if you press g the grids get activated and then zoom in you can see the grid that is the reference for drawing the layout. 

go into tkcon window:
grid 0.46um 0.34um 0.23um 0.17um 
grid [xSpacing [ySpacing [xOrigin yOrigin]]]

incrreases each invididual grid size

<img width="753" height="608" alt="image" src="https://github.com/user-attachments/assets/8461cd14-0c21-43e6-9429-47d4b7133129" />

<img width="790" height="789" alt="image" src="https://github.com/user-attachments/assets/dcd41acd-a770-4e4b-86f9-8d217fdf8072" />


new grid sizes get bigger after changing grid size in tkcon

select port then write what to get information about what was selected. 

save sky130_vsdinv.mag
(command in tkcon)

go back to vsdstdcelldesign/drc_tests and you can see new file saved called sky130_vsdinv.mag

<img width="790" height="789" alt="image" src="https://github.com/user-attachments/assets/de004639-48bb-4862-8b13-21225593f2ae" />

magic -T sky130.tech sky130_vsdinv.mag &
type this in right after in same directory

now we have to extract the lif file. 
lef write
in tkcon
<img width="790" height="789" alt="image" src="https://github.com/user-attachments/assets/78130c6d-d13c-4269-991c-dca657b9a927" />

new lef file has been created in same dir.

setting a layer as port declares pin. Lef file has all this information about pins and pin order. 

<img width="790" height="789" alt="image" src="https://github.com/user-attachments/assets/5687d021-9b2d-48ae-921e-21112c75d090" />

plug this into picorv32 flow. 

cp sky130_vsdinv.lef ../../designs/ci/picorv32a/src
lef file now included in src. 

need to have library for cell definition for synthesis. 
tool should match cell during synthesis flow so need to copy libraries into design file. 


cp sky130_fd_sc_hd__* ../../designs/ci/picorv32a/src

so that all libs get copied into src. 

go into openlane launcher (get out of vm)
-> ./flow.tcl -interactive
-> package require openlane 0.9
prep -design picorv32a

normally if you run this you would add another directory in RUNS instead of continuing an old one. So in order to continue the last one, instead 

prep -design designs/ci/picorv32a -tag [most recent run] -overwrite

<img width="819" height="722" alt="image" src="https://github.com/user-attachments/assets/9a7351ed-bc1d-44aa-9ee1-7355cda33289" />

1. set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
/openLANE_flow/designs/picorv32a/src/sky130_vsdinv.lef
2. add_lefs -src $lefs

re run the flow:
<img width="502" height="497" alt="image" src="https://github.com/user-attachments/assets/4e1d31b1-105e-421e-a88e-198d881e635a" />

Introduction to Delay Tables:

Whenever enable pin was connected to logic 1 then thats when the gate will be allowed to connect to rest of the circuit. 
During the time when it doesn't need to be on, so you're saving a lot of power. 

Split the load of 4 flops directly onto 2 buffers then that was split on one buffer. 

<img width="202" height="151" alt="image" src="https://github.com/user-attachments/assets/42971c71-2c8f-4f28-90a0-85efccc518fa" />

Clock gating technique- swap or gate above with and gate? There are things that may go wrong so will look into it later. 

<img width="202" height="151" alt="image" src="https://github.com/user-attachments/assets/3731aff0-a7b4-48a4-8dde-ae6e5439dea0" />

Observations:
-> 2 Levels of buffering
-> At every level, each node was driving same load of 55 farads
-> Identifical buffer at same level. (buffer size at level 1 is size 1 and buffer at size 2 should be size 2) Only buffers in the same level must be uniform. 

Load at the ouptut is varying- if the output load is varying the input load must be varying as well. So, the problem is varying buffer loads. 

Delay tables is solutions- 2 dimensional table. 

<img width="341" height="158" alt="image" src="https://github.com/user-attachments/assets/31b297d0-56fc-46f0-9ec1-bbbf380140be" />

the delay table becomes the timing model for buffers at the size the table is made for. So buffer at level 1 has a diff delay table from a buffer at level 2. 

It is possible to build an equation from a given set of data in a delay table and use it to estimate the rest of the values in the table.

When you change the size of something you vary the resistance of it~ varying RC constant which is basically the delay of something. 

if buffers have same input transition times, capacitative loads, and sizes, they have equal delays.

The skew value is calculated by looking at the difference of delay of buffers at the same level.
Negative skew (no matter how small) can be extremely bad when designs are scaled up to large sizes with large timing conflicts- must be caught in early clock tree creation.

Lab steps to configure synthesis settings to fix slack and include vsdinv

in openlane directory:

1. cd configuration/
2. less README.md

look through synthesis report and see where delay is building up. (anything >0.5)
wns is worst slack which is what gets reported (maximum slack)
tns is total negative slack~ addition of all negative slack

<img width="670" height="305" alt="image" src="https://github.com/user-attachments/assets/1313d7e6-930a-4069-8600-bdf00a52cc6c" />


strike balance between delay and area using strategy. you can change values during flow- don't need to redo flow. 

in openlane launcher
-> echo $::env(SYNTH_STRATEGY)
should be at 2. (mines at 0?)
-> set ::env(SYNTH_STRATEGY) 1
this sets it to one- this should increase area a little but timing itself should improve. 

<img width="696" height="680" alt="image" src="https://github.com/user-attachments/assets/c7d1df00-5c9d-4c9b-86e6-816ef1316a9d" />

SYNTH BUFFERING is enableing abc cell buffering~ reduced wire delay by inserting buffers. \

-> echo $::env(SYNTH_STRATEGY)
should be 1. 
-> echo $::env(SYNTH_SIZING)
should be 0 change to 1. 
SYNTH SIZING ~ enables abc cell sizing
-> set ::env(SYNTH_SIZING) 1

DRIVING CELL INPUT drives the to input ports. 
-> echo $::env(SYNTH_DRIVING_CELL)
I have sky130_fd_sc_hd_inv_2, but instructor sir has inv_8. 

1) run_synthesis
creates a netlist with better slack. One indicator of everything working is the that the chip area increased. Slack has been reduced from -15.96 to -3.17. 

Chaning properties before synthesis is very important. 

2) run_floorplan
3) run_placement

I had to redo it bc apparently changing synth sizing to 1 ruined synthesis for me. so had to start from the beignning and overwrite newest run. 

<img width="696" height="680" alt="image" src="https://github.com/user-attachments/assets/e84d4373-6db0-4a91-b4ab-cd8248d631b1" />

nagivate to [current run]/results/placement and run the following command (after running the previous ones):

magic -T ../../../../../../../pdks/sky130A/libs.tech/magic/sky130A.tech ...

didn't work so i tried:

magic -T ../../../../../../vsdstdcelldesign/drc_tests/sky130A.tech lef read ../tmp/merged.nom.lef def read placement.def &

<img width="696" height="680" alt="image" src="https://github.com/user-attachments/assets/cd3b1270-935a-4cf0-91ae-3ebacf1a4f63" />

This only worked because due to my mac my files r in a differnt place and my tech file in pdks doesn't load as a technology for some reason.

pay attention to overlap of this cell. 

CONGRATS YOU DID IT MADE UR CELL!!!!



Timing Analalysis (with ideal clocks)
Setup Analysis -Single Clock

-> The way a flop works:

When clock is 0- D goes through mux to get from one point to the other. There is mux1 delay and mux2 delay. Combinational delay should be less than D. 

<img width="593" height="401" alt="image" src="https://github.com/user-attachments/assets/24a06fca-5783-4139-8064-62380d40e6e8" />

When edge at T flop period reaches at clock port flop knows it will need finite amt of time for input that is coming at d to bring it somewhere at middle of flop. That amount of time must be subtracted from total clock time delay T. 



theta < (T-S) 
theta is commerical delay. This will ensure that the system will run. 

<img width="740" height="459" alt="image" src="https://github.com/user-attachments/assets/fabe618e-cff2-4b24-9e77-0101fa614502" />


more practical scenarios include jitter:
When clock is being created by PLL the clock is expected to send clock sgnals at 0, T, 2T and etc. 

<img width="740" height="459" alt="image" src="https://github.com/user-attachments/assets/3a7c59c3-974f-4b4a-831a-823b37c7add9" />

Clock circuitry is based on real chip so theres pos and neg variations. 

Clk src might not be able to generate a clock period whihc provides clock edge at the exact time its ecpeted to (0, T) 

So there is variation in clock period thats basically called jitter. This variation still accounts for failrure of circuits. 


So need to update timing equation because of jitter. 
Jitter is referred to as temporary variation of clock period. 
combinational delay < clk period - setup time

add another parameter called uncertainty:

so combinational delay < T - S - Su
where Su is th esetup uncertainty

<img width="727" height="133" alt="image" src="https://github.com/user-attachments/assets/709b673b-2346-4e65-b41d-388b8ccf9f79" />





<img width="740" height="459" alt="image" src="https://github.com/user-attachments/assets/9746cdb0-296a-4572-89e5-0add2ac57e8c" />
image above is for single clocks. 


θ = Flip-flop 1 clock-to-Q delay + estimated wire delay between flip-flop 1 and gate 1 + delay of gate 1 + estimated wire delay between gate 1 and gate 2 + delay of gate 2 + estimated wire delay between gate 2 and flip-flop 2

once this is true placement optimization will be correct (for single clocks)



set ::env(SYNTH_CAP_LOAD) 17.65
<img width="380" height="47" alt="image" src="https://github.com/user-attachments/assets/bb869a17-c190-443b-9094-aac6f632dd3b" />

pre_sta.conf 


<img width="725" height="89" alt="image" src="https://github.com/user-attachments/assets/355e4376-8227-45bd-b014-dfc243637888" />

The delay of any cell is basically a function of:

Input transition - more input slew means more delay
Output load - more output capacitance means more output delay

look at delay it is high. 
go back in openlane dir and do 
1. less pre_sta.conf
2. sta pre_sta.conf

check what is adding delays and modify those files. 

This is the iterative nature of the P&R flow - as you run forward in the flow, you realize there might be need for additional modifications.

<img width="260" height="89" alt="image" src="https://github.com/user-attachments/assets/ce530b56-eeda-4d04-8ba7-b4fd32910fd3" />

x is slew. 

Further optimization can be done by upsizing buffers that are driving high fanout nets:
replace_cell _41882_ sky130_fd_sc_hd__buf_4 
-> replaces the cell at that file.

report_checks -fields {net cap slew input_pins} -digits 4
-> this expands precision by 4 digits.

report_checks -from _50144_ -to_50075_ -through _44322_
this will allow you to see the new slack

then report_tns and report_wns in order for terminal to print each from the new report. 

------
Clock Tree Synthesis (4th Step)

Clock tree synthesis ensures that clock signals reach all flip-flops with minimal skew. Without proper clock tree design, different flip-flops would receive clock signals at different times due to varying wire lengths.

<img width="1067" height="458" alt="image" src="https://github.com/user-attachments/assets/51431044-a237-405d-9cdf-48fc50fe05de" />

Showing the connections with CLK1 (bad tree)

Essentially t2 - t1 = skew (we want skew to be as closet 0 as possible) except if you look at the image below t2 is obviously greater than t1 (not equal). 

<img width="454" height="156" alt="image" src="https://github.com/user-attachments/assets/61a37f6d-d73b-4557-a8ae-0f63519647df" />

So to solve this: 

The H-tree algorithm (midpoint strategy)
-> Calculates distance from clock source to all endpoints
-> Finds the midpoint of all flip-flops
-> Builds a balanced tree from that midpoint
-> Recursively applies the same strategy for sub-branches

This midpoint strategy ensures that clock reaches each flip-flop at approximately the same time, minimizing skew.


Clock Tree Buffering

Physical wires have resistance and capacitance. When a signal travels through long wires:

-> It must charge all capacitances in its path
-> Signal integrity problems can occur
-> Transition times degrade

Ex: if you have a step input ideally it shouldn't form a wave down but bc of some the resistance due to the length of the wire, the charge disspiates as its going through. 

To fix this: add repeaters (clock buffers) along the clock path.
Key difference: Clock buffers have equal rise and fall times, unlike regular data buffers.

Next step is clock net shielding:

Clock nets are critical nets that need protection from crosstalk. Shielding involves:

-> Placing shield wires on either side of clock nets
-> Connecting shields to VDD or ground (non-switching nets)
-> Breaking coupling capacitance between aggressor and victim nets

Problems without shielding:

Glitch: Unwanted voltage spikes that can cause false switching

<img width="533" height="352" alt="image" src="https://github.com/user-attachments/assets/d37896c8-953e-4dbf-b280-61464f0c1a02" />

(incorrect data in memory will result in inaccurate functionality)
Example of what could go wrong without sheilding is in the img above. 


Delta delay: Timing variations due to coupling effects

<img width="850" height="493" alt="image" src="https://github.com/user-attachments/assets/e758301d-ba69-48d9-b8c9-d3d183c1e0ff" />

will impact logic by some delta delay without shielding. Delay will change from delay to delay + delta. 


replace_cell _47972_ sky130_fd_sc_hd__buf_8

then reprint reports and you can see slack at that cell decrease by 1. 

After timing optimization in STA, the modified netlist needs to be written back:

write_verilog /Desktop/work/tools/openlane_working_dir/openlane/designs/ci/picorv32a/runs/RUN_2025.08.03_01.40.39/results/synthesis/picorv32a/synthesis.v

run_floorplan

Core Area has increased. So, this shows that if you want to better something you have to compromise another. This is true for timing, power, and area. 

Next stage is CTS: only command you have to run is ->
run_cts


Key CTS settings (foundin README.md):

-> CTS_TARGET_SKEW: Global skew target (20 picoseconds)
-> CTS_ROOT_BUFFER: Starting buffer for clock tree (buf_16)
-> CTS_TOLERANCE: Trade-off between Quality of Results (QoR) and runtime

QoR for CTS includes:

-> Better skew values
-> Correct pulse width
-> Proper duty cycle

QoR stands for Quality of Result. Higher val of QoR will means that your design meets specifications more, but it would mean a longer runtime.

A new CTS file should have been added. has previous synthesis file + the new clock buffers added after running. This should be in synthesis file in results. 

(cts not running for some reason on my vm so the rest of this will be me listing the commands of what I would do if it was running and will hoepfully be able to debug soon)

***
/Desktop/work/tools/openlane_working_dir/openlane/designs/ci/picorv32a/runs/RUN_2025.08.03_01.40.39/results/synthesis/picorv32a/synthesis_cts.v
***

open 

../openlane/scripts/tc_commands/
less cts.tcl 

<img width="792" height="561" alt="image" src="https://github.com/user-attachments/assets/725846bd-286d-4903-94ea-aefe937bbe5b" />

procs are like functions/methods 

<img width="792" height="561" alt="image" src="https://github.com/user-attachments/assets/b2070932-77dd-46a3-ae03-3409e307a827" />

above is what happens when you do run_cts


openroad does all the floorplanning, placement, cts, optimization, and global routing steps. (if you look back at the flow)

so we should be in the openroad folder in scripts instead. 

<img width="792" height="45" alt="image" src="https://github.com/user-attachments/assets/e5c301b0-8070-43eb-a967-a1fa7dcd1353" />



Timing Analysis (with real Clocks)

<img width="707" height="423" alt="image" src="https://github.com/user-attachments/assets/22d4b13e-3288-4d1c-b1b6-f81371029b37" />

Setup Timing Analysis:

the combinational delay has to be taken into consideration during setup. buffers need to be added to delay. new equation should take buffers into equation-> so theta + 1 (buffer 1 in img) + 2 (buffer 2 in img) < T. 

Delta 1 is the time required from the clock to reach the clock end point to reach the launch flop pin. delta 2 is the time from the clock end point to the capture flop pin. 

<img width="249" height="77" alt="image" src="https://github.com/user-attachments/assets/25a367cc-b06b-4aac-8626-cb979896d1d8" />

Basically the combinational delay shifted by delta 1 and timing delay shifted by delta 2. But theta will still be less than T. then substract uncertainty from the T side of the equation (data required time).

data arrival time < data required time
slack is the difference and is expected to be postive. 

------
Hold Analysis:

Hold condition says the the combinational delay should be greater than the hold time fo the capture flop. theta > H. 

theta + delta 1 > H + delta 2 (deltas come from the buffer delays)
shifitng the edges. 

add the uncertainty to the Hold time 
Data arrival time > data required time
slack will the the difference between them and should sstill be either positive or 0. 


<img width="773" height="416" alt="image" src="https://github.com/user-attachments/assets/945de4b2-2bf6-4897-b75c-4fe650a2cb0a" />

here this shows what would be considered the delta and the added buffer delay. 



Next step is to do a timing analysis for multiple clocks instead of just a single one: 


openroad has the openSTA integrated already so it will have the capacity to do timing within the flow itself. 

<img width="795" height="183" alt="image" src="https://github.com/user-attachments/assets/b05f9f40-ac07-4b82-9801-f12324309917" />

open openroad through the openlane laucher and not the vm terminal
read_lef /openLANE_flow/designs/picorv32a/runs/RUN_2025.08.03_01.40.39/tmp/merged.nom.lef
reults shoudl be in the results/cts folder

(couldn't find my file there is a problem with my current files~ will try to debug soon for screenshots will put commands here for now) 

read_def /openLANE_flow/designs/picorv32a/runs/RUN_2025.08.03_01.40.39/results/cts/cts.def

write_db pico_cts.db

read_verilog /openLANE_flow/designs/picorv32a/runs/RUN_2025.08.03_01.40.39/results/synthesis/synthesis_cts.v

read_liberty -max $::env(LIB_MAX)

read_sdc (this file doesn't exist on my vm/ can't read it)

set_propogated_clock [all_clocks]

report_checks -path_delay_min_max -format full_clock_expanded -digits 4



open openroad again:

read_db pico_cts.db (or whatever name you made you db)

read_verilog /openLANE_flow/designs/picorv32a/runs/RUN_2025.08.03_01.40.39/results/synthesis/synthesis_cts.v

read_liberty -max $::env(LIB_MAX)

link design picorv32a

read_sdc (don't have this file- it is telling me to create a custom one)
... (redo all the steps I previously listed out)

echo $::env(CTS_CLK_BUFFER_LIST)

<img width="795" height="79" alt="image" src="https://github.com/user-attachments/assets/43685fd1-7db0-4644-8cdb-6c178648328d" />

set ::env(CTS_CLK_BUFFER_LIST) [lreplace $::env(CTS_CLK_BUFFER_LIST) 0 0]

<img width="627" height="72" alt="image" src="https://github.com/user-attachments/assets/4a1e1d42-d200-4978-94a2-ca7aa39d08c3" />







