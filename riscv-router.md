#  risc-v router

Selling a low-cost, low-power, small (2-6 port) gigabit router that's easy to hack on the software & hardware.

- software
- RTL model
- PCB design / validation
- part selection / pricing
- compliance
- manufacturability
- sales / fulfillment
- operations / legal
- [licensing](./licensing.md)

# software

Bare metal vs. full linux distro ? 

Bare metal: high efficiency, tight integration, few use-cases
Full linux: still need drivers, massive (unaccelerated) ecosystem

Evaluation criteria:
- Boot time 
- Power efficiency
- Update/modification tooling

testing: RTL simulator vs virtualized ? Possible to mix the two?

seems important: 
- hardware/software alignment (low effort, up-to-date)
- debuggability (determinism, waveform tracing)
- validating complex interactions (e.g. edge-driven UART interrupts vs. level-driven)
- speed of feedback

all but the last point to using an RTL simulation (e.g. verilator); how do we make the simulation small/cheap enough for a tight feedback loop? Ideas include:
- hybridizing a hardware/software model to "hold one (pair) out" so the fully-simulated (slow) parts are a subset of the whole system/model 
- "jump to test case" reset vector with known/harness-controlled processor initial conditions 
- "hot reload" in a "live" simulator (there's a weird pause during startup)
- hardware acceleration (e.g. FPGA, GPU, custom ASIC?)

# RTL model

chipyard/firesim includes icenet

# PCB design / validation

How many layers?

Trace sensitivity at 1 gigabit? (i.e. what happens when we double the number of ports?)

How do we measure things at 1 gigabit?

# part selection / pricing

https://www.xilinx.com/products/boards-and-kits/arty.html ?

Xilinx Spartan 7? (in the logic analyzer) 

ethernet PHY on-FPGA or off-FPGA?

# compliance

## being compliant

what are the emissions limits for RF emissions from "unintentional emitter" digital devices?
What changes between regions? FCC vs. "EU" vs. "Asia"?

testing harnesses? home anechoic chambers?

## demonstrating compliance

booking (expensive) time at a "proving" lab?

# TBD

- manufacturability
- sales / fulfillment
- operations / legal
