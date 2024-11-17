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

Communication Example for EV2080M-K9

Request

2D;03;00;3B;00;13;72;66

ID = 2D

function code = 03 -> read registers

start register adress = 00 3B -> Register 59

quantity of registers = 00 13 -> 19 Registers to read

CRC = 72 66


Answer

2D; 03; 26; 00;01; 17;DA; 0F;6A; 46;00; 00;00; 20;DA; 19;0B; 02;58; 06;4E; 07;B5; 00;00; 04;6F; 04;6F; 04;6F; 00;00; 00;00; 00;00; 00;00; 04;6D; 04;1B

ID = 2D

function code = 03 -> read registers

quantity of bytes = 26 -> 38 bytes = 2x 19 registers

data reg 59 = 00 01 -> Power Up Status -> 1 = complete

data reg 60 = 17 DA -> Compressor Running Speed -> 6106/(2^1) = 3053 rpm

data reg 61 = 0F 6A -> Torque [Nm] -> 3946/(2^9) = 7.7 Nm (or current)

data reg 62 = 46 00 -> Torque Limit -> 17920/(2^9) = 35Nm (or current limit)

data reg 63 = 00 00 -> not used

data reg 64 = 20 DA -> DC Bus Voltage [V] -> 8410/(2^4) = 525.6 Volts ?????

data reg 65 = 19 0B -> AC Input Voltage [V] -> 6411/(2^4) = 400.6V

data reg 66 = 02 58 -> AC Input Current [A] -> 600/(2^8) = 2.34A

data reg 67 = 06 4E -> AC Input Power [W] -> 1614 /(2^0) = 1614W

data reg 68 = 07 B5 -> compressor phase current [A] -> 1973/(2^8) = 7,7A

data reg 69 = 00 00 -> not used

data reg 70 = 04 6F -> Power Module Temp [°C] > 1135/(2^5) = 35.4°C

data reg 71 = 04 6F -> unknown

data reg 72 = 04 6F -> unknown

data reg 73 = 00 00 ->  -> not used (IGBT Temp????)

data reg 74 = 00 00 -> not used

data reg 75 = 00 00 -> not used

data reg 76 = 00 00 -> not used

data reg 77 = 04 6D -> Sensor2 -> 1133/(2^5) = 35.4 °C ----> DRUCKGAS-Temperatur

CRC = 04 1B


------------------------------ 
Other examples

01;10;02;01;00;01;02;00;37;C5;97 -> set register 513 (Manuelle Ventilöffnung) to  00 37 -> 55% (Leistungsanforderung)????

01;10;02;01;00;01;51;B1


01;03;01;00;00;07;05;F4 -> get register 256+7  from EV2080M-K9

01;03;0E;02;19;2B;A0;54;65;6A;45;00;00;01;AE;1A;BC;84;71 -> Ventilöffnung Regelkreis 1 (02 19 -> 219* 1/100% = 2,2%) / 11168

2D;06;00;64;00;01;0E;79 -> set compressor enable register 100: 1 -> on

2D;06;00;64;00;01;0E;79


2D;06;00;65;0B;F1;58;CD -> set compressor speed demand register 101 = 0B F1 -> 3057/(2^0)  -> 3057 rpm

2D;06;00;65;0B;F1;58;CD


2D;03;00;00;00;01;83;A6 -> get drive modell register 0 from EV2080M-K9

2D;03;20;45;56;32;30;38;30;4D;2D;4B;39;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;A6;E8 -> answer: EV2080M-K9 :-)


2D;03;00;08;00;08;C2;62 -> get software version register 8+8 from EV2080M-K9

2D;03;10;00;00;00;00;00;00;00;00;00;39;02;00;00;39;03;00;04;D3


2D;03;00;CD;00;08;D2;5F --> get configuration register 205+8 from EV2080M-K9

2D;03;10;01;F9;00;64;00;64;00;00;00;00;00;78;00;43;00;43;F0;27


2D;03;00;05;00;01;93;A7  --> get drive status register 5 from EV2080M-K9

2D;03;02;01;30;28;06 --> 01;30 = 0000 0001 0011 0000 


2D;03;00;4E;00;08;23;B7  --> Get Error register 78-85 from EV2080M-K9

2D;03;10;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;00;7C;CF
