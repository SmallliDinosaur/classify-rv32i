# Assignment 2: Classify
## Part A: Mathematical Functions
### Task 1: ReLU
```assembly
# Function Arguments:
#   a0: Pointer to an integer memory location to be converted
#
# Returns:
#   None - Modifies the value at the pointer address directly
```

1. Function Entry and Value Loading:
```assembly
ebreak                # Breakpoint for debugging
lw t0, 0(a0)         # Load value from memory address
```
* `ebreak`: Sets a breakpoint for debugging purposes
* `lw t0, 0(a0)`: Loads the integer from the memory address stored in a0 into register t0

2. Sign Check:
```assembly
bge t0, zero, done   # If number >= 0, skip negation
```
* `bge t0, zero, done`: Branch to done if value is non-negative
* This implements the conditional logic for absolute value calculation

3. Negation Operation (for negative values):
```assembly
sub t0, zero, t0     # t0 = 0 - t0 (negate t0)
```
* `sub t0, zero, t0`: Subtracts t0 from zero to get its absolute value
* This effectively negates the negative number, making it positive

4. Store Result:
```assembly
sw t0, 0(a0)         # Store result back to memory
```
* `sw t0, 0(a0)`: Stores the converted value back to the original memory location
* This modifies the value in-place through the pointer

5. Function Return:
```assembly
done:
    jr ra            # Return to caller
```


### Task 2: ArgMax

### Task 3.1: Dot Product



### Task 3.2: Matrix Multiplication

## Part B: File Operations and Main

### Task 1: Read Matrix

### Task 2: Write Matrix


### Task 3: Classification


## Result
after bash test.sh all
```
test_abs_minus_one (__main__.TestAbs) ... ok
test_abs_one (__main__.TestAbs) ... ok
test_abs_zero (__main__.TestAbs) ... ok
test_argmax_invalid_n (__main__.TestArgmax) ... ok
test_argmax_length_1 (__main__.TestArgmax) ... ok
test_argmax_standard (__main__.TestArgmax) ... ok
test_chain_1 (__main__.TestChain) ... ok
test_classify_1_silent (__main__.TestClassify) ... ok
test_classify_2_print (__main__.TestClassify) ... ok
test_classify_3_print (__main__.TestClassify) ... ok
test_classify_fail_malloc (__main__.TestClassify) ... ok
test_classify_not_enough_args (__main__.TestClassify) ... ok
test_dot_length_1 (__main__.TestDot) ... ok
test_dot_length_error (__main__.TestDot) ... ok
test_dot_length_error2 (__main__.TestDot) ... ok
test_dot_standard (__main__.TestDot) ... ok
test_dot_stride (__main__.TestDot) ... ok
test_dot_stride_error1 (__main__.TestDot) ... ok
test_dot_stride_error2 (__main__.TestDot) ... ok
test_matmul_incorrect_check (__main__.TestMatmul) ... ok
test_matmul_length_1 (__main__.TestMatmul) ... ok
test_matmul_negative_dim_m0_x (__main__.TestMatmul) ... ok
test_matmul_negative_dim_m0_y (__main__.TestMatmul) ... ok
test_matmul_negative_dim_m1_x (__main__.TestMatmul) ... ok
test_matmul_negative_dim_m1_y (__main__.TestMatmul) ... ok
test_matmul_nonsquare_1 (__main__.TestMatmul) ... ok
test_matmul_nonsquare_2 (__main__.TestMatmul) ... ok
test_matmul_nonsquare_outer_dims (__main__.TestMatmul) ... ok
test_matmul_square (__main__.TestMatmul) ... ok
test_matmul_unmatched_dims (__main__.TestMatmul) ... ok
test_matmul_zero_dim_m0 (__main__.TestMatmul) ... ok
test_matmul_zero_dim_m1 (__main__.TestMatmul) ... ok
test_read_1 (__main__.TestReadMatrix) ... ok
test_read_2 (__main__.TestReadMatrix) ... ok
test_read_3 (__main__.TestReadMatrix) ... ok
test_read_fail_fclose (__main__.TestReadMatrix) ... ok
test_read_fail_fopen (__main__.TestReadMatrix) ... ok
test_read_fail_fread (__main__.TestReadMatrix) ... ok
test_read_fail_malloc (__main__.TestReadMatrix) ... ok
test_relu_invalid_n (__main__.TestRelu) ... ok
test_relu_length_1 (__main__.TestRelu) ... ok
test_relu_standard (__main__.TestRelu) ... ok
test_write_1 (__main__.TestWriteMatrix) ... ok
test_write_fail_fclose (__main__.TestWriteMatrix) ... ok
test_write_fail_fopen (__main__.TestWriteMatrix) ... ok
test_write_fail_fwrite (__main__.TestWriteMatrix) ... ok

----------------------------------------------------------------------
Ran 46 tests in 112.269s

OK
```
## Reference
1. [cs61c Lab 3: RISC-V, Venus](https://cs61c.org/fa24/labs/lab03/#venus-m)
2. [sysprog21/classify-rv32i](https://github.com/sysprog21/classify-rv32i)
3. [Computer Architecture](https://wiki.csie.ncku.edu.tw/arch/schedule)
