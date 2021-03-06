### Parser-related format

#### Parser action

```
| 7b | 16b |...| 16b |
```

1-entry 167b action for each tenant.

- 7b parsed total length in byte
- `10*16b` parse action, 
  - [15:13] reserved, 
  - [12:6] offset in bytes, 
  - [5:4] type of container, 
  - [3:1] index of container, 
  - [0] validity


#### Output of parser

```
| 1024b | 7b | 8b |...| 8b | 2b1b8b1b8b |...| 256b | 
```

The format of parser's output is showed above, which is 1735b in total.

- 1024b is used to hold the first at-most 4 32B (256b) AXIS segments
- 7b is the total length in byte of packet header we are interested in
- there are `24*8b`, which are divided into 3 groups, the first `8*8b` are used for 2B containers, where the second and the third are fro 4B, 8B containers, respectively.
  - in each 8b, the first 1b is the validity bit, and 7b indicates the offset in bytes
- there are `n*20b` opcode for comparison at the beginning of each logical MAT stage, where `n` (we can suppose `n=5` now) is the number of logical MAT stages
  - the first `2b` is the comparation opcode, in which `0`, `1` and `2` represent `>`, `>=` and `==`, resprectively
  - in the following `9b`, the first `1b` indicates that whether the operator comes from PHV
    - if it is `0`, then the `8b` is the constant operator
    - if it is `1`, the first `3b` is empty and the following `2b` stands for the type of container (namely, 2B, 4B, 8B), the last `3b` stands for the index of such container
  - the next `9b` is the second operator of the comparison
- the remaining 256b is used to store metadata passing through the pipeline
