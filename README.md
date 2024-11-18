# Assignment 2: Classify
## Part A: Mathematical Functions
### Task 1: ReLU
```assembly
# Function Arguments:
#   a0: Pointer to integer array to be modified
#   a1: Number of elements in array
#
# Returns:
#   None - Original array is modified directly
```

1. Input Validation:
```assembly
li t0, 1             
blt a1, t0, error     
```
* `li t0, 1`: Loads constant 1 into register t0
* `blt a1, t0, error`: If array length (a1) is less than 1, branch to error
* This ensures the input array is not empty

2. Loop Initialization:
```assembly
li t1, 0             # Initialize counter
loop_start:
bge t1, a1, done     # Check if all elements processed
```
* `li t1, 0`: Initialize counter t1 to 0
* `bge t1, a1, done`: Branch to done if counter >= array length

3. Array Element Address Calculation:
```assembly
slli t2, t1, 2       # t2 = t1 * 4 (offset calculation)
add t3, a0, t2       # t3 = base address + offset
lw t4, 0(t3)         # Load current element value
```
* `slli t2, t1, 2`: Left shift by 2 (multiply by 4) for byte offset
* `add t3, a0, t2`: Calculate actual memory address
* `lw t4, 0(t3)`: Load value from calculated address

4. ReLU Operation Implementation:
```assembly
bge t4, zero, skip_relu  # Skip if value >= 0
sw zero, 0(t3)          # Replace negative value with 0
```
* `bge t4, zero, skip_relu`: Branch if value is non-negative
* `sw zero, 0(t3)`: Store 0 back to memory address

5. Loop Control:
```assembly
skip_relu:
addi t1, t1, 1      # Increment counter
j loop_start        # Jump back to loop start
```
* `addi t1, t1, 1`: Increment loop counter
* `j loop_start`: Unconditional jump to loop start

6. Function Exit and Error Handling:
```assembly
done:
    jr ra           # Normal return
error:
    li a0, 36       # Load error code
    j exit          # Jump to program exit
```
* `jr ra`: Return to caller
* `li a0, 36`: Set error code
* `j exit`: Exit program

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
