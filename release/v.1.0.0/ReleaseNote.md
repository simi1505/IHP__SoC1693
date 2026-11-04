# SoC1693 Release v.1.0.0

Status: **preliminary**, 2026-11-02. This release registers the design for the November 2026 Open-Silicon MPW run. The layout is final unless the open items below require a change. The final release adds the full-chip LVS netlist and its LVS report.

## Contents

| File | Bytes | SHA-256 |
| --- | ---: | --- |
| `gds/SoC1693.gds.gz` | 8,509,931 | `8e1f07842662807d906bc2c431181ae1058d76acae3115675e8b5debac9f1bf3` |
| `netlist/SoC1693.v` | 2,681,789 | `57ffb6b9f8f7cbae2954a849e14ecf8602d3400fbd4ff883b67c230efa6b21b4` |
| `doc/Signoff.md` | | DRC, LVS and simulation results of this release |
| `doc/drc/` | | KLayout result databases of the minimal precheck (0 items) and the regular deck (88 items) |
| `doc/sim/` | | cocotb logs of the RTL and gate-level simulations, 6 of 6 tests pass in each |
| `doc/img/` | | layout renders and bond plan |

## Provenance

- The layout was built on 2026-08-26 by the LibreLane Chip flow (run `RUN_2026-08-26_09-58-20`) from commit `e8df994` of [iic-jku/ihp-sg13cmos5l-ams-chip-template](https://github.com/iic-jku/ihp-sg13cmos5l-ams-chip-template), followed by metal fill and the chip logo. Its file there is `layout/chip_top_logo_fill.gds.gz` (SHA-256 `6910a748aac887f5ae998b919ae0a8231376559316311e8ff05d2dfdd5081de4`), unchanged up to commit `60166ac`. The design sources and the flow reports are in that repository, and `doc/` links each file it cites at commit `60166ac`.
- `gds/SoC1693.gds.gz` is that file with the top cell renamed from `chip_top_logo_fill` to `SoC1693`, written by KLayout 0.30.12 in IIC-OSIC-TOOLS 2026.10 with the save options of the submission guidance: database unit 0.001 µm, no PCell or library context, zero-length paths converted to boundaries, maximum cell name length 32000, maximum 8000 vertices. The source holds no zero-length path, and a hierarchical KLayout `LayoutDiff` finds the geometry of all 273 cells identical to the source.
- `netlist/SoC1693.v` is the powered post-PnR Verilog netlist of the same build (`netlist/pnl/chip_top.pnl.v`, SHA-256 `7aff2479a5735994ef8695e9db5b1123ca21012c4d535d6f94d843d8dfcc5247`) with the top module renamed from `chip_top` to `SoC1693`. In the layout, `SoC1693` holds the cell `chip_top` plus fill, logos and seal ring, none of which adds devices.

## Notes for Review

- Die and seal ring: 1600 x 1600 µm, as in `sealring_x` and `sealring_y` of `doc/info.json`. The seal ring label on layer 63/0 reads "Calculated area: 2.6e-06 sq mm". That text comes from the seal ring PCell of the PDK, the die area is 2.56 mm².
- The GDS holds 19 cell names longer than 32 characters, from the IHP SRAM (`RM_IHPSG13_1P_BITKIT_16x2_*`) and router vias. They are within the maximum cell name length of the guidance.

## Open Items for the Final Release

1. Transistor-level CDL netlist of the whole chip (`netlist/SoC1693.cdl`) and a KLayout LVS run of `gds/SoC1693.gds.gz` against it with the PDK runner. `release.netlist` in `doc/info.json` then points to that file.
2. Tool versions of the build in `doc/info.json`.
