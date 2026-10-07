# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Compiles official STREAM src/stream.c inline and runs build/stream with OMP_NUM_THREADS. num_threads: OpenMP workers. array_size: STREAM_ARRAY_SIZE. num_iterations: NTIMES. Sweep dimensions: numa_node, num_threads, thread_affinity, page_size, dtype, array_size, kernel_types, num_iterations.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| numa_node | `--numa-node` | smoke=0, baseline=0, extended=0 | 0 | From Parameter list; see Execution Description With Parameters. |
| num_threads | `--num-threads` | smoke=4, baseline=16, extended=20 | 16 | From Parameter list; see Execution Description With Parameters. |
| thread_affinity | `--thread-affinity` | smoke=core, baseline=core, extended=core | core | From Parameter list; see Execution Description With Parameters. |
| page_size | `--page-size` | smoke=4KB, baseline=4KB, extended=4KB | 4KB | From Parameter list; see Execution Description With Parameters. |
| dtype | `--dtype` | smoke=FP64, baseline=FP64, extended=FP64 | FP64 | From Parameter list; see Execution Description With Parameters. |
| array_size | `--array-size` | smoke=10000000, baseline=20000000, extended=40000000 | 20000000 | From Parameter list; see Execution Description With Parameters. |
| kernel_types | `--kernel-types` | smoke=copy,scale,add,triad, baseline=copy,scale,add,triad, extended=copy,scale,add,triad | copy,scale,add,triad | From Parameter list; see Execution Description With Parameters. |
| num_iterations | `--num-iterations` | smoke=2, baseline=30800, extended=45600 | 30800 | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Compile src/stream.c with gcc -O3 -fopenmp and run build/stream. Does not invoke numactl
```

## Raw Output Format

STREAM stdout plus one CSV aggregate. A single row is duplicated to two samples

sample_index,status,copy_bandwidth_gb_s,scale_bandwidth_gb_s,add_bandwidth_gb_s,triad_bandwidth_gb_s,error_message
0,ok,180,175,190,185,

## Metrics

- **#1: Best Copy bandwidth, GB/s** — stored as `copy_bandwidth_gb_s`.
- **#2: Best Scale bandwidth, GB/s** — stored as `scale_bandwidth_gb_s`.
- **#3: Best Add bandwidth, GB/s** — stored as `add_bandwidth_gb_s`.
- **#4: Best Triad bandwidth, GB/s** — stored as `triad_bandwidth_gb_s`.

## Framework

Compiles official STREAM src/stream.c inline and runs build/stream with OMP_NUM_THREADS. num_threads: OpenMP workers. array_size: STREAM_ARRAY_SIZE.

## Installation and Execution Summary

Compile official STREAM src/stream.c with gcc -O3 -fopenmp, run build/stream under OMP_NUM_THREADS, parse Copy/Scale/Add/Triad Best Rate MB/s, convert to GB/s, and compute triad/400 efficiency, to measure CPU STREAM bandwidth. numactl is not used

## Platform Portability

- **AMD (primary):** ```bash
Compile src/stream.c with gcc -O3 -fopenmp and run build/stream. Does not invoke numactl
```
- **NVIDIA:** Primary target is AMD ROCm. NVIDIA notes in this section are reference only and are not the execution path.

## Model Context Protocols

- **Active:** None

## Execution-Loop Validation Contract

EXECUTION CHAIN: `run_benchmark.sh` ➔ raw output ➔ `scripts/parse_results.py` ➔ `results/benchmark.db` ➔ `scripts/validate_results.py`

This benchmark uses a lightweight, SQLite-integrated execution loop for result validation. All validation is performed by `scripts/validate_results.py`.

### Validation script usage

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional; select the installed interpreter

# After a live run:
".venv/bin/python" scripts/validate_results.py --db results/benchmark.db

# CI / no-GPU path (seeds fixture and validates it):
".venv/bin/python" scripts/validate_results.py --seed-fixture --quiet

# Override DB path via environment variable:
BENCHMARK_DB=tests/fixtures/benchmark.db \
  ".venv/bin/python" scripts/validate_results.py
```

### Run artifact contract

STREAM stdout plus one CSV aggregate. A single row is duplicated to two samples

sample_index,status,copy_bandwidth_gb_s,scale_bandwidth_gb_s,add_bandwidth_gb_s,triad_bandwidth_gb_s,error_message
0,ok,180,175,190,185,

```bash
bash run_benchmark.sh --help
bash run_benchmark.sh --profile smoke --validate
bash run_benchmark.sh --profile baseline --validate
bash run_benchmark.sh --profile extended --validate
```
`run_benchmark.sh --help` prints usage and exits. The harness calls `scripts/ensure_setup.sh` when `.setup_state` is absent.

### Required integrity checks (built into `validate_results.py`)

1. Latest run exists and `runs.status = 'ok'`.
2. `run.error_message` is NULL.
3. `started_at` and `finished_at` are valid ISO-8601 UTC strings.
4. All required aggregate metrics in `runs` are non-NULL and finite.
5. All required aggregate metrics are physically sensible (positive values). Compiles official STREAM src/stream.c inline and runs build/stream with OMP_NUM_THREADS. num_threads: OpenMP workers. array_size: STREAM_ARRAY_SIZE.
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Compiles official STREAM src/stream.c inline and runs build/stream with OMP_NUM_THREADS. num_threads: OpenMP workers. array_size: STREAM_ARRAY_SIZE.

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
