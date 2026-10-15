# SECDED-Protected-Memory
This project is about building a small memory that can detect and correct errors in the data it stores. I’m using extended Hamming SECDED (Single Error Correction, Double Error Detection) and SystemVerilog to implement the design and verify that it works.
The main aim is not just to write an encoder and decoder. I also want to build a testbench that checks the design, deliberately introduces bit errors, and reports whether those errors are handled correctly.
What I’m building
The memory stores 32-bit data words along with extra parity bits. When data is read, the ECC decoder checks the stored codeword before returning the result.
The project will cover:
- Encoding 32-bit data before writing it to memory.
- Correcting single-bit errors.
- Detecting double-bit errors and marking the data as uncorrectable.
- Testing normal reads and writes.
- Injecting errors during simulation to check the decoder.
- Using a reference model, scoreboard, assertions, and functional coverage to verify the design.
 
### ECC format
The plan is to protect each 32-bit data word using extended Hamming SECDED. This requires 6 Hamming parity bits and 1 overall parity bit, giving a total codeword width of 39 bits.
The Hamming code uses even parity. The Hamming positions are numbered from 1 to 38, with parity bits at positions 1, 2, 4, 8, 16, and 32. The remaining positions hold the 32 data bits, and the overall parity bit is stored separately.
The decoder calculates a syndrome and checks the overall parity to decide whether the word is clean, has a correctable single-bit error, or has an uncorrectable double-bit error.
Syndrome	Overall parity	Meaning
Zero	0	No error detected
Nonzero	1	Single-bit error in the Hamming codeword
Zero	1	Error in the overall parity bit
Nonzero	0	Double-bit error detected


### SECDED can correct single-bit errors and detect double-bit errors. It does not guarantee correct handling of every error involving three or more flipped bits.
Planned modules
secded_encoder.sv
Takes 32-bit data and produces a 39-bit codeword. It places the data in the non-parity positions and calculates the parity bits.
secded_decoder.sv
Takes a 39-bit codeword, calculates the syndrome, corrects single-bit errors, and reports uncorrectable errors.
ecc_memory.sv
Connects the encoder and decoder to a small memory. I plan to start with 16 locations, each storing one 39-bit codeword, and use a simple synchronous read/write interface.
ecc_memory_tb.sv
The testbench will drive the design, keep track of expected data, inject faults, compare outputs, and collect coverage.
Verification plan
I’ll start with simple directed tests and then move to more extensive testing.
### Basic tests
- Write data and read it back without errors.
- Test all-zero, all-one, alternating-bit, and random data patterns.
- Write and read every memory address.
- Overwrite a location and check that the new data is returned.
- Check reset behavior and read/write timing.
### Error-injection tests
A 39-bit codeword has:
- 39 possible single-bit error locations.
- 741 unique two-bit error combinations (39 choose 2).
For each single-bit fault, the testbench should check that the original data is recovered and the single-error flag is asserted. For each two-bit fault, it should check that the design reports an uncorrectable error.
The testbench should not expect the payload to remain correct when a word is flagged as uncorrectable.
Randomized testing
Once the directed tests work, I’ll add random data, addresses, read/write sequences, and selected fault patterns. A reference model and scoreboard will compare the DUT’s results against the expected behavior.
Assertions and coverage
I also plan to add assertions for read-response timing and error-status behavior. Functional coverage will track clean reads, corrected errors, uncorrectable errors, and memory address access.
Tools
I’ll use a SystemVerilog simulator that supports the features used in the testbench. Possible options include:
- EDA Playground
- Questa/ModelSim
- Vivado Simulator
I’ll add the exact simulator version and run commands once I settle on the environment.
Progress and results
This section will be updated as the project develops. I won’t mark tests as passing until I’ve run them and checked the results.
Test	Current status
Encoder and decoder	Planned
Single-bit fault tests	Planned
Double-bit fault tests	Planned
Memory read/write tests	Planned
Randomized regression	Planned
Assertions and coverage	Planned


What I want to learn
Through this project, I want to get more comfortable with SystemVerilog RTL, ECC logic, self-checking testbenches, scoreboards, fault injection, assertions, and functional coverage. Once the basic version works, I may add features such as error scrubbing or an FPGA demonstration.

### Author: SIDDHESH SINGH
### Project focus: Digital Design Verification, SystemVerilog, and ECC
