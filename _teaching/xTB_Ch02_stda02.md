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

```diff
- old line: alpha = 0.1
+ new line: alpha = 0.2
```
