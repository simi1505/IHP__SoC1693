# Mixed-Signal IP Quality Assessment using TRL scale

The provided set of the quality assessment criteria, using TRL (Technology Readiness Level) scale, is a tool for designers to auto evaluate a submitted design. The purpose of this tool is to
have a short overview of IP in terms of its maturity.

Self-assessment of SoC1693 release v.1.0.0: TRL 3, the value in `doc/info.json`. TRL1 to TRL3 are met, TRL4 and TRL5 in part. Paths below are relative to the repository root.

---

## TRL1 - Basic principles observed

- [x] Is the IP functionality clearly described? (`doc/Datasheet.md`)
- [x] Is the IP principle of operation explained in detail? (`doc/Datasheet.md`, `doc/Specification.md`)
- [x] Are the IP architecture design equations fully listed? (inverter sizing in [`macros/inverter/scripts/sizing/sizing_inverter.ipynb`](https://github.com/iic-jku/ihp-sg13cmos5l-ams-chip-template/blob/60166ac618be6cc312253b7fed33f8478d044a55/macros/inverter/scripts/sizing/sizing_inverter.ipynb), the digital part is defined by its RTL)
- [x] Do authors supply any mixed-signal HDL functional model (e.g. Verilog-A) of the IP? (SystemVerilog RTL of the digital part in [`rtl/`](https://github.com/iic-jku/ihp-sg13cmos5l-ams-chip-template/tree/60166ac618be6cc312253b7fed33f8478d044a55/rtl) and [`macros/counter/rtl/`](https://github.com/iic-jku/ihp-sg13cmos5l-ams-chip-template/tree/60166ac618be6cc312253b7fed33f8478d044a55/macros/counter/rtl), no Verilog-A model, the analog inverter is simulated at transistor level)
- [x] Are functional simulation results of the IP reported for a typical case? ([`chip_top_tb_rtl.log`](../release/v.1.0.0/doc/sim/chip_top_tb_rtl.log))

## TRL2 - Concept formulation

- [x] Is the IP architecture described at block level? (`doc/Specification.md`)
- [x] Are the specifications of each individual block of the IP architecture clearly identified? ([`macros/counter/README.md`](https://github.com/iic-jku/ihp-sg13cmos5l-ams-chip-template/blob/60166ac618be6cc312253b7fed33f8478d044a55/macros/counter/README.md), [`macros/inverter/README.md`](https://github.com/iic-jku/ihp-sg13cmos5l-ams-chip-template/blob/60166ac618be6cc312253b7fed33f8478d044a55/macros/inverter/README.md))
- [x] If the IP architecture can be configured by means of internal registers, is their mapping declared? (not applicable, the chip has no configuration registers)
- [x] Do authors supply a complete test bench to validate the IP block-level architecture? ([`testbenches/cocotb/chip_top_tb.py`](https://github.com/iic-jku/ihp-sg13cmos5l-ams-chip-template/blob/60166ac618be6cc312253b7fed33f8478d044a55/testbenches/cocotb/chip_top_tb.py))
- [x] Is any verification procedure given to check the IP block-level performance figures? (`make sim-rtl-cocotb` in the design source, see its [`README.md`](https://github.com/iic-jku/ihp-sg13cmos5l-ams-chip-template/blob/60166ac618be6cc312253b7fed33f8478d044a55/README.md))
- [x] Are architectural simulation results of the IP reported for a typical case? ([`chip_top_tb_rtl.log`](../release/v.1.0.0/doc/sim/chip_top_tb_rtl.log), 6 of 6 tests pass)

## TRL3 - Proof of concept at schematic level

- [x] Are the IP schematics available at transistor level (analog parts) and gate level (digital parts)? ([`macros/inverter/schematic/`](https://github.com/iic-jku/ihp-sg13cmos5l-ams-chip-template/tree/60166ac618be6cc312253b7fed33f8478d044a55/macros/inverter/schematic), gate level in [`release/v.1.0.0/netlist/SoC1693.v`](../release/v.1.0.0/netlist/SoC1693.v))
- [x] Are all dependencies of the IP schematics on logic libraries declared? (`doc/info.json`, `dependencies`)
- [x] Are the analog IP ports fully specified at electrical level? (`doc/Datasheet.md`, [`macros/inverter/README.md`](https://github.com/iic-jku/ihp-sg13cmos5l-ams-chip-template/blob/60166ac618be6cc312253b7fed33f8478d044a55/macros/inverter/README.md))
- [x] Are the digital IP ports fully specified at logical level (e.g. protocols)? (`doc/Datasheet.md`)
- [x] Do authors supply a mixed-signal test bench to validate the IP schematics? ([`testbenches/xschem/chip_top_tb_tran.sch`](https://github.com/iic-jku/ihp-sg13cmos5l-ams-chip-template/blob/60166ac618be6cc312253b7fed33f8478d044a55/testbenches/xschem/chip_top_tb_tran.sch))
- [x] Is any verification procedure given to check the IP schematic performance figures? (`make sim-gl-xschem` in the design source)
- [x] Are mixed-signal simulation results of the IP schematics reported for a typical case? ([`testbenches/xschem/plot_simulations/figures/`](https://github.com/iic-jku/ihp-sg13cmos5l-ams-chip-template/tree/60166ac618be6cc312253b7fed33f8478d044a55/testbenches/xschem/plot_simulations/figures))

## TRL4 - Full design at schematic level

- [ ] Are the mixed-signal simulation results of the IP schematics extended to process, supply and temperature (PVT) corners? (in part: `inverter_top` over corners in CACE, the chip timing over six STA corners, the chip-level mixed-signal bench at the typical corner only)
- [ ] Are the mixed-signal simulation results of the IP schematics extended to technology mismatching? (in part: `inverter_top` Monte Carlo mismatch in CACE only)
- [x] Are the authors defining the mixed-signal supply domains of the IP schematics and their individual power requirements? (`doc/Datasheet.md`, power per corner in [`verification/reports/stapostpnr_*_power.rpt`](https://github.com/iic-jku/ihp-sg13cmos5l-ams-chip-template/tree/60166ac618be6cc312253b7fed33f8478d044a55/verification/reports))
- [ ] Does the IP description include any power consumption model (e.g. as a function of input stimuli)?

## TRL5 - Full design at layout level

- [x] Is the IP complete layout available? (`release/v.1.0.0/gds/SoC1693.gds.gz`)
- [x] Are all dependencies of the IP layout on logic libraries declared? (`doc/info.json`)
- [ ] Do authors supply a clean DRC report? (in part: the minimal precheck is clean, the regular deck reports 78 recommended pad-rule items and 10 `NW.d` items reviewed in `release/v.1.0.0/doc/Signoff.md`)
- [ ] Do authors supply a clean LVS report? (in-flow Netgen LVS with black boxes matches, full-chip LVS in progress)
- [ ] Are post-layout mixed-signal simulation results of the IP layout reported for PVT corners?

## TRL6 - Full design for SoC integration

- [ ] Does the IP come with suitable descriptors for digital-on-top integration (e.g. Liberty, LEF)? (not applicable, SoC1693 is a complete chip)
- [x] Is the IP incorporating any BIST mechanism? If so, is it properly documented? (the SRAM BIST ports are tied off, `doc/Specification.md`)
- [x] Does the IP document fully define its digital interface? (`doc/Datasheet.md`)

## TRL7 - Lab demonstrator prototype

- [x] Has any dedicated test chip been designed for the hard IP? (SoC1693 is the test chip)
- [x] Is the hard IP test chip fully documented (e.g. pad ring)? (`doc/Datasheet.md`)
- [ ] Are experimental results available from the IP dedicated test chip (Silicon proven)?
- [ ] Do authors supply any comprehensive comparison between IP test chip and post-layout results?

## TRL8 - In-field demonstrator prototype

- [ ] Has the hard IP been integrated in a SoC context?
- [ ] Are experimental IP results available from this SoC (Silicon proven)?
- [ ] Do authors supply any comprehensive comparison between IP SoC and test-chip results?

## TRL9 - Commercial application

- [x] Are the IP authors providing support for bug fixing or enhancement requests? (GitHub issues of the design source)
- [x] Does the IP documentation include any training materials? (tutorial of the design source, https://iic-jku.github.io/ihp-sg13cmos5l-ams-chip-template/)
- [x] Are the EDA tools and versions used for developing the IP documented? (`doc/info.json`, `release/v.1.0.0/ReleaseNote.md`)
- [ ] Is the hard IP integrated in any commercial IC?
