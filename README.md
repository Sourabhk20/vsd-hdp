# vsd-hdp

Author: Sourabh Kulkarni

i. [Day 0](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#day-0)   
ii. [Day 1](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#day-1)  
iii. [Day 2](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#day-2)   
iv. [Day 3](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#day-3)


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


C. [Multiplier] (https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#2-bit-multiplier-and-3-bit-multiplier)
### Timing Libs, Hierarchical vs Flat Synthesis

*Below steps are followed while simulating the Multiple Module design for Hierarchical vs Flat Synthesis*

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

	**Multiple module opt netlist**

			 		/* Generated by Yosys 0.34+43 (git sha1 d21c464ae, gcc 9.4.0-1ubuntu1~20.04.2 -fPIC -Os) */
			
			module multiple_module_opt(a, b, c, d, y);
			  wire _0_;
			  wire _1_;
			  wire _2_;
			  wire _3_;
			  wire _4_;
			  wire \U1.y ;
			  input a;
			  wire a;
			  input b;
			  wire b;
			  input c;
			  wire c;
			  input d;
			  wire d;
			  output y;
			  wire y;
			  sky130_fd_sc_hd__a21o_1 _5_ (
			    .A1(_2_),
			    .A2(_1_),
			    .B1(_3_),
			    .X(_4_)
			  );
			  assign _2_ = b;
			  assign _3_ = c;
			  assign y = _4_;
			  assign _1_ = a;
			endmodule








	**Multiple_module opt_2 netlist**
			 			/* Generated by Yosys 0.34+43 (git sha1 d21c464ae, gcc 9.4.0-1ubuntu1~20.04.2 -fPIC -Os) */
			
			module multiple_module_opt2(a, b, c, d, y);
			  wire _0_;
			  wire _1_;
			  wire _2_;
			  wire _3_;
			  wire _4_;
			  wire \U1.y ;
			  wire \U2.y ;
			  wire \U3.y ;
			  input a;
			  wire a;
			  input b;
			  wire b;
			  input c;
			  wire c;
			  input d;
			  wire d;
			  output y;
			  wire y;
			  assign _4_ = 1'h0;
			  assign _0_ = a;
			  assign _2_ = c;
			  assign _1_ = b;
			  assign _3_ = d;
			  assign y = _4_;
			endmodule

 	

**Sequential Opt**

	DFF CONST1:

 			module dff_const1(input clk, input reset, output reg q);
			always @(posedge clk, posedge reset)
			begin
				if(reset)
					q <= 1'b0;
				else
					q <= 1'b1;
			end
			
			endmodule



	DFF const2:

 			module dff_const1(input clk, input reset, output reg q);
			always @(posedge clk, posedge reset)
			begin
				if(reset)
					q <= 1'b1;
				else
					q <= 1'b1;
			end
			
			endmodule



	DFF CONST3

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





   	DFF CONST5

		      		
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


          

**Counter opt**

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



  **Counter opt 2**

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










