# vsd-hdp

Author: Sourabh Kulkarni

i. [Day 0](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#day-0)   
ii. [Day 1](https://github.com/Sourabhk20/vsd-hdp/blob/main/README.md#day-1)


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

### Mux 2:1 (Behaviroal Level modeling)

**Learning how to use iverilog, gtkwave and yosys for RTL Design, simulation and Synthesis**

*Below steps are followed while simulating the design:*
1. iverilog:
       Verilog code for mux is written using below commands

           $ iverilog design_verilog.v tb_design_verilog.v
           $ ./a.out
   Dumpfile is generated using gtkwave
           $ gkwave design.vcd 

    RTL Design:
     
       module good_mux (input i0, input i1, input sel, output reg y);
           always @ (*)
           begin
               if(sel)
                 y <= i1;
               else
                 y <= i0;
               end
           endmodule

    Testbench:


   	
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


2. gtkwave simulation waveform:
  

   	 <img width="634" alt="gtkave_waveform" src="https://github.com/Sourabhk20/vsd-hdp/assets/148907305/2a84e1f6-d11b-46e4-ae6f-f8e811f90641">



3. Synthesis:
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




	
  		

  		
          









