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

Obviously it should spit out UART somewhere so we can plug Vgaterm into it.

## Design Goals

All of these are in direct tension with each other:

- Low cost (~$30)
- Low power (single-digit watts?)
- Gigabit line rate (802.3ac)

And some broader tensions that have impacts here:

- Security and experimentation
- Modularity and reliability
- Instrumentation and performance


### Comparison

<table>
 <th>
   <td>Design Goal</td>
   <td><a href="https://store.ui.com/us/en/pro/category/wired-edge-max-routing/products/er-x">EdgeRouter X</a></td>
   <td><a href="https://store.ui.com/us/en/pro/category/all-wired/products/er-x-sfp">EdgeRouter X-SFP</a></td>
</th>
 <tr><td>Price</td><td>$40</td><td>$60</td><td>$100</td></tr>
 <tr><td>Power (max)</td><td>5W</td><td colspan=2>5W</td></tr>
 <tr><td>Power (avg)</td><td>??</td><td colspan=2><5W ?</td></tr>
 <tr><td>ISA</td><td>RISC-V</td><td colspan=2>MIPS</td></tr>
 <tr><td>Networking</td><td>(2-4) 10/100/1000 RJ45 ports</td><td>(3) 10/100/1000 RJ45 ports<br>(1) 10/100/1000 RJ45 port (Data/PoE input)<br>(1) 10/100/1000 RJ45 port (Data/PoE passthrough)</td><td>(5) 10/100/1000 RJ45 ports<br>(1) 100/1000 SFP port </td></tr>
 <tr><td>PoE Output</td><td>??</td><td>N/A</td><td>Passive 12W (24V)</td></tr>
 <tr><td>Software</td><td>??</td><td colspan=2>Linux (Vyatta)</td></tr>
 <tr><td>Other Hardware</td><td>??</td><td colspan=2>
     <table>
      <tr><td>Processor</td><td>Dual-core 880 MHz, MIPS1004Kc</td></tr>
      <tr><td>Memory</td><td>256 MB DDR3 RAM</td></tr>
      <tr><td>Storage</td><td>256 MB NAND</td></tr>
     </table>
    </td>
 	
 <tr><td>Ambient operating temperature</td><td>At least +10 to 40°C</td><td colspan=2>-10 to 45° C (14 to 113° F)</td></tr>
 <tr><td>Ambient operating humidity</td><td>??</td><td colspan=2>10 to 90% noncondensing</td></tr>
 <tr><td>Certifications</td><td>??</td><td colspan=2>CE, FCC, IC</td></tr>

 <!--
 <tr><td>TITLE</td><td>GOAL</td><td colspan=2>UBNT</td></tr>
 -->

 
</table>



### Gigabit Ethernet

What is the minimum power (in watts) for a gigabit-capable device to route a signal?

What is the minimum clockspeed? Per https://en.wikipedia.org/wiki/Gigabit_Ethernet#Copper it's a lot less than I thought: 62.5 MHz (1000 Gb / 4 lanes / 2 bits/clk / 2 ??? )


## Questions
* Do we want to make a router or switch?
* What is Router?
* What is Switch?
* What do *we* want it to do?
* Who do we think this is for? Why do they want it? 
* Name?
  * Open Switch (lol, like a electronic switch?)
  * Closed Switch (lol, irony here since we're open?)
  * Mycelium (the small fungi filaments that connect nutrient pathways between trees and fungi)
  * \<moar ideas pls?\>


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
- debugability (determinism, waveform tracing)
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

This looks promising: https://www.microchip.com/en-us/products/fpgas-and-plds/fpgas/igloo-2-fpgas

> IGLOO® 2 Flash FPGA devices are ideal for general-purpose functions such as Gigabit Ethernet ...

And they start at ~$14/chip: https://www.mouser.com/c/?marcom=134980455&sort=pricing

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
