# SoC1693 Specification

This document describes the architecture, the physical implementation and the verification status of SoC1693. The pinout and the functions seen from the pins are in [Datasheet.md](Datasheet.md). The numbers below are taken from the design source, [iic-jku/ihp-sg13cmos5l-ams-chip-template](https://github.com/iic-jku/ihp-sg13cmos5l-ams-chip-template), whose `doc/specifications.md`, `doc/pinout.md` and `doc/floorplan.md` hold the full description.

## Architecture

| Block | Cell | Count | Kind | Description |
| --- | --- | --- | --- | --- |
| Pad ring | `sg13cmos5l_io` cells | 32 pads | IHP IO library | 4 supply, 3 input, 17 output, 4 bidirectional and 4 analog pads, corner and filler cells, 70 x 70 µm bondpads |
| `counter1`, `counter2` | `counter_top` | 2 | digital hard macro | 8-bit synchronous up-counter, RTL in SystemVerilog, hardened with LibreLane on `sg13cmos5l_stdcell` |
| `inverter1`, `inverter2` | `inverter_top` | 2 | analog hard macro | four CMOS inverters, schematic in Xschem, hand-drawn layout in KLayout, 54.18 x 81.92 µm |
| `sram_0` | `RM_IHPSG13_1P_1024x32_c2_bm_bist` | 1 | IHP SRAM macro | 1024 x 32 bit single-port SRAM with bit mask and BIST |
| Glue logic | `sg13cmos5l_stdcell` | | standard cells | enable gating, bidirectional pad control, SRAM output XOR |
| Logos | `sg13cmos5l_ip__jku`, `sg13cmos5l_ip__jku_names` | 2 | layout only | 100 x 100 µm, Metal4, no devices |

The top-level RTL is `SoC1693-main/rtl/chip_top.sv` (pad ring) and `SoC1693-main/rtl/chip_core.sv` (macro instances and glue logic). Both macros have their own source, flow and verification under `SoC1693-main/macros/`.

## Physical Implementation

| Parameter | Value |
| --- | --- |
| Die area | `[0, 0, 1600, 1600]` µm |
| Core area | `[365, 365, 1235, 1235]` µm |
| Metal stack | Metal1 to Metal4 plus TopMetal1 (no Metal5, no TopMetal2) |
| Flow | LibreLane Chip flow (`SoC1693-main/flow/librelane/config.yaml`), seal ring from its `KLayout.Sealring` step |
| Clock constraint | 20 ns (`SoC1693-main/flow/librelane/chip_top.sdc`) |
| PDN | core ring and stripes on Metal4 (vertical) and TopMetal1 (horizontal), 15 µm wide ring with 5 µm spacing (`SoC1693-main/flow/librelane/pdn_cfg.tcl`) |
| Fill | metal fill and the chip logo on TopMetal1 added after the LibreLane run |

Macro placement, lower-left corner and orientation:

| Instance | Lower left (µm) | Orientation |
| --- | --- | --- |
| `sram_0` | (435, 495) | N |
| `counter1` | (935, 995) | N |
| `counter2` | (935, 495) | FS |
| `inverter1` | (1080.00, 850.08) | N |
| `inverter2` | (1080.00, 658.14) | N |

The four analog nets between the analog pads and `inverter2` are excluded from resizing and routed with a non-default rule of 0.5 µm width and spacing on Metal1 to Metal4.

The SRAM is characterized by IHP up to 1.32 V. For the three timing corners above 1.32 V (`nom_fast_1p65V_m40C`, `nom_slow_1p35V_125C`, `nom_typ_1p50V_25C`) the flow uses its 1.32 V, -55 °C Liberty file, so the SRAM timing at those corners is an estimate.

## Verification Status

Status of release v.1.0.0. Details and file hashes are in [release/v.1.0.0/ReleaseNote.md](../release/v.1.0.0/ReleaseNote.md) and [release/v.1.0.0/doc/Signoff.md](../release/v.1.0.0/doc/Signoff.md).

| Check | Result | Evidence |
| --- | --- | --- |
| RTL simulation (cocotb, Icarus Verilog) | 6 of 6 tests pass | `SoC1693-main/testbenches/cocotb/results/chip_top_tb_rtl.log` |
| Gate-level simulation (cocotb, post-PnR netlist) | 6 of 6 tests pass | `SoC1693-main/testbenches/cocotb/results/chip_top_tb_gl.log` |
| Mixed-signal transient (Xschem, ngspice with XSPICE digital models, typical corner) | waveforms of counters, digital and analog inverters | `SoC1693-main/testbenches/tran/xschem/plot_simulations/figures/` |
| Static timing, six corners | no setup or hold violation, worst setup slack 2.18 ns, worst hold slack 0.107 ns, 21 max-slew violations at the slow corners | `SoC1693-main/timing/` |
| IR drop, `VDD` at `nom_typ_1p50V_25C` | 1.8 mV worst case (0.12 %), 4.16 mW total power | `SoC1693-main/verification/reports/irdrop.rpt` |
| Antenna | no violation | `SoC1693-main/verification/reports/antenna_summary.rpt` |
| LVS in the LibreLane flow (Netgen, standard cells and macros as black boxes) | circuits match uniquely | `SoC1693-main/verification/lvs/lvs.netgen.rpt` |
| DRC on the release GDS, minimal precheck | no violation | `release/v.1.0.0/doc/Signoff.md` |
| DRC on the release GDS, regular deck with density | 78 recommended pad-rule items in the pad ring and 10 `NW.d` items at decap NWell ties, reviewed as no violation of the layout | `release/v.1.0.0/doc/Signoff.md` |
| Full-chip transistor-level LVS on the release GDS | in progress for the final release | |
| Macro sign-off (`counter_top`, `inverter_top`) | DRC, LVS and Magic PEX per macro, CACE over corners and mismatch for `inverter_top` | `SoC1693-main/macros/*/README.md` |
