i will be using a label printer that has these specifications on the order form, which is in this in a pdf in this folder.

the overview of the workflow for the full pipeline is:

- use a vision system to identify a box on the inbound ramp
- determine the precise location of the top of the box and communicate with a robot to place a gripper on that box
- lift that box and move it through a 3D region where there are barcode cameras
- the current barcode will be read from the box and available to the control system, call it "UL-01"
- that barcode will then be sent from UL-01 to another system, called "IT-01"
- IT-01 will look up that barcode and return to UL-01 a rendering of the actual label
- that rendering, in some format that the printer can use, will be sent to the label printer
- the label printer will then print the label and place it on the top side of the box

i will need assistance to understand certain aspects of this pipeline, and also to build certain pieces of it in python.

here are some things i know:

- communication to the printer will be using MOD-BUS
- communication to IT-01 will be an http request that includes a 'code' that will be 13 characters and begin with "1Z"
- the response from IT-01 will be an http response to that request and be some medium size nested json
- this json should be in a format the the print engine will recognize and be able to use


### GOAL
- a series of scripts or standalone systems (maybe dockerized) to model the entire workflow
- the main system would read codes from an http address
- transmit that code to the IT-01 on another http address (or socket) and receive some big json response
- take that json response and do whatever needs to be happen to get it into a format that the specific printer needs
- transmit that to the printer on MOD-BUS, whatever that takes
print engine:
- https://www.zebra.com/us/en/products/printers/print-engines/ze500/ze521.html





