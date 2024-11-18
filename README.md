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

```assembly
# Function Arguments:
#   a0: Pointer to the integer array
#   a1: Number of elements in array
#
# Returns:
#   a0: Index of the first maximum element (0-based)
```

1. Input Validation:
```assembly
li t6, 1
blt a1, t6, handle_error
```
* `li t6, 1`: Loads constant 1 into register t6
* `blt a1, t6, handle_error`: If array length < 1, branch to error handler
* Ensures array has at least one element

2. Initialization:
```assembly
lw t0, 0(a0)        # Load first element as initial maximum
li t1, 0            # Initialize max index to 0
li t2, 1            # Initialize current index to 1
```
* `lw t0, 0(a0)`: Loads first element as current maximum
* `li t1, 0`: Sets initial maximum index to 0
* `li t2, 1`: Sets loop counter starting from second element

3. Loop Setup and Address Calculation:
```assembly
loop_start:
bge t2, a1, loop_end    # Check if done
slli t3, t2, 2          # Calculate byte offset
add t4, a0, t3          # Calculate element address
lw t5, 0(t4)            # Load current element
```
* `bge t2, a1, loop_end`: Exit if all elements processed
* `slli t3, t2, 2`: Multiply index by 4 for byte offset
* `add t4, a0, t3`: Calculate memory address
* `lw t5, 0(t4)`: Load current element value

4. Maximum Value Comparison:
```assembly
ble t5, t0, loop_continue  # Skip if not greater
mv t0, t5                  # Update maximum value
mv t1, t2                  # Update maximum index
```
* `ble t5, t0, loop_continue`: Skip update if current ≤ maximum
* `mv t0, t5`: Update maximum value if greater
* `mv t1, t2`: Update index of maximum

5. Loop Control:
```assembly
loop_continue:
addi t2, t2, 1     # Increment counter
j loop_start       # Continue loop
```
* `addi t2, t2, 1`: Increment loop counter
* `j loop_start`: Jump back to loop start

6. Function Return and Error Handling:
```assembly
loop_end:
    mv a0, t1          # Set return value
    jr ra              # Return to caller
handle_error:
    li a0, 36          # Set error code
    j exit             # Exit program
```

### Task 3.1: Dot Product

```assembly
# Function Arguments:
#   a0: Pointer to first array
#   a1: Pointer to second array
#   a2: Number of elements
#   a3: Stride for first array
#   a4: Stride for second array
#
# Returns:
#   a0: Dot product result
```

1. Input Validation:
```assembly
li t0, 1
blt a2, t0, error_36       # Check element count
blt a3, t0, error_37       # Check stride1
blt a4, t0, error_37       # Check stride2
```
* Validates element count must be ≥ 1
* Validates both strides must be ≥ 1
* Different error codes for different validation failures

2. Initialization:
```assembly
li t0, 0                   # Initialize result
li t1, 0                   # Initialize counter
slli a3, a3, 2            # Convert stride1 to bytes
slli a4, a4, 2            # Convert stride2 to bytes
```
* `t0`: Accumulates the dot product result
* `t1`: Tracks current element index
* Converts strides from elements to bytes (×4)

3. Main Loop Structure:
```assembly
loop_start:
beq t1, a2, loop_end      # Check loop termination
lw t2, 0(a0)              # Load from first array
lw t3, 0(a1)              # Load from second array
```
* Processes elements until count reached
* Loads corresponding elements from both arrays

4. Multiplication Implementation:
```assembly
li t4, 0                  # Initialize product
mv t5, t2                 # Copy multiplicand
mv t6, t3                 # Copy multiplier

multiply:
beqz t6, multiply_end     # Check if done
andi t3, t6, 1           # Check LSB
beqz t3, multiply_skip   # Skip if LSB is 0
add t4, t4, t5           # Add if LSB is 1
```
* Implements multiplication using shift-and-add method
* Uses bitwise operations for efficiency
* Accumulates partial products

5. Multiply Loop Control:
```assembly
multiply_skip:
slli t5, t5, 1          # Shift multiplicand left
srli t6, t6, 1          # Shift multiplier right
j multiply              # Continue multiplication
```
* Implements binary multiplication algorithm
* Shifts and accumulates partial results

6. Result Accumulation and Pointer Update:
```assembly
multiply_end:
add t0, t0, t4          # Add to total
add a0, a0, a3          # Update first pointer
add a1, a1, a4          # Update second pointer
addi t1, t1, 1         # Increment counter
```
* Accumulates products into final result
* Updates array pointers using strides
* Maintains element counter

7. Function Return and Error Handling:
```assembly
loop_end:
    mv a0, t0              # Set return value
    jr ra                  # Return to caller
error_36:
    li a0, 36              # Element count error
    j exit
error_37:
    li a0, 37              # Stride error
    j exit
```



### Task 3.2: Matrix Multiplication

```assembly
# Function Arguments:
#   Matrix M0:
#     a0: Base address
#     a1: Rows (m)
#     a2: Columns (n)
#   Matrix M1:
#     a3: Base address
#     a4: Rows (n)
#     a5: Columns (p)
#   Result Matrix:
#     a6: Base address for result
```

1. Input Validation:
```assembly
li t0 1
blt a1, t0, error    # Check M0 rows
blt a2, t0, error    # Check M0 cols
blt a4, t0, error    # Check M1 rows
blt a5, t0, error    # Check M1 cols
bne a2, a4, error    # Check multiplication compatibility
```
* Validates positive dimensions for both matrices
* Ensures matrices can be multiplied (M0_cols = M1_rows)

2. Stack Frame Setup:
```assembly
addi sp, sp, -28     # Allocate stack space
sw ra, 0(sp)         # Save return address
sw s0, 4(sp)         # Save callee-saved registers
...
sw s5, 24(sp)
```
* Creates stack frame for saved registers
* Preserves return address and callee-saved registers

3. Register Initialization:
```assembly
li s0, 0    # Outer loop counter (M0 rows)
li s1, 0    # Inner loop counter (M1 cols)
mv s2, a6   # Result matrix pointer
mv s3, a0   # M0 pointer
mv s4, a3   # M1 pointer
```
* Sets up loop counters and matrix pointers
* Preserves original arguments

4. Outer Loop (Rows of M0):
```assembly
outer_loop_start:
    li s1, 0        # Reset inner counter
    mv s4, a3       # Reset M1 pointer
    blt s0, a1, inner_loop_start
```
* Processes each row of first matrix
* Resets inner loop variables

5. Inner Loop (Columns of M1):
```assembly
inner_loop_start:
    beq s1, a5, inner_loop_end
    # Save registers before dot product call
    addi sp, sp, -24
    sw a0, 0(sp)    # Save arguments
    ...
```
* Processes each column of second matrix
* Prepares for dot product calculation

6. Dot Product Setup:
```assembly
mv a0, s3   # M0 row pointer
mv a1, s4   # M1 column pointer
mv a2, a2   # Number of elements (M0 cols)
li a3, 1    # M0 stride
mv a4, a5   # M1 stride
```
* Sets up arguments for dot product call
* Configures appropriate strides

7. Result Storage and Pointer Updates:
```assembly
sw t0, 0(s2)        # Store result
addi s2, s2, 4      # Update result pointer
li t1, 4
add s4, s4, t1      # Update M1 pointer
```
* Stores dot product results
* Updates matrix pointers

8. Loop Control and Clean Up:
```assembly
inner_loop_end:
    slli t0, a2, 2   # Calculate row size
    add  s3, s3, t0  # Update M0 row pointer
outer_loop_end:
    # Restore saved registers
    lw ra, 0(sp)
    ...
    addi sp, sp, 28
```

## Part B: File Operations and Main

### Task 1: Read Matrix

```assembly
# Function Arguments:
#   a0: Filename pointer
#   a1: Row count storage address
#   a2: Column count storage address
# Returns:
#   a0: Matrix data pointer
```

1. Stack Frame Setup:
```assembly
addi sp, sp, -40    # Allocate stack
sw ra, 0(sp)        # Save return address
sw s0, 4(sp)        # File descriptor
sw s1, 8(sp)        # Total elements
sw s2, 12(sp)       # Matrix pointer
sw s3, 16(sp)       # Row pointer
sw s4, 20(sp)       # Col pointer
```
* Creates stack frame
* Preserves callee-saved registers

2. File Opening:
```assembly
mv s3, a1           # Save row pointer
mv s4, a2           # Save col pointer
li a1, 0            # Read-only mode
jal fopen
li t0, -1
beq a0, t0, fopen_error
mv s0, a0           # Save file descriptor
```
* Opens file in read mode
* Checks for open errors
* Saves file descriptor

3. Reading Dimensions:
```assembly
mv a0, s0           # File descriptor
addi a1, sp, 28     # Buffer for dimensions
li a2, 8            # Read 8 bytes
jal fread
li t0, 8
bne a0, t0, fread_error
```
* Reads matrix dimensions
* Validates read operation
* Stores in stack buffer

4. Storing Dimensions:
```assembly
lw t1, 28(sp)       # Load rows
lw t2, 32(sp)       # Load cols
sw t1, 0(s3)        # Store rows
sw t2, 0(s4)        # Store cols
```
* Extracts dimensions
* Stores at provided addresses

5. Size Calculation:
```assembly
multiply:
    beq t4, x0, multiply_done
    andi t5, t4, 1
    beqz t5, multiply_skip
    add s1, s1, t3
multiply_skip:
    slli t3, t3, 1
    srli t4, t4, 1
```
* Calculates total elements (rows × cols)
* Uses bit manipulation for multiplication
* Stores result for memory allocation

6. Memory Allocation:
```assembly
slli t6, s1, 2      # Multiply by 4 for bytes
sw t6, 24(sp)       # Save size
mv a0, t6           # Size in bytes
jal malloc
beq a0, x0, malloc_error
mv s2, a0           # Save matrix pointer
```
* Calculates required bytes
* Allocates memory
* Checks allocation success

7. Reading Matrix Data:
```assembly
mv a0, s0           # File descriptor
mv a1, s2           # Buffer
lw a2, 24(sp)       # Bytes to read
jal fread
lw t0, 24(sp)
bne a0, t0, fread_error
```
* Reads matrix elements
* Validates read operation
* Stores in allocated memory

8. Cleanup and Return:
```assembly
mv a0, s0
jal fclose
li t0, -1
beq a0, t0, fclose_error
mv a0, s2           # Return matrix pointer
```
* Closes file
* Verifies close operation
* Returns matrix pointer

9. Error Handling:
```assembly
malloc_error:
    li a0, 26
    j error_exit
fopen_error:
    li a0, 27
    j error_exit
# ... other error handlers
```

### Task 2: Write Matrix


```assembly
# Function Arguments:
#   a0: Filename pointer
#   a1: Matrix data pointer
#   a2: Row count
#   a3: Column count
```

1. Stack Frame Setup:
```assembly
addi sp, sp, -44     # Allocate stack space
sw ra, 0(sp)         # Save return address
sw s0, 4(sp)         # File descriptor
sw s1, 8(sp)         # Matrix pointer
sw s2, 12(sp)        # Rows
sw s3, 16(sp)        # Columns
sw s4, 20(sp)        # Total elements
```
* Creates stack frame
* Saves callee-saved registers

2. Argument Preservation:
```assembly
mv s1, a1            # Save matrix pointer
mv s2, a2            # Save rows
mv s3, a3            # Save columns
```
* Preserves input arguments in saved registers

3. File Opening:
```assembly
li a1, 1             # Write mode
jal fopen
li t0, -1
beq a0, t0, fopen_error
mv s0, a0            # Save file descriptor
```
* Opens file in write mode
* Checks for open errors
* Stores file descriptor

4. Writing Dimensions:
```assembly
sw s2, 24(sp)        # Store rows
sw s3, 28(sp)        # Store columns
mv a0, s0            # File descriptor
addi a1, sp, 24      # Buffer address
li a2, 2             # Two integers
li a3, 4             # Int size
jal fwrite
```
* Writes matrix dimensions first
* Uses stack buffer for temporary storage
* Verifies write operation

5. Size Calculation:
```assembly
multiply:
    beq t1, x0, multiply_done
    andi t2, t1, 1   # Check LSB
    beqz t2, multiply_skip
    add s4, s4, t0   # Add if LSB is 1
multiply_skip:
    slli t0, t0, 1   # Shift left
    srli t1, t1, 1   # Shift right
```
* Calculates total elements (rows × cols)
* Uses bit manipulation for multiplication
* Stores result for writing data

6. Writing Matrix Data:
```assembly
mv a0, s0            # File descriptor
mv a1, s1            # Matrix data
mv a2, s4            # Element count
li a3, 4             # Int size
jal fwrite
bne a0, s4, fwrite_error
```
* Writes matrix elements
* Verifies write operation
* Checks for write errors

7. File Cleanup:
```assembly
mv a0, s0
jal fclose
li t0, -1
beq a0, t0, fclose_error
```
* Closes file
* Verifies close operation
* Handles close errors

8. Error Handling:
```assembly
fopen_error:
    li a0, 27
    j error_exit
fwrite_error:
    li a0, 30
    j error_exit
fclose_error:
    li a0, 28
    j error_exit
```


### Task 3: Classification

```assembly
# Function Arguments:
#   a0: Filename pointer
#   a1: Matrix data pointer
#   a2: Row count
#   a3: Column count
```

1. Stack Frame Setup:
```assembly
addi sp, sp, -44     # Allocate stack space
sw ra, 0(sp)         # Save return address
sw s0, 4(sp)         # File descriptor
sw s1, 8(sp)         # Matrix pointer
sw s2, 12(sp)        # Rows
sw s3, 16(sp)        # Columns
sw s4, 20(sp)        # Total elements
```
* Creates stack frame
* Saves callee-saved registers

2. Argument Preservation:
```assembly
mv s1, a1            # Save matrix pointer
mv s2, a2            # Save rows
mv s3, a3            # Save columns
```
* Preserves input arguments in saved registers

3. File Opening:
```assembly
li a1, 1             # Write mode
jal fopen
li t0, -1
beq a0, t0, fopen_error
mv s0, a0            # Save file descriptor
```
* Opens file in write mode
* Checks for open errors
* Stores file descriptor

4. Writing Dimensions:
```assembly
sw s2, 24(sp)        # Store rows
sw s3, 28(sp)        # Store columns
mv a0, s0            # File descriptor
addi a1, sp, 24      # Buffer address
li a2, 2             # Two integers
li a3, 4             # Int size
jal fwrite
```
* Writes matrix dimensions first
* Uses stack buffer for temporary storage
* Verifies write operation

5. Size Calculation:
```assembly
multiply:
    beq t1, x0, multiply_done
    andi t2, t1, 1   # Check LSB
    beqz t2, multiply_skip
    add s4, s4, t0   # Add if LSB is 1
multiply_skip:
    slli t0, t0, 1   # Shift left
    srli t1, t1, 1   # Shift right
```
* Calculates total elements (rows × cols)
* Uses bit manipulation for multiplication
* Stores result for writing data

6. Writing Matrix Data:
```assembly
mv a0, s0            # File descriptor
mv a1, s1            # Matrix data
mv a2, s4            # Element count
li a3, 4             # Int size
jal fwrite
bne a0, s4, fwrite_error
```
* Writes matrix elements
* Verifies write operation
* Checks for write errors

7. File Cleanup:
```assembly
mv a0, s0
jal fclose
li t0, -1
beq a0, t0, fclose_error
```
* Closes file
* Verifies close operation
* Handles close errors

8. Error Handling:
```assembly
fopen_error:
    li a0, 27
    j error_exit
fwrite_error:
    li a0, 30
    j error_exit
fclose_error:
    li a0, 28
    j error_exit
```


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
