# Results: 307 STREAM - CPU (Copy/Scale/Add/Triad)

**Main goal:** Measure sustained CPU STREAM bandwidth  
**Tools:** Compile src/stream.c with gcc -O3 -fopenmp and run build/stream

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_146 · recorded 2026-11-18). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× AMD Instinct MI300X VF (196288 MB) · INTEL(R) XEON(R) PLATINUM 8568Y+ · Ubuntu 26.04.1 LTS · kernel 7.0.0-38-generic · driver 7.1.9.31600000
- **Run:** 2026-11-18T10:52:25Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 00:05 · benchmark 00:03 · whole run 00:14
- **Source:** run 307_20261118_105225
- **Command:** `bash run_benchmark.sh --smoke --numa-node 0 --num-threads 4 --thread-affinity core --array-size 10000000 --num-iterations 368`

| # | Metric | Result |
|---|---|---|
| 1 | Best Copy bandwidth, GB/s (copy_bandwidth_gb_s) | 100.6366 |
| 2 | Best Scale bandwidth, GB/s (scale_bandwidth_gb_s) | 80.088 |
| 3 | Best Add bandwidth, GB/s (add_bandwidth_gb_s) | 118.7203 |
| 4 | Best Triad bandwidth, GB/s (triad_bandwidth_gb_s) | 123.4351 |
<!-- /profile:smoke -->
