# Mem_LatTestPro.py 
# MemLat Pro — Cache & Memory Latency Profiler with Loaded Latency Testing

A Python-based memory subsystem profiler that measures both single-threaded 
cache/DRAM latency curves and multi-core loaded latency degradation. 

The loaded latency test (Option 5) Attempts to  implement the methodology described in 
Chester Lam's ["Pushing AMD's Infinity Fabric to its Limits"](https://chipsandcheese.com/p/pushing-amds-infinity-fabric-to-its) 
(Chips and Cheese, November 2024), but I am not sure if I succeeded. 
Buffer sizes are set as a percentage of cache size  (default 5%) 
to try to make  results more comparable across CPUs with different L3 sizes.

## My example results: 

On a Ryzen 7 7800X3D (Zen 4, 96 MB L3 with V-Cache), loaded latency 
degrades from ~110 ns (unloaded) to ~747 ns (7 bandwidth threads) — a 
6.8x penalty at JEDEC settings. The IFOP communication between CCD and IO die 
saturates at approximately 2 cores of streaming bandwidth (~64 GB/s), 
after which XI queue back up causes compounding latency delays.

Overclocking (DDR5-6000 CL28, 2133 MHz FCLK) improved baseline latency 
but worsened the degradation ratio to 8.7x, confirming that its hard if impossible to tune past the problem.

## Requirements

- Python 3.9+
- numpy
- numba (required for Option 5, strongly recommended for all tests)
- psutil (required for Option 5 core pinning)
- matplotlib (optional, for plot generation)
```
pip install numpy numba psutil matplotlib
```

**RAM requirements for Option 5:** Approximately 10-18 GB depending on 
CPU L3 cache size. The tool checks available memory before starting and 
will reduce buffer sizes if needed.

## Test Options

| Option | Name | What It Measures | Runtime |
|--------|------|-----------------|---------|
| 1 | Quick | Single-thread latency curve, 256 MB max | ~12 min |
| 2 | Full | Single-thread latency curve, 1 GB max | ~25 min |
| 3 | Custom | Configurable sizes and patterns | varies |
| 4 | Compare | Side-by-side diff of two previous runs | instant |
| 5 | **Loaded Latency** | **Multi-core contention stress test** | ~5-8 min |

## Option 5: Loaded Latency

Pins a latency-sensitive pointer chase to one core while progressively 
spawning bandwidth workers (Numba JIT sequential streaming) on other 
cores. Measures how latency degrades under load.

Outputs JSON, PNG plot, and an interactive HTML dashboard with:
- Dual-axis latency/bandwidth chart
- Saturation detection
- Severity assessment
- Generational comparison against reference architectures
- Full methodology explanation

## Output Files

Each run produces:
- `.json` — raw data, machine-readable
- `.html` — interactive dashboard (Chart.js, opens in any browser)
- `.png` — static plot
- `.txt` — console log with raw data tables

## Usage
```
python MemLat_ProExtreme.py          # interactive menu
python MemLat_ProExtreme.py --loaded-latency   # run Option 5 directly
python MemLat_ProExtreme.py --quick  # run Option 1 directly
```

## Contributing

If you run this on different hardware (especially Intel, Zen 5, 
dual-CCD AMD parts), I would love to see the results. 
Copy your text file with a Zen timings Jpeg and become part of the data pool.
MemLatBro@gmail.com 
(Memlatpro at gmail was taken) its MemLat Bro b as in bravo  @gmail
 Ill update this later if this develops enough interest.
