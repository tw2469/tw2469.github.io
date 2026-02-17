---
title: "xTB_Ch01. sTDA: a Quick Run"
collection: teaching
type: "Tutorial"
venue: "Online"
date: 2026-02-16
location: "—"
permalink: /teaching/xTB_Ch01
section: "xtb"
---


First upload: 2026-02-16. Last update: 2026-02-16. Email twang91@uw.edu or e0046935@hotmail.com for any questions.

This tutorial is introducing the usage of `stda` for the excited state calculations (using xTB references as wfn.xtb).

## 1. Introduction of sTDA
The `stda` program is designed to calculate the excited states for large systems by the simplified time-dependent density functional theory (sTD-DFT) and the simplified Tamm-Dancoff approximation. The program is developed by Grimme's Group. You can find the release at https://github.com/grimme-lab/std2/releases/tag/v1.6.3. Our following tutorial is also based on stda version 1.6.3, released at Oct 10, 2022.

The improved program is called `std2`, we will cover `std2` in later chapters.
>The std2 program is the rebranded and updated version of the stda program. Originally, stda was implemented only for the simplified time-dependent density functional theory using the Tamm-Dancoff approximation (sTDA) method. With the implementation of more simplified quantum chemistry (sQC) methods in stda, the name was not fitting the application of the program anymore.


## 2. Run a calculations
* Generally, the calculation need two steps. Step 1 is run another binary called `xtb4stda` to generate the `wfn.xtb` files for the calculations. Steps 2 is run `stda` (reading the generated `wfn.xtb`).
* Binary file of `stda` is available directly from the release of v1.6.3. We will compile from the source code in the next chapter. Here we will directly use the released binary.

### 2.1. Step 1: Prepare a structure
Let's use 1,3-Butadiene as an example. Save the following as butadiene.xyz
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
### 2.2. Step2: Install and Run `xtb4stda` to Get wfn.xtb
Clone the `xtb4stda` repository.
```
git clone https://github.com/grimme-lab/xtb4stda.git
```
Then `ls -a` the directory as `ls -a xtb4stda`. Make sure you see the following parameter files: `.param_stda1.xtb  .param_stda2.xtb`.

Make a directory storing all bin files for this tutorial. Then download bin files.
```
mkdir tutorialBin
cd tutorialBin
wget https://github.com/grimme-lab/xtb4stda/releases/download/v1.0/xtb4stda
chmod +x xtb4stda
cd ..
```
In your current directory, you should see: two directories of `tutorialBin` and `xtb4stda`, one structure file `butadiene.xyz`. Then set up environment variables for `xtb4stda`. You can add them to your `.bashrc` file or to your SLURM/PBS script.
```
# Change the path to the path of your cloned xtb4stda repository.
export XTB4STDAHOME=$HOME/website/xtb_ex01/xtb4stda
# Change the path to the path of the directory you store xtb4stda bin files.
export PATH=$PATH:$HOME/website/xtb_ex01/tutorialBin
```
At the directory where you store your `butadiene.xyz` file, run `xtb4stda` as:
```
xtb4stda butadiene.xyz
```
A successful run will generate a `wfn.xtb` file and you will also see the following at the end of the standard output like this:
```
writing mo output ...
molden style : F

speedup 50.44
cpu  time for all   27.03 s
wall time for all    0.54 s
```
### 2.3. Step 3. Install and Run `stda`
Download `stda` as:
```
cd tutorialBin
wget https://github.com/grimme-lab/std2/releases/download/v1.6.3/stda_v1.6.3
chmod +x stda_v1.6.3
cd ..
```
Then run the calculation. Please read the manual for details of the options.
```
stda_v1.6.3 -xtb -e 8.0
```
* `-xtb`. Invoke sTDA-xTB calculation scheme. In this case, the `xtb4stda` binary
wavefunction file (`wfn.xtb`) is read.
* `-e`. This specifies the energy threshold (**in eV**) for configurations to be considered in the sTDA calculation. States with energies less than this threshold will be included.The default value is 7 eV.

successful run will generate a `tda.dat` file and you will also see the following at the end of the standard output like this:
```
sTDA done.
2026-2-16 1:3:37.282
```
You will see the excited state information in the standard output. Each column stands for: number of states, energy in eV, energy in nm, oscillator strength, rotatory strength, and contribution of MO pairs:
```
excitation energies, transition moments and TDA amplitudes
state    eV      nm       fL        Rv(corr)
   1    5.986   207.1     0.9541     0.0000     0.97(   9->  10) -0.17(   8->  12)  0.13(   9->  15)
   2    6.327   196.0     0.0000    -0.0001     0.99(   9->  11) -0.14(   8->  13) -0.06(   8->  14)
   3    6.452   192.2     0.0000    -0.0000     0.73(   8->  10) -0.68(   9->  12) -0.02(   8->  15)
   4    7.074   175.3     0.0000    -0.0002     0.94(   9->  13) -0.25(   8->  11) -0.22(   9->  14)
   5    7.207   172.0     0.0001    -0.0000     0.96(   9->  14) -0.19(   8->  11)  0.17(   9->  13)
   6    7.432   166.8     0.0000    -0.0000    -0.99(   7->  10)  0.12(   6->  12)  0.04(   3->  10)
   7    7.965   155.7     0.0111    -0.0000     0.97(   9->  15)  0.14(   8->  18)  0.13(   8->  12)
```
These information is also printed to the tda.dat:
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
  1    5.9864     0.954149     0.188605     0.000100     0.000017
  2    6.3271     0.000000     0.000000    -0.000051    -0.000087
  3    6.4519     0.000000     0.000000     0.000001    -0.000012
  4    7.0737     0.000001     0.187640    -0.000008    -0.000172
  5    7.2073     0.000051     0.093315    -0.000009    -0.000006
  6    7.4316     0.000016     0.000575    -0.000000    -0.000000
  7    7.9653     0.011056     0.000829    -0.000011    -0.000009
```

## 3. Plot the spectrum.
### 3.1 Use SpecDis
You can use **SpecDis** to read `tda.dat` and plot the spectrum: https://specdis-software.jimdofree.com/.
>SpecDis is a handy tool to process the results of quantum-chemical ECD/UV, ORD, or VCD/IR calculations. It supports diverse software packages like ORCA, Gaussian03, Gaussian09, Gaussian16, Turbomole (escf, ricc2 tested only up to version 6.7),  NWChem, ADF 2014, DALTON, or Grimme´s sTDA software (simplified TDDFT).

Just run SpecDis and open the tda.dat file. You will see the absorption and ECD spectra:
![](/images/teaching/xtb_Ch01/xTB_Ch01_001.png)

### 3.2 Python Script
This method is bit complicated. It worth the time if you have large number of output files. The script rely on a python module `lrspectrum`: https://github.com/awild82/lrspectrum/. It only reads the Gaussian output format. So we need to convert `tda.dat` to Gaussian log file using the script `stda2gaulog.sh` as below:

``` shell
#!/usr/bin/env bash
# Usage:
#   ./dat2gaulog.sh 08L.dat > formatted.txt
#   cat 08L.dat | ./dat2gaulog.sh > formatted.txt

echo " Entering Gaussian System, Link 0=g16"

in="${1:-/dev/stdin}"

awk -v start=14 '
BEGIN{
  HC = 1239.841984;   # eV*nm
}
NR < start { next }
NF < 2 { next }       # Skip empty lines or line without enough columns.

{
  st = $1 + 0;
  E  = $2 + 0.0;
  #f  = $(NF) + 0.0;  # Last column is rotatry strength, use this line if you want to plot ECD.
  f  = $3 + 0.0;      # The 3rd column is oscillator strength, use this line for absorption.

  nm = (E > 0 ? HC / E : 0);

  printf(" Excited State %3d:      Singlet-A      %8.4f eV  %7.2f nm  f=%-9.4g  <S**2>=0.000\n",
         st, E, nm, f);
}
' "$in"
```

Now run: `./stda2gaulog.sh tda.dat > tda.log`. Your `tda.log` should look like:
```
Entering Gaussian System, Link 0=g16
Excited State   1:      Singlet-A        5.9864 eV   207.11 nm  f=0.9541     <S**2>=0.000
Excited State   2:      Singlet-A        6.3271 eV   195.96 nm  f=0          <S**2>=0.000
Excited State   3:      Singlet-A        6.4519 eV   192.17 nm  f=0          <S**2>=0.000
Excited State   4:      Singlet-A        7.0737 eV   175.27 nm  f=1e-06      <S**2>=0.000
Excited State   5:      Singlet-A        7.2073 eV   172.03 nm  f=5.1e-05    <S**2>=0.000
Excited State   6:      Singlet-A        7.4316 eV   166.83 nm  f=1.6e-05    <S**2>=0.000
Excited State   7:      Singlet-A        7.9653 eV   155.66 nm  f=0.01106    <S**2>=0.
```

Then, use the following python script `plot_lr.py`:

``` Python
import os
import sys
import numpy as np
import lrspectrum
import matplotlib.pyplot as plt

# -------- settings --------
wlim = (3, 8)      # eV (still used for generating spectrum)
broad = 0.1
meth = "gaussian"
res = 5000
xshift = 0.0       # shift in eV (applied before nm conversion)
xunit = "eV"       # "eV" or "nm"
# --------------------------

# Accept N log files: python script.py a.log b.log c.log ...
logfiles = sys.argv[1:] if len(sys.argv) > 1 else ["haha.log"]

# Basic check
missing = [f for f in logfiles if not os.path.isfile(f)]
if missing:
    raise FileNotFoundError("These files do not exist:\n" + "\n".join(missing))

plt.figure()
ax = plt.gca()

# Options: viridis, turbo, tab10, plasma
colors = plt.cm.turbo(np.linspace(0, 1, len(logfiles)))

for i, logfile in enumerate(logfiles):
    color = colors[i]

    lr = lrspectrum.LRSpectrum(logfile)
    lr.gen_spect(broad=broad, meth=meth, wlim=wlim, res=res)

    base = os.path.splitext(os.path.basename(logfile))[0]

    # Always save eV grid (original behavior)
    np.savetxt(f"{base}X_eV.dat", lr.freq)
    np.savetxt(f"{base}Y.dat", lr.spect)

    if xunit.lower() == "ev":
        # Original plotting
        lr.plot(sticks=False, lw=2, color=color, xshift=xshift, label=base)
        lr.plot(do_spect=False, lw=1.5, color=color, ls="solid", xshift=xshift)

    elif xunit.lower() == "nm":
        # ---- spectrum line in nm ----
        E = np.asarray(lr.freq, dtype=float) + float(xshift)   # eV
        S = np.asarray(lr.spect, dtype=float)

        mask = E > 0.0   # avoid division by zero / negative
        E = E[mask]
        S = S[mask]

        wl = 1240.0 / E  # nm

        # Make wavelength increasing left->right
        order = np.argsort(wl)
        wl = wl[order]
        S = S[order]

        #np.savetxt(f"{base}X_nm.dat", wl)  # extra output in nm, has bug: the order is reversed.

        ax.plot(wl, S, lw=2, color=color, label=base)

        # ---- sticks: let lrspectrum plot in eV, then convert the NEWLY added lines' xdata to nm ----
        n_before = len(ax.lines)
        lr.plot(do_spect=False, lw=1.5, color=color, ls="solid", xshift=xshift)

        new_lines = ax.lines[n_before:]  # only the sticks just added
        for ln in new_lines:
            x = np.asarray(ln.get_xdata(), dtype=float)
            # x is in eV; convert to nm; protect nonpositive
            x_nm = np.where(x > 0.0, 1240.0 / x, np.nan)
            ln.set_xdata(x_nm)

        # Set nm x-limits consistent with wlim (best effort)
        e_lo, e_hi = wlim
        nm_lo = 1240.0 / e_hi if e_hi > 0 else np.nanmin(wl)
        if e_lo > 0:
            nm_hi = 1240.0 / e_lo
        else:
            nm_hi = np.nanmax(wl)
        ax.set_xlim(nm_lo, nm_hi)

    else:
        raise ValueError('xunit must be "eV" or "nm"')

# Labels
if xunit.lower() == "nm":
    ax.set_xlabel("Wavelength (nm)")
else:
    ax.set_xlabel("Energy (eV)")

ax.set_ylabel("Intensity (a.u.)")

plt.legend()
plt.show()
```

Change `wlim`, `broad`, `xshift`, and `xunit` accordingly. Then run: `python plot_lr.py tda.log`. You will see the spectrum like this:
![](/images/teaching/xtb_Ch01/xTB_Ch01_002.png)
