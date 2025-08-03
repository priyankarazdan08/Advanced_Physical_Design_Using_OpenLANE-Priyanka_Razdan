
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

 


