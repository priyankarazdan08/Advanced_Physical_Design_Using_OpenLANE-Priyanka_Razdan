
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

<img width="819" height="722" alt="image" src="https://github.com/user-attachments/assets/8ac49e21-bad6-41cc-85d0-65345c3438c4" />


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

strike balance between delay and area using strategy. you can change values during flow~ don't need to redo flow. 

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

look at delay it is high. 
go back in openlane dir and do 
1. less pre_sta.conf
2. sta pre_sta.conf

check what is adding delays and modify those files. 

<img width="260" height="89" alt="image" src="https://github.com/user-attachments/assets/ce530b56-eeda-4d04-8ba7-b4fd32910fd3" />

x is slew. 

1. replace_cell _41882_ sky130_fd_sc_hd__buf_4 
replaces the cell at that file. 
2. report_checks -fields {net cap slew input_pins} -digits 4
this expands precision by 4 digits.


