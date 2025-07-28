
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
gird [xSpacing [ySpacing [xOrigin yOrigin]]]

incrreases each invididual grid size

<img width="753" height="608" alt="image" src="https://github.com/user-attachments/assets/8461cd14-0c21-43e6-9429-47d4b7133129" />


 


