##### 1GHz clock generation with 70% duty cycle
```system verilog
`timescale 1ns/1ps
 module tb;
   reg clk;
   initial repeat (10) begin
    
     #0.03 clk = 1;
     #0.07 clk = 0;
    end
    initial begin
      $monitor($time,"clk = %0b",clk);
      $dumpfile("dump.vcd");
      $dumpvars;
    end
  endmodule
```

##### 10GHz clock generation with 70% duty cycle
```system verilog
`timescale 10ns/1ps
 module tb;
   reg clk;
   initial repeat (10) begin
    
     #0.03 clk = 1;
     #0.07 clk = 0;
    end
    initial begin
      $monitor($time,"clk = %0b",clk);
      $dumpfile("dump.vcd");
      $dumpvars;
    end
  endmodule
```
# 12.5 MHz Clock Generator with 25% Duty Cycle in Verilog

This repository contains Verilog code examples to generate a **12.5 MHz clock signal with a 25% duty cycle**. It includes implementations for both simulation (testbenches) and synthesizable hardware (FPGA RTL).

---

## ❓ Question / Problem Statement
How do you design a Verilog module and simulation testbench to generate a 12.5 MHz clock signal featuring a 25% duty cycle?

---

## 📐 The Math & Specifications

To design this clock, we calculate the exact time durations required for the high and low states:
<img width="278" height="122" alt="Image" src="https://github.com/user-attachments/assets/54ed0de3-9204-46c4-89b1-74a1e4ab2dac" />

---

## 💻 Implementations

### 1. For Simulation (Testbench Code)
Use this code inside your behavioral simulators (e.g., Vivado Simulator, ModelSim, Icarus Verilog). It uses explicit time delays (`#20` and `#60`) to strictly enforce the high and low windows.

> ⚠️ **Note:** This behavioral code is **not** synthesizable and cannot be deployed to FPGA hardware.

```verilog
`timescale 1ns / 1ps

module tb_clock_gen_25;
    reg clk;

    always begin
        clk = 1; #20;  // Stay HIGH for 20ns (25%)
        clk = 0; #60;  // Stay LOW for 60ns (75%)
    end

    initial begin
        clk = 0;       // Initialize clock
        #1000 \$finish; // Run simulation for 1000ns
    end
endmodule
```

### 2. For Hardware (Synthesizable FPGA RTL)
To deploy this on an actual FPGA, you must divide a stable base onboard clock down using counter logic. 

#### Option A: Using a 50 MHz Master Clock
A **50 MHz base clock** has a period of exactly 20 ns. By implementing a 2-bit counter that cycles from `0` to `3`, we can decode exactly one state to remain HIGH, resulting in a perfect 25% duty cycle.

```verilog
module clock_divider_25 (
    input  wire clk_in,   // Connect to 50 MHz onboard clock (20ns period)
    input  wire rst_n,    // Active-low reset
    output reg  clk_out   // Outputs 12.5 MHz clock with 25% duty cycle
);

    reg [1:0] counter;

    always @(posedge clk_in or negedge rst_n) begin
        if (!rst_n) begin
            counter <= 2'b00;
            clk_out <= 1'b0;
        end else begin
            counter <= counter + 1'b1;
            
            // High for 1 master clock cycle (20ns), Low for 3 cycles (60ns)
            if (counter == 2'b00) begin
                clk_out <= 1'b1;
            end else begin
                clk_out <= 1'b0;
            end
        end
    end
endmodule
```

#### Option B: Using a 100 MHz Master Clock
If your FPGA board relies on a **100 MHz base clock** (10 ns period), the 80 ns target cycle requires an 8-count sequence. To secure a 25% duty cycle, your output needs to hold HIGH for 2 counts (20 ns) and LOW for 6 counts (60 ns):

```verilog
// Add this logic block inside your 100 MHz clock domain module:
reg [2:0] counter; // 3-bit counter (0 to 7)

always @(posedge clk_in or negedge rst_n) begin
    if (!rst_n) begin
        counter <= 3'b000;
        clk_out <= 1'b0;
    end else begin
        counter <= counter + 1'b1;

        if (counter < 3'd2) begin  // HIGH during count 0 and 1 (20ns total)
            clk_out <= 1'b1;
        end else begin             // LOW during counts 2 through 7 (60ns total)
            clk_out <= 1'b0;
        end
    end
end
```

---

## 🚀 How to Use
1. Copy the **Simulation Code** directly into your testbench file (`.v`) to drive your Unit Under Test (UUT).
2. Choose **Option A or B** for your hardware depending on your target FPGA's oscillator, and map the ports in your constraint file (`.xdc` or `.ucf`).

