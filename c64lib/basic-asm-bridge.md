# C64 BASIC ↔ ASM Bridge

## Calling ASM from BASIC

### `SYS` — call a machine code address
```basic
10 SYS 49152
```
Calls routine at `$C000`. ASM must end with `RTS`.

### Pass arguments via POKE/PEEK
```basic
10 POKE 251, 42       : REM store value in zero page ($FB)
20 SYS 49152          : REM call routine
30 PRINT PEEK(252)    : REM read result ($FC)
```

### `USR()` — call ASM as a float function
Vector at `$0311/$0312`. Argument in FAC (`$61`). Rarely worth the complexity vs POKE/PEEK.

### Load `.prg` then call
```basic
10 LOAD "MYCODE.PRG",8,1   : REM absolute load to embedded address
20 SYS 49152
```
The `,1` flag loads to the address in the first two bytes of the `.prg`.

### POKE bytes in directly (tiny routines only)
```basic
10 FOR I = 49152 TO 49154
20 READ B : POKE I, B
30 NEXT I
40 SYS 49152
50 DATA 169, 42, 96    : REM LDA #$2A, RTS
```

---

## Architecture: BASIC as UI shell, ASM as engine

```
BASIC program                    ASM at $C000–$C7FF (2K)
─────────────────                ──────────────────────────
display / UI / menus    ◄──────► heavy lifting
PRINT, INPUT, POKE/PEEK          serial I/O, CIA control, timing
SYS to call routines             multiple entry points, RTS back
```

---

## Multiple entry points

```asm
*= $C000

INIT:                   ; $C000 = 49152
    ; set up CIA, serial port
    rts

SEND:                   ; $C004 = 49156
    lda PARAM1
    ; IEC bus send code
    rts

RECV:                   ; $C008 = 49160
    ; read from serial
    sta RESULT1
    rts
```

BASIC side:
```basic
100 SYS 49152           : REM INIT
110 POKE 51200, 65      : REM set PARAM1
120 SYS 49156           : REM SEND
130 SYS 49160           : REM RECV
140 PRINT PEEK(51204)   : REM read RESULT1
```

---

## Shared mailbox layout (recommended: $C7E0–$C7E5)

| Address | Decimal | Purpose |
|---------|---------|---------|
| `$C7E0` | 51168 | PARAM1 — byte in |
| `$C7E1` | 51169 | PARAM2 — byte in |
| `$C7E2` | 51170 | PARAM3 — byte in |
| `$C7E3` | 51171 | RESULT1 — byte out |
| `$C7E4` | 51172 | RESULT2 — byte out |
| `$C7E5` | 51173 | STATUS — flags/error codes |

Both sides (BASIC and ASM) read/write these fixed addresses.

---

## BASIC UI shell example

```basic
10  SYS 49152                      : REM init hardware
20  PRINT "1. SEND  2. RECV  3. QUIT"
30  GET K$ : IF K$="" THEN 30
40  IF K$="1" THEN GOSUB 200
50  IF K$="2" THEN GOSUB 300
60  IF K$="3" THEN END
70  GOTO 20

200 REM --- SEND ---
210 INPUT "VALUE";V
220 POKE 51168, V
230 SYS 49156
240 S = PEEK(51173)
250 IF S<>0 THEN PRINT "ERROR:";S
260 RETURN

300 REM --- RECV ---
310 SYS 49160
320 PRINT "GOT:"; PEEK(51171)
330 RETURN
```

---

## Division of labour

| Use BASIC for | Use ASM for |
|---------------|-------------|
| PRINT, INPUT, menus | CIA register access |
| IF/THEN, GOSUB flow | IEC serial bus timing |
| GET K$ keypress loops | Precise timing loops |
| Formatting output | Interrupt handlers |
| Error messages | Bulk memory operations |
| Math display | Hardware init sequences |

---

## Breadbin memory safety rules

- **Safe zero page**: `$FB`–`$FE` (251–254) — free for user programs
- **Never corrupt**: `$0000`–`$00FF` (zero page system), `$0100`–`$01FF` (stack)
- **BASIC lives at**: `$0800` — ASM at `$C000` is safely above it
- **2K block**: `$C000`–`$C7FF` — plenty for CIA/IEC control + buffers
- **POKE/SYS/PEEK round trip**: costs milliseconds — don't call ASM in tight BASIC loops

---

## Built-in monitor (breadbin)

The standard breadbin has **no monitor in ROM**. Options:
- **VICE emulator**: `Alt+H` opens full monitor with assembler/disassembler
- **Action Replay / Final Cartridge**: physical cartridge with monitor
- **TurbAssembler**: loadable `.prg` monitor/assembler

---

## Key CIA registers (breadbin)

| Area | Chip | Address | Notes |
|------|------|---------|-------|
| Keyboard matrix | CIA1 PRA/PRB | `$DC00/$DC01` | Column select / row read |
| IEC serial bus | CIA2 PRA | `$DD00` | bits 0-4: DATA, CLK, ATN |
| User port / RS-232 | CIA2 PRB + SP | `$DD01/$DD04` | Byte serial, CNT handshake |
| Joystick port 2/1 | CIA1 PRA/PRB | `$DC00/$DC01` | Port 2=PRA, Port 1=PRB |
| Timers / IRQ | CIA1/2 | `$DC0D/$DD0D` | IRQ/NMI control |
| VIC-II bank select | CIA2 PRA | `$DD00` bits 0-1 | 4×16K banks |

---

## c64lib repositories (KickAssembler)

```bash
git clone https://github.com/c64lib/chipset.git    # CIA/VIC-II/MOS6510 registers
git clone https://github.com/c64lib/common.git     # math, memory, RLE, branching
git clone https://github.com/c64lib/copper64.git   # raster IRQ handling
git clone https://github.com/c64lib/cbmdoc.git     # "All About Your 64" reference
```

Requires **KickAssembler** (`KickAss.jar`) + Java: `java -jar KickAss.jar foo.asm`  
Official download: http://www.theweb.dk/KickAssembler

Alternatively use **ca65** (part of cc65): `sudo apt install cc65 vice acme`
