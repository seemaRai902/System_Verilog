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

