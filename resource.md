# Resources for the Jane Street Protocol Emulator ASIC

**Deadline:** January 18, 2027, 23oct-2025


Companion file: `protocol-emulator-asic-guide.md` (the 9-phase roadmap). The phase numbers below match it.

---

## Time budget at a glance

| # | Resource | Phase | When | Time | Cost |
|---|---|---|---|---|---|
| 0 | Jane Street competition post | – | Day 1, then reread often | 30 min | Free |
| 1 | Branch Education video | Context | Week 1 | 30 min | Free |
| 2 | NandGame (selected levels) | 1 | Week 1 | 1 weekend | Free |
| 3 | HDLBits (selected sections) | 2 | Weeks 1–3 | 30–45 min/day | Free |
| 4 | ZipCPU Verilog tutorial | 2, 3, 7 | Weeks 2–3 | ~1.5 weeks | Free |
| 5 | Tiny Tapeout docs + SiliWiz + template | 8 | Weeks 2–4 | ~4 evenings | Free |
| 6 | Nand2Tetris, ISA and assembler units only | 4, 6 | Week 4 | ~4 evenings | Free (audit) |
| 7 | "From Blinker to RISC-V", Episode I | 4 | Weeks 4–5 | ~1 week | Free |
| 8 | PIO intro article + RP2040 datasheet PIO chapter | 5 | Week 5 | ~3 evenings | Free |
| 9 | cocotb docs | 7 | Weeks 6–10 | Ongoing | Free |
| 10 | ZipCPU formal verification + SymbiYosys docs | 7 | Weeks 6–10 | Ongoing | Free |
| 11 | Jane Street: "Using ASCII waveforms to test hardware designs" | 7 | Week 6 | 1 hour | Free |

That is **12 items**, and every one is free.

---

## The core resources

### 0. The competition post itself
**Link:** https://blog.janestreet.com/protocol-emulator-asic-competition/

**Why:** it is the actual spec. It contains the rules, the area limit, the process, the deadline, and what they value (novelty and verification).

**Do:** reread it at the start of every phase. Things that seem vague in week 1 will make sense in week 6.

**Also:** fill in the sign-up form linked there, which gets you updates such as the possible 8×4 tile increase. If anything is ambiguous, email asic-competition@janestreet.com.

---

### 1. Branch Education: "How are Microchips Made?" (YouTube)
**Find it:** search "Branch Education How are Microchips Made" on YouTube.

**Why:** a 30-minute animated tour from transistor to finished CPU. It gives you the mental picture of what "GDS," "layers" and "fabrication" physically mean before you touch the tools.

**Do:** watch it once. Don't take notes.

**Skip:** every other "how chips are made" video. This one is enough.

---

### 2. NandGame, selected levels only
**Link:** https://nandgame.com

**Why:** a fast, free way to cross from "I know gates" to "I understand how a CPU is built from them": registers, counters, memory, and a working CPU.

**Do:**
- **Skip** the basic gate levels. You already know these.
- **Play** the memory and sequential levels (latches, flip-flops, registers, counters).
- **Play** the CPU levels.

**Checkpoint:** you can explain how a program counter and an instruction decoder work together.

**Why NandGame over Turing Complete:** it's free, browser-based, and quicker. Turing Complete goes deeper (see the footnotes) but costs money and many more hours.

---

### 3. HDLBits, selected sections ⭐
**Link:** https://hdlbits.01xz.net

**Why:** the best way to get fluent in Verilog. These are short problems with instant automatic checking, like LeetCode for hardware. Fluency is what lets you move fast in weeks 6–11.

**Do these sections, in order:**
1. Getting Started
2. Verilog Language: Basics, Vectors, Modules, Procedures (`always` blocks, `if`, `case`)
3. Circuits → Sequential Logic: D flip-flops, counters, **shift registers**, **finite state machines (FSMs)**
4. Verification: Writing Testbenches

**Skip:** Karnaugh maps (you know this), the Conway's Game of Life and Lemmings puzzles, and the very large FSM problems after you've done about 10 FSMs.

**Rhythm:** 30–45 minutes a day, alongside everything else in weeks 1–3.

**Checkpoint:** you can write a working FSM and a shift register without looking anything up.

---

### 4. ZipCPU beginner's Verilog tutorial ⭐
**Link:** https://zipcpu.com/tutorial/

**Why:** this is the most project-aligned tutorial that exists.
- It teaches the **whole toolchain** together: Verilator (simulator), GTKWave (waveforms) and SymbiYosys (formal verification).
- It **ends with a serial port, which is literally a UART transmitter, receiver and FIFO**. That is your first protocol.

**Do:** the full beginner series (about 10 lessons). Set up the tools on your laptop (Linux or WSL recommended).

**Checkpoint:** a working, simulated UART TX and RX that you wrote yourself. This also clears the post's own advice: "Start by getting a UART transmitter out of a pin."

---

### 5. Tiny Tapeout: SiliWiz, docs, and the CMOS5L template ⭐
**Links:**
- SiliWiz: https://www.tinytapeout.com/siliwiz/introduction/
- Docs: https://www.tinytapeout.com
- Competition template: https://github.com/TinyTapeout/ttihp-verilog-template/tree/cmos5l

**Why:** this is the exact service and toolchain the competition uses. You must know it cold before the deadline.

**Do:**
1. **SiliWiz** (about 3 hours, in week 2). Draw a transistor and a gate, and see how the factory builds it. This is the physical layer under everything else.
2. **Read the getting-started and digital design guides** on the Tiny Tapeout site.
3. **By the end of week 4:** fork the CMOS5L template, set the tile size to 6×4 in `info.yaml`, put in a trivial design (a counter, then your UART), and get it all the way to GDS through the GitHub Action.
4. **Read the report:** cell count, utilization and timing. This is how you'll track area for the rest of the project.

**Also look at:** the Tiny Tapeout SRAM example linked in the competition post. You'll need it when you decide how to store instructions.

**Checkpoint:** a green GitHub Action and a GDS for your UART.

---

### 6. Nand2Tetris: only the ISA and assembler units
**Link:** https://www.coursera.org/learn/build-a-computer (audit for free)

**Why:** two of its units teach exactly what this project's core design work needs:
- designing a **machine language** (instruction set)
- writing an **assembler** that turns it into bits

**Do:**
- **Unit 4** (Machine Language)
- **Unit 5** (Computer Architecture)
- **Unit 6** (Assembler)

**Skip:** Units 1–3. NandGame and HDLBits already covered gates, the ALU and memory.

**Checkpoint:** you can design a small instruction encoding on paper and write a roughly 50-line Python assembler for it.

---

### 7. "From Blinker to RISC-V," Episode I (Bruno Levy) ⭐
**Link:** https://github.com/BrunoLevy/learn-fpga/blob/master/FemtoRV/TUTORIALS/FROM_BLINKER_TO_RISCV/README.md

**Why:** it shows how a real CPU is written in Verilog, step by step, changing one thing at a time. Your protocol emulator *is* a small CPU, and this tutorial bridges "I know Verilog" and "I can build a processor." It runs fully in simulation, so no board is needed.

**Do:** Episode I, from the blinky step up to a working core that runs programs.

**Skip:**
- Episode II (pipelining). Your pin-helper should avoid complex pipelines, because they hurt deterministic timing.
- The graphics demos.

**Checkpoint:** you understand fetch → decode → execute *in Verilog*, and can sketch how your own `SET`, `WAIT`, `OUT` and `JMP` core would be structured.

---

### 8. PIO: an intro article, then the official datasheet chapter ⭐
**Links:**
1. Intro: https://blues.com/blog/raspberry-pi-pico-pio/
2. Source: RP2040 datasheet, PIO chapter — https://datasheets.raspberrypi.com/rp2040/rp2040-datasheet.pdf

**Why:** PIO is the main design you're competing against. You need to know its 9 instructions, the delay field and side-set, its shift registers and FIFOs, and *why* each exists, so you can argue what your design does better.

**Do:**
1. Read the blues.com article for intuition (about 30 minutes).
2. Read the datasheet's PIO chapter carefully, especially the instruction set and the example programs (UART TX/RX, SPI, I2C).
3. Write down **three things PIO does cleverly** and **three things that are painful or impossible** (for example, USB bit-stuffing or CRC).

**Checkpoint:** you can explain the PIO UART TX program line by line. Your "three painful things" list becomes your novelty pitch.

---

### 9. cocotb: Python testbenches
**Link:** https://docs.cocotb.org

**Why:** writing tests in Python is much faster than in Verilog. It's also the easiest way to build the **random tests** and **Python reference model** that make up a strong verification story, which Jane Street explicitly values.

**Do:** the quickstart and tutorial. Then rewrite your UART testbench in cocotb, and add a test that sends random bytes and decodes the waveform.

**Checkpoint:** a test that runs your programmable core on random programs and compares its output against a Python model of your instruction set.

---

### 10. ZipCPU formal verification + SymbiYosys docs ⭐
**Links:**
- https://zipcpu.com/formal/formal.html
- https://symbiyosys.readthedocs.io

**Why:** formal verification mathematically proves properties for *all* inputs, using SAT and SMT solvers underneath. This is where your logic and P vs NP background is a real advantage, and it's the strongest way to stand out on verification.

**Do:**
- Read the ZipCPU beginner formal posts.
- Run the SymbiYosys quickstart.
- Prove small properties first, for example "WAIT n takes exactly n cycles" or "the program counter never exceeds the program length."

**Checkpoint:** at least one meaningful property formally proven on your core.

---

### 11. Jane Street: "Using ASCII waveforms to test hardware designs"
**Link:** https://blog.janestreet.com/using-ascii-waveforms-to-test-hardware-designs/

**Why:** it shows how the judges themselves think about testing hardware. It's short, and it gives you a feel for the verification style they appreciate.

**Do:** read it once around week 6, when you start building your test suite.

---

## How the core list maps to the 12 weeks

| Week | Main resource | Side resource |
|---|---|---|
| 1 | NandGame (2), Branch video (1) | HDLBits (3) daily |
| 2 | ZipCPU tutorial (4) | HDLBits (3), SiliWiz (5) |
| 3 | ZipCPU tutorial (4), finishing UART | HDLBits (3), Tiny Tapeout docs (5) |
| 4 | Nand2Tetris units 4–6 (6) | **UART through the Tiny Tapeout flow (5)** |
| 5 | Blinker to RISC-V (7), PIO (8) | – |
| 6–7 | Design your own instruction set (no new resource) | cocotb (9), Jane Street waveforms post (11) |
| 7–9 | Build the programmable core | cocotb (9), formal (10) |
| 9–10 | Formal proofs, random tests | formal (10) |
| 10–12 | Place-and-route, area, timing, write-up | Tiny Tapeout docs (5) |

---

## Footnotes: everything else, and when you'd need it

These were all found in research but **left out of the core path**. Use one only if its condition applies to you.

### Use only if a specific situation arises

[1]: **Turing Complete** (Steam, paid). It goes from NAND gates all the way to designing your own assembly language. *Use if:* NandGame felt too shallow and you want deeper intuition for instruction-set design, and you're happy to spend money and 20+ hours.

[2]: **Sebastian Lague, "Exploring How Computers Work" (YouTube) + Digital Logic Sim.** Beautiful visual explanations. *Use if:* sequential logic (latches, memory) still feels abstract after NandGame.

[3]: **Ben Eater's protocol videos** (YouTube: serial, SPI, I2C, USB). *Use if:* you're stuck understanding a protocol's waveform. Watch **only** the video for that protocol. Skip his full 8-bit breadboard CPU series, which needs physical parts and many weeks.

[4]: **Nandland.com and the book *Getting Started with FPGAs* (Russell Merrick).** *Use if:* you buy an FPGA board for real-hardware testing (Phase 9). It's also a gentler Verilog explanation if ZipCPU feels too dense.

[5]: **Project F** (projectf.io). Clean open-source Verilog, plus a RISC-V assembler series. *Use if:* you have an FPGA board, or want to see well-written Verilog modules for reference.

[6]: **Zero to ASIC Course** (Matt Venn, paid). The structured, paid version of the Tiny Tapeout material. *Use if:* you get stuck on the ASIC flow and the free Tiny Tapeout docs and Discord aren't enough.

[7]: **Tiny Tapeout online workshop** (paid). Two guided sessions that submit a design. *Use if:* you'd learn the flow faster with live guidance. Otherwise the free docs cover the same steps.

[8]: **Adafruit, "Intro to RP2040 PIO with CircuitPython."** *Use if:* you buy a Raspberry Pi Pico (cheap in India) to run real PIO programs and see the waveforms you'll be emulating.

[9]: **Circuit Cellar, "RP2040 Programmable IO."** A more technical PIO write-up with real project examples. *Use if:* you want a second explanation of PIO after the datasheet.

[^10]: **TI PRU reference guide (AM335x).** *Use if:* you're considering a more CPU-like (PRU-style) design and want to see how a full real-time core handles deterministic I/O. Skim only.

[^11]: **Ken Shirriff's blog** (righto.com). Reverse-engineering real chips from die photos. *Use if:* you're curious about real silicon. It's also good preparation for Jane Street's separate reverse-engineering puzzle. Not needed for this project.

[^12]: **Harris & Harris, *Digital Design and Computer Architecture* (RISC-V edition), chapters 3, 6, 7.** *Use if:* you want a textbook reference for sequential logic, ISA and microarchitecture alongside the tutorials. Use as a lookup, not a read-through.

[^13]: **ASIC Design Roadmap** (GitHub: abdelazeem201/ASIC-Design-Roadmap). *Use if:* you decide on a longer-term career in chip design after this competition. Not needed for the next 12 weeks.

[^14]: **Generic "ASIC design flow" articles** (AnySilicon, VLSI training-institute blogs). *Use if:* you want a one-page overview of the industry flow, including DFT and signoff. Most of it describes commercial tools you won't use.

### Skip entirely (for this project)

[^15]: **MHRD.** Reviews call it short and tedious for anyone who already knows digital logic. NandGame covers the same ground for free.

[^16]: **TIS-100, Shenzhen I/O.** Fun assembly puzzle games, but not about hardware design. Play them for fun after the deadline, not before.

[^17]: **Hard Chip.** Transistor-level puzzles. SiliWiz covers the physical layer in about 3 hours, in the actual competition ecosystem.

[^18]: **TED-Ed "How are microchips made?" and other fabrication videos.** The Branch Education video already covers this.

[^19]: **Full Nand2Tetris (Units 1–3, and Part II).** Gates and memory are covered by NandGame and HDLBits. Part II (compilers, operating systems) is unrelated to this project.

[^20]: **"From Blinker to RISC-V," Episode II (pipelining).** Pipelines make timing less predictable, which is the opposite of what a protocol emulator wants. Skip unless your architecture specifically needs it.
