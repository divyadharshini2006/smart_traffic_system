# **Smart Traffic System**
---

## **Aim**
- To design, simulate, and verify a Smart Traffic Controller system using Verilog HDL. The system must control traffic light transitions (Green → Yellow → Red) based on a timer and include an Emergency Override feature that grants immediate priority to ambulances or fire trucks.
---

## **Apparatus Required**
- Computer with **Vivado Design Suite** installed  
---

## **Objectives**
The main objectives of this project are:

1. To design a Smart Traffic Controller using Verilog HDL.
2. To implement a Finite State Machine (FSM) that cycles through Red, Yellow, and
Green states.
3. To integrate an Emergency Override Logic that forces the signal to Green when an
emergency input is detected.
4. To simulate and verify the design using a testbench and waveform analysis.
---
## Block Diagram 
<img width="895" height="428" alt="Screenshot 2025-11-19 084303" src="https://github.com/user-attachments/assets/b1fc5133-bc92-4004-8712-43d825a9a598" />


## FSM State Diagram 
<img width="920" height="543" alt="Screenshot 2025-11-19 083559" src="https://github.com/user-attachments/assets/87b7cce4-0b8c-41dc-a152-215b39ea36b7" />

## Verilog Code
```verilog
module smart_traffic_system (
    input clk,
    input rst,
    input emergency,    
    output reg red,
    output reg yellow,
    output reg green
);

  
    parameter S_GREEN  = 2'b00;
    parameter S_YELLOW = 2'b01;
    parameter S_RED    = 2'b10;
    parameter DELAY_COUNT = 32'd500_000_000; 


    reg [1:0] state, next_state;
    reg [3:0] count;

  
    always @(posedge clk or posedge rst) begin
        if (rst) begin
            state <= S_GREEN;
            count <= 0;
        end 
        else begin
            state <= next_state;

            if (state != next_state)
                count <= 0;
            else
                count <= count + 1;
        end
    end

    always @(*) begin
        next_state = state;

       
        if (emergency) begin
            next_state = S_GREEN;
        end
        else begin
            case (state)
                S_GREEN:
                    if (count == 4'd5)
                        next_state = S_YELLOW;

                S_YELLOW:
                    if (count == 4'd5)
                        next_state = S_RED;

                S_RED:
                    if (count == 4'd5)
                        next_state = S_GREEN;
            endcase
        end
    end

  
    always @(*) begin
        red = 0; yellow = 0; green = 0;

        case (state)
            S_GREEN:  green  = 1;
            S_YELLOW: yellow = 1;
            S_RED:    red    = 1;
        endcase
    end

endmodule
```
## Testbench
```
`timescale 1ns/1ps

module tb_smart_traffic_system;

    reg clk, rst;
    reg emergency;

    wire red, yellow, green;

    smart_traffic_system DUT (
        .clk(clk),
        .rst(rst),
        .emergency(emergency),
        .red(red),
        .yellow(yellow),
        .green(green)
    );

    always #5 clk = ~clk;

    initial begin
        clk = 0;
        rst = 1;
        emergency = 0;

        #20 rst = 0;
        #200;
        emergency = 1;
        #100;
        emergency = 0;
        #200;

        $stop;
    end

endmodule
```
## Simulation Output 

<img width="1470" height="923" alt="image" src="https://github.com/user-attachments/assets/3ef7d075-c9d1-419e-8133-abc9e1cb974b" />

## FPGA BOARD SIMULATION ##
**(if reset =1 ; Green stays on )**
<img width="1600" height="1134" alt="image" src="https://github.com/user-attachments/assets/910a204d-00f2-4d4d-994f-f34091acf6d1" />

**(if emergency =1 ;green stays on )**
<img width="1600" height="1141" alt="image" src="https://github.com/user-attachments/assets/ad5c8fec-c44c-405a-8ee4-f28760a27022" />


**(if emergency =0 the normal loop goes on)**
<img width="1600" height="1076" alt="image" src="https://github.com/user-attachments/assets/0e5808f0-87b9-4c6b-ad28-30657896317d" />
<img width="1600" height="1164" alt="image" src="https://github.com/user-attachments/assets/4efbfc8d-e7cf-401b-877e-7a37e32fd60a" />
<img width="1600" height="1077" alt="image" src="https://github.com/user-attachments/assets/69ff76ca-2ff1-4afc-9699-c352919033f4" />


## Result

The Smart Traffic Controller utilizing a Moore Finite State Machine was successfully designed and verified through simulation.
