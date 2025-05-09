## Project Summary

This project analyzes 5 hashing programs, using various levels of compiler optimization. Each program has the same input and produces the same output, using different methods to increase speed. The input is available both as `Data.txt`, an ASCII version, and `Data.bin` a binary version containing the same numbers.

Each contain:

- a single number, `numHashes`, indicating how many hash data sets are in the file. (Represented as a `size_t` in `Data.bin`).
- `numHashes` sets of hash data which contain:
  - a single number, `numBytes`, with the number of bytes for its associated hash data set. (Represented as a `size_t` in `Data.bin`).
  - `numBytes` values composing the data to be hashed. Each value is an `unsigned char` in the binary file.

The hash programs each computer 1,000,000 hashes. The programs are:

1. `hash-00` reads the input file Data.txt one value at a time.
2. `hash-01` reads Data.bin. First it reads how many hashes should be computed, then reads the number of bytes in the data set for each hash, allocates an array of `unsigned chars` (using the `new` operator), then reads the data from the file into the array. It then computes the hash, and deletes the allocated data.
3. `hash-02` identical to `hash-0`, but uses the faster `alloca()` function to allocate the arrays. In contrast to `new`, `alloca()` allocates dynamic memory on the stack rather than the heap, however its allocation only exists within a function-call block. Because of this `hash-02` uses a C++ lambda function to provide the function-call environment.
4. `hash-03` similar to `hash-01`, but creates a fixed-size array (allocated on the stack) instead of allocating a dynamic memory for storing input each iteration in the loop.
5. `hash-04` memory maps the binary file into the programs address space, meaning the whole file is directly available in the program as a pointer to an array of bytes.

## Runtime stats

| Program Name | Optimization Level | Real Time | User Time | System Time | Memory Usage | Throughput | Performance Improvement over `hash-00` |
| ------------ | ------------------ | --------- | --------- | ----------- | ------------ | ---------- | -------------------------------------- |
| `hash-00`    | `-O0`              | 364.08s   | 358.35s   | 3.96s       | 2880 KB      | 2746.65    |                                        |
| `hash-01`    | `-O0`              | 17.93s    | 16.57s    | 1.21s       | 3456 KB      | 55772.45   | 20.31                                  |
| `hash-02`    | `-O0`              | 15.86s    | 14.45s    | 1.23s       | 2880 KB      | 63051.70   | 22.96                                  |
| `hash-03`    | `-O0`              | 16.41s    | 15.11s    | 1.12s       | 2880 KB      | 60938.45   | 22.19                                  |
| `hash-04`    | `-O0`              | 14.32s    | 13.76s    | 0.46s       | 5012360 KB   | 69832.40   | 25.42                                  |
| `hash-00`    | `-O1`              | 339.98s   | 333.98s   | 4.27s       | 2884 KB      | 2941.35    |                                        |
| `hash-01`    | `-O1`              | 7.96s     | 6.63s     | 1.17s       | 2880 KB      | 125628.14  | 42.71                                  |
| `hash-02`    | `-O1`              | 7.87s     | 6.62s     | 1.18s       | 3456 KB      | 127064.80  | 43.20                                  |
| `hash-03`    | `-O1`              | 7.95s     | 6.66s     | 1.15s       | 3456 KB      | 125786.16  | 42.76                                  |
| `hash-04`    | `-O1`              | 7.28s     | 6.68s     | 0.43s       | 5012352 KB   | 137362.64  | 46.70                                  |
| `hash-00`    | `-O2`              | 334.07s   | 327.79s   | 4.10s       | 2880 KB      | 2993.38    |                                        |
| `hash-01`    | `-O2`              | 8.32s     | 6.92s     | 1.18s       | 2880 KB      | 120192.31  | 40.15                                  |
| `hash-02`    | `-O2`              | 8.33s     | 6.96s     | 1.20s       | 2880 KB      | 120048.02  | 40.10                                  |
| `hash-03`    | `-O2`              | 8.11s     | 6.81s     | 1.13s       | 2880 KB      | 123304.56  | 41.19                                  |
| `hash-04`    | `-O2`              | 7.05s     | 6.58s     | 0.40s       | 5011771 KB   | 141843.97  | 47.39                                  |
| `hash-00`    | `-O3`              | 337.72s   | 331.50s   | 4.41s       | 2880 KB      | 2961.03    |                                        |
| `hash-01`    | `-O3`              | 16.02s    | 7.78s     | 2.94s       | 2880 KB      | 62421.973  | 21.08                                  |
| `hash-02`    | `-O3`              | 8.10s     | 6.82s     | 1.18s       | 2884 KB      | 123456.79  | 41.69                                  |
| `hash-03`    | `-O3`              | 8.12s     | 6.83s     | 1.18s       | 3456 KB      | 123152.71  | 41.59                                  |
| `hash-04`    | `-O3`              | 7.18s     | 6.68s     | 0.40s       | 5012352 KB   | 139275.77  | 47.04                                  |

## Analysis Questions

1. What operation do you think accounts for most of `hash-00`'s runtime?

   I think it's likely that reading the data is the operation that takes longest for `hash-00`, since it's using the stream extraction operator (`>>`), and reading from the `Data.txt` file rather than `Data.bin`. The reason the programs reading from `Data.bin` are so much faster is that they use the `read` operator. `>>` has logic to worry about datatypes or formatting while `read` considers all data to just be a collection of bytes and type conversion can be done afterwards.

2. `hash-01` and `hash-02` both dynamically allocate memory for each hash computation. Is there much difference time-wise between their two allocation methods?

   With no optimization hash-02 appears slightly faster than `hash-01` (by 2.07s). With `-O1` `hash-02` is only 0.11s faster. With `-02`

3. `hash-03` avoids the allocation by using a fixed-size array. Is there an appreciable speed difference?

   In general while `hash-03` is significantly faster than `hash-00` its speed appears similar to `hash-02` and `hash-01`. The largest difference is with `-O2` optimization, where it ran in 8.11s in comparison to `hash-01` which ran in 8.32s and `hash-02` which ran in 8.32s. It seems there's not really an appreciable speed difference.

4. Why is `hash-04`'s memory usage so much larger than any of the other versions? Hint: recall when we discussed how the operating system reads a file and makes it available to an application. Specifically, the O/S will transfer data from disk to its own memory, and then copy from there into buffers provided by the application. In the memory map case, the O/S is merely sharing the copy of the file's data that is in its (the O/S's) memory, and not making an additional copy from its memory into memory only in the application

   Since `hash-04` is using a memory map, it has to allocate room for the whole binary file. The size of `Data.bin` is about 5009162 KB. On average across the 4 different optimization levels, `hash-04` used 5012208 KB, so subtracting the size of `Data.bin` would mean each run allocated about 3046 KB, which is just slightly more than what the other programs allocated.

5. What other compiler options did you try, and did they help at all?

   I tried `-O1`, and `-O3`. Using `-O1` resulted in the fastest (real time) performance for `hash-00`, `hash-01`, `hash-02`, and `hash-03`, but not `hash-04`. Using `-O3` was generally faster than results with `-O0` but slower than `-O1` for all but `hash-04`.
