# Engine patches for T. W. Goode's dissertation

Branch `goode-dissertation` is OpenRadioss commit `15b9676` (2026-09-19) plus five engine
patches. It is the source of the OpenRadioss engines used for the impact and
compression-after-impact simulations in:

> Tyler Wayne Goode, *Impact Damage Characterization and Residual Strength of Carbon Fiber
> Laminates Across Velocity Regimes*, PhD dissertation, North Carolina State University
> (in preparation).

The OpenRadioss repository was removed from GitHub in October 2026. This fork of
[OpenCourant](https://github.com/OpenCourant/OpenCourant), which keeps the full OpenRadioss
history, preserves the commit we built on.

## Patches (one commit each, in this order)

| # | File | Change | Active when |
|---|---|---|---|
| 1 | `engine/source/materials/mat/mat117/sigeps117.F90` | LAW117 cohesive law: with `Idel` > 4 on the card, failed integration points are kept (damage saturates at 1: zero tension and shear, full compression stiffness) and the element is never deleted. `suser43.F` hard-codes the deletion threshold to 4 failed points, so the patch works in the material law. | `Idel` > 4 |
| 2 | same | Coulomb friction on failed, closed points. The coefficient is read once from `RAD_COH_MU`. | `RAD_COH_MU` > 0 |
| 3 | `engine/source/elements/thickshell/solidec/schour3_1.F` | Thick-shell (Isolid 15) hourglass energy booked at mid-step, 0.5 (F_old + F_new). The original booking adds 0.5 K (v_hg DT1)^2 per cycle with no matching nodal work. Forces are not changed: kinematics are bit-identical, only the internal energy output changes. | always |
| 4 | `sigeps117.F90` | Phase switch of the interface card: from `RAD_COH_TSW` the law uses `RAD_COH_TN2`, `RAD_COH_TT2` (strengths) and `RAD_COH_GIC2`, `RAD_COH_GIIC2` (toughnesses) and recomputes delta0 = T/K and delta_f = 2G/T (linear shape). Damage stays monotonic. | `RAD_COH_TSW` set |
| 5 | `sigeps117.F90` | Benzeggagh-Kenane exponent in the phase switch: `RAD_COH_ETA2`. | `RAD_COH_TSW` and `RAD_COH_ETA2` > 0 |

Unset variables or a value <= 0 keep the card value. Each patched engine prints a
`LAW117 patch: ...` line in the engine listing when the patch reads its variable.

The patches were applied with the scripts `patch117.py`, `patch117_fric.py`,
`patch_schour3_1_hgenergy.py`, `patch117_sw.py` and `patch117_eta.py` (dissertation code
release). The patched files in these commits are byte-identical to the source files of the
engines built on NC State's Hazel cluster (checked 2026-10-25).

## Tags = built engines

| Tag | Patches | Engine binary (Hazel) | Built |
|---|---|---|---|
| `goode-engine-20260928` | 1-2 | `engine_linux64_gf_ompi` | 2026-09-28 |
| `goode-engine-hgfix-20261001` | 1-3 | `engine_linux64_gf_ompi_hgfix` | 2026-10-01 |
| `goode-engine-hgfix-sw-20261005` | 1-4 | `engine_linux64_gf_ompi_hgfix_sw` | 2026-10-05 |
| `goode-engine-hgfix-sw2-20261014` | 1-5 | `engine_linux64_gf_ompi_hgfix_sw2` | 2026-10-14 |
| `goode-dissertation-v1.0` | 1-5 + this file | (same source as the line above) | |

The starter was not patched.

## Build

- Hazel: gcc 13.2.0, OpenMPI 4.1.6, CMake 3.24.1; in `engine/`:
  `./build_script.sh -arch=linux64_gf -mpi=ompi -mpi-root=$MPIROOT -release -static-link`.
- Hazel-only link change, NOT in this branch: `-l:libnsl.so.1` appended to the five `mpi_lib`
  lines of the `ompi` block in `engine/CMake_Compilers/cmake_linux64_gf.txt`.
- External libraries: `EXTLIB_VERSION.json` names extlib v81, whose download URL no longer
  exists. The author keeps a copy of that package (sha256
  `53a6d0d57a681c3861b8b90f6f07d0cdf1e8b69e6f6ba35094e2c4b3bc1828d8`). OpenCourant archives
  v82 at https://github.com/OpenCourant/extlib/releases; this branch was not built with v82.

## User materials

The ply laws (USER01 Hashin with energy-based softening, USER02 LaRC05) are a separate user
library, built with the OpenRadioss user-library SDK. Their source is part of the dissertation
code release.

## License

GNU AGPL v3, as OpenRadioss. Modified by Tyler W. Goode: `sigeps117.F90` (2026-09-28,
2026-10-05, 2026-10-14) and `schour3_1.F` (2026-10-01). The commit history shows each change.
