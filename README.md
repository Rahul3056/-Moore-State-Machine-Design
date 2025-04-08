### **Day 28: Moore State Machine Design**

#### **Concept Overview**  
A **Moore State Machine** is a type of Finite State Machine (FSM) where the **output depends only on the current state** and **not on the input**. This makes Moore machines **more stable** since outputs change only at clock edges.

#### **Detailed Explanation**  
- **States** represent the various conditions of the machine.  
- **Transitions** occur on the rising edge of the clock depending on the input conditions.  
- The **output is associated with each state**, not the transitions.

**Components of a Moore FSM**:  
- `state_reg`: Holds current state  
- `next_state`: Logic to compute next state  
- `output_logic`: Output derived from current state only

#### **Applications**  
- Traffic light controllers  
- Sequence detectors  
- Communication protocols  
- Elevator control systems

#### **Execution Steps**  
1. Define all states using `parameter` or `typedef enum`  
2. Create the state transition logic  
3. Write the state register using `always @(posedge clk)`  
4. Assign output based on the current state  
5. Simulate with a testbench to verify behavior

#### **Real-World Example for Practice**  
**Design a sequence detector for "101" using Moore FSM**

#### **Example Verilog Code (Sequence Detector - "101")**
```verilog
module moore_seq_detector(
    input clk, rst, x,
    output reg y
);
    typedef enum reg [1:0] {S0, S1, S2, S3} state_t;
    state_t state, next;

    // State register
    always @(posedge clk or posedge rst) begin
        if (rst)
            state <= S0;
        else
            state <= next;
    end

    // Next state logic
    always @(*) begin
        case(state)
            S0: next = x ? S1 : S0;
            S1: next = x ? S1 : S2;
            S2: next = x ? S3 : S0;
            S3: next = x ? S1 : S2;
            default: next = S0;
        endcase
    end

    // Output logic (Moore: depends only on state)
    always @(*) begin
        case(state)
            S3: y = 1;
            default: y = 0;
        endcase
    end
endmodule
```

#### **Testbench**
```verilog
module tb_moore_seq_detector;
    reg clk = 0, rst, x;
    wire y;

    moore_seq_detector dut (.clk(clk), .rst(rst), .x(x), .y(y));
    always #5 clk = ~clk;

    initial begin
        rst = 1; x = 0; #10; rst = 0;
        x = 1; #10;
        x = 0; #10;
        x = 1; #10; // Should detect "101" here
        x = 1; #10;
        x = 0; #10;
        x = 1; #10;
        $finish;
    end
endmodule
```

#### **Expected Output**
At the third `101` pattern, output `y` will go high for one clock cycle, as it's triggered by being in `S3`.

#### **Enhancements**  
- Add LED outputs for state visualization  
- Extend it to detect overlapping patterns  
- Implement using `casez` for wildcard-based transitions

