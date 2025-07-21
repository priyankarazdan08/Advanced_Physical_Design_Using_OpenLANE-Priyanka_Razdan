Go to latest run:
1) cd results
2) cd floorplan
3) cd floorplan.def
4) magic -T

<img width="674" height="548" alt="image" src="https://github.com/user-attachments/assets/67e0fbb2-8e0c-4e73-99f4-da38e84c695f" />

This is old- from day 2 but notice how each pin is equidistant after the floorplan. 

Now go back to configuration folder- open floorplan.tcl: should contain default configs about floorplan. 

<img width="828" height="548" alt="image" src="https://github.com/user-attachments/assets/c85aa82b-36d8-4a6d-a66b-8916adf30ca8" />

<img width="828" height="548" alt="image" src="https://github.com/user-attachments/assets/0fa616e0-7712-4e81-bfe7-4d208e2aa798" />

(newer version of openlane means some syntax is different so no double colon needed here)

<img width="828" height="548" alt="image" src="https://github.com/user-attachments/assets/b0500d5b-7b05-444f-8d55-c26886a1e2f0" />

Now run magic command again back in terminal in results/floorplan & see what comes up:

<img width="828" height="548" alt="image" src="https://github.com/user-attachments/assets/9e080c6b-9c24-49e9-9c51-7d2580e5bc47" />

no longer equidistant and cells are placed on top of each other. 

Spice Simulations
1) Create SPICE Deck: connectivity info, tap points, etc (basically a netlist)

<img width="493" height="539" alt="image" src="https://github.com/user-attachments/assets/02bef6a0-d8b5-4256-a5da-f054333362da" />

2) Define component values (values for pmos and nmos) pmos should be wider than nmos usualy but in the case we're using now for simplicity we are keeping them the same width for now. Assume values for inputs/outputs (2.5 v for now) calculation will be talked abt later
3) Identify 'nodes'; node means a point where it is between 2 components.
4) Name 'nodes' 
<img width="424" height="373" alt="image" src="https://github.com/user-attachments/assets/715dd235-205a-4865-85db-5fdd617974a2" />

<img width="370" height="164" alt="image" src="https://github.com/user-attachments/assets/44e23974-e082-4c1b-bbbb-72631d4c1266" />
Example above is a spice deck!

line underlined: defines connectivity of pmos (m1)
drain-gate-subtrate-src
Ex:
M1 drain is connected to out node
M1 gate is connected to in node
M1 substrate is connected to vdd node
M1 src is connected to vdd node. 
M1 is a pmos
W = width of M1
L = length of M1

Ex of output load capacitor:
cload is name
connected between out and 0 
value of it is 10f

Ex supply voltage:
name is vdd
connected between vdd and 0
value is 2.5V

Simulation Commands:
.op
.dc Vin 0 2.5 0.05
means sweeping the gate input from 0-2.5 in steps of 0.05
calculating voltage from gate to output! 

<img width="370" height="241" alt="image" src="https://github.com/user-attachments/assets/424d571e-5ec8-49ba-b60b-f79a3c653bea" />

-> descibe model file; this is file it takes all its descriptions of the model

<img width="370" height="64" alt="image" src="https://github.com/user-attachments/assets/34a0e106-0b22-46b8-8ba2-fe29a9edec00" />

SPICE waveform: Wn=Wp=0.375u=,Ln,p=0.25u device (Wn/Ln=Wp/Lp = 1.5)

<img width="497" height="269" alt="image" src="https://github.com/user-attachments/assets/0f97f0fd-0639-4d0b-9f66-41da7d2f9f3b" />

source circuit file
excecute circuit
dc1 transfer characteristics
plout out vs. in

<img width="497" height="381" alt="image" src="https://github.com/user-attachments/assets/2563a73c-7022-46f7-b10b-e8edb76f6f31" />

slihglty shifted towards the left; 

Shapes of waveforms are the same for when wdith of pmos is increased. 
Cmos is a robust device- whenever ur input is 0 ur output is high
1) switching threshold, Vm is point where Vin = Vout
2) if you draw a 45 degree line across the graph- the place where it intersects is Vm
3) usually around 0.9V
4) Area where both are turned on/saturated (pmos and nmos)
5) high possibilty current flows from power to ground. 

<img width="691" height="291" alt="image" src="https://github.com/user-attachments/assets/a44a6f23-afd3-419b-a944-e981293fbd6d" />


Vgs = Vds 
IdsP = -IdsN
directions of current from pmo and nmos are inverse

<img width="367" height="281" alt="image" src="https://github.com/user-attachments/assets/be9a6b48-9b5e-48f1-bcc2-d6413e676a2b" />

Ex: pulse 0 2.5 0 10p 10p in 2n 
starts from 0 ends at 2.5v 
shift at 0 (starts at 0.0)
rise time of 10picosec
fall time of 10picosec
complete cycle is 2nanosec

continue cycle of calculations to figure out robustness of cmos inverter. 


In the normal VM envt:
clone ts https://github.com/nickson-jose/vsdstdcelldesign

<img width="789" height="232" alt="image" src="https://github.com/user-attachments/assets/5a28b7d5-af5f-4455-ac3b-815833d13cb5" />

it'll download into openlane & you'll see it if you list the contents.
if you cd into the new github you can see all the files that were availble online. 

<img width="789" height="74" alt="image" src="https://github.com/user-attachments/assets/29ad4b95-7136-4ee8-a739-241f9f6d4bb8" />

cd /home/beaver/Desktop/work/tools/openlane_working_dir/

wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz

tar xfz drc_tests.tgz

cd drc_tests

ls -al

# view with VIM, not GVIM which is not installed
# vi .magicrc

magic -d XR &

----------------

16-mask CMOS process
1) Selecting a substrate
The layout u create get fabricated onto the substrate u pick.
Well-doping is what u use to fabricate pmos and nmos

2) create active region for transistors.
-> places where u actually see pmos and nmos
create small buckets thats called active regions; pockets should have a small gap between them.

place 40 nm of Silicon Dioxide
place 80 nm of Silicon Nitrate onto layer of silicon dioxide
place 1um of photoresist

<img width="555" height="301" alt="image" src="https://github.com/user-attachments/assets/f448322e-d5e3-4e6d-841c-5a6f21e68e16" />

<img width="555" height="301" alt="image" src="https://github.com/user-attachments/assets/bb1529cc-4b66-4690-8652-abf316f50742" />

The way to protect the weels is through photolithography; protected with masks on top of the photoresist. That way the wells are safe. 

Etch of silicon nitrade; then remove the photoresist itself (chemically removed). Placed in oxidation furnace so the other oxide in other areas will grow. 

<img width="888" height="463" alt="image" src="https://github.com/user-attachments/assets/bbd684a0-34ce-42b9-bfaf-7d4a536e471f" />

Creates isolation regions so that the wells have clearly defined isolation areas.
Field oxide is grown. This process is called "LOCOS" or "Local Oxidation of Silicon"

Next etch of silicon nitrate. 

3) N-Well and P-Well formation

<img width="197" height="369" alt="image" src="https://github.com/user-attachments/assets/854f74f6-85aa-4f3b-9e87-64eda373f579" />

Boron creates a p-well and it's part of the p-substrates ion implantation. Needs high energy bc it has to pass through the silicon oxide layer and enter the p-well in the substrate. Needs at least 200 keV. It does damage the oxide layer a little bit. 

Similar steps for n-well as well. 
Use phosphorous as material -for n-well; energy needed is 400keV whihc is higher bc phosphorous is a bit heavier than boron. 

Place the whole thing into a high temp furnace- will cause drive-in diffusion. Will create 2 distinct wells. 

<img width="713" height="399" alt="image" src="https://github.com/user-attachments/assets/5ef49164-654d-46c7-abaf-f68e31415868" />

next we have to create a gate. Create 2 diff gates for pmos and nmos. 




