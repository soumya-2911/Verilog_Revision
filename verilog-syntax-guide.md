# Verilog Syntax and Programming Concepts - Revision Guide

## Table of Contents
1. [Introduction to Verilog](#introduction-to-verilog)
2. [Basic Syntax and Lexical Conventions](#basic-syntax-and-lexical-conventions)
3. [Data Types](#data-types)
4. [Operators](#operators)
5. [Modules and Ports](#modules-and-ports)
6. [Control Structures](#control-structures)
7. [Always Blocks and Procedural Statements](#always-blocks-and-procedural-statements)
8. [Tasks and Functions](#tasks-and-functions)
9. [Finite State Machines (FSM)](#finite-state-machines-fsm)
10. [Timing and Delays](#timing-and-delays)
11. [Testbenches](#testbenches)
12. [Common Mistakes and Debugging](#common-mistakes-and-debugging)
13. [Best Practices](#best-practices)

---

## 1. Introduction to Verilog

**Verilog** is a Hardware Description Language (HDL) used for modeling digital systems and circuits. It is case-sensitive and uses a C-like syntax.

### Key Features:
- **Concurrent execution**: Multiple blocks execute simultaneously
- **Case-sensitive**: All keywords are lowercase
- **Synthesizable**: Can be converted to actual hardware
- **Hierarchical**: Supports modular design

### Abstraction Levels:
1. **Behavioral Level**: Algorithm-oriented (high-level)
2. **Dataflow Level**: Expression-based using `assign` statements
3. **Gate Level**: Logic gate interconnections
4. **Switch Level**: Transistor-level modeling

---

## 2. Basic Syntax and Lexical Conventions

### Comments
```verilog
// Single-line comment
/* Multi-line
   comment */
```

### Identifiers
- Must start with letter or underscore
- Can contain letters, digits, underscores, and `$`
- Case-sensitive
- Keywords are reserved (all lowercase)

### Numbers
```verilog
// Decimal
123, 'd123
// Binary  
4'b1010, 'b1010
// Octal
4'o12, 'o12
// Hexadecimal
4'hA, 'hA
// With size specification
<size>'<base><value>
```

### Logic Values
- **0**: Logic zero, false
- **1**: Logic one, true  
- **x** or **X**: Unknown value
- **z** or **Z**: High impedance (tri-state)

---

## 3. Data Types

### 3.1 Nets (Physical Connections)
Represent connections between hardware components.

```verilog
wire a;              // Single bit wire
wire [7:0] bus;      // 8-bit bus
tri [3:0] data;      // Tri-state net
```

**Common Net Types:**
- `wire`: Standard interconnect
- `tri`: Three-state wire
- `wor`, `wand`: Wired-OR/AND logic
- `supply0`, `supply1`: Power/ground nets

### 3.2 Variables (Storage Elements)
Store values until next assignment.

```verilog
reg a;               // Single bit register
reg [7:0] data;      // 8-bit register
integer count;       // 32-bit signed integer
real voltage;        // Floating-point number
time sim_time;       // Time variable
```

### 3.3 Arrays and Vectors
```verilog
reg [7:0] memory [0:255];    // Array of 8-bit registers
wire [31:0] bus;             // 32-bit vector
reg [7:0] data [0:3][0:15];  // 2D array
```

### 3.4 Parameters and Constants
```verilog
parameter WIDTH = 8;
parameter DELAY = 10;
localparam INTERNAL_CONST = 5;  // Local to module
```

---

## 4. Operators

### 4.1 Arithmetic Operators
```verilog
+    // Addition
-    // Subtraction  
*    // Multiplication
/    // Division
%    // Modulus
**   // Power
```

### 4.2 Logical Operators
```verilog
!    // Logical NOT
&&   // Logical AND
||   // Logical OR
```

### 4.3 Bitwise Operators
```verilog
~    // Bitwise NOT
&    // Bitwise AND
|    // Bitwise OR
^    // Bitwise XOR
^~   // Bitwise XNOR
~^   // Bitwise XNOR
```

### 4.4 Reduction Operators
```verilog
&a   // Reduction AND
|a   // Reduction OR
^a   // Reduction XOR
~&a  // Reduction NAND
~|a  // Reduction NOR
~^a  // Reduction XNOR
```

### 4.5 Relational Operators
```verilog
>    // Greater than
<    // Less than
>=   // Greater than or equal
<=   // Less than or equal
```

### 4.6 Equality Operators
```verilog
==   // Logical equality
!=   // Logical inequality
===  // Case equality (includes X, Z)
!==  // Case inequality (includes X, Z)
```

### 4.7 Shift Operators
```verilog
<<   // Logical left shift
>>   // Logical right shift
<<<  // Arithmetic left shift
>>>  // Arithmetic right shift
```

### 4.8 Concatenation and Replication
```verilog
{a, b, c}        // Concatenation
{4{a}}           // Replication (aaaa)
{2{a,b}}         // Replication (abab)
```

---

## 5. Modules and Ports

### 5.1 Module Declaration
```verilog
module module_name (port_list);
    // Module contents
endmodule
```

### 5.2 Port Declaration Styles

#### Verilog-1995 Style
```verilog
module counter(clk, reset, count);
    input clk, reset;
    output [3:0] count;
    reg [3:0] count;
    
    // Module body
endmodule
```

#### Verilog-2001 Style (ANSI-C)
```verilog
module counter(
    input wire clk,
    input wire reset,
    output reg [3:0] count
);
    // Module body
endmodule
```

### 5.3 Parameterized Modules
```verilog
module counter #(
    parameter WIDTH = 4
)(
    input wire clk,
    input wire reset,
    output reg [WIDTH-1:0] count
);
    // Module body
endmodule
```

### 5.4 Module Instantiation
```verilog
// Positional instantiation
counter c1(clk, rst, cnt);

// Named instantiation
counter c2(
    .clk(clock),
    .reset(rst),
    .count(cnt)
);

// Parameter override
counter #(.WIDTH(8)) c3(
    .clk(clock),
    .reset(rst),
    .count(cnt)
);
```

---

## 6. Control Structures

### 6.1 if-else Statement
```verilog
if (condition) begin
    // statements
end
else if (condition2) begin
    // statements
end
else begin
    // statements
end
```

### 6.2 case Statement
```verilog
case (expression)
    value1: statement1;
    value2: statement2;
    value3, value4: statement3;
    default: default_statement;
endcase
```

#### casex and casez
```verilog
casex (expression)  // X is don't care
    4'b1xx0: statement1;
    4'b0x1x: statement2;
    default: statement3;
endcase

casez (expression)  // Z is don't care
    4'b1??0: statement1;  // ? represents Z
    default: statement2;
endcase
```

### 6.3 Loops

#### for Loop
```verilog
for (i = 0; i < 8; i = i + 1) begin
    // statements
end
```

#### while Loop
```verilog
while (condition) begin
    // statements
end
```

#### repeat Loop
```verilog
repeat (count) begin
    // statements
end
```

#### forever Loop
```verilog
forever begin
    // statements
end
```

---

## 7. Always Blocks and Procedural Statements

### 7.1 Always Block Types
```verilog
// Combinational logic
always @(*) begin
    // All inputs in sensitivity list automatically
end

// Sequential logic
always @(posedge clk) begin
    // Triggered on positive clock edge
end

// Mixed sensitivity
always @(posedge clk or negedge reset) begin
    // Multiple edge sensitivity
end
```

### 7.2 Initial Block
```verilog
initial begin
    // Executed once at time 0
    // Non-synthesizable (testbench only)
end
```

### 7.3 Assignment Types

#### Blocking Assignment (=)
```verilog
always @(*) begin
    a = b;
    c = a;  // Uses new value of 'a'
end
```

#### Non-blocking Assignment (<=)
```verilog
always @(posedge clk) begin
    a <= b;
    c <= a;  // Uses old value of 'a'
end
```

### 7.4 Coding Guidelines
- Use **blocking assignments** for combinational logic
- Use **non-blocking assignments** for sequential logic
- Don't mix blocking and non-blocking in same always block
- Use `always @(*)` for combinational logic sensitivity

---

## 8. Tasks and Functions

### 8.1 Functions
```verilog
function [7:0] add_function;
    input [7:0] a, b;
    begin
        add_function = a + b;
    end
endfunction

// Usage
result = add_function(x, y);
```

**Function Rules:**
- Return single value
- No time-consuming constructs
- No `#` delays, `wait`, `posedge`
- Only blocking assignments
- Can call other functions

### 8.2 Tasks
```verilog
task add_task;
    input [7:0] a, b;
    output [7:0] sum;
    begin
        #5 sum = a + b;  // Can have delays
    end
endtask

// Usage
add_task(x, y, result);
```

**Task Rules:**
- Can have multiple inputs/outputs
- Can contain time delays
- Can use both blocking and non-blocking assignments
- Can call other tasks and functions

---

## 9. Finite State Machines (FSM)

### 9.1 FSM Types
- **Moore Machine**: Output depends only on current state
- **Mealy Machine**: Output depends on current state and inputs

### 9.2 FSM Coding Styles

#### Two Always Block Style (Recommended)
```verilog
// State encoding
typedef enum {IDLE, START, PROCESS, DONE} state_t;

module fsm_example (
    input clk, reset, start,
    output reg busy, done
);

state_t current_state, next_state;

// State register
always @(posedge clk or posedge reset) begin
    if (reset)
        current_state <= IDLE;
    else
        current_state <= next_state;
end

// Next state logic and outputs
always @(*) begin
    next_state = current_state;  // Default
    busy = 0;
    done = 0;
    
    case (current_state)
        IDLE: begin
            if (start)
                next_state = START;
        end
        
        START: begin
            busy = 1;
            next_state = PROCESS;
        end
        
        PROCESS: begin
            busy = 1;
            next_state = DONE;
        end
        
        DONE: begin
            done = 1;
            next_state = IDLE;
        end
    endcase
end

endmodule
```

### 9.3 State Encoding
```verilog
// Binary encoding
parameter IDLE = 2'b00, START = 2'b01;

// One-hot encoding
parameter IDLE = 4'b0001, START = 4'b0010;

// Gray encoding
parameter IDLE = 2'b00, START = 2'b01;
```

---

## 10. Timing and Delays

### 10.1 Delay Types

#### Regular Delay
```verilog
#10 a = b;  // Wait 10 time units, then assign
```

#### Intra-assignment Delay
```verilog
a = #10 b + c;  // Evaluate b+c now, assign after 10 units
```

#### Gate Delays
```verilog
and #(2,3) gate1(out, a, b);  // Rise=2, Fall=3
not #5 gate2(out, in);        // All delays = 5
```

### 10.2 Continuous Assignment Delays
```verilog
assign #5 sum = a + b;  // Inertial delay
```

---

## 11. Testbenches

### 11.1 Basic Testbench Structure
```verilog
`timescale 1ns/1ps

module tb_counter();

// Testbench signals
reg clk, reset;
wire [3:0] count;

// Device Under Test (DUT)
counter dut(
    .clk(clk),
    .reset(reset),
    .count(count)
);

// Clock generation
initial begin
    clk = 0;
    forever #5 clk = ~clk;  // 10ns period
end

// Test stimulus
initial begin
    reset = 1;
    #20 reset = 0;
    #100 $finish;
end

// Monitor results
initial begin
    $monitor("Time=%0t, Count=%d", $time, count);
end

endmodule
```

### 11.2 System Tasks
```verilog
$display("Message");         // Print message
$monitor("var=%d", var);     // Monitor variable changes
$time                        // Current simulation time
$finish                      // End simulation
$stop                        // Pause simulation
$readmemh("file.hex", mem); // Read memory from file
```

---

## 12. Common Mistakes and Debugging

### 12.1 Synthesis Issues

#### Inferring Unwanted Latches
```verilog
// BAD - Creates latch
always @(*) begin
    if (sel)
        out = a;
    // No else - creates latch
end

// GOOD - No latch
always @(*) begin
    if (sel)
        out = a;
    else
        out = b;
end
```

#### Incomplete Sensitivity List
```verilog
// BAD
always @(a) begin
    out = a & b;  // Missing 'b' in sensitivity
end

// GOOD
always @(*) begin  // or @(a, b)
    out = a & b;
end
```

### 12.2 Common Coding Errors

#### Multiple Drivers
```verilog
// BAD - Multiple drivers for same signal
always @(*) out = a;
always @(*) out = b;  // Error!

// GOOD - Single driver
always @(*) begin
    if (sel)
        out = a;
    else
        out = b;
end
```

#### Mixing Blocking/Non-blocking
```verilog
// BAD - Mixed assignments
always @(posedge clk) begin
    a <= b;
    c = a;   // Uses old value of 'a' - confusing!
end

// GOOD - Consistent assignments
always @(posedge clk) begin
    a <= b;
    c <= a;  // Both non-blocking
end
```

### 12.3 Debugging Techniques
- Use `$display` and `$monitor` for debugging
- Check RTL viewer for unexpected logic
- Verify sensitivity lists are complete
- Use waveform viewers for timing analysis
- Check for race conditions between always blocks

---

## 13. Best Practices

### 13.1 Coding Style
- Use descriptive names for signals and modules
- Follow consistent indentation and formatting
- Add comments to explain complex logic
- Use parameters for constants and widths

### 13.2 Design Guidelines
- Separate combinational and sequential logic
- Use non-blocking assignments for sequential logic
- Use blocking assignments for combinational logic
- Avoid using `#` delays in synthesizable code
- Initialize all registers in reset conditions

### 13.3 FSM Guidelines
- Use enumerated types for states
- Implement separate always blocks for state register and next-state logic
- Always include default cases in case statements
- Use descriptive state names

### 13.4 Testbench Guidelines
- Create comprehensive test vectors
- Use self-checking testbenches when possible
- Include corner cases and error conditions
- Use meaningful assertions
- Generate good coverage reports

---

## Quick Reference Summary

### Key Verilog Constructs
```verilog
module name(ports);        // Module declaration
always @(*)               // Combinational always
always @(posedge clk)     // Sequential always
if-else                   // Conditional execution
case-endcase              // Multi-way branch
for, while, repeat        // Loops
task/endtask              // Task definition
function/endfunction      // Function definition
assign                    // Continuous assignment
```

### Data Types Quick Reference
```verilog
wire [7:0] bus;           // 8-bit net
reg [7:0] data;           // 8-bit variable
integer count;            // 32-bit integer
parameter WIDTH = 8;      // Parameter
localparam LOCAL = 4;     // Local parameter
```

### Assignment Types
```verilog
assign wire_sig = expr;   // Continuous assignment
reg_var = expr;           // Blocking procedural
reg_var <= expr;          // Non-blocking procedural
```

---

This comprehensive guide covers the essential Verilog syntax and programming concepts needed for effective hardware design and verification. Regular practice with these constructs and adherence to best practices will lead to robust and maintainable Verilog code.