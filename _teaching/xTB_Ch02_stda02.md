---
title: "xTB_Ch02. Compile stda"
collection: teaching
type: "Tutorial"
venue: "Online"
date: 2026-02-16
location: "—"
permalink: /teaching/xTB_Ch02
section: "xtb"
---

First upload: 2026-05-20. Last update: 2026-05-20. Email twang91@uw.edu or e0046935@hotmail.com for any questions.

## 1. Compile `stda-1.6.3.3`
### 1.1 Get the Source

```
wget -O std2-v1.6.3.3.tar.gz https://github.com/grimme-lab/std2/archive/refs/tags/v1.6.3.3.tar.gz
tar -xzf std2-v1.6.3.3.tar.gz
cd std2-1.6.3.3
```

### 1.2 Edit `Makefile`
Revise **Line 15 and 16** as:
```diff
-     LINKER = ifort -static -qopenmp  -I$(MKLROOT)/include/intel64/lp64 -I$(MKLROOT)/include
-     LIBS   = $(MKLROOT)/lib/intel64/libmkl_blas95_lp64.a $(MKLROOT)/lib/intel64/libmkl_lapack95_lp64.a -Wl,--start-group $(MKLROOT)/lib/intel64/libmkl_intel_lp64.a $(MKLROOT)/lib/intel64/libmkl_core.a $(MKLROOT)/lib/intel64/libmkl_intel_thread.a -Wl,--end-group -lpthread -lm
+    LINKER = ifort  -qopenmp  -mkl
+    LIBS   = -lpthread -lm -ldl
```

### 1.3 Load an Intel Compiler and Compile
Here on UW supercomputer Hyak-Klone, we load the module as: `module load intel/oneAPI/2021.1.1`.

Then we compile `stda` by running `make`.

### 1.4 Test the Compiled `stda` Binary
Use the example in `xTB_Ch01`. This is the geometry, `butadiene.xyz`:
```
10
Butadiene
C                 -3.09206617    0.96720752    0.00000000
H                 -2.55833369    0.03982969    0.00000000
H                 -4.16206517    0.96867056   -0.00000000
C                 -2.41286206    2.13991755    0.00000000
H                 -2.94659453    3.06729539    0.00000000
C                 -0.87286350    2.13781187   -0.00000000
H                 -0.33913102    1.21043404    0.00000000
C                 -0.19365939    3.31052190    0.00000000
H                 -0.72739186    4.23789974   -0.00000000
H                  0.87633961    3.30905887    0.00000000
```
Run `xtb4stda butadiene.xyz` and then run `stda -xtb -e 8.0`. Here is the result in `tda.dat` we have:
```
NM
VELO
MMASS
  54.0914000000000
LFAKTOR
 0.5
RFAKTOR
 1.0
WIDTH
 0.20
SHIFT
 0.00
DATXY
  1    5.9863     0.954146     0.188605     0.000145    -0.000010
  2    6.3271     0.000000     0.000000    -0.000018    -0.000046
  3    6.4519     0.000000     0.000000     0.000000    -0.000004
  4    7.0737     0.000001     0.187642    -0.000027    -0.000002
  5    7.2073     0.000051     0.093313    -0.000003     0.000035
  6    7.4316     0.000016     0.000575     0.000001     0.000004
  7    7.9653     0.011056     0.000829     0.000009     0.000005
```
If you see the same results, you compilation is likely to be successful.
