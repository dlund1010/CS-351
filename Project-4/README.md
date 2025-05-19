# iota

Both programs use an `iota` function to fill a vector with successively increasing values. They were then both tested for various sizes of vectors.

iota.cpp: uses the `std::iota` function, meaning it uses the CPU.

| Vector<br>Length | Wall Clock<br>Time | User Time | System Time |
| :--------------: | -----------------: | --------: | ----------: |
|        10        |               0.00 |      0.00 |        0.00 |
|       100        |               0.00 |      0.00 |        0.00 |
|       1000       |               0.00 |      0.00 |        0.00 |
|      10000       |               0.00 |      0.00 |        0.00 |
|      100000      |               0.00 |      0.00 |        0.00 |
|     1000000      |               0.00 |      0.00 |        0.00 |
|     5000000      |               0.02 |      0.00 |        0.02 |
|    100000000     |               0.55 |      0.08 |        0.47 |
|    500000000     |               2.75 |      0.44 |        2.30 |
|    1000000000    |               5.37 |      0.84 |        4.52 |
|    5000000000    |              36.13 |      6.28 |       29.85 |

iota.cu: uses CUDA to run a custom version of `iota` on the GPU.

| Vector<br>Length | Wall Clock<br>Time | User Time | System Time |
| :--------------: | -----------------: | --------: | ----------: |
|        10        |               0.31 |      0.01 |        0.29 |
|       100        |               0.20 |      0.00 |        0.20 |
|       1000       |               0.21 |      0.01 |        0.20 |
|      10000       |               0.21 |      0.00 |        0.20 |
|      100000      |               0.20 |      0.01 |        0.19 |
|     1000000      |               0.21 |      0.00 |        0.21 |
|     5000000      |               0.24 |      0.01 |        0.22 |
|    100000000     |               0.87 |      0.18 |        0.69 |
|    500000000     |               3.37 |      0.80 |        2.56 |
|    1000000000    |               6.38 |      1.56 |        4.81 |
|    5000000000    |              48.96 |     10.95 |       38.02 |

In general it appears that the version running on the gpu was slightly slower.

Question: Are the results what you expected? Speculate as to why it looks like CUDA isn't a great solution for this problem.

Since this problem only involves simple addition most of the run time is likely reading and writing the data. GPUs are able to make computations faster, but in this case there's not much improvement to be made, and the CPU was better at reading/writing.

# Julia sets

Both programs generate an image of a Julia set, specifically centered at 0,0 meaning they produce the Mandelbrot set.

julia.cpp: A CPU implementation of producing a Julia set.

![Mandelbrot set calculated with CPU](mandelbrot-cpu.ppm)

julia.cu: A GPU implementation of producing a Julia set.

![Mandelbrot set calculated with GPU](mandelbrot-gpu.ppm)

Additional Julia Sets:

![Julia set with starting z 1.0, 0.0](1_0.ppm)

![Julia set with starting z -0.74, 0.148](-0.74_0.148.ppm)

![Julia set with starting z 1.138, 0.6](1.13_0.6.ppm)
