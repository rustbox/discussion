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

## References

- https://zipcpu.com/blog/2023/11/25/eth10g.html
- https://www.chili-chips.xyz/open-cologne/ (note the paring of a Raspberry Pi CM4 "IO board" with a Lattice/Cologne FPGA)

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

## fpgas

https://www.xilinx.com/products/boards-and-kits/arty.html ?

Xilinx Spartan 7? (in the logic analyzer) 

ethernet PHY on-FPGA or off-FPGA?

This looks promising: https://www.microchip.com/en-us/products/fpgas-and-plds/fpgas/igloo-2-fpgas

> IGLOO® 2 Flash FPGA devices are ideal for general-purpose functions such as Gigabit Ethernet ...

And they start at ~$14/chip: https://www.mouser.com/c/?marcom=134980455&sort=pricing

### Rough notes

It seems like there's a few layers to try teasing apart here:

- Development board/kit; probably we should expect to go through a few of these, probably starting with whatever's easiest for beginners and going from there? There's a lot of details in the FPGA design space, and it's not clear how to tease them apart yet. There's also a lot of play in the FPGA-board-maker space, too: most seem to also integrate whole SDKs on top of the board on top of the FPGA itself (see "programming software" bullet below)
- "Production" chip: defining the envelope we need from the part is one thing; finding anyone brave enough to have an open FPGA core is a whole 'nother. Also, availability & price seem weirdly un-correlated to the dev boards that I've seen so far.
- "Acceleration" chips: The best way to do FPGA development seems to be on an FPGA.... but, not "directly," instead using the FPGA to coorindate with the RTL simulator. What's _that_ chip selection look like?
  - Amazon will rent you an [F1 instance with a single (1) attached FPGA](https://instances.vantage.sh/aws/ec2/f1.2xlarge) for $1.6500/hr on-demand (~$0.6488/hr spot), which is about $1200 a month ($500 for spot). They've got a density problem, it seems, since the largest size is an [`f1.16xlarge`](https://instances.vantage.sh/aws/ec2/f1.16xlarge) which comes with 8 FPGAs and a whopping 976.0 GiB of RAM with	64 vCPUs; that and the dedicated 25 Gigabit networking suggest they're renting you the whole blade at that point. But: in our (very limited) experience with RTL simulation, a fairly complex model with no acceleration takes on the order of ones of CPUs and tens of _Megabytes_ of memory. Meaning, a burst-credit model for way oversubscribing the CPU and nano- or micro-scale memory (0.5 to 1 GB) might get us equivalent performance to the `f1.2xlarge` at literally 1/100th the cost of the `f1.2xlarge`. But, to sell us that, assuming their blade is already otherwise "minimally sized" for an AWS DC, they'd need to slot some 512 or 1024 FPGAs per blade. Maybe in the `f2` class, eh?
  - Or, it's possible that _is_ a good deal for running a workload more than a couple hours a month, and I've wildly missed my guesses above. In which case, the FPGA itself makes up a significant proportion of the price, and the kind of hardware that people use to simulate other hardware costs on the order of tens of thousands of dollars. I sure hope not!
  - But, uh, "firesim" (which is the FPGA accelerator underneath chipyard?) [supports](https://docs.fires.im/en/stable/FireSim-Basics.html#choose-your-platform-to-get-started) the Xilinx Alveo U250 (or U280, which is [discontinued](https://www.xilinx.com/products/boards-and-kits/alveo/u280.html)), [Xilinx VCU118](url), and the RHS Research Nitefury II. That's a [$10,000 "accelerator card"](https://www.mouser.com/ProductDetail/AMD-Xilinx/A-U250-A64G-PQ-G?qs=unwgFEO1A6vi%2FTDpEsBygA%3D%3D), a [$10,000 evaluation kit](https://www.xilinx.com/products/boards-and-kits/vcu118.html) (although you can back-order the chip itself inside the evaluation board for a [cool $50,000](https://www.digikey.com/en/products/detail/amd/XCVU9P-L2FLGA2104E/7421960), which, uh 😵‍💫). But, [RHS Research](https://rhsresearch.com/pages/about-us) to the rescue! Their Nitefury II is a M.2 form-factor accelerator for $150, which is an awful lot more "in budget", even if it only seems to be available via [Amazon](https://www.amazon.com/dp/B0B9FMBF6C). Also of interest: https://www.crowdsupply.com/rhs-research & https://github.com/RHSResearchLLC/NiteFury-and-LiteFury . 
- programming software: seems like an awful lot of _that_ stack is vendor-specific, locked-down, slow, lacking in Linux support (but there's always WINE), and/or varies greatly in quality. 
- so-called "IP" availability: there's a somewhat eclectic mix of "parts" available to "plug in" to a design.
  - it sure would be nice to have an index where we could say, e.g. "ETH PHY 1000" and get a list of gigabit-capable "core"s (not the CPU kind of "core"). Maybe someone should make that (us?).
  - To be useful, it'd probably also need to be further filterable by "integration details," maybe HDL? "inward" facing protocols (SPI; TileLink; that ARM interlink protocol thing; etc.)?

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
