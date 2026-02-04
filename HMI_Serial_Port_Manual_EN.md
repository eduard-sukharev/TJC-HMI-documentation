markdown
# Serial Port HMI Instruction Set

**Notes:**
1. The device receives instruction terminator as "0XFF 0XFF 0XFF" three bytes (HEX data, not string data).
2. All instruction names and parameters use ASCII string data, not HEX data, for easy reading and debugging.
3. All instruction names use lowercase letters (this only refers to instruction names being lowercase; parameters should use uppercase when required).

## Object and System Operation Instructions Summary Table
(Click on instruction name or parameter to jump to detailed explanation)

| No. | Instruction | Function | Parameters |
|-----|-------------|----------|------------|
| 1 | page | Refresh page | page pageid |
| 2 | ref | Redraw control | ref obj |
| 3 | click | Activate control press/release event | click obj,event |
| 4 | get | Get variable/constant value with format | get att |
| 5 | prints | Print variable/constant from serial port | prints att,lenth |
| 6 | printh | Print Hex from serial port | printh hex |
| 7 | vis | Hide/Show control | vis obj,state |
| 8 | tsw | Control touch enable | tsw obj,state |
| 9 | randset | Set random number range | randset minval,maxval |
| 10 | add | Add data to curve control | add objid,ch,val |
| 11 | cle | Clear data in curve control | cle objid,ch |
| 12 | addt | Curve data pass-through instruction | addt objid,ch,qyt |
| 13 | doevents | Transfer system control to screen refresh | No parameters |
| 14 | sendme | Send current page ID to serial port | No parameters |
| 15 | covx | Variable type conversion | covx att1,att2,lenth,format |
| 16 | strlen | String variable character length test | strlen att0,att1 |
| 17 | btlen | String variable byte length test | btlen att0,att1 |
| 18 | substr | String extraction | substr att0,att1,star,lenth |
| 19 | spstr | String split | spstr src,dec,key,index |
| 20 | touch_j | Touch calibration | No parameters |
| 21 | ref_stop | Pause screen refresh | No parameters |
| 22 | ref_star | Resume screen refresh | No parameters |
| 23 | com_stop | Pause serial port instruction execution | No parameters |
| 24 | com_star | Resume serial port instruction execution | No parameters |
| 25 | code_c | Clear serial port instruction buffer | No parameters |
| 26 | rest | Reset | No parameters |
| 27 | wepo | Write variable to user storage area | wepo att,add |
| 28 | repo | Read data from user storage area to variable | repo att,add |
| 29 | wept | Pass-through data write to user storage area | wept add,lenth |
| 30 | rept | Pass-through data from user storage area to serial port | rept add,lenth |
| 31 | cfgpio | Extended IO mode configuration | cfgpio id,state,obj |
| 32 | crcrest | Reset CRC initial value | crcrest crctype,initval |
| 33 | crcputs | CRC verify variable/constant | crcputs att,length |
| 34 | crcputh | CRC verify Hex group | crcputh Hex |
| 35 | crcputu | CRC verify serial buffer data segment (valid when recmod=1) | crcputu star,length |
| 36 | setlayer | Change control layer order during runtime (X3, X5 series only) | setlayer obj0,obj1 |
| 37 | move | Control movement (X3, X5 series only) | move obj,starx,stary,endx,endy,first,time |
| 38 | play | Audio playback (X3, X5 series only) | play ch,audio,loop |
| 39 | twfile | Serial port pass-through file (X3, X5 series only) | twfile filepath,filesize |
| 40 | delfile | Delete file (X3, X5 series only) | delfile filepath |
| 41 | refile | Rename file (X3, X5 series only) | refile srcfilepath,decfilepath |
| 42 | findfile | Find file (X3, X5 series only) | findfile filepath,att |
| 43 | rdfile | Pass-through read file (X3, X5 series only) | rdfile filepath,addr,size,crc |
| 44 | newfile | Create file (X3, X5 series only) | newfile filepath,size |
| 45 | newdir | Create folder (X3, X5 series only) | newdir dir |
| 46 | deldir | Delete folder (X3, X5 series only) | deldir dir |
| 47 | redir | Rename folder (X3, X5 series only) | redir srcdir,decdir |
| 48 | finddir | Find folder (X3, X5 series only) | finddir dir,att |

## GUI Drawing Instructions Summary Table
(Click on instruction name or parameter to jump to detailed explanation)

**Note:** GUI drawing instructions are mainly used in the following situations:
When the upper PC interface editing software cannot achieve your specific display requirements, use GUI instructions to draw and achieve the desired display effect yourself. In most cases, these drawing instructions are not needed; most applications can be implemented through control operations in the interface editing software.

| No. | Instruction | Function | Parameters |
|-----|-------------|----------|------------|
| 1 | cls | Clear screen | cls color |
| 2 | pic | Refresh image | pic x,y,picid |
| 3 | picq | Crop image | picq x,y,w,h,picid |
| 4 | xpic | Advanced crop image | xpic x,y,w,h,x0,y0,picid |
| 5 | xstr | Write text | xstr x,y,w,h,fontid,pointcolor,backcolor,xcenter,ycenter,sta,string |
| 6 | fill | Area fill | fill x,y,w,h,color |
| 7 | line | Draw line | line x,y,x2,y2,color |
| 8 | draw | Draw rectangle | draw x,y,x2,y2,color |
| 9 | cir | Draw hollow circle | cir x,y,r,color |
| 10 | cirs | Draw solid circle | cirs x,y,r,color |

---

## Object and System Operation Instructions - Detailed Explanation

### 1. page - Refresh Page

**Format:** `page pageid`

**Parameters:**
- pageid: Page ID or page name

**Examples:**
- `page 0` (Refresh page with ID 0)
- `page main` (Refresh page named main)

**Notes:**
1. Device automatically refreshes page 0 on power-up.
2. Can also assign to system variable dp to jump pages (e.g., dp=0). System variable dp can be set and read; see system variable list for details.
3. The page instruction is a jump instruction. Code written after the page instruction will be ignored and not executed.

---

### 2. ref - Redraw Control

**Format:** `ref obj`

**Parameters:**
- obj: Control ID or control name

**Examples:**
- `ref 1` (Redraw control with ID 1)
- `ref t0` (Redraw control named t0)

**Notes:**
If a control is covered by GUI-drawn content or another control and needs to be displayed again, use ref to redraw.

---

### 3. click - Activate Control Press/Release Event

**Format:** `click obj,event`

**Parameters:**
- obj: Control ID or control name
- event: Event number: 0 for release, 1 for press

**Examples:**
- `click b0,1` (Activate press event of control named b0)
- `click 2,0` (Activate release event of control with ID 2)

**Notes:**
Control press/release events are automatically activated when touching the screen. If you want to manually activate without touching, use the click instruction.

---

### 4. get - Get Variable/Constant Value with Format

**Format:** `get att`

**Parameters:**
- att: Variable name

**Examples:**
- `get t0.txt` (Return txt attribute value of control t0)
- `get j0.val` (Return val attribute value of control j0)
- `get "123"` (Return constant string "123")
- `get 123` (Return constant value: "123")

**Notes:**
1. When using the get instruction to retrieve a string variable, the device returns 0X70 + string content + terminator. For numeric types (like progress bar val attribute), the device returns 0X71 + 4-byte hexadecimal data (int type) + terminator. Numeric storage mode is little-endian (low byte first, high byte last).
2. The get instruction can be sent via serial port or written in user code in the upper PC software editor to enable active sending by the screen (when actively sending, you can use the printh instruction to add custom identifiers to tell the MCU which control this variable belongs to).
3. The get instruction is very similar to the print instruction; the only difference is that get returns data with start identifier (0x70 or 0x71) and terminator (0xff 0xff 0xff), while print does not.
4. See "Serial Port HMI Device Return Data Format" table for specific data return format.

---

### 5. prints - Print Variable/Constant from Serial Port

**Format:** `prints att,lenth`

**Parameters:**
- att: Variable name
- lenth: Send length (0 for automatic length)

**Examples:**
- `prints t0.txt,0` (Send txt attribute value of control t0, length is actual length)
- `prints j0.val,0` (Send val attribute value of control j0, default length is 4-byte integer data, little-endian storage)
- `prints "123",0` (Send constant string "123" i.e., 0x31 0x32 0x33)
- `prints 123,0` (Send constant value: 123 i.e., 0x7b 0x00 0x00 0x00)
- `prints 123,1` (Send low 1 byte of constant value 123 i.e., 0x7b)

**Notes:**
1. When sending string variables with prints, the device directly returns the string content. For numeric types (like progress bar val attribute), the device directly returns 4-byte integer data (Hex data, stored in little-endian mode, low byte first).
2. When using prints instruction to retrieve data, the device only sends data content, without start identifier or terminator.
3. The prints instruction can be used with printh instruction to add custom identifiers at the front to tell the MCU which control this variable belongs to.
4. The prints instruction is similar to the get instruction; the difference is that get sends data with start identifier (0x70 or 0x71) and terminator (0xff 0xff 0xff), while prints does not, but prints can continue with printh statements to add any custom identifiers.

---

### 6. printh - Print Hex from Serial Port

**Format:** `printh hex`

**Parameters:**
- hex: Hexadecimal string expression of characters to send

**Example:**
- `printh d0 a0` (Make device send 0xd0 0xa0 two bytes)

**Notes:**
1. When using printh instruction to send data, the device only sends specified characters, does not send start character, does not send spaces, does not send terminator.
2. Each group of characters in parameters must be separated by exactly one space; hexadecimal string expressions support both uppercase and lowercase.
3. printh can only send constants, cannot send variables. Variables need to use prints instruction.

---

### 7. vis - Hide/Show Control

**Format:** `vis obj,state`

**Parameters:**
- obj: Control name or control ID
- state: State (0 or 1)

**Examples:**
- `vis b0,0` (Hide b0 control)
- `vis b0,1` (Show b0 control)
- `vis 1,0` (Hide control with ID 1)
- `vis 1,1` (Show control with ID 1)

**Notes:**
First parameter 255 indicates all controls on current page, e.g., `vis 255,0` (Hide all controls on current page), `vis 255,1` (Show all controls on current page).

---

### 8. tsw - Control Touch Enable

**Format:** `tsw obj,state`

**Parameters:**
- obj: Control name or control ID
- state: State (0 or 1)

**Examples:**
- `tsw b0,0` (Make control named b0 touch-disabled)
- `tsw b0,1` (Make control named b0 touch-enabled)
- `tsw 1,0` (Make control with ID 1 touch-disabled)
- `tsw 1,1` (Make control with ID 1 touch-enabled)

**Notes:**
First parameter 255 indicates all controls on current page, e.g., `tsw 255,0` (All controls on current page touch-disabled), `tsw 255,1` (All controls on current page touch-enabled).

---

### 9. randset - Set Random Number Range

**Format:** `randset minval,maxval`

**Parameters:**
- minval: Minimum value
- maxval: Maximum value

**Example:**
- `randset 1,100` (Set current random number generation range to minimum 1, maximum 100)

**Notes:**
1. Before using random numbers, you need to use randset instruction to set the random number generation range once. If not set, default is minimum 0, maximum 2147483647. After setting the range, each time you read the system variable rand, you will get a random number.
2. Each time you use randset instruction to set a range, it remains effective until power-off or device reset, then returns to default.
3. Random number setting range data type is int type (i.e., minimum -2147483648, maximum 2147483647).

---

### 10. add - Add Data to Curve Control

**Format:** `add objid,ch,val`

**Parameters:**
- objid: Curve control ID number (must be ID number, does not support using control name)
- ch: Curve control channel number
- val: Data (maximum 255, minimum 0)

**Examples:**
- `add 1,0,30` (Add data 30 to channel 0 of curve control with ID 1)
- `add 1,1,n0.val` (Add data n0.val to channel 1 of curve control with ID 1)

**Notes:**
1. Curve data only supports 8-bit data, minimum 0, maximum 255.
2. Each page supports maximum 4 curve controls, each curve control supports maximum 4 channels. Can continuously send data; control will automatically scroll to display data. During data sending, you can also modify control attributes at any time, such as foreground or background color of each channel.

---

### 11. cle - Clear Data in Curve Control

**Format:** `cle objid,ch`

**Parameters:**
- objid: Curve control ID number (must be ID number, does not support using control name)
- ch: Curve control channel number (255 indicates all channels)

**Examples:**
- `cle 1,0` (Clear channel 0 data of curve control with ID 1)
- `cle 1,255` (Clear all channel data of curve control with ID 1)

**Notes:**
1. When channel number is 255, it means clear all channel data in this curve control.

---

### 12. addt - Curve Data Pass-Through Instruction

**Format:** `addt objid,ch,qyt`

**Parameters:**
- objid: Curve control ID number (must be ID number, does not support using control name)
- ch: Channel number in curve control
- qyt: Number of points to pass through this time

**Example:**
- `addt 1,0,100` (Curve control with ID 1 enters data pass-through mode, pass-through point count is 100 points)

**Notes:**
1. Curve data only supports 8-bit data, minimum 0, maximum 255. Single pass-through data maximum 1024 bytes.
2. After sending the pass-through instruction, user needs to wait for device response before starting to pass through data. After device receives pass-through instruction, it takes about 5ms to prepare pass-through initialization data (if there are many other instructions in the serial buffer before pass-through instruction execution, it takes longer). After device is ready for pass-through initialization, it will send pass-through ready data to user (0XFE + terminator), indicating device is ready and pass-through data can start sending. Pass-through data is pure hexadecimal data, no longer uses strings, no longer needs terminator. After device receives specified data amount, it will resume instruction receiving state. Otherwise, it will remain in data pass-through state. After pass-through data is complete, device will send end marker to user (0XFD + terminator).
3. Before specified pass-through quantity transmission is complete, curve will not refresh. After pass-through is complete, it will immediately refresh automatically.

---

### 13. doevents - Transfer System Control to Screen Refresh

**Format:** `doevents`

**Example:**
- `doevents` (This instruction requires no parameters)

**Notes:**
1. During execution of a process with many instructions, or in a long-time loop statement, all system control is occupied by this process. Before the process ends, although corresponding memory data can be read and written normally, the screen will not refresh. Adding doevents can transfer control to screen refresh. After executing doevents, the screen will refresh all changed controls, and after refreshing, control is returned to the current process to continue execution. Prevents screen from appearing to be in a frozen display state.
2. doevents is mostly used with while or for statements. For usage methods, refer to while or for statement examples.

---

### 14. sendme - Send Current Page ID to Serial Port

**Format:** `sendme`

**Example:**
- `sendme` (This instruction requires no parameters)

**Notes:**
When device receives this instruction, it will immediately send the current page ID to serial port. If you want to automatically send page ID each time page is refreshed, write sendme statement in page initialization event. For send format, refer to "Serial Port HMI Device Return Data Format" table.

---

### 15. covx - Variable Type Conversion

**Format:** `covx att1,att2,lenth,format`

**Parameters:**
- att1: Source variable
- att2: Target variable
- lenth: String length (0 for automatic length, non-0 for fixed length)
- format: Declare numeric type (0-number; 1-currency; 2-Hex)

**Examples:**
- `covx h0.val,t0.txt,0,0` (Convert slider h0 val numeric variable to decimal number string and assign to text t0 txt variable, length is automatic)
- `covx t0.txt,h0.val,0,0` (Convert text t0 txt decimal number string variable to numeric value and assign to slider h0 val numeric variable, length is automatic)

**Notes:**
1. lenth always indicates string length. When converting number to string, it's target variable length; when converting string to number, it's source variable length.
2. If target variable and source variable are same type, conversion fails.

---

### 16. strlen - String Variable Character Length Test

**Format:** `strlen att0,att1`

**Parameters:**
- att0: String variable to test
- att1: Assign test result to this variable

**Example:**
- `strlen t0.txt,n0.val` (Assign actual character length of string variable t0.txt to n0.val)

**Notes:**
1. strlen tests length in character units, while btlen tests length in byte units. For example, a Chinese character tested with btlen has length of 2 bytes, tested with strlen has length of 1 character.
2. Tested variable must be string type, written variable must be numeric type, otherwise error occurs.

---

### 17. btlen - String Variable Byte Length Test

**Format:** `btlen att0,att1`

**Parameters:**
- att0: String variable to test
- att1: Assign test result to this variable

**Example:**
- `btlen t0.txt,n0.val` (Assign actual byte length of string variable t0.txt to n0.val)

**Notes:**
1. btlen tests length in byte units, while strlen tests length in character units. For example, a Chinese character tested with btlen has length of 2 bytes, tested with strlen has length of 1 character.
2. Tested variable must be string type, written variable must be numeric type, otherwise error occurs.

---

### 18. substr - String Extraction

**Format:** `substr att0,att1,star,lenth`

**Parameters:**
- att0: Source variable (must be string variable)
- att1: Target variable (must be string variable)
- star: Character start position in source variable
- lenth: Extract string length

**Example:**
- `substr t0.txt,t1.txt,0,2` (Extract 2 characters starting from position 0 in t0.txt and assign to t1.txt)

---

### 19. spstr - String Split

**Format:** `spstr src,dec,key,index`

**Parameters:**
- src: Source variable (must be string variable)
- dec: Target variable (must be string variable)
- key: Delimiter string (must be string variable)
- index: Take which part (after splitting src string with key string, take index part content and assign to dec variable)

**Example:**
If data0.txt character content is: aaaa^bbbb^cccc^dddd
Execute command: `spstr data0.txt,t0.txt,"^",2`
Result: t0.txt content is: cccc

---

### 20. touch_j - Touch Calibration

**Format:** `touch_j`

**Example:**
- `touch_j` (Enter touch calibration function, this instruction requires no parameters)

**Notes:**
All devices are calibrated before leaving factory; generally no need to use this function.

---

### 21. ref_stop - Pause Screen Refresh

**Format:** `ref_stop`

**Example:**
- `ref_stop` (This instruction requires no parameters)

**Notes:**
1. After pausing screen refresh, all statements will continue to be parsed and executed, corresponding attribute assignment operations will also run normally, but controls on screen will not refresh. Modifying any attribute of any control will not automatically refresh display (but attributes have been modified normally). Until device receives resume refresh instruction (ref_star), modified controls will immediately refresh display.
2. After pausing refresh, even using ref instruction will not immediately refresh, until executing ref_star instruction will it refresh uniformly. However, all GUI drawing instructions (such as drawing points, lines, circles, etc.) are not affected and will display immediately.

---

### 22. ref_star - Resume Screen Refresh

**Format:** `ref_star`

**Example:**
- `ref_star` (This instruction requires no parameters)

**Notes:**
This instruction is used together with ref_stop.

---

### 23. com_stop - Pause Serial Port Instruction Execution

**Format:** `com_stop`

**Example:**
- `com_stop` (This instruction requires no parameters)

**Notes:**
1. After pausing serial port instruction execution, device will continue to receive instructions but will not execute them, all are placed in instruction cache area. Until receiving "com_star" instruction, device will execute all instructions from pause point to current point.
2. When using instruction pause and resume function, evaluate whether your device's serial buffer size and instruction cache queue maximum quantity are sufficient to support number of instructions you need to cache. These two parameters can be found in parameter table in device specification sheet you purchased.

---

### 24. com_star - Resume Serial Port Instruction Execution

**Format:** `com_star`

**Example:**
- `com_star` (This instruction requires no parameters)

**Notes:**
1. After device receives this instruction, it will execute all instructions from pause point to current point.
2. When using instruction pause and resume function, evaluate whether your device's serial buffer size and instruction cache queue maximum quantity are sufficient to support number of instructions you need to cache. These two parameters can be found in parameter table in device specification sheet you purchased.

---

### 25. code_c - Clear All Unexecuted Instructions in Serial Port Instruction Buffer

**Format:** `code_c`

**Example:**
- `code_c` (This instruction requires no parameters)

**Notes:**
Immediately clear all unexecuted instructions in serial port instruction buffer.

---

### 26. rest - Reset

**Format:** `rest`

**Example:**
- `rest` (This instruction requires no parameters)

---

### 27. wepo - Write Variable to User Storage Area (EEPROM)
(Only supported by hardware with user storage)

**Format:** `wepo att,add`

**Parameters:**
- att: Variable/constant
- add: User storage area location (starting from 0)

**Examples:**
- `wepo t0.txt,10` (Write t0.txt content to user storage area at location 10, occupied space in storage area is t0.txt maximum setting value + 1, i.e., t0's txt-maxl attribute size + 1)
- `wepo "abcd",10` (Write string "abcd" to user storage area at location 10, occupied size in storage area is 5 bytes)
- `wepo 125,10` (Write numeric value 125 to user storage area at location 10, occupied size in storage area is 4 bytes)

**Notes:**
1. When write content is variable string, occupied space in storage area is this variable's maximum character count + 1; when write content is constant string, occupied space in storage area is this constant string's actual character count + 1.
2. When write content is variable numeric or constant numeric, occupied space in storage area is uniformly 4 bytes.
3. When using user storage area read/write operations, remember to plan data area locations well to avoid data coverage errors due to position overlap.

---

### 28. repo - Read Data from User Storage Area (EEPROM) to Variable
(Only supported by hardware with user storage)

**Format:** `repo att,add`

**Parameters:**
- att: Target variable
- add: User storage area location (starting from 0)

**Examples:**
- `repo t0.txt,10` (Read data from user storage area location 10 to t0.txt variable, read data amount in storage area is t0.txt maximum setting value + 1, i.e., t0's txt-maxl attribute size + 1)
- `repo n0.val,10` (Read data from user storage area location 10 to n0.val, read data amount in storage area is 4 bytes)

**Notes:**
1. When read content is variable string, read data amount in storage area is this variable's maximum character count + 1.
2. When read content is variable numeric, read data amount in storage area is uniformly 4 bytes.
3. When using user storage area read/write operations, remember to plan data area locations well to avoid data coverage errors due to position overlap.

---

### 29. wept - Pass-Through Data Write to User Storage Area (EEPROM)
(Only supported by hardware with user storage)

**Format:** `wept add,lenth`

**Parameters:**
- add: User storage area location (starting from 0)
- lenth: Pass-through length

**Example:**
- `wept 10,30` (Pass through 30 bytes of data to EEPROM at location 10, occupied space is 10-39)

**Notes:**
1. After sending pass-through instruction, user needs to wait for device response before starting to pass through data. After device receives pass-through instruction, it takes about 5ms to prepare pass-through initialization data (if there are many other instructions in serial buffer before pass-through instruction execution, it takes longer). After device is ready for pass-through initialization, it will send pass-through ready data to user (0XFE + terminator), indicating device is ready and pass-through data can start sending. Pass-through data is pure hexadecimal data, no longer uses strings, no longer needs terminator. After device receives specified data amount, it will resume instruction receiving state. Otherwise, it will remain in data pass-through state. After pass-through data is complete, device will send end marker to user (0XFD + terminator).

---

### 30. rept - Read Data from User Storage Area and Pass-Through Send to Serial Port
(Only supported by hardware with user storage)

**Format:** `rept add,lenth`

**Parameters:**
- add: User storage area location (starting from 0)
- lenth: Read and pass-through send length

**Example:**
- `rept 10,30` (Read 30 bytes from user storage area at location 10 and pass-through send to serial port)

**Notes:**
Regardless of whether data in storage area is string or numeric, device reads and sends specified byte count to serial port in hexadecimal, and does not send terminator.

---

### 31. cfgpio - Extended IO Mode Configuration
(Only supported by hardware with extended IO)

**Format:** `cfgpio id,state,obj`

**Parameters:**
- id: Extended IO number
- state: Configuration mode (0-pull-up input mode, 1-control event binding input mode, 2-push-pull output mode, 3-PWM output mode, 4-open-drain mode)
- obj: Bind control name or ID (this parameter only valid in control event binding input mode, invalid in other modes)

**Examples:**
- `cfgpio 0,0,0` (Configure io0 as pull-up input. After configuring to this mode, can use system variable pio0 to read current input level at any time, e.g., n0.val=pio0)
- `cfgpio 1,2,0` (Configure io1 as push-pull output. After configuring to this mode, can use system variable pio1 to control current output level at any time, e.g., pio1=1)
- `cfgpio 2,1,b0` (Configure io2 as control event binding input, bind control b0. After configuring to this mode, when io2 generates falling edge, it will trigger b0 control press event; when generating rising edge, it will trigger b0 control release event)
- `cfgpio 4,3,0` (Configure io4 as PWM output mode. Before configuration, need to set duty cycle first, i.e., system variable pwm4)

**Notes:**
1. K0 series only io4-io7 support PWM output, X5 series only io6-io7 support PWM output. Other IOs do not support. Configuring other IOs as PWM mode will cause error.
2. When using control event binding input mode, must bind controls on current page at current configuration moment, cannot bind controls on other pages (even if globally memory-occupied controls cannot). After binding current page controls, when refreshing page or switching to other pages, binding events will not continue to trigger. Therefore, need to rebind each time page is refreshed. Recommend writing binding code in page pre-initialization event most appropriately.

---

### 32. crcrest - Reset CRC Initial Value

**Format:** `crcrest crctype,initval`

**Parameters:**
- crctype: CRC verification type (must be 1, Modbus CRC16 verification method)
- initval: Initial value (generally 0xffff)

**Example:**
- `crcrest 1,0xffff` (Reset CRC initial value to 0xffff for subsequent data verification)

**Notes:**
1. After reset, can use crcputs or crcputh or crcputu to verify specified data. After verification is complete, read system variable crcval to get verification result.
2. For complete CRC verification example code, refer to: Using CRC to Verify Data in Programs

---

### 33. crcputs - CRC Verify Variable/Constant

**Format:** `crcputs att,length`

**Parameters:**
- att: Variable name
- length: Data length to verify (0 for automatic length)

**Examples:**
- `crcputs t0.txt,0` (CRC verify string variable t0.txt)
- `crcputs "abc",0` (CRC verify string constant "abc")

**Notes:**
1. Use crcputs or crcputh or crcputu to verify specified data. After verification is complete, read system variable crcval to get verification result.
2. For complete CRC verification example code, refer to: Using CRC to Verify Data in Programs

---

### 34. crcputh - CRC Verify Hex Group

**Format:** `crcputh Hex`

**Parameters:**
- Hex: Hexadecimal string expression of data to verify (each byte separated by space)

**Example:**
- `crcputh 03 25` (CRC verify hex: 0x03, 0x25)

**Notes:**
1. Use crcputs or crcputh or crcputu to verify specified data. After verification is complete, read system variable crcval to get verification result.
2. For complete CRC verification example code, refer to: Using CRC to Verify Data in Programs

---

### 35. crcputu - CRC Verify Serial Buffer Data Segment (Valid when recmod=1)

**Format:** `crcputu star,length`

**Parameters:**
- star: Serial buffer data start position
- length: Data length to verify

**Example:**
- `crcputu 0,22` (Verify first 22 bytes in serial buffer, valid when recmod=1)

**Notes:**
1. Use crcputs or crcputh or crcputu to verify specified data. After verification is complete, read system variable crcval to get verification result.
2. For complete CRC verification example code, refer to: Using CRC to Verify Data in Programs

---

### 36. setlayer - Change Control Layer Order During Runtime
(X3, X5 series only)

**Format:** `setlayer obj0,obj1`

**Parameters:**
- obj0: Control ID or control name that needs layer changed
- obj1: Control ID or control name (place obj0 control above this control. This parameter 0 means place obj0 control below all controls. This parameter 255 means place obj0 control above all controls)

**Examples:**
- `setlayer n0,b0` (Place n0 control above b0 layer)
- `setlayer n0,255` (Place n0 control at top layer)

---

### 37. move - Control Movement
(X3, X5 series only)

**Format:** `move obj,starx,stary,endx,endy,first,time`

**Parameters:**
- obj: Control name or control ID
- starx: Start coordinate X
- stary: Start coordinate Y
- endx: End coordinate X
- endy: End coordinate Y
- first: Priority (0-100, larger number higher priority)
- time: Movement time (unit: ms)

**Example:**
- `move t0,0,0,200,200,0,300` (Control t0 moves from coordinates (0,0) to coordinates (200,200), priority 0, time 300ms)

**Notes:**
Can write multiple move instructions with different priorities simultaneously. System will move according to priority order. Higher priority moves first; after completion, next priority instruction starts.

---

### 38. play - Audio Playback
(X3, X5 series only)

**Format:** `play ch,audio,loop`

**Parameters:**
- ch: Audio channel number
- audio: Audio ID
- loop: Whether to loop

**Example:**
- `play 1,3,0` (Play audio file with ID 3 on audio channel 1, do not loop)

**Notes:**
1. The play instruction is only used to configure and start audio playback. For pause and stop operations, use system variables audio0, audio1.
2. The play instruction controls audio channels independent of video, has no relationship with audio channels used in video, and will not conflict.
3. Audio playback function is global, does not belong to a specific page. Therefore, after play instruction starts playback, even when jumping pages, audio will continue to play. If you want to stop playback after leaving page, you can use audio0/audio1 system variables in page leave event to close or pause specified channel audio playback status.

---

### 39. twfile - Serial Port Pass-Through File
(X3, X5 series only)

**Format:** `twfile filepath,filesize`

**Parameters:**
- filepath: Target file path (e.g., "ram/0.jpg" or "sd0/1.jpg")
- filesize: File size

**Examples:**
- `twfile "ram/0.jpg",10345` (Pass through a file of size 10345 to memory file system, file name is "0.jpg")
- `twfile "sd0/a.jpg",10345` (Pass through a file of size 10345 to SD card root directory, file name is "a.jpg")

**Notes:**
1. To use memory file system, must first configure memory file system size in project configuration options. New project default memory file system size is 0, i.e., cannot be used.
2. For detailed file pass-through logic, refer to "Advanced Applications and Special Instructions Detailed Explanation" document. Click here to jump to detailed description page.

---

### 40. delfile - Delete File
(X3, X5 series only)

**Format:** `delfile filepath`

**Parameters:**
- filepath: File path (e.g., "ram/0.jpg" or "sd0/1.jpg")

**Examples:**
- `delfile "ram/0.jpg"` (Delete "0.jpg" file in memory file system)
- `delfile "sd0/a.jpg"` (Delete "a.jpg" file in SD card root directory)

**Notes:**
To use memory file system, must first configure memory file system size in project configuration options. New project default memory file system size is 0, i.e., cannot be used.

---

### 41. refile - Rename File
(X3, X5 series only)

**Format:** `refile srcfilepath,decfilepath`

**Parameters:**
- srcfilepath: Source file path
- decfilepath: Target file path

**Examples:**
- `refile "ram/0.jpg","ram/1.jpg"` (Rename 0.jpg to 1.jpg in memory file system)
- `refile "sd0/a.jpg","sd0/b.jpg"` (Rename a.jpg to b.jpg in SD card root directory)

**Notes:**
To use memory file system, must first configure memory file system size in project configuration options. New project default memory file system size is 0, i.e., cannot be used.

---

### 42. findfile - Find File
(X3, X5 series only)

**Format:** `findfile filepath,att`

**Parameters:**
- filepath: File path
- att: Find result write variable, must be numeric variable (0-find failed; 1-find successful)

**Examples:**
- `findfile "ram/0.jpg",va0.val` (Find 0.jpg in memory file system, return result to va0.val)
- `findfile "sd0/a.jpg",sys0` (Find a.jpg in SD card root directory, return result to sys0)

**Notes:**
To use memory file system, must first configure memory file system size in project configuration options. New project default memory file system size is 0, i.e., cannot be used.

---

### 43. rdfile - Pass-Through Read File
(X3, X5 series only)

**Format:** `rdfile filepath,addr,size,crc`

**Parameters:**
- filepath: File path
- addr: File data start address
- size: Pass-through read data size **(If 0, returns 4-byte integer data in little-endian mode representing file size)**
- crc: CRC verification code setting at end of data (0-no crc; 1-crc16; 10-crc32)

**Examples:**
- `rdfile "ram/0.jpg",0,10,1` (Starting from data position 0 of "0.jpg" file in memory file system, read 10 bytes and pass through to serial port, add crc16 verification code at end of data. Total is 10+2=12 bytes)
- `rdfile "sd0/a.jpg",0,10,10` (Starting from data position 0 of "a.jpg" file in SD card root directory, read 10 bytes and pass through to serial port, add crc32 verification code at end of data. Total is 10+4=14 bytes)

**Notes:**
1. To use memory file system, must first configure memory file system size in project configuration options. New project default memory file system size is 0, i.e., cannot be used.
2. CRC16 verification algorithm is MODBUS CRC16. Click here to view reference function code. CRC32 verification algorithm is standard CRC32.

---

### 44. newfile - Create File
(X3, X5 series only)

**Format:** `newfile filepath,size`

**Parameters:**
- filepath: File path (e.g., "sd0/1.txt")
- size: File size (in bytes, maximum 2147483647)

**Examples:**
- `newfile "ram/0.txt",4096` (Create a file named "0.txt" in memory file system, size 4096 bytes)
- `newfile "sd0/1.txt",4096` (Create a file named "1.jpg" in SD card root directory, size 4096 bytes)

**Notes:**
1. To use memory file system, must first configure memory file system size in project configuration options. New project default memory file system size is 0, i.e., cannot be used.

---

### 45. newdir - Create Folder
(X3, X5 series only)

**Format:** `newdir dir`

**Parameters:**
- dir: Folder directory (e.g., "sd0/newfolder/")

**Example:**
- `newdir "sd0/a/"` (Create subdirectory named a in SD card root directory)

**Notes:**
1. Memory file system does not support subdirectories. SD card supports subdirectories. Directory path must end with "/".

---

### 46. deldir - Delete Folder
(X3, X5 series only)

**Format:** `deldir dir`

**Parameters:**
- dir: Folder directory (e.g., "sd0/newfolder/")

**Example:**
- `deldir "sd0/a/"` (Delete subdirectory named a in SD card root directory)

**Notes:**
1. Memory file system does not support subdirectories. SD card supports subdirectories. Directory path must end with "/".

---

### 47. redir - Rename Folder
(X3, X5 series only)

**Format:** `redir srcdir,decdir`

**Parameters:**
- srcdir: Source folder directory (e.g., "sd0/newfoldersrc/")
- decdir: Target folder directory (e.g., "sd0/newfolderdec/")

**Example:**
- `redir "sd0/a/", "sd0/b/"` (In SD card root directory, rename subdirectory named a to b)

**Notes:**
1. Memory file system does not support subdirectories. SD card supports subdirectories. Directory path must end with "/".

---

### 48. finddir - Find Folder
(X3, X5 series only)

**Format:** `finddir dir,att`

**Parameters:**
- dir: Folder directory (e.g., "sd0/newfolder/")
- att: Find result write variable (0-find failed; 1-find successful. This variable must be numeric type variable)

**Example:**
- `finddir "sd0/a/",sys0` (Find if there is subdirectory a in SD card root directory, return result to sys0)

**Notes:**
1. Memory file system does not support subdirectories. SD card supports subdirectories. Directory path must end with "/".

---

## GUI Drawing Instructions - Detailed Explanation

**Note:** GUI drawing instructions are mainly used in the following situations:
When the upper PC interface editing software cannot achieve your specific display requirements, use GUI instructions to draw and achieve the desired display effect yourself. In most cases, these drawing instructions are not needed; most applications can be implemented through control operations in the interface editing software.

### 1. cls - Clear Screen Instruction

**Format:** `cls color`

**Parameters:**
- color: Decimal color value or color code

**Examples:**
- `cls 1024` (Refresh screen with decimal color value 1024)
- `cls RED` (Refresh screen with color code RED (RED represents red))

**Notes:**
1. To understand device-supported color code table, refer to "Serial Port HMI Color Code Table" at end of this table.
2. All color parameters in all instructions in this instruction table can use device-supported color codes or decimal color values.

---

### 2. pic - Refresh Image Instruction

**Format:** `pic x,y,picid`

**Parameters:**
- x: Start point x coordinate
- y: Start point y coordinate
- picid: Image ID

**Examples:**
- `pic 10,20,0` (Display image with ID 0 from resource file at coordinates (10,20))
- `pic 40,50,1` (Display image with ID 1 from resource file at coordinates (40,50))

---

### 3. picq - Crop Image Instruction

**Format:** `picq x,y,w,h,picid`

**Parameters:**
- x: Screen start point x coordinate
- y: Screen start point y coordinate
- w: Area width
- h: Area height
- picid: Image ID

**Example:**
- `picq 20,50,30,20,0` (Crop area of image 0 starting from coordinates (0,0) with width 30 height 20 to display on screen, screen display start coordinates are (20,50))

**Notes:**
This instruction requires image to be full-screen image, otherwise cropped image is not what you want. Crop area on image and display area on screen overlap.

---

### 4. xpic - Advanced Crop Image Instruction

**Format:** `xpic x,y,w,h,x0,y0,picid`

**Parameters:**
- x: Screen start point x coordinate
- y: Screen start point y coordinate
- w: Area width
- h: Area height
- x0: Image start point x coordinate
- y0: Image start point y coordinate
- picid: Image ID

**Example:**
- `xpic 20,50,30,20,40,15,0` (Crop area of image 0 starting from coordinates (40,15) with width 30 height 20 to display on screen, screen display start coordinates are (20,50))

---

### 5. xstr - Write Text Instruction

**Format:** `xstr x,y,w,h,fontid,pointcolor,backcolor,xcenter,ycenter,sta,string`

**Parameters:**
- x: Start point coordinate x
- y: Start point coordinate y
- w: Area width
- h: Area height
- fontid: Font library ID
- pointcolor: Font color
- backcolor: Background color (when sta is set to crop or image, backcolor represents image ID)
- xcenter: Horizontal alignment (0 for left align, 1 for center, 2 for right align)
- ycenter: Vertical alignment (0 for top align, 1 for center, 2 for bottom align)
- sta: Background fill method (0 for crop, 1 for solid color, 2 for image, 3 for no background. When sta is set to crop or image, backcolor represents image ID)
- string: Text content

**Example:**
- `xstr 0,0,100,30,1,RED,BLACK,1,1,1,"China"`

**Example explanation:** Use font library 1 in area starting at coordinates (0,0) with width 100, height 30 to write "China", font color is RED, background color is BLACK (if you don't want background color (i.e., no background), can set sta parameter to 3), horizontal alignment is center, vertical alignment is also center.

**Notes:**
1. After text is written beyond set w, it will automatically wrap. If after wrapping to h there are still remaining characters not written, they will be ignored.
2. For color value explanation, refer to cls instruction notes.

---

### 6. fill - Area Fill Instruction

**Format:** `fill x,y,w,h,color`

**Parameters:**
- x: Start point coordinate x
- y: Start point coordinate y
- w: Area width
- h: Area height
- color: Fill color

**Example:**
- `fill 0,0,100,30,RED` (Fill RED color in area starting at coordinates (0,0) with width 100, height 30)

**Notes:**
For color value explanation, refer to cls instruction notes.

---

### 7. line - Draw Line Instruction

**Format:** `line x,y,x2,y2,color`

**Parameters:**
- x: Start point coordinate x
- y: Start point coordinate y
- x2: End point coordinate x
- y2: End point coordinate y
- color: Line color

**Example:**
- `line 0,0,100,100,RED` (Draw a RED colored line between coordinates (0,0) and coordinates (100,100))

**Notes:**
For color value explanation, refer to cls instruction notes.

---

### 8. draw - Draw Rectangle

**Format:** `draw x,y,x2,y2,color`

**Parameters:**
- x: Start point coordinate x
- y: Start point coordinate y
- x2: End point coordinate x
- y2: End point coordinate y
- color: Line color

**Example:**
- `draw 0,0,100,100,RED` (Draw a rectangle, top-left corner at (0,0), bottom-right corner at (100,100), color RED)

**Notes:**
1. draw draws hollow rectangle. To fill solid rectangle, directly use fill area fill instruction.
2. For color value explanation, refer to cls instruction notes.

---

### 9. cir - Draw Hollow Circle

**Format:** `cir x,y,r,color`

**Parameters:**
- x: Circle center coordinate x
- y: Circle center coordinate y
- r: Radius
- color: Line color

**Example:**
- `cir 100,100,30,RED` (Draw hollow circle with center at coordinates (100,100) and radius 30, color RED)

**Notes:**
For color value explanation, refer to cls instruction notes.

---

### 10. cirs - Draw Solid Circle

**Format:** `cirs x,y,r,color`

**Parameters:**
- x: Circle center coordinate x
- y: Circle center coordinate y
- r: Radius
- color: Fill color

**Example:**
- `cirs 100,100,30,RED` (Draw solid circle with center at coordinates (100,100) and radius 30, fill color RED)

**Notes:**
For color value explanation, refer to cls instruction notes.

**Tip:** All color parameters in all instructions in this instruction table can use device-supported color codes or decimal color values.

---

# HMI Writing Syntax

## Table of Contents
1. Assignment Operations
2. Arithmetic Operations
3. Cross-Page Control Attribute Operations
4. HMI Logic Statements

---

## 1. Assignment Operations

● All assignment operations can be written in control events in upper PC editing state, or transmitted via serial port (remember to add three 0xff terminators for serial transmission)

### String Attribute Assignment:

**Correct examples:**
```
t0.txt="123"        // Assign "123" to txt attribute of t0 control
t0.txt=t1.txt       // Assign txt attribute of t1 control to txt attribute of t0 control
```

**Incorrect examples:**
```
t0.txt=123          // Error reason: t0 control's txt attribute is string type; string type attribute assignment constants must have double quotes
t0.txt=h0.val       // Error reason: h0 control's val attribute is numeric type, cannot assign to string type attribute
```

### Numeric Attribute Assignment:

**Correct examples:**
```
n0.val=123          // Assign 123 to val attribute of n0 control
n0.val=h0.val       // Assign val attribute of h0 control to val attribute of n0 control
dim=80              // Assign 80 to system variable dim (backlight brightness immediately becomes 80)
baud=115200         // Assign 115200 to system variable baud (screen baud rate immediately becomes 115200)
n0.val=baud         // Assign current baud rate system variable of screen to val attribute of n0 control
```

**Incorrect examples:**
```
n0.val="123"        // Error reason: n0 control's val attribute is numeric type; numeric type attribute assignment constants should not have double quotes
n0.val=t0.txt       // Error reason: t0 control's txt attribute is string type, cannot assign to numeric type attribute
```

**Warm Tips:**
1. So far, only txt attribute is string type; other attributes are all numeric types.
2. String type and numeric type can be converted to each other through covx instruction. For details, refer to covx instruction description in instruction set.

---

## 2. Arithmetic Operations

All arithmetic operations do not support multiplication/division priority, also do not support parenthesis priority; uniformly from left to right order. Pay special attention.

● All arithmetic operations can be written in control events in upper PC editing state, or transmitted via serial port (remember to add three 0xff terminators for serial transmission)

### Numeric Type Variable Arithmetic Operations

**Supported operators:** + - * / % & | ^ << >>

**Examples:**
```
n0.val=n0.val+n1.val+2
n0.val++
n0.val+=2
n0.val=n1.val%3
n0.val=h0.val*10
n0.val*=10
n0.val=n1.val&3
n0.val=n1.val^5
.....
```

**Incorrect examples:**
```
n0.val=t0.txt+1     // Error reason: Numeric type variables must do arithmetic with numeric type variables and assign to numeric type variables
n0.val=1+"2"        // Error reason: Numeric type variables must do arithmetic with numeric type variables and assign to numeric type variables
```

### String Type Variable Arithmetic Operations

**Operator "+"**

**Examples:**
```
t0.txt="1"+"2"
t0.txt=t0.txt+t1.txt
t0.txt+="abc"+"xy"
```

**Incorrect examples:**
```
t0.txt=1+2          // Error reason: 1 and 2 are both numeric constants. String type variables can only add with string constants/variables, cannot add with numeric constants/variables
t0.txt=t0.txt+h0.val // Error reason: h0.val is numeric variable, cannot add with string variable
```

**Operator "-"**

**Examples:**
```
t0.txt=t0.txt-1     // Delete last 1 character of t0.txt
t0.txt=t0.txt-3     // Delete last 3 characters of t0.txt
t0.txt-=n0.val      // Delete last n0.val characters of t0.txt
```

In string variable arithmetic, "-" represents delete. So when using "-", string variable must "-" a numeric constant/variable to indicate how many characters to delete. This is different from using "+". When using "+", must be string + string; when using "-", must be string - numeric.

---

## 3. Cross-Page Control Attribute Operations

In most cases, we operate control attributes on the current page. If you need to operate control attributes on other pages, write as follows:

**Format:** `[page].[control].[attribute]=XXX`

**Examples:**
```
main.t0.txt="123"           // Assign "123" to txt attribute of t0 on main page
main.t0.txt=set.t3.txt      // Assign txt of t3 on set page to txt of t0 on main page
set.t4.txt="abc"            // Assign "abc" to txt attribute of t4 on set page
```

**Special attention:** When operating control attributes across pages, whether reading or assigning, the operated control's vscope attribute must be set to global (default is private), otherwise operation will fail.

---

## 4. HMI Logic Statements

● Note: All logic statements can only be written in control events in upper PC editing state, do not support serial transmission of logic statements.

### 1. if Statement

**Example 1:** (If t0.txt equals "123456" then switch to page 1)
```
if(t0.txt=="123456")
{
    page 1
}
```

**Example 2:** (If t0.txt not equal to "a" and not equal to "b" then switch to page 1)
```
if(t0.txt!="a"&&t0.txt!="b")
{
    page 1
}
```

**Example 3:** (If t0.txt equals "a" or equals "b" then switch to page 1)
```
if(t0.txt=="a"||t0.txt=="b")
{
    page 1
}
```

**Example 4:** (Following statement written in b0 button press event will toggle b0's txt content between start and stop)
```
if(b0.txt=="Start")
{
    b0.txt="Stop"
}else
{
    b0.txt="Start"
}
```

**Example 5:** (Following statement written in b0 button press event will cycle b0's txt content among 1,2,3)
```
if(b0.txt=="1")
{
    b0.txt="2"
}else if(b0.txt=="2")
{
    b0.txt="3"
}else
{
    b0.txt="1"
}
```

**Notes:**
1. Numeric type variables support: >; <; ==; !=; >=; <=
2. String type only supports: ==; !=
3. if judgment does not support parenthesis priority, e.g., `if((t0.txt=="a"||t0.txt=="b")&&t1.txt=="1")` is not supported! Also do not have extra spaces.
4. if judgment does not allow arithmetic, e.g., `if(n0.val+2==3)` is not supported.

---

### 2. while Statement

**Example 1:** (n0.val keeps incrementing to 100. During increment, screen will not refresh display until all statements in entire process end)
```
while(n0.val<100)
{
    n0.val++
}
```

**Example 2:** (n0.val keeps incrementing to 100. During increment, screen will continuously refresh n0 control display)
```
while(n0.val<100)
{
    n0.val++
    doevents
}
```

**Notes:**
1. During execution of a process with many instructions, or in a long-time loop statement, all system control is occupied by this process. Before the process ends, although corresponding memory data can be read and written normally, the screen will not refresh. Adding doevents can transfer control to screen refresh. After executing doevents, the screen will refresh all changed controls, and after refreshing, control is returned to the current process to continue execution. Prevents screen from appearing to be in a frozen display state.
2. During while statement loop, device will not respond to touch events. Serial port instructions will be received into buffer but not executed until all statements in current process are executed. Use with caution to prevent entering infinite loop.

---

### 3. for Statement

**Example 1:** (n0.val keeps incrementing to 100. During increment, screen will not refresh display until all statements in entire process end)
```
for(n0.val=0;n0.val<100;n0.val++)
{
}
```

**Example 2:** (n0.val keeps incrementing to 100. During increment, screen will continuously refresh n0 control display)
```
for(n0.val=0;n0.val<100;n0.val++)
{
    doevents
}
```

**Notes:**
1. During execution of a process with many instructions, or in a long-time loop statement, all system control is occupied by this process. Before the process ends, although corresponding memory data can be read and written normally, the screen will not refresh. Adding doevents can transfer control to screen refresh. After executing doevents, the screen will refresh all changed controls, and after refreshing, control is returned to the current process to continue execution. Prevents screen from appearing to be in a frozen display state.
2. During for statement loop, device will not respond to touch events. Serial port instructions will be received into buffer but not executed until all statements in current process are executed. Use with caution to prevent entering infinite loop.

---

# Serial Port HMI System Variable List

**Note:** All variable names use lowercase characters

| No. | Name | Meaning | Example/Notes |
|-----|------|---------|---------------|
| 1 | dp | Current page ID | 1. `dp=1` (Set current page to 1, equivalent to page 1)<br>2. `prints dp,0` (Send current page ID to serial port)<br>3. `n0.val=dp` (Assign current page ID to n0.val) |
| 2 | dim | Current backlight brightness value (0-100) | `dim=50`<br>`dim=dim+10`<br>`dim=dim-10` |
| 3 | dims | Power-on default backlight brightness value (0-100) | `dims=50`<br>`dims=dims+10`<br>`dims=dims-10` |
| 4 | baud | Current baud rate value (this modification, lost after power-off) | `baud=9600`<br>Note: Device supported baud rates: 2400, 4800, 9600, 19200, 38400, 57600, 115200, 230400, 256000, 512000, 921600 |
| 5 | bauds | Power-on default baud rate value (saved after power-off, continues to be effective after next power-on) | `bauds=9600` |
| 6 | spax | Character display horizontal spacing (power-on default 0) | `spax=2`<br>Note: Only effective for characters written by xstr instruction. Character display spacing of controls is determined by internal attributes of controls. |
| 7 | spay | Character display vertical spacing (power-on default 0) | `spay=2`<br>Note: Only effective for characters written by xstr instruction. Character display spacing of controls is determined by internal attributes of controls. |
| 8 | thc | Touch drawing pen color | `thc=RED`<br>`thc=1024` |
| 9 | thdra | Touch drawing function switch | `thdra=0` (Close)<br>`thdra=1` (Open) |
| 10 | ussp | Auto-sleep time without serial data (unit: seconds, minimum 3, maximum 65535, power-on default 0 [0 means close serial data timeout auto-sleep]) | `ussp=30` (Automatically enter sleep mode after 30 seconds without serial data) |
| 11 | thsp | Auto-sleep time without touch operation (unit: seconds, minimum 3, maximum 65535, power-on default 0 [0 means close touch timeout auto-sleep]) | `thsp=30` (Automatically enter sleep mode after 30 seconds without touch operation) |
| 12 | thup | Touch auto-wake in sleep mode switch (power-on default 0) | `thup=0` (Touch will not auto-wake after sleep)<br>`thup=1` (Touch auto-wake after sleep)<br>Note: Regardless of whether thup is 0 or 1, when there is touch operation in sleep mode, device will send touch coordinates to serial port. |
| 13 | usup | Serial data auto-wake in sleep mode switch (power-on default 0) | `usup=0` (Serial port will not auto-wake after sleep)<br>`usup=1` (Serial port auto-wake after sleep)<br>Note: Power-on default is 0, will not auto-wake. Need to send `sleep=0` to wake screen. If set to 1, any serial port data will immediately auto-wake. |
| 14 | wup | Refresh page setting after sleep wake | `wup=255` (Power-on default, refresh page before sleep after wake)<br>`wup=2` (Refresh specified page after sleep wake: 2)<br>Note: When device is already in sleep state, can also execute wup=X assignment transmitted via serial port. |
| 15 | sleep | Sleep | `sleep=0` (Exit sleep)<br>`sleep=1` (Enter sleep)<br>Note: In sleep state, can execute following instructions: get, print, printh. Can also execute `sleep=1`, `wup=X` assignment statements, and supports upper PC software connection. Other instructions will not execute. If hardware has extended IO, when IO configured as bound control event, will not generate interrupt event in sleep mode. |
| 16 | bkcmd | Set serial instruction execution success or failure data return (power-on default 2) | `bkcmd=0` (Do not return result)<br>`bkcmd=1` (Only return success result)<br>`bkcmd=2` (Only return failure result)<br>`bkcmd=3` (Return both success or failure result)<br>Note: This setting only affects serial instruction execution success or failure result return. When writing instructions in upper PC software editing interface, errors will definitely return error results when executed, success will definitely not return execution results. This setting also does not affect data return when getting device control data. |
| 17 | sendxy | Real-time send touch coordinates function switch | `sendxy=0` (Close)<br>`sendxy=1` (Open)<br>Note: 1. After opening send function, when there is touch press, device will send touch coordinates via serial port.<br>2. For send coordinate format, refer to "Serial Port HMI Device Return Data Format" table. |
| 18 | delay | Delay | `delay=100` (Make device pause 100ms)<br>Note: After executing delay instruction, device CPU will not execute any instructions but will continue to receive serial port instructions and save to serial port instruction cache. |
| 19 | rand | Random number | `dim=rand` (Assign random number to backlight brightness)<br>`n0.val=rand` (Assign random number to n0.val variable)<br>Note: 1. Before using random numbers, need to use randset instruction to set random number generation range once. If not set, default is minimum 0, maximum 2147483647. After setting range, each time you read system variable rand, you will get a random number.<br>2. Each time you use randset instruction to set range, it remains effective until power-off or device reset, then returns to default. |
| 20 | tch0-tch3 | Real-time touch coordinates | tch0: Current touch coordinate X<br>tch1: Current touch coordinate Y<br>tch2: Last press coordinate X<br>tch3: Last press coordinate Y<br>Note: Touch coordinates can only be read, cannot assign. When not pressed, real-time coordinate data is 0. |
| 21 | addr | Device address | String write: `addr=256`<br>HEX write: `addr=0x0100`<br>Above two writes mean same thing, configure same address, has power-off save function after configuration.<br>Note: 1. Valid address range is 256-2815 (i.e., 0x0100-0x0aff). 0 is no address. 65535 is broadcast address. Broadcast address can only be used to broadcast data, cannot configure device as broadcast address. Factory default address is 0, i.e., no address.<br>2. When sending instructions to a device with address, need to add 2-byte address data before instruction, send in hex mode, 2 bytes in little-endian mode. For example, if device configured address is addr=256, then when sending instructions to it, need to add two bytes before instruction: 0x00 0x01 (note, when configuring is 0x0100, when sending instruction is low byte first, so is 0x00 0x01, opposite of configuration write). |
| 22 | crcval | CRC verification result (can only get, cannot set. Before use, use crcrest instruction to reset initial value) | `n0.val=crcval` (Assign current CRC verification result to n0.val)<br>`prints crcval,2` (Print low 2 bytes of current CRC verification result to serial port)<br>Note: 1. First use crcrest to reset CRC value. After reset, can use crcputs or crcputh or crcputu to verify specified data. After verification complete, read system variable crcval to get verification result.<br>2. For complete CRC verification example code, refer to: Using CRC to Verify Data in Programs |
| 23 | rtc0-rtc6 | RTC clock variables (only supported by hardware with RTC) | `n0.val=rtc5` (Assign current RTC second value to n0.val)<br>`rtc0=2016` (Set RTC year to 2016)<br>`covx rtc5,t0.txt,0` (Convert current RTC second value to t0.txt)<br>Note: 1. rtc0-rtc6 represent year, month, day, hour, minute, second, week respectively.<br>2. rtc6 (week) is read-only. Automatically calculated and generated based on current year, month, day. |
| 24 | pio0-pio7 | Extended IO ports (only supported by hardware with extended IO) | `pio4=1` (Set IO4 to 1)<br>`n0.val=pio2` (Assign io2 level state to n0.val)<br>`covx pio3,t0.txt,0` (Convert io3 level state to t0.txt)<br>Note: 1. Before using pio ports, must first use cfgpio instruction to configure IO mode properly.<br>2. Power-on default all extended IO modes are pull-up input (internal pull-up resistor is 50K). |
| 25 | pwm4-pwm7 | Extended IO duty cycle (only supported by hardware with extended IO)<br>K0 series only io4-io7 support PWM<br>X5 series only io6-io7 support PWM | `pwm6=30` (Set pwm6 duty cycle to 30)<br>`pwm7=90` (Set pwm7 duty cycle to 90)<br>Note: 1. Duty cycle minimum value 0, maximum value 100, power-on default 50.<br>2. pwm4-pwm7 correspond to io4-io7 in extended IO respectively.<br>3. After setting PWM duty cycle, need to use cfgpio instruction to configure this IO mode as PWM output mode, corresponding IO will start outputting PWM. After configuring PWM mode, can modify duty cycle at any time during PWM output, no need to reconfigure.<br>4. Power-on default all extended IO modes are pull-up input (internal pull-up resistor is 50K). |
| 26 | pwmf | PWM output frequency (only supported by hardware with extended IO) | `pwmf=1024` (Set pwm frequency to 1024HZ)<br>`n0.val=pwmf` (Assign PWM frequency to n0.val)<br>`covx pwmf,t0.txt` (Convert PWM frequency to t0.txt)<br>Note: 1. Frequency unit is: HZ, range is minimum 1, maximum 65535HZ, power-on default 1000HZ.<br>2. All PWM outputs uniformly use one frequency, cannot set individually. |
| 27 | eql, eqm, eqh | eql - Bass attenuation (31HZ-125HZ)<br>eqm - Mid attenuation (250HZ-2000HZ)<br>eqh - Treble attenuation (4000HZ-16000HZ)<br>(Only supported by hardware with audio)<br>Upper PC simulator does not support | Setting range 0-15<br>0-6 is attenuation, smaller number greater attenuation<br>8-15 is boost, larger number greater boost<br>7 is balance, no attenuation, no boost<br>Note: System底层 operates according to eq0-eq9 settings. If separately modify eql, eqm, eqh, equivalent to separately modifying eq0-eq2, eq3-eq6, eq7-eq9. But modifying eq0-eq9 does not affect eql, eqm, eqh values. |
| 28 | eq0-eq9 | Independent frequency point attenuation (only supported by hardware with audio)<br>Upper PC simulator does not support | eq0@31HZ, eq1@62HZ, eq2@125HZ<br>eq3@250HZ, eq4@500HZ, eq5@1000HZ<br>eq6@2000HZ, eq7@4000HZ<br>eq8@8000HZ, eq9@16000HZ<br>Setting range 0-15<br>0-6 is attenuation, smaller number greater attenuation<br>8-15 is boost, larger number greater boost<br>7 is balance, no attenuation, no boost<br>Note: System底层 operates according to eq0-eq9 settings. If separately modify eql, eqm, eqh, equivalent to separately modifying eq0-eq2, eq3-eq6, eq7-eq9. But modifying eq0-eq9 does not affect eql, eqm, eqh values. |
| 29 | volume | System volume (minimum 0, maximum 100)<br>(Only supported by hardware with audio) | `volume=60` (Set volume 60)<br>Note: Volume setting range is 0-100. Each setting is automatically saved, remains effective after power-off and power-on again. |
| 30 | audio0, audio1 | Audio channel control<br>0-stop; 1-play; 2-pause<br>(Only supported by hardware with audio) | `audio0=2` (Pause audio playback on channel 0)<br>`audio0=0` (Stop audio playback on channel 0)<br>`audio1=1` (Continue audio playback on channel 1)<br>Note: 1. play instruction is used to configure and start audio playback. System variables audio0, audio1 are used to control channel state.<br>2. Only when channel state is paused can it be configured to continue playing. If channel state is stopped, cannot configure to continue playing. Need to use play instruction to configure and start playback.<br>3. Audio playback function is global, does not belong to a specific page. Therefore, after play instruction starts playback, even when jumping pages, audio will continue to play. If you want to stop playback after leaving page, can use audio0/audio1 system variables in page leave event to close or pause specified channel audio playback status. |

---

# Serial Port HMI Color Code Table

**Note:** All code writing is in uppercase

| Code | Decimal | Represented Color |
|------|---------|-------------------|
| RED | 63488 | Red |
| BLUE | 31 | Blue |
| GRAY | 33840 | Gray |
| BLACK | 0 | Black |
| WHITE | 65535 | White |
| GREEN | 2016 | Green |
| BROWN | 48192 | Orange |
| YELLOW | 65504 | Yellow |

---

# Serial Port HMI Device Return Data Format

## Table 1: Serial Instruction Execution Success or Failure Notification Format

**Notes:**
1. Only when system variable bkcmd is non-zero will it return instruction execution success or failure data. After each power-on, bkcmd default is 2, i.e., only return instruction execution error results.
2. Code written in resource file during upper PC software editing is not affected by bkcmd. When execution has errors, will definitely return error data; when successful, does not return data.
3. Device return data terminator is "0XFF 0XFF 0XFF" three bytes.

| Return Data First Byte | Meaning | Format |
|------------------------|---------|--------|
| 0X00 | Invalid instruction | 0X00 + terminator (returned when invalid instruction received from user) |
| 0X01 | Instruction successfully executed | 0X01 + terminator (returned when user's instruction successfully executed) |
| 0X02 | Control ID invalid | 0X02 + terminator (returned when user's instruction contains invalid control ID or invalid control name) |
| 0X03 | Page ID invalid | 0X03 + terminator (returned when user's instruction contains invalid page ID or invalid page name) |
| 0X04 | Image ID invalid | 0X04 + terminator (returned when user's instruction contains invalid image ID) |
| 0X05 | Font ID invalid | 0X05 + terminator (returned when user's instruction contains invalid font ID) |
| 0x06 | File operation failed | 0X06 + terminator (returned when file operation fails) |
| 0x09 | CRC verification failed | 0X09 + terminator (returned when instruction with verification fails) |
| 0X11 | Baud rate setting invalid | 0X11 + terminator (returned when user's baud rate configuration instruction contains invalid baud rate parameter)<br>Device supported baud rates: 2400, 4800, 9600, 19200, 38400, 57600, 115200 |
| 0X12 | Curve control ID or channel number invalid | 0X12 + terminator (returned when curve control ID or channel number invalid when user uses add instruction to add data to curve control) |
| 0X1A | Variable name invalid | 0X1A + terminator (returned when received variable name from serial port is invalid)<br>Note: Control attributes are also called variables. For example, when you set a control attribute, if you input an attribute name it doesn't have, will also return this data. |
| 0X1B | Variable operation invalid | 0X1B + terminator<br>For example, text control t0's txt attribute assignment should be written as t0.txt="abc". If you write t0.txt=abc, will error. For example, progress bar j0's val attribute should be numeric, so should write j0.val=50. If write j0.val="50" or j0.val=abc, will also error. |
| 0X1C | Assignment operation failed | 0X1C + terminator (returned when attribute assignment fails) |
| 0X1D | EEPROM operation failed | 0X1D + terminator (returned when EEPROM operation fails) |
| 0X1E | Parameter quantity invalid | 0X1E + terminator (returned when parameter quantity in user input instruction is wrong) |
| 0X1F | IO operation failed | 0X1F + terminator (returned when IO operation fails) |
| 0X20 | Escape character use error | 0X20 + terminator (returned when escape character use is wrong) |
| 0X23 | Variable name too long | 0X23 + terminator (variable name length maximum 29 characters, will return this data if exceeded) |
| 0X24 | Serial buffer overflow | 0X24 + terminator (returned when serial buffer is full. After buffer overflow, after instructions in buffer queue are executed, will free up buffer space to continue receiving instructions. Before this, data received by serial port will be discarded) |

---

## Table 2: Other Data Return Formats

**Notes:**
1. Device return data terminator is "0XFF 0XFF 0XFF" three bytes.
2. Following data returns are not affected by bkcmd.

| Return Data First Byte | Meaning | Format |
|------------------------|---------|--------|
| 0X65 | Touch hot zone event return | 0X65 + page ID + button ID + touch event + terminator (returned when user-created control is pressed or released, provided you checked control's "send key value" checkbox)<br>(Touch event definition: press event 0x01, release event 0X00)<br>Example: 0X65 0X00 0X02 0X01 0XFF 0XFF 0XFF<br>Meaning: page 0, button 2, press |
| 0X66 | Current page ID number return | 0X66 + page ID + terminator (device returns this data when receiving "sendme" instruction)<br>Example: 0X66 0X02 0XFF 0XFF 0XFF<br>Meaning: Current page ID is 2 |
| 0X67 | Touch coordinate data return | 0X67 + coordinate X high byte + coordinate X low byte + coordinate Y high byte + coordinate Y low byte + touch event status + terminator (when system variable "sendxy" is 1, returns this data when there is touch event)<br>(Touch event definition: press event 0x01, release event 0X00)<br>Example: 0X67 0X00 0X7A 0X00 0X1E 0X01 0XFF 0XFF 0XFF<br>Meaning: coordinates (122,30), event: press |
| 0X68 | Sleep mode touch event | 0X68 + coordinate X high byte + coordinate X low byte + coordinate Y high byte + coordinate Y low byte + touch event status + terminator (after device enters sleep mode, returns this data when there is touch event)<br>(Touch event definition: press event 0x01, release event 0X00)<br>Example: 0X68 0X00 0X7A 0X00 0X1E 0X01 0XFF 0XFF 0XFF<br>Meaning: coordinates (122,30), event: press |
| 0X70 | String variable data return | 0X70 + variable content ASCII code + terminator (when variable retrieved using get instruction is string type, returns this data)<br>Example: 0X70 0X61 0X62 0X63 0XFF 0XFF 0XFF<br>Meaning: Return string data: "abc" |
| 0X71 | Numeric variable data return | 0X71 + variable binary data (4 bytes little-endian mode, low byte first) + terminator (when variable retrieved using get instruction is numeric, returns this data)<br>Example: 0X71 0X66 0X00 0X00 0X00 0XFF 0XFF 0XFF<br>Meaning: Return numeric data: 102 |
| 0X86 | Device auto-enters sleep mode | 0x86 + terminator (only when device auto-enters sleep mode will return this data. If entered sleep by executing serial instruction sleep=1, will not return this data) |
| 0X87 | Device auto-wake | 0x87 + terminator (only when device auto-wakes will return this data. If woke from sleep by executing serial instruction sleep=0, will not return this data) |
| 0X88 | System startup successful | 0x88 + terminator (device sends this data after power-on initialization successful) |
| 0X89 | Start SD card upgrade | 0x89 + terminator (device sends this data after power-on detecting SD card, then enters upgrade interface) |
| 0XFD | Pass-through data complete | 0xFD + terminator (sends this data after pass-through data ends and data processing complete) |
| 0XFE | Data pass-through ready | After device receives data pass-through instruction, enters pass-through data initialization. After initialization complete, sends this data, indicating now entered data pass-through mode and can start data pass-through |

---

# Name Group Usage Instructions

In most cases, we operate control attributes like this (this is also our recommended control operation method):
```
t0.txt="123"    // Assign "123" to txt attribute of t0 control
n0.val=15       // Assign 15 to val attribute of n0 control
z0.val=185      // Assign 185 to val attribute of z0 control
```

If we don't know control name, only know control ID, how to operate? This requires using name groups.

### Control Name Group Format: b[Control ID].attribute

**Example:**
```
b[5].txt="123"              // Assign "123" to txt attribute of control with ID 5 on current page
b[n0.val].txt="123"         // Assign "123" to txt attribute of control with ID n0.val on current page
```

**Note:** Must ensure this control on current page has txt attribute, otherwise assignment will fail.

### Page Name Group Format: p[Page ID].b[Control].attribute

**Example:**
```
p[2].b[4].txt="456"                     // Assign "456" to txt attribute of control with ID 4 on page with ID 2
p[n0.val].b[n1.val].txt="456"          // Assign "456" to txt attribute of control with ID n1.val on page with ID n0.val
```

**Note:** Must ensure this control on this page has txt attribute, otherwise assignment will fail.

### Combined with for Statement to Batch Change Control Attributes
(Load consecutive values starting from eeprom address 100 to numeric controls n0~n9 (ID: 5~14)):

```
sys1=100                                // Set eeprom read location
for(sys0=5;sys0<=14;sys0++)            // for loop read eeprom and assign to numeric controls
{
    repo b[sys0].val,sys1               // Read eeprom data at specified location
    sys1+=4                             // eeprom read address plus 4, because one numeric type occupies 4 bytes space
}
```

**Note:** Must ensure controls with ID from 5-14 all have val attribute, otherwise assignment will fail.

### Important Notes:
- All control ID numbers are automatically assigned by software, cannot be manually set. When user edits UI interface, controls placed in sequence will be assigned consecutive IDs by software.
- Using quick bar "bring to front", "send to back" functions will cause control ID changes, because layer front-back relationship is associated with control ID. ID smallest is at bottom layer (so page ID is 0), ID largest is at top layer. Each control has its own layer, all distinguished by ID for front-back relationship.

---

# HMI Advanced Applications and Special Instructions Detailed Explanation

This document mainly explains some advanced applications, implementing some special functions. Most projects don't need these functions. If your project doesn't need them, you can ignore this document.

## Chapter Index
1. Serial Data Parse Mode - Active Parse Mode Application Detailed Explanation
2. HMI Download Protocol Detailed Explanation
3. Adding CRC Verification to Serial Instructions
4. Using CRC to Verify Data in Programs
5. Serial Port Transmit Images to Memory or SD Card During Runtime

---

## 1. Serial Data Parse Mode - Active Parse Mode Application Detailed Explanation

This section involves the following content:
1. Serial data parse mode system variable: recmod
2. Serial buffer data size system variable: usize
3. Serial buffer data array: u[index]
4. Serial buffer data copy instruction: ucopy
5. Serial data parse mode exit password

**Note:** When screen is set to active parse mode and upper PC software simulator connects to screen, screen will be forced to exit active parse mode. At this time, need to power off and on screen again to normally connect simulator for debugging.

### 1. Serial Data Parse Mode System Variable recmod (0 is passive parse mode, 1 is active parse mode)

Screen power-on recmod is 0, i.e., passive parse mode. In this mode, external device sends serial instructions to screen for execution according to standard instruction set instruction format. If you set recmod to 1 (can write recmod=1 in power-on default page initialization event), then screen enters active parse mode, then all serial instructions will not be executed (note: serial instructions will not be executed; firmware instructions written in events during upper PC software editing are not affected, still execute normally). All serial data is stored in serial buffer, waiting for you to actively read it. After reading data each time, use udelete instruction to delete number of bytes already read in buffer, otherwise after buffer overflows cannot receive new data.

To use this function, please ensure you have the following 2 foundations:
1. Understand what is HEX, what is String, what is ASCII, what is their relationship, how to convert.
2. Understand what is single-byte numeric value, double-byte numeric value, four-byte numeric value, what are their differences, what is their storage method in memory. Understand what is little-endian mode, what is big-endian mode. Understand what is low byte first.

If you are clear about the above 2 points, please continue reading. Otherwise, strongly recommend not to continue reading, because most projects don't need this function. Using default passive parse mode is sufficient. No need to configure to active parse mode below.

### 2. Serial Buffer Data Size System Variable usize (can only read, cannot set)

Reading this variable can know how much data is currently cached in serial buffer.

### 3. Serial Buffer Data Array

Serial buffer data array write format is u[index] (index is number)

**Example 1:** Get a 1-byte numeric value starting from buffer position 0, assign to numeric control n0, write as follows:
```
n0.val=u[0]
```

**Example 2:** Get a 2-byte numeric value (little-endian mode, low byte first) starting from buffer position 0, assign to numeric control n0, write as follows:
```
n0.val=u[1]
n0.val<<=8
n0.val+=u[0]
```

**Example 3:** Get a 4-byte numeric value (little-endian mode, low byte first) starting from buffer position 0, assign to numeric control n0, write as follows:
```
n0.val=u[3]
n0.val<<=8
n0.val+=u[2]
n0.val<<=8
n0.val+=u[1]
n0.val<<=8
n0.val+=u[0]
```

Can assigning 4-byte integer variable buffer content only be done in 4 single-byte assignments plus 3 shifts? Of course not! Of course there are more convenient methods, please continue reading!

### 4. Serial Buffer Data Copy Instruction ucopy

This instruction can continuously copy specified quantity of data from specified position in serial buffer to target variable (target variable can be string variable or numeric variable).

**Format:** `ucopy att, srcstar, lenth, decstar`

**Description:** Copy data from serial buffer to variable (valid in recmod=1 mode)

**Parameters:**
- att: Target variable name
- srcstar: Serial buffer data start position
- lenth: Copy length
- decstar: Target variable data start position

**Example 1:** Get a 4-byte numeric value (little-endian mode, low byte first) starting from buffer position 0, assign to numeric control n0, write as follows:
```
ucopy n0.val,0,4,0
udelete 4       // Delete first 4 bytes in buffer, if there is other data behind, will automatically move up
```

**Warm Tip:** Each numeric variable is allocated 4 bytes of memory by system. If you use ucopy to get less than 4 bytes of numeric value from buffer, must pay attention to processing remaining byte data to avoid data abnormalities. For operation method, refer to this example below:

**Example 2:** Get a 2-byte numeric value (little-endian mode, low byte first) starting from buffer position 0, assign to low 16 bits of numeric control n0.val, write as follows:
```
n0.val=0
ucopy n0.val,0,2,0      // If want to assign to high 16 bits, write as ucopy n0.val,0,2,2
udelete 2               // Delete first 2 bytes in buffer, if there is other data behind, will automatically move up
```

**Explanation:** First assign n0.val to 0, purpose is to ensure all 4 bytes of n0.val are set to 0, then copy 2 bytes from buffer. Otherwise, because you only copied 2 bytes, n0.val's original remaining 2 bytes of data are still there, causing n0.val's final numeric value not to be what you want.

**Example 3:** Get a 10-byte string starting from buffer position 0, assign to text control t0, write as follows:
```
if(usize>=10)                   // Ensure buffer data size is sufficient for 10
{
    ucopy t0.txt,0,10,0
    udelete 10                  // Delete first 10 bytes in buffer, if there is other data behind, will automatically move up
}
```

**Important Tip:** code_c instruction will clear buffer data, may cause data not yet read to be deleted prematurely. So in active parse mode, recommend using udelete instruction to delete content already read, rather than using code_c instruction.

All statements and instructions used in above cases are firmware instructions written in events during upper PC editing interface. Once serial port is configured as active parse mode, serial instructions can no longer be executed. So must use firmware instructions to operate and read serial data, cannot use serial instructions (otherwise would be self-contradictory).

### 5. Exit Active Parse Mode

Conventional method to exit active parse mode is to write recmod=0 firmware instruction in event. If want to exit via serial data, sending recmod=0 via serial definitely won't work. Can exit active parse mode by sending an exit password. Exit password is a string of 24 bytes + 3-byte terminator.

**24-byte string:**
```
DRAKJHSUYDGBNCJHGJKSHBDN (string data, must be uppercase)
```

**3-byte terminator (Hex data):**
```
0xff 0xff 0xff
```

**Total 27 bytes**

---

## 2. HMI Download Protocol Detailed Explanation

The HMI download protocol described in this document only applies to users who want to create their own download program or want MCU to control HMI download resource files. It belongs to advanced application category, not HMI interface design category. Therefore, requires users with certain foundation to operate. Shenzhen Taojingchi Electronic Co., Ltd. only publishes and explains this protocol, does not provide any technical support related to download protocol. If friends who are not familiar with serial port operations, suggest ignoring this explanation. Please directly use USART HMI software for download. No need to understand this protocol.

**Warm Tip:** Official has developed dedicated download tool (TFTFileDownload) according to this protocol and opens source code. Welcome to download and use: Click here to download

### Download Step 1: Connection Operation

This step is mainly used to search which serial port HMI device is on and device's current baud rate. If these two conditions are known, you can skip this step in your program, directly fix serial port number and device's currently used baud rate, then jump directly to step 2 to start download.

#### Search Method:
Send connection instruction to each serial port of computer at different baud rates respectively: `connect` + terminator

After device receives connection instruction, will return connection data. If correct connection data is received, device successfully connected. At this point, get device's current serial port number and currently used baud rate.

Because always sending instructions in loop, when screen receives data at correct baud rate, there will definitely be some error data from last time at wrong baud rate at front of data. Therefore, at this time first instruction will definitely be treated as error instruction. So each time sending, need to send two instructions: first send 4-byte HEX empty instruction (00 ff ff ff), second is connect + terminator.

**Delay explanation:** After trying connection instruction each time, minimum time needed to wait for data return is (unit: ms): (1000000/attempted baud rate)+30

If trying to connect at 9600 baud rate, minimum time to wait for return is:
1000000/9600+30=134ms
Other baud rates calculated similarly.

#### Connection Instruction Send Description:

Taking TJC4024T032_011R device as example, device returns following 8 groups of data (each group separated by comma):
```
comok 1,101,TJC4024T032_011R,52,61488,D264B8204F0E1828,16777216
```

**Data Interpretation:**
- comok: Handshake response
- 1: Indicates with touch (0 is without touch)
- 101: Device internal reserved data
- TJC4024T032_011R: Device model
- 52: Device firmware version number
- 61488: Device main control chip internal code
- D264B8204F0E1828: Device unique serial number
- 16777216: Device FLASH size (unit: bytes)

### Download Step 2: Start Download

At this time, already know device on which serial port number, also know device's current baud rate. Can send download instruction.

#### First Step: Send instruction whmi-wri filesize,baud,res0

**Parameters:**
- filesize: tft file size (unit: bytes)
- baud: Force download use baud rate
- res0: Reserved data, use any ASCII character

**Example:** If need to download tft file size 10000 bytes, need to use 115200 baud rate for download, then send instruction:
```
whmi-wri 10
