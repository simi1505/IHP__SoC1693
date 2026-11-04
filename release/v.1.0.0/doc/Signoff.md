# SoC1693 v.1.0.0 Sign-off

Verification of `release/v.1.0.0/gds/SoC1693.gds.gz` (SHA-256 `8e1f07842662807d906bc2c431181ae1058d76acae3115675e8b5debac9f1bf3`), run on 2026-11-02.

| Item | Value |
| --- | --- |
| Container | IIC-OSIC-TOOLS 2026.10 |
| PDK | `ihp-sg13cmos5l` at IHP-Open-PDK `a49358cc0b20635fcb53c0940505e3563763fc7a` |
| KLayout | 0.30.12 |
| Top cell | `SoC1693`, the only top cell |
| Design size | 1600 x 1600 µm, 11,907,897 polygons |

## DRC

Both runs use the PDK runner `$PDK_ROOT/$PDK/libs.tech/klayout/tech/drc/run_drc.py` with the options of the submission guidance. `--mp` sets only how many parts the deck is split into for parallel execution and was lowered from 32 to 8 to fit the memory of the machine. `--density_thr` was set to its 14 cores.

### Minimal Precheck: Clean

```sh
python3 $PDK_ROOT/$PDK/libs.tech/klayout/tech/drc/run_drc.py --precheck_drc --mp=8 --no_offgrid --density_thr=14 --no_angle --disable_extra_rules --no_recommended --path=SoC1693.gds.gz
```

0 items, "KLayout DRC Check Passed: No DRC violations detected in the layout.", 158 s. Result database: [drc/SoC1693_minimum_drc.lyrdb](drc/SoC1693_minimum_drc.lyrdb).

### Regular Deck: 88 Items, None a Violation of the Layout

```sh
python3 $PDK_ROOT/$PDK/libs.tech/klayout/tech/drc/run_drc.py --mp=8 --density_thr=14 --path=SoC1693.gds.gz
```

1175 s, density included. Result database: [drc/SoC1693_regular_drc.lyrdb](drc/SoC1693_regular_drc.lyrdb).

| Rule | Items | Kind | Where | Review |
| --- | ---: | --- | --- | --- |
| `Pad.fR_TM1` (TopMetal1 exit length 7.0 µm) | 38 | recommended | pad ring, all 38 outside the core | The 70 x 70 µm bondpads abut the `sg13cmos5l_io` cells, and the TopMetal1 that leaves each pad opening into the IO cell is shorter than 7.0 µm. Recommended rules are not part of the minimal precheck. |
| `Pad.d1R` (pad to Activ 11.20 µm) | 40 | recommended | pad ring, all 40 outside the core | Activ of the `sg13cmos5l_io` cells lies closer than 11.2 µm to the pad openings. Recommended rule, as above. |
| `NW.d` (NWell space to external N+ Activ 0.31 µm) | 10 | mandatory | core, about 10 µm off the corners of `sram_0`, `counter1`, `counter2` and `inverter1` | See below. |

All 10 `NW.d` items have the same geometry. Where the standard-cell rows step around the placement halo of a macro, the merged NWell of `sg13cmos5l_decap_4`, `sg13cmos5l_decap_8` and `sg13cmos5l_fill_*` cells forms a concave corner. The flagged N+ Activ, 1.859 µm² in every case, is the NWell tie of a decap cell and lies completely inside that NWell, 0.24 µm from the corner. The rule targets N+ Activ outside the NWell. A separation check of the NWell against N+ Activ outside the NWell finds no pair below 0.31 µm within 3 µm of any of the 10 markers. Marker centers in µm: (425.14, 484.14), (861.74, 484.14), (925.30, 484.14), (1144.94, 484.14), (1070.26, 839.47), (1143.98, 839.47), (1070.26, 944.69), (1143.98, 944.69), (925.30, 983.11), (1144.94, 983.11).

The waiver status of these items is our review, not a foundry decision.

## LVS

| Check | Result |
| --- | --- |
| Netgen LVS in the LibreLane flow, standard cells, IO cells and macros as black boxes | "Circuits match uniquely" ([`verification/reports/lvs.netgen.rpt`](https://github.com/iic-jku/ihp-sg13cmos5l-ams-chip-template/blob/60166ac618be6cc312253b7fed33f8478d044a55/verification/reports/lvs.netgen.rpt)) |
| KLayout LVS of the release GDS against a transistor-level CDL of the whole chip | in progress for the final release |

## Simulation

| Check | Result |
| --- | --- |
| cocotb, RTL, Icarus Verilog | 6 of 6 tests pass ([`chip_top_tb_rtl.log`](sim/chip_top_tb_rtl.log)) |
| cocotb, post-PnR gate-level netlist | 6 of 6 tests pass ([`chip_top_tb_gl.log`](sim/chip_top_tb_gl.log)) |
| Xschem and ngspice transient with XSPICE digital models, typical corner | [`testbenches/xschem/plot_simulations/figures/`](https://github.com/iic-jku/ihp-sg13cmos5l-ams-chip-template/tree/60166ac618be6cc312253b7fed33f8478d044a55/testbenches/xschem/plot_simulations/figures) |
