---
title: "xTB_Ch03. Compile stda for Better Output Format"
collection: teaching
type: "Tutorial"
venue: "Online"
date: 2026-05-22
location: "—"
permalink: /teaching/xTB_Ch03
section: "xtb"
---


First upload: 2026-05-22. Last update: 2026-05-22. Email twang91@uw.edu or e0046935@hotmail.com for any questions.

## 1. Compile `stda-1.6.3.3` Again
### 1.1 One Issue in `tda.dat` Format
You may notice that the width of `tda.dat` is not wide enough. The rotatory strength (the 5th column in `tda.dat`) in some large systems could exceed the maximum width and you will see `*************` strings as the output.
### 1.2 Run pacth to edit the source files.
Three source files should be modified in this case: `stda.f`, `apbtrafo.f`, and `printvec.f`. Here is a pacth file and you can save it as `stda_patch.sh`:
```
# ===== 0) 备份（sed -i.bak 也会生成备份；这里可选）=====
cp -p stda.f stda.f.orig
cp -p apbtrafo.f apbtrafo.f.orig
cp -p printvec.f printvec.f.orig

# ===== 1) 修 stda.f：print_tdadat 写 tda.dat 的 DATXY 数据行 =====
# - 在 "write(26,*)'DATXY'" 后插入 9100 FORMAT
# - 把旧的 3 行 write block 换成短行 + continuation（F77 安全）
sed -i.bak \
  -e "/write(26,\\*)'DATXY'/a\\
 9100 format(i6,1x,f12.6,4(1x,ES24.14E3))" \
  -e "/write(26,'(i4,F10\\.4,4f13\\.6)')/,/trstr(4,i)/c\\
         write(26,9100) i,energy(i)*27.21139d0,\\
     . trstr(1,i),trstr(2,i),trstr(3,i),trstr(4,i)" \
  stda.f

# ===== 2) 修 apbtrafo.f：所有写 tda.dat 的地方（4 个子程序都有）=====
# - 每个子程序里都有一行 write(28,*)'DATXY'：在其后插入 9100 FORMAT
# - 把长的内联 format 改成 ,9100) 以避免 >72 列截断
# - 顺便也兼容旧的 (i4,F10.4,4f13.6) 写法（如果你文件里仍存在）
sed -i.bak \
  -e "/write(28,\\*)'DATXY'/a\\
 9100 format(i6,1x,f12.6,4(1x,ES24.14E3))" \
  -e "s/,'(i6,1x,f12\\.6,4(1x,ES24\\.14E3))')/,9100) /g" \
  -e "s/,'(i4,F10\\.4,4f13\\.6)')/,9100) /g" \
  apbtrafo.f

# ===== 3) 修 printvec.f：把 d20.14 加宽，避免数值写成 ****** 或挤压 =====
sed -i.bak "s/(d20\\.14)/(d30.14)/g" printvec.f

```
Then you can compile `stda` using the same proceeder in our last chapter `xtb_Ch02`.

### Test `stda` Binary
Here is the geometry, `butadiene.xyz`:
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
    1     5.986347    9.54146266591439E-001    1.88604912828923E-001    1.44539620406469E-004   -1.04750752620217E-005
    2     6.327069    8.86419604202056E-015    1.29627230794837E-013   -1.84152200298005E-005   -4.57867499954700E-005
    3     6.451911    1.78271325640068E-014    1.84062179634246E-014    2.15987323769713E-008   -4.18013024195079E-006
    4     7.073685    9.27793960376567E-007    1.87641518504645E-001   -2.72827075676680E-005   -1.71705204513116E-006
    5     7.207312    5.12838449394102E-005    9.33133228326614E-002   -2.82583413196406E-006    3.48528296668775E-005
    6     7.431632    1.60555549269562E-005    5.75088217229956E-004    5.52006653412696E-007    3.86238715996136E-006
    7     7.965330    1.10559370050258E-002    8.29316886358549E-004    8.93695851992999E-006    5.35567159504567E-006
```
You can see the width of the output is larger. Scientific notation is used to ensure that large values fit within the specified output width.
