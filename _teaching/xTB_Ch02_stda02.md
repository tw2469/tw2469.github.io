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
