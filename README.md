# vsd-hdp

Author: Sourabh Kulkarni 

1. [Day 0](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#day-0) - **Installation of open source tools iverilog, gtkwave and yosys**   
2. [Day 1](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#day-1) - **Simulation of combinational designs using open source tools**
3. [Day 2](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#day-2) - **Hierarchical and flat synthesis and Simulation of sequential circuits**
4. [Day 3](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#day-3) - **Combinational and Sequential Logic optimization**
5. [Day 4](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#day-4) - **Gate Level Simulation using iverilog**
6. [Day 5](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#day-5) - **Introduction to RISC-V ISA and GNU Compiler Toolchain**
7. [Day 6](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#day-6) - **Introduction to ABI and basic verification flow**
8. [Day 7](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#day-7) - **Digital Logic with TL-Verilog and Makerchip**
9. [Day 8](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#day-8) - **RISC-V implmentation** 
10. [Day 9](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#day-9) - **Solutions to pipeline hazards and load-store implementtaion**





## Day 0 

### Tool 1: Yosys
**Objectives**
    - Install Ubuntu 20.04 and install yosys, iverilog and gtkwave.
    - Check and verify yosys is succesfully installed.

**Installation**

*Steps followed to install Yosys*


    $ git clone https://github.com/YosysHQ/yosys.git
    $ cd yosys
    $ sudo apt install make (If make is not installed please install it) 
    $ sudo apt-get install build-essential clang bison flex \
    libreadline-dev gawk tcl-dev libffi-dev git \
    graphviz xdot pkg-config python3 libboost-system-dev \
    libboost-python-dev libboost-filesystem-dev zlib1g-dev
    $ make config-gcc
    $ make 
    $ sudo make install


Progrees Image of Yosys:

![yosys](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/8fbce3e6-98d4-4d10-a633-9fb0bc75f395)


*Steps followed to install iverilog*

    $ sudo apt-get install iverilog

Progrees Image of iverilog:

![iverilog](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/2c19ad5e-283b-4ea3-80ca-88750321d6d4)



*Steps followed to install gtkwave*

    $ sudo apt update
    $ sudo apt install gtkwave


Progrees Image of iverilog:

![gtkave](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/1aafbcd3-3970-4169-9c91-dd34c493ce0f)


## Day 1

### Mux 2:1 implementation

**Learning how to use iverilog, gtkwave and yosys for RTL Design, simulation and Synthesis**

*Below steps are followed while simulating the design:* 

**1. iverilog**:
       Verilog code for mux is written using below commands

           $ iverilog design_verilog.v tb_design_verilog.v
           $ ./a.out
   Dumpfile is generated using gtkwave
           $ gkwave design.vcd 

   **RTL Design**:
     
       module good_mux (input i0, input i1, input sel, output reg y);
           always @ (*)
           begin
               if(sel)
                 y <= i1;
               else
                 y <= i0;
               end
           endmodule

    **Testbench**:


   	
		`timescale 1ns / 1ps
		module tb_good_mux;
		// Inputs
		reg i0,i1,sel;
		// Outputs
		wire y;
   		// Instantiate the Unit Under Test (UUT)
			good_mux uut (
				.sel(sel),
				.i0(i0),
				.i1(i1),
				.y(y));

			initial begin
			$dumpfile("tb_good_mux.vcd");
			$dumpvars(0,tb_good_mux);

			// Initialize Inputs
			sel = 0;
			i0 = 0;
			i1 = 0;
			#300 $finish;
			end

		always #75 sel = ~sel;
		always #10 i0 = ~i0;
		always #55 i1 = ~i1;
		endmodule


**2. gtkwave simulation waveform**:
  

   	 <img width="634" alt="gtkave_waveform" src="https://github.com/Sourabhk20/vsd-hdp/assets/148907305/2a84e1f6-d11b-46e4-ae6f-f8e811f90641">



**3. Synthesis**:
          Synthesis is a process of transforming RTL into a gtae level netlist and mapping it to the technology specific gates.

          Commands used to run the synthesis:
                  Skywater PDK 130 nm library is used for the synthesis.

	                yosys
	                read_liberty -lib <lib_path>/ lib file 
	                read_verilog <verilog_file.v>
	                synth -top <verilog_file>  (Include top level RTL design name here)
	                abc -liberty <lib_path>/ lib file (matches the design to the gates which specified in the lib file)
	                show 
	                write_verilog <netlist_file_name>.v (generates a netlist)


	Synthesis output for 2:1 Mux showing number of input, output signals that are used in the design:


![yosys_no of cells](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/88336a1b-90e5-4b66-a1aa-ee541be87bac)


  	Generated logic for 2:1 Mux after synthesis:
   		

![yosys_lab1_show](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/51072939-3b8b-4e33-9f86-baaea1780407)


  Netlist generated after synthesis:

     		/* Generated by Yosys 0.34+43 (git sha1 d21c464ae, gcc 9.4.0-1ubuntu1~20.04.2 -fPIC -Os) */
		(* top =  1  *)
		(* src = "good_mux.v:2.1-10.10" *)
		module good_mux(i0, i1, sel, y);
		  (* src = "good_mux.v:2.24-2.26" *)
		  wire _0_;
		  (* src = "good_mux.v:2.35-2.37" *)
		  wire _1_;
		  (* src = "good_mux.v:2.46-2.49" *)
		  wire _2_;
		  (* src = "good_mux.v:2.63-2.64" *)
		  wire _3_;
		  (* src = "good_mux.v:2.24-2.26" *)
		  input i0;
		  wire i0;
		  (* src = "good_mux.v:2.35-2.37" *)
		  input i1;
		  wire i1;
		  (* src = "good_mux.v:2.46-2.49" *)
		  input sel;
		  wire sel;
		  (* src = "good_mux.v:2.63-2.64" *)
		  output y;
		  wire y;
		  sky130_fd_sc_hd__mux2_1 _4_ (
		    .A0(_0_),
		    .A1(_1_),
		    .S(_2_),
		    .X(_3_)
		  );
		  assign _0_ = i0;
		  assign _1_ = i1;
		  assign _2_ = sel;
		  assign y = _3_;
		endmodule


## Day 2 ##

A. [Timing Libs, Hierarchical vs Flat Synthesis](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#timing-libs-hierarchical-vs-flat-synthesis)

B. [Simulation and Synthesis of various Flip Flops](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#simulation-and-synthesis-of-various-flip-flops)

C. [Multiplier](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#2-bit-multiplier-and-3-bit-multiplier)


### Timing Libs, Hierarchical vs Flat Synthesis

**Below steps are followed while simulating the Multiple Module design for Hierarchical vs Flat Synthesis**

 Multiple module consists of two submodules (submodule1 and submodule2):

 Hierarchical Synthesis output:
 	Hierarchical synthesis preserves each module and evaluates the output with abstract representation of the submodules.
  
![show](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/5ec2d379-c946-49c3-a002-34b4ccdd0704)


Flat Synthesis output:
	Flat synthesis gives the deatiled representation of the submodules at the gate level.
 
![show_faltten](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/729a7088-afc6-4dc1-a2a8-b608ed1a4d8b)
	

Difference between Hierarchical vs Flat Synthesis netlist:

![flat vs hier](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/f3b74936-4ec8-491d-9d55-b46ffe298910)


 Netlist:

 	Hierarchies are preserved
  
 		/* Generated by Yosys 0.34+43 (git sha1 d21c464ae, gcc 9.4.0-1ubuntu1~20.04.2 -fPIC -Os) */

		module multiple_modules(a, b, c, y);
		  input a;
		  wire a;
		  input b;
		  wire b;
		  input c;
		  wire c;
		  wire net1;
		  output y;
		  wire y;
		  sub_module1 u1 (
		    .a(a),
		    .b(b),
		    .y(net1)
		  );
		  sub_module2 u2 (
		    .a(net1),
		    .b(c),
		    .y(y)
		  );
		endmodule
		
		module sub_module1(a, b, y);
		  wire _0_;
		  wire _1_;
		  wire _2_;
		  input a;
		  wire a;
		  input b;
		  wire b;
		  output y;
		  wire y;
		  sky130_fd_sc_hd__and2_0 _3_ (
		    .A(_1_),
		    .B(_0_),
		    .X(_2_)
		  );
		  assign _1_ = b;
		  assign _0_ = a;
		  assign y = _2_;
		endmodule
		
		module sub_module2(a, b, y);
		  wire _0_;
		  wire _1_;
		  wire _2_;
		  input a;
		  wire a;
		  input b;
		  wire b;
		  output y;
		  wire y;
		  sky130_fd_sc_hd__or2_0 _3_ (
		    .A(_1_),
		    .B(_0_),
		    .X(_2_)
		  );
		  assign _1_ = b;
		  assign _0_ = a;
		  assign y = _2_;
		endmodule


### Simulation and Synthesis of various Flip Flops:

**1. DFF Sync reset:**

   **a. RTL Code:**
		
  		module dff_syncres ( input clk , input async_reset , input sync_reset , input d , output reg q );
		always @ (posedge clk )
		begin
			if (sync_reset)
				q <= 1'b0;
			else	
				q <= d;
		end
		endmodule

  

   **b. Simulation:**

![dff_gtkwave_sync](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/3f2d905b-26b2-4f95-814f-12b848c1a35f)


  **c. Synthesis:**

![dff_sync_show](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/edba1026-cca2-4d1d-959d-d2d2a721a43d)



**2. DFF async reset:**

   **a. RTL Code:**
		
  		module dff_asyncres ( input clk , input async_reset , input sync_reset , input d , output reg q );
		always @ (posedge clk, posedge async_reset )
		begin
			if (async_reset)
				q <= 1'b0;
			else	
				q <= d;
		end
		endmodule

  

   **b. Simulation:**

![dff_gtkwave](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/7f25557e-fdf3-4916-80b9-0a2eabc38fe2)



  **c. Synthesis:**

![show_dff](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/47337d9e-fa6b-4606-bf03-9a8102358d52)



**3. DFF async_sync reset:**

   **a. RTL Code:**
		
  		module dff_syncres ( input clk , input async_reset , input sync_reset , input d , output reg q );
		always @ (posedge clk, posedge async_reset)
		begin
			if (sync_reset)
				q <= 1'b0;
			else	
				q <= d;
		end
		endmodule

  

   **b. Simulation:**

![dff_gtkwave_sync](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/3f2d905b-26b2-4f95-814f-12b848c1a35f)


  **c. Synthesis:**

![dff_sync_show](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/edba1026-cca2-4d1d-959d-d2d2a721a43d)




### 2-Bit Multiplier and 3-bit Multiplier

**2-Bit Multiplier**
		Since, 2-bit multiplier gives us a result when we shift the nput bits to the left. (Or when we append the 0 to the input bits, we will get the output.
  
  **1. RTL:**
			
		module mul2(a, y);
		  input [2:0] a;
		  wire [2:0] a;
		  output [3:0] y;
		  wire [3:0] y;
		  assign y = { a, 1'h0 };
		endmodule

  **2. Synthesis:**
  		During synthesis, as we will get the result when we append the 0 to the input bits, we will get the output.
So, the synthesis tool will not infer any combinational or sequnetial cells.

![mul2_synth](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/a251d96e-ebe4-483c-bacd-4241a737ac1e)


  Output of the Synthesis:

![mul2_show](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/95077a60-6329-4944-a836-a19487406846)




**3-bit Multiplier**
		Since, 3-bit multiplier gives us 6-bit result, we get the result by appending the input bits followed by the input bits. 

  
  **1. RTL:**
			
	module mult8(a, y);
	  input [2:0] a;
	  wire [2:0] a;
	  output [5:0] y;
	  wire [5:0] y;
	  assign y = { a, a };
	endmodule

 
  **2. Synthesis:**
  		The synthesis tool will not infer any combinational or sequnetial cells.

![mul8_synth](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/b25c5e19-051a-4e64-a759-06f4e19149c5)




  Output of the Synthesis:

![mul8_show](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/dacd51a0-8996-4ba5-b05f-37da78a0e386)





## Day 3 ##


A. [Combinational Logic Optimization](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#Combinational-logic-optimization)

B. [Sequential Logic Optimization](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#sequential-logic-optimization)

### Combinational Logic Optimization

Combinational logic optimization is necessary to improve the power, performance area of the design.

For combinational logic below optimization techniques are use:
  
**1. Constant propagation:** This means when cobinational logic is propgating a constant value irrespective of any chnages 
       in the input signal.

**2. Boolean optimization:** Thre are various boolean optimization techniques that can be used to optimize the logic such as Min/Max, K-map etc.

     
	Below steps are followed to synthesize the design for optimization

 		yosys> read_liberty -lib <sky130_fd_sc_hd__tt_025C_1v80.lib>
		yosys> read_verilog <name of the verilog file: opt_check.v>
		yosys> synth -top <name of the top module>
		yosys> opt_clean -purge  (This command identifies unused signals and cells and removes them from the design)   
		yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
		yosys> show



    
  **1. Opt_check.v:**
	
		module opt_check (input a, input b, output y)
			assign y = a?b:0;
   		endmodule

  Synthesis Output:

![show_iopt check](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/07f0d297-8a02-4559-8776-024b6e424552)
  		




  **2. Opt_check2.v:**
	
		module opt_check (input a, input b, output y)
			assign y = a?1:b;
   		endmodule



  Synthesis Output:

![optcheck2_show](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/e2bd8778-617f-4b06-afa0-590b6833cf75)


  
  
  
  **3. Opt_check3.v:**
	
		module opt_check (input a, input b, input c, output y)
			assign y = a?(c?b:0):0;
   		endmodule

	Optimization:
		y = a!. 0 + a (c!. 0 + c.b)
		y = abc




  Synthesis Output:

 
![opt_check3](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/1bb1d9a6-a2aa-494e-b982-ac590914b048)





  **4. Opt_check4.v:**
	
		module opt_check (input a, input b, input c, output y)
			assign y = a?(b?(a & c):c):(!c);
   		endmodule

  Synthesis Output:

![show_optcheck 4](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/a3ea435c-18eb-4efc-8efb-4312f9cfd846)


  **5. Multiple_Modules_opt.v:**
	
		module opt_check (input a, input b, output y)
			assign y = a?b:0;
   		endmodule

  Synthesis Output:

![flatten opt multiple](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/6827e2bc-eba1-4c9a-9a63-17b1a1449bad)

  		
 
  **6. Multiple_Modules_opt2.v:**
	
		module opt_check (input a, input b, output y)
			assign y = a?b:0;
   		endmodule

  Synthesis Output:


![mul_module_opt2](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/58fe37c0-52ac-4ad5-bc01-2e2918624708)
  		
 
 


### Sequential Logic Optimization

Sequential logic optimization is used for below scenarios:

**1. Constant Propagation**- Which means when constant value is propagated through the Flop. 

**2. state optimization**- when unused states of the design are optimized.
    
**3. retiming**- when there is a lrage positive slack, combinational logic between two flip flops is split and another Flip flop is added to remove the negative slack decrease timing of the different logic portions and increase frequency. 

**4. sequential logic cloning**- This is used for physical aware synthesis to optimize the floop plan. (Physical aware synthesis takes logical representation of the design and transforms it into a physical layout that can be manufactured which involves differnet stpes such as floorplan, placment and routing)



	Below steps are followed to synthesize the design for optimization

 		yosys> read_liberty -lib <sky130_fd_sc_hd__tt_025C_1v80.lib>
		yosys> read_verilog <name of the verilog file: opt_check.v>
		yosys> synth -top <name of the top module>
		yosys> opt_clean -purge  (This command identifies unused signals and cells and removes them from the design)   
  		yosys> dfflibmap -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
		yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
		yosys> show


  

  **1. DFF CONST1:**

 			module dff_const1(input clk, input reset, output reg q);
			always @(posedge clk, posedge reset)
			begin
				if(reset)
					q <= 1'b0;
				else
					q <= 1'b1;
			end
			
			endmodule

  Synthesis:


![dff_const1_synth op](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/e93c5b27-02f7-4b5d-9614-5ae2a205d4f9)
	

  ![dff_const1_show_active low reset](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/5401f0d6-8d7d-4fa4-9a76-d9594703e985)



  **2. DFF const2:**

 			module dff_const1(input clk, input reset, output reg q);
			always @(posedge clk, posedge reset)
			begin
				if(reset)
					q <= 1'b1;
				else
					q <= 1'b1;
			end
			
			endmodule

  Simulation:

![dff_const2_synth op](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/65067592-45e4-44a7-9e1c-f91a78107b4e)



 ![dff_const2 show](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/efe66d20-6ffe-44cf-9c8a-53eda333d449)
 	

  Synthesis:
  	
![dff_const2_q_1](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/f5fc818a-e1af-4a14-acd6-ad3d0177f751)



  **3. DFF CONST3:**

			module dff_const3(input clk, input reset, output reg q);
			reg q1;
			
			always @(posedge clk, posedge reset)
			begin
				if(reset)
				begin
					q <= 1'b1;
					q1 <= 1'b0;
				end
				else
				begin
					q1 <= 1'b1;
					q <= q1;
				end
			end
			
			endmodule

  Simulation:


  Synthesis:

![dff_const3](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/b813e3be-c1fe-4b5f-a78c-d18ce750e8b6)



  **4. DFF CONST4:**

            module dff_const3(input clk, input reset, output reg q);
			reg q1;
			
			always @(posedge clk, posedge reset)
			begin
				if(reset)
				begin
					q <= 1'b1;
					q1 <= 1'b1;
				end
				else
				begin
					q1 <= 1'b1;
					q <= q1;
				end
			end
			
			endmodule


   Simulation:

![dff_const4_always 1](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/456a3d2e-4376-4e0e-9057-59700fdf992b)


   Synthesis:

![dff4_show](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/85d30edc-0458-45d9-824e-14677f0a7210)
     
   	
   
  **DFF CONST5:**

	  
		module dff_const5(input clk, input reset, output reg q);
		reg q1;
		
		always @(posedge clk, posedge reset)
		begin
			if(reset)
			begin
				q <= 1'b0;
				q1 <= 1'b0;
			end
			else
			begin
				q1 <= 1'b1;
				q <= q1;
			end
		end
		
		endmodule



  Simulation:

![dff_const5](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/66253d6e-c131-4260-824e-9f087240b099)
  	

  Synthesis:
  
![dff5_show](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/4473d22b-ebbc-4eff-a8bd-e5eef7b251f8)

  

  **Counter opt**
  
    Here, the ourput of the 2bit counter changes from 000 to 111 and again starts from 3'b0.
    But, as we have assigned the output q only to the bit 0 (count [0]).
	The synthesis tool will only one flip flop and optimize the other logic as the primary output is dependent on only one bit.

 		module counter_opt (input clk , input reset , output q);
		reg [2:0] count;
		assign q = count[0];
		
		always @(posedge clk ,posedge reset)
		begin
			if(reset)
				count <= 3'b000;
			else
				count <= count + 1;
		end
		
		endmodule



  Synthesis:

![counter_opt_show](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/fd166794-a0be-4dd3-bb4f-527410188b71)





  **Counter opt 2**

  Here, we have assigned the output q only to all the 3 bits of the count for specific value (q is high when count = 3'b100)
	The synthesis tool will infer 3 flip flops as the primary output of the flip flop depends on 3 bits.

 
		module counter_opt (input clk , input reset , output q);
		reg [2:0] count;
		assign q = (count[2:0] == 3'b100);
		
		always @(posedge clk ,posedge reset)
		begin
			if(reset)
				count <= 3'b000;
			else
				count <= count + 1;
		end
		
		endmodule



  Synthesis:


  ![counter_opt2_show](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/1fcc448f-3a8d-4ecc-9ac6-504a3c11f977)




## Day 4

### Gate Level Simulation

When we write RTL code, we use a test bench to validate it by providing the test vectors.
Gate Level Simulation (GLS) is necessary to verify the logical correctness of the design once synthesis is completed. The netlist is provided to the simulation tool along with the testbench. 
  
There are two types of vaildation that can be ensured by the GLS:

  1. Functional: Functional validation is necessary and it checks the conditions such as **Missing sensitivity list** and **Use of blocking and non-blocking assignments**
 
  2. Timing Aware: This type of validation checks functionality as well as ensure timing is met. (for different corners)


  **1. RTL, Simulation and Synthesis of Ternary Operator along with GLS Simulation:**

	Below RTL code is used for the Ternary operator example

  		module ternary_operator_mux (input i0, input i1, input sel, output y);
    			assign y = sel?i1:i0;
       		endmodule

  For Simulation below commands are used:

    	iverilog <name verilog: ternary_operator_mux.v> <name testbench: tb_ternary_operator_mux.v>
	./a.out
	gtkwave tb_ternary_operator_mux.vcd

  Simulation result:
 		As mentioned in the code output y will be loaded with value of input i1 when select line(sel) is 1. When Sel is 0, output y will be loaded with the i0 value.


![show_ternary](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/be6aca4a-45b0-4dee-9f0a-9f451f7c7869)


   
Synthesis is performed with 

   		yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
		yosys> read_verilog <name of verilog file: ternary_operator_mux.v>
		yosys> synth -top <name of the top module: ternary_operator_mux>
		yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
		yosys> write_verilog -noattr <name of netlist: ternary_operator_mux_net.v>
		yosys> show



![ternary_show](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/54a0db37-7e22-4d28-998f-591122060a6c)



Steps followed for GLS simulation:

  	Verilog files related to standard cells are provided for the simulation.
    
   		iverilog <path to verilog model: ../mylib/verilog_model/primitives.v> <path to 		 
                sky130_fd_sc_hd__tt_025C_1v80.lib: ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib> <name netlist:ternary_operator_mux_net.v> <name testbench: 				tb_ternary_operator_mux.v>
		./a.out
		gtkwave tb_ternary_operator_mux.vcd
  
  Synthesis Simulation result:
 		As shown in the image, output y follows the correct behavior of the input signal similar to the RTL simulation. Hence, Synthesis simulation and RTL 
  simulation are correct for this design.
  	

![GLS_netlist_ternary](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/d1419d6b-4a17-4d45-b1f2-3bddd3a001e8)



	


  **2. RTL, Simulation and Synthesis of Bad mux along with GLS Simulation:**
	Here, an example of 2:1 Mux is shown which will give mismatch between RTL simulation and Gate level simulation as there is a missing sensitivity list.

  	
  Below RTL code is used for the 2:1 Mux:
	Here, inside the sensitivity list all the inputs are not mentioned, hence during the simulation the output will only change if sel changes.
 	So, the simulation shows the behavior like a flop where output changes when sel is 1 or 0.


  		module bad_mux (input i0 , input i1 , input sel , output reg y);
		always @ (sel)
		begin
			if(sel)
				y <= i1;
			else 
				y <= i0;
		end
		endmodule



For Simulation below commands are used:

    		iverilog <name verilog: ternary_operator_mux.v> <name testbench: tb_ternary_operator_mux.v>
		./a.out
		gtkwave tb_ternary_operator_mux.vcd

Simulation result:
 	As observed in the fig, the output y doesn't change when sel doesn't change even if i0 and i1 changes, this is not the actual behavior of the mux.


<img width="511" alt="bad_mux_rtl" src="https://github.com/Sourabhk20/vsd-hdp/assets/148907305/62679e15-f66d-4def-a983-a2fe5900f6f9">


   
Synthesis is performed with below commands:

   		yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
		yosys> read_verilog <name of verilog file: bad_mux.v>
		yosys> synth -top <name of the top module: bad_mux>
		yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
		yosys> write_verilog -noattr <name of netlist: bad_mux_net.v>
		yosys> show




![bad_mux show](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/cba7c1c1-1232-4051-9f01-3bf736081af0)



Steps followed for GLS simulation:

  		Verilog files related to standard cells are provided for the simulation.
    
   		iverilog <path to verilog model: ../mylib/verilog_model/primitives.v> <path to sky130_fd_sc_hd__tt_025C_1v80.lib: 				 
                ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib> <name netlist: bad_mux_net.v> <name testbench: tb_bad_mux.v>
		./a.out
		gtkwave tb_bad_mux.vcd


  
Simulation result after synthesis:

  	

![bad_mux_always_netlist](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/29551b0c-7f0c-4226-8acc-eeb43947e544)












**3. RTL, Simulation and Synthesis of Blocking assignment along with GLS Simulation:**

  Here, an example of blocking assignment and effect of blocking assignmnet on the output. As shown in the code the 1st statement (y = q & C) will be evaluated before the 
  next statement (q = a | b). So, when evalauting y, it will consider previous value of the q and hence this design will not observe the intended behavior.
  	
Below RTL code is used for the Blocking assignment:
  Here, inside the sensitivity list all the inputs are not mentioned, hence during the simulation the output will only change if sel changes.
  So, the simulation shows the behavior like a flop where output changes when sel is 1 or 0.


  		module bad_mux (input a , input b , input c , output reg y);
    		wire q;
		always @ (*)
		begin
			y = q & c;
   			q = a | b;
		end
		endmodule



For Simulation below commands are used:

    		iverilog <name verilog: blocking_caveat.v> <name testbench: tb_blocking_caveat.v>
		./a.out
		gtkwave tb_blocking_caveat.vcd

	
 
Simulation result:
 
![bloking_gtkwave](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/63fb22c4-1191-48fb-9aec-6a167deed942)



   
Synthesis is performed with below commands:

   		yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
		yosys> read_verilog <name of verilog file: blocking_caveat.v>
		yosys> synth -top <name of the top module: blocking_caveat>
		yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
		yosys> write_verilog -noattr <name of netlist: blocking_caveat_net.v>
		yosys> show



![blocking_show](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/ba17fe6e-64dd-48fe-bdbe-f11fc6596184)



Steps followed for GLS simulation:

  		Verilog files related to standard cells are provided for the simulation.
    
   		iverilog <path to verilog model: ../mylib/verilog_model/primitives.v> <path to 		 
                sky130_fd_sc_hd__tt_025C_1v80.lib: ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib> <name netlist: blocking_caveat_net.v> <name testbench: tb_blocking_caveat.v>
		./a.out
		gtkwave tb_blocking_caveat.vcd
  
  
Simulation result:

   
![bloking_gtkwave_netlist_stnthesis_simulation](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/534f316b-2458-44e6-b289-ec6d413a0676)





# Day 5

## Introduction to RISC-V ISA and GNU Compiler Toolchain

**LAB 1**
	a. Write a C program to compute Sum from 1 to N.

  	b. Run this program using RISC-V simulator.
   		Below commands are used to run this:

			riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o sum1ton.o sum1ton.c
   			Here,
      			riscv64-unknown-elf-gcc = to invoke the gcc compiler
	 		-O1 = Specifies the optimization level. (O1 is moderate level of optimization and Ofast is highest level of optimization
    			-mabi = This flag specifies the ABI (Application Binary Interface) to be used. In this case, it is set to lp64, which stands for "long 				and pointer 64-bit." This ABI defines the sizes of basic C types, such as int and pointers. lp64 means that int is 32 bits, and 				pointers and long types are 64 bits.
       			-march = specifies the target RISCV architecture and ISA, rv64i means a 64-bit RISC-V architecture with the base integer ISA.
	  		-o = output file name and format
     			-c = input source file and name

  		Assembly code for the C program:
    
				d - dissassemble 
       			riscv64-unknown-elf-objdump -d sum1ton.o | less

  		Leramed how to find the number of instrcutions from this assembly code.
    			
![sum1ton_main_O1](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/6de636b4-b640-421a-9bfb-c23ee9908029)

	
	  
   
  
**LAB3**:
	Debug, we want to find the way how to perform a.out on the RISC-v compiler.
 	spike

Sum 1 to N, run it with the Ofast and O1 option where, O1 is the moderate level of optimizationand O1 is highest level of optimization.
 

 ![sum1ton_main_Ofast](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/4e81253c-5153-436d-94cb-f469b24007c0)

  
1. luipc: load uppper immediate, bit 12 to 31 is filled with the contents mentioned in the instruction
	stack , 16 dec, 10 hex subtarcted from the pointer


Spike debugger output showing the contents of stack pointer got updated.

![spike_debug instructions_stack pointer](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/c9b261a1-013c-46b2-a149-ce38f587fed6)





**Lab** : **C and assembly program to count sum from 1 to N**

	C Code:

   		#include <stdio.h>

		extern int load(int x, int y);
		
		int main() {
			int result = 0;
			int count = 9;
			result = load(0x0, count+1);
			printf("Sum of number from 1 to %d is %d \n", count, result);
		}



  	Assembly code:

			.section .text
			.global load
			.type load, @function
			
			load: 
				add a4, a0, zero 	// Intialize sum register a4 with 0x0
				add a2, a0, a1 		// Store count of 10 in register a2. Register a1 is loaded with 0xa (decimal 10) from main
				add a3, a0, zero 	// Initialize intermediate sum register a3 by 0
			loop:	add a4, a3, a4		// Incremental addition
				addi a3, a3, 1		// Increment intermediate register by 1
				blt a3, a2, loop 	// If a3 is less than a2, branch to label named <loop>
				add a0, a4, zero  	// Store final result to register a0, so that it can be read by main program
				ret


# Day 6

## Introduction to ABI and basic verification flow



# Day 7 

## Digital Logic with TL- Verilog and Makerchip
Makerchip is an online platform which provides instant access to the open source tools from the browser which is developed by Redwood EDA.
It allows us to code, compile, simulate, and debug Verilog designs, all from the browser. We can simulate the design and obesrve the bock diagrams as 		well as waveforms, and novel visualization capabilities of our design.

Makerchip uses Transaction level verilog is advanced version to the verilog and provides a simple syntax and also allows for pipelines and transactions.

### Combinational Logic uisng Makerchip

	Understood the basic combinational circuits and logic gates along with thier implementtaion in TL verilog.

 	1. Vector: 
  		Addition of two input vectors and store it in an output vector.

![Vectors](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/13e0a2ae-1536-48fa-b93a-62c8fcec2ebe)

	2. Mux: 
 		Verified the functioanlity with waveform as well as circuit for different Logic gates and Mux.

![mux ](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/de3a129c-ef5a-47ea-8bea-e5b8cb4351d3)


	3. Combinational caluclator

 ![image](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/8033e385-5225-424d-a55b-a2a3bcdf06ff)


  		
  	
		



### Sequential Logic uisng Makerchip

	1. Counter:	
  		In TL verilog to add a previous value to the next (>>1 means ahead by 1 and will be conisdred for the next cycle)

![counter](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/dbb42f3e-18fe-45f2-9219-49728d7c32ad)

	2. Sequential Calculator:

		Output value is fed back to the value 1 by giving the feedback loop from out to val1 input.
   		Based on the value of op signal (2 bits), either sum, diff, prod or quot is calculated.
     
![seq calculator feedback new](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/6114d371-108f-46f1-8935-d16901f5362e)

    		Seq Calc:

      				\m5_TLV_version 1d: tl-x.org
				\m5
				   
				   // =================================================
				   // Welcome!  New to Makerchip? Try the "Learn" menu.
				   // =================================================
				   
				   //use(m5-1.0)   /// uncomment to use M5 macro library.
				\SV
				   // Macro providing required top-level module definition, random
				   // stimulus support, and Verilator config.
				   m5_makerchip_module   // (Expanded in Nav-TLV pane.)
				\TLV
				   $reset = *reset;
				   
				   $val1[31:0] = >>1$out[31:0];
				   $val2[31:0] = $rand2[3:0];
				   $op[1:0] = $rand3[3:0];
				   
				   //Save power by selcting perofrming operations we need to perform
				   $sum[31:0] = $val1[31:0] + $val2[31:0];
				   $diff[31:0] = $val1[31:0] - $val2[31:0];
				   $prod[31:0] = $val1[31:0] * $val2[31:0];
				   $quot[31:0] = $val1[31:0] / $val2[31:0];
				   
				   $out[31:0] = $reset ? 32'b0 : (($op[1:0] == 2'b00) ? $sum : 
				                                 ($op[1:0] == 2'b01) ? $diff :
				                                    ($op[1:0] == 2'b10) ? $prod :
				                                       $quot);
				\SV
				   endmodule




	Seq calc with counter:

 			WHen we add counter logic to the existing sequential calcultor we get,

   				\m5_TLV_version 1d: tl-x.org
				\m5
				   
				   // =================================================
				   // Welcome!  New to Makerchip? Try the "Learn" menu.
				   // =================================================
				   
				   //use(m5-1.0)   /// uncomment to use M5 macro library.
				\SV
				   // Macro providing required top-level module definition, random
				   // stimulus support, and Verilator config.
				   m5_makerchip_module   // (Expanded in Nav-TLV pane.)
				\TLV
				   |calc
				      
				      @0
				         
				         $reset = *reset;
				
				         $val1[31:0] = >>1$out[31:0];
				         $val2[31:0] = $rand2[3:0];
				         $op[1:0] = $rand3[3:0];
				
				         //Save power by selcting perofrming operations we need to perform
				         $sum[31:0] = $val1[31:0] + $val2[31:0];
				         $diff[31:0] = $val1[31:0] - $val2[31:0];
				         $prod[31:0] = $val1[31:0] * $val2[31:0];
				         $quot[31:0] = $val1[31:0] / $val2[31:0];
				
				         $out[31:0] = $reset ? 32'b0 : (($op[1:0] == 2'b00) ? $sum : 
				                                       ($op[1:0] == 2'b01) ? $diff :
				                                          ($op[1:0] == 2'b10) ? $prod :
				                                             $quot);
				                                             
				         $cnt = $reset ? 0 : (>>1$cnt + 1);
				\SV
				   endmodule



	

 	3. Cycle Calculator:
			perform operations every other cycle.


   ![cycle_calc_valid](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/2bff9eee-5dc5-4431-a2be-ba93571a3729)


    				\m5_TLV_version 1d: tl-x.org
				\m5
				   
				   // =================================================
				   // Welcome!  New to Makerchip? Try the "Learn" menu.
				   // =================================================
				   
				   //use(m5-1.0)   /// uncomment to use M5 macro library.
				\SV
				   // Macro providing required top-level module definition, random
				   // stimulus support, and Verilator config.
				   m5_makerchip_module   // (Expanded in Nav-TLV pane.)
				\TLV
				   |calc
				      
				            
				      
				
				      @1
				         $reset = *reset;
				          
				         $val1[31:0] = >>2$out[31:0];
				         $val2[31:0] = $rand2[3:0];
				         $op[1:0] = $rand3[3:0];
				
				         //Save power by selcting perofrming operations we need to perform
				         $sum[31:0] = $val1[31:0] + $val2[31:0];
				         $diff[31:0] = $val1[31:0] - $val2[31:0];
				         $prod[31:0] = $val1[31:0] * $val2[31:0];
				         $quot[31:0] = $val1[31:0] / $val2[31:0];
				      
				         $valid = $reset ? 0 : (>>1$valid + 1);
				         
				         
				      @2
				         $out[31:0] = ($reset | !$valid) ? 32'b0 : (($op[1:0] == 2'b00) ? $sum : 
				                                       ($op[1:0] == 2'b01) ? $diff :
				                                          ($op[1:0] == 2'b10) ? $prod :
				                                             $quot);
				                                       
				                                       
				         
				         //we need 2 cycyles to perform
				\SV
				   endmodule


	4. Valid bit:
 		There will be output only if the signal is valid which is executed by valid bit.


![pythagores waveform valid](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/68046ce7-4c27-4013-805f-840e62645b8d)

   



# Day 8

## RISC-V Microarchitecture

   Block diagram of RISCV microarchitecture: 
   
<img width="739" alt="rfread op" src="https://github.com/Sourabhk20/vsd-hdp/assets/148907305/e49fa77e-abcc-40bf-9ba9-9bd8183cf0d8">

   

 To implement RV32I Base Instruction set.
 



  **1. Fetch**
     PC holds the address of the next instruction
     Here, if earlier instruction is reset then for the next instruction PC address should start at zero and for other case it is incremented by 32'd4.
     The program counter holds the address of the next instruction execution and PC loads the address conent into the instruction memory.

   ![fetch waveform](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/1c2b794b-b5fa-4471-bc34-73ec62bfdcb8)


   **2. Decode**
   	Based on the opcode provided by the instruction, the instruction is divided into various register types such as R-type, I-type, S-type, B-type, U-type and J-type.

<img width="719" alt="ISA" src="https://github.com/Sourabhk20/vsd-hdp/assets/148907305/70c3913f-bc3a-4a6b-8c8e-e2888ac5a787">


![Instr decode ](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/0845527c-11e0-4c36-a771-f93aacbcc229)



    
   Makerchip output block diagram after implementing fetch and decode operations.


![block diagram](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/b204d673-d254-4057-952c-3679056cf25c)




**Control Logic**

     Reg file read and write operation:
     	 Intruction 


![block diagram](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/f71e5077-4a37-425e-a7d4-860f84ee1ec9)




**3 Stage Pipeline**

	Block diagram:

<img width="863" alt="pipeline3" src="https://github.com/Sourabhk20/vsd-hdp/assets/148907305/309192a6-915d-43b5-9510-2011c23067ea">


 	Makerchip output block diagram after implementing 3 stage pipelining

  ![image](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/76399307-885c-4164-ac34-08a32bf46943)



# Day 9

## Solutions to the pipeline hazards:
	Implementers register file bypass for Read After Write (RAW) hazard where the write data will be provided to the next instruction if that instruction is dependent on the previous hazard.

Register file bypass:
![image](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/0655fc77-2567-4ef1-9af4-61cb287e2538)

	Updtaed the logic of Program Counter (PC) for branch instructions during pipeline hazards.
	Implemented all the instructions related to ALU operations which will be performed at execute stage.


 ## Load Store Implementation

 Implemneted the load and store instructions which will be executed at 4th and 5th stage respectively.

 	LOAD rd, imm(rs1)
  		Loads data from the memory address (address given by imm + rs1) into the register rd.
    	STORE rs2, imm(rs1)
  		Stores the content of rs2 into the memory address (rs2 + imm)

Implemented Data memory logic which uses dmem macro and executed at 4th stage of the pipeline.
Data memory has 1 read write op (dmem_rd_data) and store data from rs2 to memory. This memory has 16 entries, 32 bits wide and it needs 4-bit address to access the data memory. This address is provided by the result bits [5:2] and we can perform write operation.


**Complete RISC-V core implementation**

![image](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/1deea9dc-4304-4d92-ae56-24135c47cdbe)


  		





