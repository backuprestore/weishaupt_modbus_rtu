# weishaupt_modbus_rtu
Weishaupt Heatpump Biblock WBB20 - Modbus RTU RS485 - reverse engineering

goal is to sniff the bus and get the missing data from modbus TCP so i get rid of WEM Portal

Protokoll:
RS485 19200/8/1/Even

--------------------------------------

devices:
ID; Command; ......
ID=01 something like Emerson EXD-HP1/2 -documentation TI_EXD-HP12_A1_A2L_A3_DE_Rev03
ID=2D Emerson EV2080M-K9 - documentation AE8-1405 R2

--------------------------------------
Commmunikation Example for EXP-HP1/2
Request
01;10;02;00;00;01;02;00;15;44;5F
ID = 01
function code = 10 -> write data (multiple registers)
start register adress = 02 00 -> "Handbetrieb Regelkreis 1"
quantity of registers = 00 01 -> one register
amount of bytes = 02 -> two bytes
data = 00 15 = not sure
CTC = 44 5F

Answer
01;10;02;00;00;01;00;71
ID = 01
function code = 10 
start register adress = 02 00 -> "Handbetrieb Regelkreis 1"
quantity of registers = 00 01 -> one register
CRC = 00 71

--------------------------------------

Communication Example for EVB2080M-K9
Request
2D;03;00;3B;00;13;72;66
ID = 2D
function code = 03 -> read registers
start register adress = 00 3B -> Register 59
quantity of registers = 00 13 -> 19 Registers to read
CRC = 72 66

Answer
2D;03;26;00;01;17;DA;0F;6A;46;00;00;00;20;DA;19;0B;02;58;06;4E;07;B5;00;00;04;6F;04;6F;04;6F;00;00;00;00;00;00;00;00;04;6D;04;1B
ID = 2D
function code = 03 -> read registers
quantity of bytes = 26 -> 38 bytes = 2x 19 registers
data reg 59 = 00 01 -> Power Up Status -> 1 = complete
data reg 60 = 17 DA -> Compressor Running Speed -> 6001 rpm
data reg ?? = 0F 6A
data reg ?? = 46 00
data reg ?? = 00 00
data reg ?? = 20 DA
data reg ?? = 19 0B
data reg ?? = 02 58
data reg ?? = 06 4E
data reg ?? = 07 B5
data reg ?? = 00 00
data reg ?? = 04 6F
data reg ?? = 04 6F
data reg ?? = 04 6F
data reg ?? = 00 00
data reg ?? = 00 00
data reg ?? = 00 00
data reg ?? = 04 6D
CRC = 04 1B

------------------------------ 
Other examples

01;10;02;01;00;01;02;00;37;C5;97
01;10;02;01;00;01;51;B1

01;03;01;00;00;07;05;F4
01;03;0E;02;19;2B;A0;54;65;6A;45;00;00;01;AE;1A;BC;84;71

2D;06;00;64;00;01;0E;79
2D;06;00;64;00;01;0E;79

2D;06;00;65;0B;F1;58;CD
2D;06;00;65;0B;F1;58;CD

2D;03;00;00;00;01;83;A6
2D;03;20;45;56;32;30;38;30;4D;2D;4B;39;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;A6;E8

2D;03;00;08;00;08;C2;62
2D;03;10;00;00;00;00;00;00;00;00;00;39;02;00;00;39;03;00;04;D3

2D;03;00;CD;00;08;D2;5F
2D;03;10;01;F9;00;64;00;64;00;00;00;00;00;78;00;43;00;43;F0;27

2D;03;00;05;00;01;93;A7
2D;03;02;01;30;28;06



2D;03;00;4E;00;08;23;B7
2D;03;10;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;7C;CF
