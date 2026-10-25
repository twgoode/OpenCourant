# Engine patches on OpenCourant main

Branch `goode-patches-main` is OpenCourant `main` at `843b3c4` (2026-10-21) plus the five engine
patches of branch `goode-dissertation`, one commit each, with the OpenCourant modification
notice. Branch `goode-dissertation` (OpenRadioss `15b9676` + the same patches) is the exact
source of the engines used in T. W. Goode's dissertation; its `GOODE_PATCHES.md` describes each
patch and its environment variable.

## Check against the dissertation engine (2026-10-25)

- Both patched files (`sigeps117.F90`, `schour3_1.F`) have the same code as on
  `goode-dissertation`; only the copyright header differs.
- Full-size test deck: 24-ply quasi-isotropic IM7/8552 plate, gas-gun impact at 74 m/s, then the
  start of compression after impact, to 0.5 ms (496,179 nodes, 441,748 bricks). All five patches
  active: `Idel` 99, `RAD_COH_MU` 0.5, Isolid 15 thick shells, `RAD_COH_TSW` 0.15 ms with
  `RAD_COH_GIC2` 0.21 and `RAD_COH_GIIC2` 0.79, `RAD_COH_ETA2` 1.45.
- A = the dissertation engine (`15b9676`, extlib v81); B = this branch (extlib v82 from
  OpenCourant). Same compiler and MPI (gcc 13.2.0, OpenMPI 4.1.6), same user material library,
  64 MPI ranks, on NC State's Hazel cluster.
- Result: the time-history outputs of A and B are byte-identical (1,152 channels, 100 samples,
  7 significant digits in the CSV export); the engine energy prints and the cycle counts
  (17,994 + 41,318) are identical.

## Build

As for `goode-dissertation`, except that `load_extlib.py` downloads OpenCourant's extlib v82.

## License

GNU AGPL v3. Modified by Tyler W. Goode: `sigeps117.F90` and `schour3_1.F`; the commit history
shows each change.
