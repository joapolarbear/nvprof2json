# nvprof2json

![nvprof2json in chrome://tracing](screenshot.png)

Converter of nvvp profiler output (SQLite3 databases) to
Google Trace Event Format, for use with about:tracing.
Inspired by Andrew Tulloch and Nicolas Vasilache's gnvprof

Documents:
* https://stackoverflow.com/questions/26145167/how-can-i-obtain-timing-values-from-the-output-of-nvprof-or-of-nvidia-visual-pro
* Trace Event Format https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/preview

Tested with CUDA 8.0.

## How to use

```
nvprof -o foo.nvvp your_program
python3 nvprof2json.py --filename foo.nvvp --filter CUPTI_ACTIVITY_KIND_MEMCPY,CUPTI_ACTIVITY_KIND_CONCURRENT_KERNEL > foo.json
# Open foo.json in chrome://tracing
```
We can use `--filter` to avoid loading all traces, since sometimes CUDA trace file is large, and a 2.5 G `.json` file would be generated for a ~1G `.nvvp` file.
1. `CUPTI_ACTIVITY_KIND_MEMCPY,CUPTI_ACTIVITY_KIND_CONCURRENT_KERNEL` corresponds to `... Overview` pid
2. `all`: keep all traces
3. `CUPTI_ACTIVITY_RUNTIME`, show the traces for each CUDA kernel by line.


## Known bugs

* Times are inflated by x1000, since nvprof records at ns precision,
  while Google Trace Event viewer takes ms precision.  You can modify
  `munge_time` to divide by 1000 but this will cause the viewer to
  render less precisely.

## How to help

* The SQLite database contains a lot of undocumented information which
  we haven't put into the Google Trace Event format, but in principle
  could.  If you have some info you need, look at some of the sample
  database row outputs and see which has the information you're looking
  for.

* This Python script isn't written particularly efficiently.  Could be
  made much faster!

* The JSON output can become quite big.  Does chrome://tracing support
  a binary format?  If so, we should use that.
