
All timing for all registers in the basic computer is controlled by a master clock generator. These clock pulses are applied to all flip-flops and registers in the system, including the CU. 

There are two major types of control organization : hardwired control and microprogrammed control.

#### Hardwired Control
The logic circuit for CU is implemented using gates, flip-flops, decoders and other digital circuits. It can produce fast modes of operation. Any modifications require change in physical wiring .

#### Microprogrammed Organization
The control memory is programmed to initiate the required sequence of microoperations. Changes in control can be achieved by updating the microprogram in control memory.

We will study the hardwired control only here.
The block diagram of a 