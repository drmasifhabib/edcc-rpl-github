# EDCC-RPL (ETX + Delay + Child Count + Residual Energy) — Reproducible Research Code

This repository contains a **reproducible reference implementation** of the EDCC-RPL objective function described in the paper:

> **EDCC-RPL: A Novel Energy-Efficient and Load-Balanced Objective Function for RPL Routing in IoT Networks**  
> (implemented in **Contiki/Cooja** with Sky motes; evaluated on 20–50 nodes)

## What you get
- A drop-in **EDCC objective function** for RPL (C code)
- A lightweight **child-count dissemination mechanism** (parent advertises current child count)
- A lightweight **delay probe** (neighbor RTT sampling + sliding window average)
- An **energy estimator** based on Energest counters (suitable for Cooja)
- Example applications (sink + nodes)
- Headless simulation runner and log parser (Python)

> Note: Contiki has multiple maintained variants (Contiki 2.7, Contiki-NG).  
> This repo targets **Contiki-NG (RPL Classic)** layout by default, and also includes notes for Contiki 2.7 users.

---

## Quick start (Contiki-NG)

### 1) Get Contiki-NG
```bash
git clone https://github.com/contiki-ng/contiki-ng.git
cd contiki-ng
```

### 2) Copy the EDCC-RPL sources
Copy the folder `src/contiki-ng/os/` from this repo into the root of Contiki-NG, **merging** folders:

```bash
rsync -av --ignore-existing ../edcc-rpl-github/src/contiki-ng/os/ ./os/
```

If your Contiki-NG tree differs, adjust paths accordingly.

### 3) Build the example
```bash
cd ../edcc-rpl-github/examples/edcc-rpl
make TARGET=cooja
```

### 4) Run in Cooja
Open Cooja, load the example, or import `cooja/edcc-20nodes.csc` etc.

---

## Configuration

All key parameters are in:
- `os/net/routing/rpl-classic/rpl-of-edcc.h`

Typical defaults:
- weights: `alpha1=0.34` (ETX), `alpha2=0.33` (ChildCount), `alpha3=0.33` (Delay), `alpha4=0.15` (Energy penalty)
- child threshold: `TAU_CC=6`
- delay window: `W=5`
- hysteresis margin: `THETA=0.05`

You can override at compile time:
```bash
make CFLAGS+=" -DEDCC_ALPHA1=0.4 -DEDCC_ALPHA2=0.3 -DEDCC_ALPHA3=0.3"
```

---

## Reproducing paper-style experiments

1) Use the provided `.csc` templates in `cooja/` (20/30/40/50).
2) Run headless batches:
```bash
python3 scripts/run_batch.py --contiki /path/to/contiki-ng --nodes 20 30 40 50 --runs 10 --time 3600
```
3) Parse logs and compute metrics:
```bash
python3 scripts/parse_metrics.py results/*.log --out results/metrics.csv
```

---

## Folder structure
- `src/contiki-ng/` — EDCC-RPL sources intended to be merged into Contiki-NG
- `examples/edcc-rpl/` — sink + node apps
- `scripts/` — automation + parsing
- `cooja/` — Cooja scenario templates

---

## License
MIT
