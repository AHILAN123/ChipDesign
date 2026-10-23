# Jane Street Protocol Emulator ASIC: Overview and Study Roadmap

**Competition post:** https://blog.janestreet.com/protocol-emulator-asic-competition/
**Deadline:** January 18, 2027. That is about 12 weeks from October 23, 2026.
**Starting point assumed:** you know basic digital logic (gates, Boolean algebra, maybe flip-flops), but have no hardware-design or chip-design experience.

---

## 1. What are they trying to make?

A **small programmable chip that can "speak" any electronic wire-protocol**.

- Chips talk to each other by turning wires on and off (1s and 0s) in agreed patterns called **protocols**. UART, SPI and I2C are examples.
- Normally, a chip has a **fixed circuit for each protocol**. A UART block can only ever speak UART.
- Jane Street wants a chip containing a **tiny special-purpose CPU** whose instructions are made for pin work:
  - set or read a pin
  - wait an exact number of clock cycles
  - shift bits in and out
  - loop and branch
- Each protocol is then just a **small program** loaded into it. If you load the UART program, the chip speaks UART. If you load the SPI program, the *same* chip speaks SPI.
- It must be able to learn **new protocols after it's manufactured**, within its speed and pin limits.

**Analogy:** a normal chip is a single-brand TV remote. Their chip is a **universal remote** that you can teach new codes.

**What you actually hand in:**
- the chip design, written as code (Verilog or similar)
- programs for it (UART, SPI, I2C, and more if possible)
- tests proving it works
- the layout files from the Tiny Tapeout flow

Everything must be open source, and the design must fit in about **0.7 mm²** (6×4 tiles, roughly 24K logic cells).

**Prize:** Jane Street pays to manufacture the best designs (March 2027 shuttle), and winners get the real chip on a dev board.

---

## 2. Is it new? Where is the novelty?

### It is not a new *kind* of chip

This idea already exists:

| Existing | What it is |
|---|---|
| **RP2040 PIO** (Raspberry Pi Pico) | Tiny pin state machines: 9 instructions, programs of up to 32 instructions, 1 cycle per instruction |
| **TI PRU** (BeagleBone) | Full small real-time CPUs with exact timing |
| **FPGAs** | Reprogrammable hardware that can implement anything, but large and expensive |
| **Bit-banging on a normal CPU** | Works, but timing is unreliable |

So you are **not inventing a new category**. You are **designing your own version** of a known category, under tight constraints. Jane Street says so directly: study PIO and PRU and consider what you'd do differently.

### Where the novelty can come from

They judge on **"most novel"** and on **verification methodology**. Novelty can live in any of these places:

1. **Architecture and instruction-set design.** What is the smallest, smartest set of instructions that can express UART, SPI, I2C *and* stretch to USB or Ethernet? This is the heart of the problem. PIO is tiny but struggles with complex protocols. PRU is flexible but large. Your answer can be a better middle point, or a different idea entirely.
2. **Area efficiency.** Doing more inside 0.7 mm² than an obvious design would allow.
3. **Flexibility.** Supporting protocols that PIO-style designs find hard, such as USB's bit-stuffing or Ethernet's Manchester encoding.
4. **Verification.** Proving correctness with formal methods, constrained random testing, reference models, or AI-assisted checking. They explicitly say verification will matter more and more.
5. **Usability.** A clean assembler, a good programming model, or easy debugging.
6. **Extras.** Features nobody asked for that your architecture makes possible, such as acting as a logic analyzer, or sniffing a bus while also driving it.

**In one line:** the novelty is not *what* the chip does, but *how cleverly* you design it, fit it, and prove it correct.

---

## 3. Study roadmap

This is ordered so that each topic builds on the one before it. Every phase ends with a **checkpoint**: a small thing to build that proves you understood it.

### Phase 1: Sequential digital logic (about 1 week)

You know gates. Now you need circuits that **remember and count over time**.

**Topics**
- Flip-flops (D flip-flop), registers, the clock
- Counters, shift registers (crucial, because serial protocols are shift registers)
- **Finite state machines (FSMs)**, Moore vs Mealy. Every protocol is an FSM.
- Timing basics: clock period, setup and hold time, critical path
- **Metastability and synchronizers.** When your chip reads an outside pin that isn't aligned to your clock, it needs a 2-flip-flop synchronizer.
- Multiplexers, decoders, register files

**Resources**
- *Digital Design and Computer Architecture* by Harris & Harris (RISC-V edition), chapters 1–3
- Nand2Tetris, parts 1–3, for building intuition

**Checkpoint:** on paper, draw the FSM and datapath for a UART transmitter (start bit, 8 data bits, stop bit).

### Phase 2: Verilog, the hardware description language (about 1.5–2 weeks)

This is how you "write" a chip.

**Topics**
- Modules, ports, `wire` vs `reg`, `assign`
- `always @(posedge clk)` (sequential) vs `always @(*)` (combinational)
- **Blocking (`=`) vs non-blocking (`<=`)** assignments. This is the classic beginner trap.
- Writing FSMs in Verilog
- Synthesizable vs non-synthesizable code: what can become real hardware
- Parameters and `generate`
- **Testbenches**: simulating your design and checking outputs
- Reading **waveforms**

**Tools (all free)**
- **Icarus Verilog** or **Verilator** for simulation
- **GTKWave** or **Surfer** for viewing waveforms

**Resources**
- **HDLBits** (hdlbits.01xz.net): hundreds of small exercises with automatic checking. This is the best place to start.
- Harris & Harris, chapter 4 (HDLs)

**Checkpoint:** a working UART transmitter in Verilog, with a testbench that shows the correct waveform for the letter `A`.

**Optional:** Jane Street uses **Hardcaml** (OCaml). Other modern options include **Amaranth** (Python) and **SpinalHDL**. Learn plain Verilog first, because the Tiny Tapeout template uses it.

### Phase 3: The protocols themselves (about 1.5 weeks, overlapping Phase 2)

Learn each protocol well enough to draw its waveform from memory. Then build each one as a **fixed** Verilog module first. You need to know what the programmable version must reproduce.

**Core**

| Protocol | Key details to learn |
|---|---|
| **UART** | Baud rate, start and stop bits, parity, sampling in the middle of a bit, oversampling on receive |
| **SPI** | Controller vs peripheral, chip select, **the 4 modes (CPOL/CPHA)**, full-duplex shifting |
| **I2C** | Open-drain wires and pull-ups, start and stop conditions, 7-bit addressing, ACK/NACK, clock stretching |

**Stretch (study after the core works)**

| Protocol | Key details to learn |
|---|---|
| **Low-speed USB** | 1.5 Mbit/s, differential pair (D+/D−), NRZI encoding, bit stuffing, SYNC and EOP, packet structure |
| **10BASE-T Ethernet** | 10 Mbit/s, Manchester encoding, preamble, link pulses |
| **JTAG / SWD** | The TAP state machine (JTAG), SWD request/ack/data phases |
| **PS/2, CAN** | PS/2 is easy. CAN has bit stuffing, arbitration and CRC. |

**Resources**
- Ben Eater's YouTube videos on serial, SPI, I2C and USB
- TI and NXP application notes (search for "I2C bus specification" and "SPI basics")
- A cheap **logic analyzer** (about ₹600–1000) plus PulseView, if you want to see real signals

**Checkpoint:** fixed-function UART TX/RX, SPI controller and I2C controller in Verilog, each passing its own testbench.

### Phase 4: Computer architecture and CPU design (about 1.5 weeks)

Your chip *is* a tiny CPU, so you need to know how CPUs are built.

**Topics**
- **Instruction set architecture (ISA):** what an instruction is, opcodes, operands, encoding into bits
- Datapath vs control unit
- Program counter, instruction memory, fetch → decode → execute
- Single-cycle CPU design
- Branches and jumps, loops, counters
- **FIFOs** (queues between your core and the outside world)
- Why **deterministic timing** matters, and what breaks it (caches, interrupts, pipeline stalls)
- Basic pipelining (enough to know when *not* to use it)

**Resources**
- Harris & Harris, chapters 6–7 (architecture, microarchitecture)
- Nand2Tetris, parts 4–5 (build a CPU)

**Checkpoint:** design a toy 4–8 instruction CPU in Verilog that can blink a pin with an exact delay.

### Phase 5: Study the existing designs, PIO and PRU (about 1 week)

This is where you learn what you are competing against.

**Topics**
- **RP2040 PIO:** read the PIO chapter of the **RP2040 datasheet**
  - the 9 instructions (JMP, WAIT, IN, OUT, PUSH, PULL, MOV, IRQ, SET)
  - the delay field and side-set
  - clock dividers, FIFOs, the shift registers (ISR/OSR)
- Read the official PIO example programs: UART, SPI, I2C, WS2812
- **TI PRU:** skim the PRU reference guide (AM335x). Focus on its deterministic timing and direct pin access.
- Write down what's **clever** and what's **painful** about each design. Your novelty starts here.

**Checkpoint:** explain from memory how the PIO UART TX program works, line by line, and write a list of three things you'd change.

### Phase 6: Your own ISA design and assembler (ongoing from about week 6)

This is no longer study: it's the core creative work.

**Topics**
- Designing an instruction encoding to fit a tight bit width
- Trade-offs between instruction count, timing precision and area
- Writing a simple **assembler**, a short Python script that turns your instruction text into bits
- Writing a **reference model**, a Python simulator of your CPU to compare against the hardware

**Checkpoint:** UART, SPI and I2C written as programs in *your* instruction set, running in simulation.

### Phase 7: Verification (start alongside Phase 2, deepen from about week 6)

Jane Street stresses verification, so treat it as a first-class skill, not an afterthought.

**Topics**
- Self-checking testbenches that compare against expected outputs automatically
- **cocotb**: write testbenches in Python. This is much faster than Verilog testbenches.
- **Constrained random testing**: generate thousands of random-but-valid inputs
- **Reference model comparison**: hardware output vs Python model output
- **Coverage**: how much of your design the tests actually exercised
- **Formal verification**: proving properties for *all* inputs with **SymbiYosys** (SBY) and SystemVerilog assertions
- **Protocol-level checking**: decode your chip's output waveform as a real UART, SPI or I2C receiver would

**Resources**
- cocotb documentation and tutorials
- The **ZipCPU blog** (zipcpu.com), which has excellent beginner formal-verification tutorials
- SymbiYosys documentation

**Checkpoint:** a cocotb test suite with random tests and at least one formal proof (for example, "the instruction counter never skips").

### Phase 8: The ASIC flow and Tiny Tapeout (about 1 week, starting by week 4)

This turns your code into a manufacturable chip.

**Topics**
- **Synthesis:** RTL → gates. Tool: **Yosys**.
- **Standard cells and the PDK.** The PDK is the "parts library" for the IHP SG13G2 / CMOS5L process.
- **Place-and-route** (OpenROAD / LibreLane / OpenLane)
- **Static timing analysis (STA):** does every path meet your clock period?
- Clock tree, area utilization, routing congestion
- **GDS**, **DRC** (design rule check) and **LVS** (layout vs schematic)
- Tiny Tapeout specifics:
  - `info.yaml`
  - tile size (6×4)
  - the fixed set of input, output and bidirectional pins you get. Check the template.
  - SRAM examples for instruction memory

**Resources**
- **Tiny Tapeout docs** (tinytapeout.com), which walk you through the whole process
- The **CMOS5L Verilog template** on GitHub (linked in the post)
- The Tiny Tapeout SRAM example on IHP

**Checkpoint:** push a trivial design (even just a counter) through the full template flow and get a GDS. **Do this early, around week 4**, so the toolchain never surprises you near the deadline.

### Phase 9 (optional): FPGA prototyping

- A cheap FPGA board (Tang Nano 9K, iCEBreaker, or similar) lets you run your design on real hardware and talk to real sensors.
- Toolchain: Yosys + nextpnr (open source)
- This is not required, but it's very convincing evidence that your design works.

---

## Suggested 12-week timeline

| Week | Focus |
|---|---|
| 1 | Phase 1 (sequential logic) + start HDLBits |
| 2–3 | Phase 2 (Verilog) + Phase 3 (protocols); build fixed UART |
| 3–4 | Fixed SPI and I2C; **trivial design through Tiny Tapeout flow** (Phase 8 intro) |
| 4–5 | Phase 4 (CPU design) + Phase 5 (PIO/PRU study) |
| 6–7 | **Design your ISA**, assembler, reference model (Phase 6) |
| 7–9 | Build the programmable core; UART/SPI/I2C as firmware; cocotb tests (Phase 7) |
| 9–10 | Formal proofs, random tests; stretch protocols if area allows |
| 10–11 | Full place-and-route, fix timing and area; write-up and documentation |
| 12 | Buffer week, final checks, submit before **Jan 18, 2027** |

**Biggest risks to watch**
- Learning everything before building anything. Build tiny things from week 1.
- Leaving the ASIC flow until the end. Run it early and often.
- Area. Synthesize regularly and check your cell count against about 24K.
- Working alone. The post strongly recommends teams. Possible splits are architecture, verification and tooling.

---

## Glossary (quick reference)

- **ASIC:** custom chip built for one purpose
- **RTL:** register-transfer level, the chip described as code (Verilog)
- **FSM:** finite state machine
- **ISA:** instruction set architecture, the instructions your CPU understands
- **Bit-banging:** implementing a protocol by toggling pins in software
- **Synthesis:** turning RTL into logic gates
- **Place-and-route:** placing gates on the die and wiring them up
- **STA:** static timing analysis, checking that every signal arrives in time
- **PDK:** process design kit, the factory's parts library and rules
- **GDS:** the final layout file sent to the factory
- **Tape-out:** submitting the design for manufacturing
- **Shuttle:** a shared manufacturing run for many small designs
- **PIO / PRU:** existing programmable pin controllers (RP2040 / TI Sitara)
- **Formal verification:** mathematically proving a property for all inputs
- **cocotb:** Python framework for writing hardware testbenches
