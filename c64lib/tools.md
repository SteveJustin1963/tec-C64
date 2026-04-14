                                                                                                                                                                            
#  tools i need to code in asm for c64, 

i have the old breadbin version, i want to use the kb monitor serial port and io control of aattached devices. 
see          
  https://github.com/c64lib                                                                                    
                                                               
                                                                                                                                                   
● Fetch(https://github.com/orgs/c64lib/repositories)                                                                                                                        
  ⎿  Received 326.1KB (200 OK)                                                                                                                                              
                                                                                                                                                                            
● Bash(dpkg -l | grep -iE 'kick|ca65|xa|acme|dasm|cbm|vice|64tass' 2>/dev/null; which ca65 xa kickass acme dasm 64tass 2>/dev/null; ls /opt/ 2>/dev/null)                   
  ⎿  ii  accountsservice                                  23.13.9-2ubuntu6                         amd64        query and manipulate user account information               
     ii  at-spi2-common                                   2.52.0-1build1                           all          Assistive Technology Service Provider Interface (common     
      files)                                                      
     … +93 lines (ctrl+o to expand)                                                                                                                                         
                                                                                                                                                                            
● Fetch(https://github.com/c64lib/chipset)         
  ⎿  Received 265.5KB (200 OK)                                                                                                                                              
                                                                                                                                                                            
● Fetch(https://github.com/c64lib/common)          
  ⎿  Received 270.2KB (200 OK)                                                                                                                                              
                                                                                                                                                                            
● Bash(which java 2>/dev/null && java -version 2>&1; ls /opt/ 2>/dev/null; find /usr/local /opt /home/sj -name "KickAss*" -o -name "kickass*" -o -name "ca65" -o -name…)
  ⎿  /usr/bin/java                                                                                                                                                          
     openjdk version "21.0.10" 2026-01-20                                                                                                                                   
     OpenJDK Runtime Environment (build 21.0.10+7-Ubuntu-124.04)
     … +3 lines (ctrl+o to expand)                                                                                                                                          
                                                                                                                                                                            
● Fetch(https://github.com/c64lib/chipset/tree/master/lib)                                                                                                                  
  ⎿  Received 228.9KB (200 OK)                                                                                                                                              
                                                                  
● Bash(gh api orgs/c64lib/repos --jq '.[].name' 2>/dev/null || true)                                                                                                        
  ⎿  (No output)                                                  
                                                                                                                                                                            
● Fetch(https://raw.githubusercontent.com/c64lib/chipset/master/lib/cia.asm)                                                                                                
  ⎿  Received 3.5KB (200 OK)                                                                                                                                                
                                                                                                                                                                            
● Fetch(https://github.com/c64lib/asm-ka)                                                                                                                                   
  ⎿  Received 232.9KB (200 OK)                                                                                                                                              
                                                                                                                                                                            
● Fetch(https://raw.githubusercontent.com/c64lib/chipset/master/lib/cia.asm)
  ⎿  Received 3.5KB (200 OK)                                                                                                                                                
                                                                                                                                                                            
● Bash(apt-cache show cc65 2>/dev/null | head -5; apt-cache show vice 2>/dev/null | head -5; apt-cache show acme 2>/dev/null | head -5)
  ⎿  Package: cc65                                                                                                                                                          
     Architecture: amd64                                                                                                                                                    
     Version: 2.19-1                                   
     … +12 lines (ctrl+o to expand)                                                                                                                                         
                                                                                                                                                                            
● Fetch(https://github.com/c64lib/cbmdoc)
  ⎿  Received 239KB (200 OK)                                                                                                                                                
                                                                                                                                                                            
● Here's a full rundown of what's available and how to set up your C64 breadbin dev environment:
                                                                                                                                                                            
  ---                                                                                                                                                                       
  Tools available in your environment
                                                                                                                                                                            
  - Java 21 (OpenJDK) - ready for KickAssembler                   
  - cc65 (ca65 assembler) - available via apt                                                                                                                               
  - VICE emulator - available via apt                                                                                                                                       
  - ACME assembler - available via apt                                                                                                                                      
  - SDCC - already installed (not 6502-focused, ignore)                                                                                                                     
                                                                                                                                                                            
  The c64lib org uses KickAssembler exclusively. Since Java is installed, you can drop the .jar in directly.                                                                
                                                                                                                                                                            
  ---                                                                                                                                                                       
  Recommended install                                             

  # Option A — cc65 suite (ca65 assembler) + VICE emulator
  sudo apt install cc65 vice acme                                                                                                                                           
   
  # Option B — KickAssembler (what c64lib uses natively)                                                                                                                    
  # Download KickAss.jar from http://www.theweb.dk/KickAssembler  
  # then run with: java -jar KickAss.jar foo.asm                                                                                                                            
                                                                                                                                                                            
  ---                                                                                                                                                                       
  Clone the relevant c64lib libraries                                                                                                                                       
                                                                                                                                                                            
  cd /home/sj/c64lib
  git clone https://github.com/c64lib/chipset.git      # CIA1/CIA2/VIC-II/MOS6510 registers                                                                                 
  git clone https://github.com/c64lib/common.git        # math, memory copy, RLE, branching                                                                                 
  git clone https://github.com/c64lib/copper64.git      # raster IRQ handling                                                                                               
  git clone https://github.com/c64lib/cbmdoc.git        # "All About Your 64" reference docs                                                                                
                                                                                                                                                                            
  ---                                                                                                                                                                       
  What the chipset lib covers for your use case                                                                                                                             
                                                                                                                                                                            
  ┌────────────────────────────────┬───────────────┬────────────────┬─────────────────────────────────┐
  │              Area              │     Chip      │    Address     │              Notes              │                                                                     
  ├────────────────────────────────┼───────────────┼────────────────┼─────────────────────────────────┤                                                                     
  │ Keyboard matrix                │ CIA1 PRA/PRB  │ $DC00/$DC01    │ Column select / row read        │
  ├────────────────────────────────┼───────────────┼────────────────┼─────────────────────────────────┤                                                                     
  │ IEC serial bus (drive/printer) │ CIA2 PRA      │ $DD00          │ bits 0-4: DATA in/out, CLK, ATN │                                                                     
  ├────────────────────────────────┼───────────────┼────────────────┼─────────────────────────────────┤                                                                     
  │ User port / RS-232             │ CIA2 PRB + SP │ $DD01/$DD04    │ Byte serial, handshake via CNT  │                                                                     
  ├────────────────────────────────┼───────────────┼────────────────┼─────────────────────────────────┤                                                                     
  │ Joystick ports                 │ CIA1 PRA/PRB  │ $DC00/$DC01    │ Port 2 = PRA, Port 1 = PRB      │
  ├────────────────────────────────┼───────────────┼────────────────┼─────────────────────────────────┤                                                                     
  │ Timers / IRQ                   │ CIA1/2        │ $DC0D/$DD0D    │ IRQ/NMI control                 │
  ├────────────────────────────────┼───────────────┼────────────────┼─────────────────────────────────┤                                                                     
  │ VIC-II bank select             │ CIA2 PRA      │ $DD00 bits 0-1 │ 4×16K banks                     │
  └────────────────────────────────┴───────────────┴────────────────┴─────────────────────────────────┘                                                                     
                                                                  
  The cia.asm file gives you named constants for all of these. The disableCIAInterrupts() macro is the standard init step before writing your own IRQ handler.              
                                                                  
  ---                                                                                                                                                                       
  Built-in machine code monitor (breadbin)                        
                                                                                                                                                                            
  The standard breadbin has no built-in monitor in ROM — you need one loaded. Common options:
                                                                                                                                                                            
  - Action Replay / Final Cartridge — physical cartridge with monitor                                                                                                       
  - VICE built-in monitor — hit Alt+H in VICE, full assembler/disassembler/memory editor                                                                                    
  - TurbAssembler — popular 1980s cartridge-style monitor/assembler, loadable .prg                                                                                          
  - SYS 1 — not a monitor; the Kernal entry point map is different                                                                                                          
                                                                                                                                                                            
  For your serial port / IEC device control work, tell me:                                                                                                                  
  - Are you targeting the IEC bus (1541/1571/printer), the user port RS-232 ($DD00/$DD01), or both?                                                                         
  - Which assembler do you want to use — KickAssembler (with c64lib macros) or ca65/ACME?                                                                                   
                                                                                         
  That'll let me wire up the right register headers and a working project skeleton.                                                                                         
                                                                                                                                                                            



# tool summary 

The tools directory exists but is empty, and the only other file is the .md we just created.                                                                              
   
  Nothing has been cloned or installed yet. We only browsed GitHub during this session — no actual downloads happened.                                                      
                                                                  
  To get everything set up:                                                                                                                                                 
                                                                  
  cd /home/sj/c64lib
                                                                                                                                                                            
  # Assembler + emulator
  sudo apt install cc65 vice acme                                                                                                                                           
                                                                  
  # c64lib libraries                                                                                                                                                        
  git clone https://github.com/c64lib/chipset.git
  git clone https://github.com/c64lib/common.git                                                                                                                            
  git clone https://github.com/c64lib/copper64.git                
  git clone https://github.com/c64lib/cbmdoc.git                                                                                                                            
   
  # KickAssembler (if you want the c64lib native toolchain)                                                                                                                 
  # Download KickAss.jar manually from http://www.theweb.dk/KickAssembler
  # then move it to:                                                                                                                                                        
  mv KickAss.jar /home/sj/c64lib/tools/                           
                                                               

                                                                                                                                                                            
# can basic call asm code ?                                                                                                                                                 
                            
● Yes — several ways, from simplest to most powerful:                                                                                                                       
                                                                                
  ---
  1. SYS — call a machine code address directly                                                                                                                             
                                                                                                                                                                            
  10 SYS 49152                                                                                                                                                              
  Calls the routine at $C000 (49152 decimal). Your ASM code must end with RTS.                                                                                              
                                                                                                                                                                            
  The CPU registers at the moment of SYS carry whatever BASIC left in them, so you can't easily pass arguments this way — but it works for self-contained routines.         
                                                                                                                                                                            
  ---                                                             
  2. Pass arguments via memory (most common pattern)                                                                                                                        
                                                                                                                                                                            
  10 POKE 251, 42       : REM store value in zero page
  20 SYS 49152          : REM call routine that reads $FB                                                                                                                   
  30 PRINT PEEK(252)    : REM read result back                                                                                                                              
                                                                                                                                                                            
  ASM side:                                                                                                                                                                 
  *= $C000                                                                                                                                                                  
      lda $FB       ; read arg from zero page                     
      ; ... do work ...                                                                                                                                                     
      sta $FC       ; write result back                                                                                                                                     
      rts                                                                                                                                                                   
                                                                                                                                                                            
  Zero page $FB–$FE (251–254) are free for user programs on the breadbin.
                                                                                                                                                                            
  ---
  3. USR() — call ASM as a function, get a float back                                                                                                                       
                                                                                                                                                                            
  The BASIC USR vector lives at $0311/$0312. The argument passed to USR(x) is in the FAC (floating point accumulator) at $61. You can return a value by leaving a result in
  FAC before RTS.                                                                                                                                                           
                                                                  
  10 POKE 785, <address : REM low byte of USR vector ($0311)                                                                                                                
  20 POKE 786, >address : REM high byte                                                                                                                                     
  30 X = USR(42)
                                                                                                                                                                            
  Rarely worth the FAC complexity — use SYS + POKE/PEEK instead.                                                                                                            
                                                                                                                                                                            
  ---                                                                                                                                                                       
  4. Load your .prg with BASIC, then SYS                          
                                                                                                                                                                            
  10 LOAD "MYCODE.PRG",8,1   : REM load to its own address (,1 = absolute)
  20 SYS 49152                                                                                                                                                              
                                                                  
  The ,1 flag tells the kernal to load to the address stored in the first two bytes of the .prg file (your assembler sets this). This is the standard workflow with a 1541. 
                                                                  
  ---                                                                                                                                                                       
  5. POKE the code in directly (tiny routines)                    
                                                                                                                                                                            
  10 FOR I = 49152 TO 49154
  20 READ B : POKE I, B                                                                                                                                                     
  30 NEXT I                                                                                                                                                                 
  40 SYS 49152
  50 DATA 169, 42, 96    : REM LDA #$2A, RTS                                                                                                                                
                                                                                                                                                                            
  Good for tiny hand-assembled snippets, painful for anything real.                                                                                                         
                                                                                                                                                                            
  ---                                                                                                                                                                       
  Summary                                                         

  ┌──────────────────────┬────────────────────────────────────┐
  │        Method        │              Best for              │
  ├──────────────────────┼────────────────────────────────────┤
  │ SYS addr             │ Any self-contained routine         │
  ├──────────────────────┼────────────────────────────────────┤
  │ POKE/PEEK + SYS      │ Passing args / returning values    │                                                                                                             
  ├──────────────────────┼────────────────────────────────────┤                                                                                                             
  │ LOAD "...",8,1 + SYS │ Real programs loaded from disk     │                                                                                                             
  ├──────────────────────┼────────────────────────────────────┤                                                                                                             
  │ USR(x)               │ Float-in / float-out math routines │   
  └──────────────────────┴────────────────────────────────────┘                                                                                                             
                                                                  
  The standard breadbin pattern is: write ASM starting at $C000 (safe user RAM), end with RTS, call with SYS 49152. Pass data in/out via a few fixed zero-page or high-RAM  
  locations.                                                      
                                                                                                                                                                            
  Want a skeleton project that does this — BASIC loader + ASM routine assembled with ca65 or KickAssembler?                                                                 
   
# so can i call a 2k asm code from basic pass things back and forth and use basic to disply and conrol the asm code ?                                                    
                                                                                                                         
● Yes, absolutely — this is actually a classic C64 pattern. Here's how it works cleanly:                                                                                    
                                                                                                                                                                            
  ---                                                                                                                                                                       
  The architecture                                                                                                                                                          
                                                                                                                                                                            
  BASIC program                    ASM at $C000–$C7FF (2K = $800 bytes)                                                                                                     
  ─────────────────                ──────────────────────────────────────                                                                                                   
  display / UI / menus    ◄──────► does the heavy work
  PRINT, INPUT, POKE/PEEK          serial I/O, CIA control, timing                                                                                                          
  SYS to call routines             multiple entry points, RTS back                                                                                                          
                                                                                                                                                                            
  BASIC handles what it's good at (text display, user input, flow control) and drops into ASM for everything that needs speed or hardware access.                           
                                                                                                                                                                            
  ---                                                                                                                                                                       
  Multiple entry points — the key trick                           
                                       
  Your 2K ASM block can have many entry points, each doing a different job:
                                                                                                                                                                            
  *= $C000
                                                                                                                                                                            
  ; Entry point 1 — init hardware                                                                                                                                           
  INIT:
      ; set up CIA, serial port etc                                                                                                                                         
      rts                                                                                                                                                                   
   
  ; Entry point 2 — send a byte to serial device                                                                                                                            
  SEND:                                                           
      lda PARAM1        ; read arg from shared RAM                                                                                                                          
      ; ... IEC bus code ...                                                                                                                                                
      rts
                                                                                                                                                                            
  ; Entry point 3 — receive a byte                                                                                                                                          
  RECV:
      ; ... read from serial ...                                                                                                                                            
      sta RESULT1                                                 
      rts

  ; Shared parameter area at fixed addresses                                                                                                                                
  PARAM1:  .byte 0
  PARAM2:  .byte 0                                                                                                                                                          
  RESULT1: .byte 0                                                                                                                                                          
  RESULT2: .byte 0
                                                                                                                                                                            
  BASIC side:                                                     
  100 SYS 49152         : REM call INIT   ($C000)
  110 POKE 51200, 65    : REM PARAM1 = 65 ($C800)
  120 SYS 49156         : REM call SEND   ($C004)                                                                                                                           
  130 SYS 49160         : REM call RECV   ($C008)                                                                                                                           
  140 R = PEEK(51204)   : REM read RESULT1                                                                                                                                  
  150 PRINT "GOT:"; R                                                                                                                                                       
                                                                                                                                                                            
  ---                                                             
  A clean shared memory layout                                                                                                                                              
                                                                  
  Pick a fixed area for your "mailbox" — the bridge between BASIC and ASM:                                                                                                  
                                                                                                                                                                            
  $C7E0   PARAM1    byte in
  $C7E1   PARAM2    byte in                                                                                                                                                 
  $C7E2   PARAM3    byte in                                       
  $C7E3   RESULT1   byte out                                                                                                                                                
  $C7E4   RESULT2   byte out                                      
  $C7E5   STATUS    flags/error codes                                                                                                                                       
                                                                                                                                                                            
  All at known addresses, both sides know where to look. BASIC POKEs params in, calls SYS, PEEKs results out.                                                               
                                                                                                                                                                            
  ---                                                                                                                                                                       
  BASIC as the UI shell                                           
                                                                                                                                                                            
  10  SYS 49152              : REM init ASM/hardware
  20  PRINT "1. SEND  2. RECV  3. QUIT"                                                                                                                                     
  30  GET K$ : IF K$="" THEN 30                                                                                                                                             
  40  IF K$="1" THEN GOSUB 200                                                                                                                                              
  50  IF K$="2" THEN GOSUB 300                                                                                                                                              
  60  IF K$="3" THEN END                                                                                                                                                    
  70  GOTO 20                                                     
                                                                                                                                                                            
  200 REM --- SEND routine ---                                    
  210 INPUT "VALUE";V
  220 POKE 51168, V          : REM stuff param                                                                                                                              
  230 SYS 49156              : REM call SEND entry                                                                                                                          
  240 S = PEEK(51173)        : REM read STATUS                                                                                                                              
  250 IF S<>0 THEN PRINT "ERROR:";S                                                                                                                                         
  260 RETURN                                                      
                                                                                                                                                                            
  300 REM --- RECV routine ---                                    
  310 SYS 49160              : REM call RECV entry
  320 PRINT "GOT:"; PEEK(51171)  : REM print RESULT1                                                                                                                        
  330 RETURN                                                                                                                                                                
                                                                                                                                                                            
  ---                                                                                                                                                                       
  What BASIC handles well vs ASM                                  
                                
  ┌───────────────────────┬─────────────────────────────────┐
  │     Use BASIC for     │           Use ASM for           │                                                                                                               
  ├───────────────────────┼─────────────────────────────────┤
  │ PRINT, INPUT, menus   │ CIA register access             │                                                                                                               
  ├───────────────────────┼─────────────────────────────────┤     
  │ IF/THEN, GOSUB flow   │ IEC serial timing (bit-banging) │                                                                                                               
  ├───────────────────────┼─────────────────────────────────┤                                                                                                               
  │ GET K$ keypress loops │ Precise timing loops            │                                                                                                               
  ├───────────────────────┼─────────────────────────────────┤                                                                                                               
  │ Formatting output     │ Interrupt handlers              │     
  ├───────────────────────┼─────────────────────────────────┤                                                                                                               
  │ Math display          │ Bulk memory operations          │     
  ├───────────────────────┼─────────────────────────────────┤                                                                                                               
  │ Error messages        │ Hardware init sequences         │     
  └───────────────────────┴─────────────────────────────────┘                                                                                                               
   
  ---                                                                                                                                                                       
  Practical limits to know                                        
                          
  - BASIC is slow — a POKE/SYS/PEEK round trip takes ~milliseconds, so don't call ASM thousands of times per second
  - 2K at $C000 is plenty — the IEC serial + CIA control code fits in a few hundred bytes, leaving room for buffers                                                         
  - Don't let ASM corrupt BASIC — never write to $0000–$00FF (zero page) without using the safe area ($FB–$FE), and never touch $0100–$01FF (stack)                         
  - BASIC lives at $0800 — your ASM at $C000 is safely above it                                                                                                             
                                                                                                                                                                            
  ---                                                                                                                                                                       
  Want me to build this out?                                                                                                                                                
                                                                  
  I can write a complete skeleton:
  - ASM stub at $C000 with init/send/recv entry points wired to CIA2 (IEC serial)                                                                                           
  - Shared mailbox layout at $C7E0                                                                                                                                          
  - BASIC loader that LOADs the .prg then runs the menu loop                                                                                                                
  - Makefile using ca65 or KickAssembler                                                                                                                                    
                                                                                                                                                                            
  Just confirm:                                                                                                                                                             
  1. Assembler — ca65 (apt install cc65) or KickAssembler (.jar)?                                                                                                           
  2. Target device — IEC bus (1541 drive, printer) or user port RS-232?                                                                                                     
                                                                  
                                                                            
● Write(basic-asm-bridge.md)                                                                                                                                                
  ⎿  Wrote 181 lines to basic-asm-bridge.md                                                                                                                                 
      1 # C64 BASIC ↔ ASM Bridge                                                                                                                                            
      2                                                                                                                                                                     
      3 ## Calling ASM from BASIC                                                                                                                                           
      4                                                           
      5 ### `SYS` — call a machine code address                                                                                                                             
      6 ```basic                                                  
      7 10 SYS 49152
      8 ```
      9 Calls routine at `$C000`. ASM must end with `RTS`.
     … +171 lines (ctrl+o to expand)

● Saved to /home/sj/c64lib/basic-asm-bridge.md.

  Covers everything from the conversation: all the ways BASIC can call ASM, the mailbox pattern, the UI shell example, memory safety rules, CIA register map, monitor       
  options, and the c64lib repo links.
                                                                                                                                                                            


