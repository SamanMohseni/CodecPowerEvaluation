# Codec Power Evaluation
This project is part of the **Fault-Tolerant System Design** course.

Verilog implementation of three different codec units and the evaluation of their power consumption using Design Compiler on TSMC 180nm technology.

## Project Overview
This project conducts a detailed analysis of the energy and power consumption for fault-tolerant coding using three different coding methods for a 16-bit register: Triple Modular Redundancy (TMR), Vertical Horizontal Overlapped Parity (VHOP), and Hamming Code. The goal is to code a 16-bit register using each method and provide insights into the power/energy consumption of each implementation. 

## Repository Structure and Contents
The repository is organized with the following directory structure to facilitate the design process, simulation, and analysis of each fault-tolerant circuit:

- `TMR`, `VerticalHorizontal`, `Hamming`: Directories containing the respective circuit design files, including Verilog source code and analysis outputs.
- `db`: Houses the TSMC 180nm technology library file in .db format, which is used for the circuit synthesis and simulation processes.
- `source`: Includes the Verilog files describing the design of the fault-tolerant circuits.
- `output`: Contains the results of the synthesis including timing, area, and power analysis data generated by the Design Compiler.
- `test`: Stores the testbench files for circuit simulation in Modelsim and the netlist files generated from the synthesis process.
- `work`: Temporary directory for files created during the execution of the Design Compiler.
- `activity`: Contains VCD files from Modelsim simulations and SAIF files used for power analysis.
- `script.src`: Script files executed by the Design Compiler for synthesizing the designs.
- `modelsim_transcript.txt`: Contains Modelsim terminal commands for running simulations.
- `post_script.scr`: Scripts executed at the end of the Design Compiler process to finalize the analysis.

## Design Compiler Workflow
To perform the analysis, the following steps are executed for each of the circuit methodologies:

1. **Launch Design Compiler**: Start the graphical interface of the Design Compiler by entering `design_vision` in the terminal.

2. **Synthesis Script Execution**: Navigate to `File`, select `Execute Script…`, and open the `script.src` file. This script synthesizes the design and generates timing and area data in the `output` folder. The synthesized netlist is saved in the `test` directory. Keep the Design Compiler open for later steps.

3. **Modelsim Simulation**: To determine the 'activity' of the circuit under test conditions, the files located in the `test` directory are simulated using Modelsim. This directory contains the synthesized netlist (`netlist.v`), the Verilog technology library (`tsmc18_neg.v`), and the testbench file (`Test.v`). Follow these steps to perform the simulation:

   a. Launch Modelsim by entering the `vsim -novopt` command in a new terminal window.
   
   b. In Modelsim, go to the `Compile` menu and select `Compile…`. Navigate to the `test` directory, select all files within it, and compile them.
   
   c. If prompted to create a `work` directory, confirm by clicking `Yes`, and then `Done` once the directory is created.
   
   d. After the compilation is complete, go to the `Simulate` menu and select `Start Simulation…`.
   
   e. From the `work` directory within the simulation dialogue, choose the `Test` file and click `OK`. If the `Enable optimization` option is checked, uncheck it.
   
   f. Add all signals to the wave window by selecting them in the `Objects` section, right-clicking, and choosing `Add > To Wave > Selected Signals`.
   
   g. Copy the commands from the `modelsim_transcript.txt` file and paste them into the Modelsim `Transcript` window. The commands include several steps:
      i. Begin with a warm-up phase to obtain more accurate activity results without saving signal changes by running `run 100ns`.
      ii. Create a `activity.vcd` file in the `activity` directory with the next command.
      iii. Prepare the signal activities to be added to the `activity.vcd` file with the following command.
      iv. Execute the `run 10us` command to simulate for 10 microseconds, writing the signal activities to the `activity.vcd` file.
   
   h. Exit Modelsim after the simulation is complete. (The simulation results for all circuits will be discussed in the following sections of the report.)

By following these detailed steps, you can simulate the synthesized netlist for each fault-tolerant coding technique in Modelsim. The output VCD file captures the signal activities which are essential for subsequent power analysis using the Design Compiler.

4. **Activity File Conversion**: Convert the VCD file from Modelsim to a SAIF file using the `vcd2saif -i activity.vcd -o activity.saif` command in the `activity` directory.

5. **Power Analysis**: Return to the Design Compiler and execute the `post_script.scr` script to perform the power analysis using the SAIF file. The power consumption data is then saved in the `output` folder.

## Design and Power Analysis Summary
Each fault-tolerant coding strategy is implemented, simulated, and analyzed, with particular emphasis on correcting a single bit error:

- **TMR**: Involves triplicating the input data and voting to correct errors. Despite its simplicity, it may have higher dynamic power due to the increased capacitive load of the 48-bit output between the Coder and Decoder.

- **Vertical Horizontal Overlapped Parity**: Implements horizontal and vertical parity coding, resulting in a 24-bit output from the encoder. Its power consumption is comparable to TMR when considering leakage power.

- **Hamming Code**: Features a more complex design with parity bits calculated for specific data bit positions, leading to the highest power consumption among the three methods due to its complexity.

## Conclusion
The analysis of the power consumption data reveals several important insights into the performance and efficiency of the three fault-tolerant circuit designs under consideration.

The Hamming Code, with its relatively complex design, results in the highest power consumption. This is likely due to the intricate way in which parity bits are calculated and placed within the encoded data, which requires a larger number of gates and consequently more power to operate.

Following the Hamming Code, the Triple Modular Redundancy (TMR) design exhibits the next highest level of power consumption. Notably, TMR is the simplest of the three designs, suggesting that its higher power consumption relative to Vertical Horizontal Overlapped Parity (VHOP) is not due to circuit complexity. Instead, the increased power usage can be attributed to the larger number of wires in the output of the Coder and the input of the Decoder (48 bits in total). The capacitive load introduced by these wires plays a significant role in the power consumed by the circuit, particularly in terms of dynamic power.

When considering leakage power, the power consumption of TMR becomes nearly equivalent to that of VHOP. This is an interesting observation, as it implies that the simpler circuitry of TMR, which uses fewer transistors, results in lower leakage power. Leakage power is less dependent on activity and more on the static characteristics of the circuit, such as transistor count and sizing.

VHOP, with its intermediate level of power consumption when considering only dynamic power, becomes more competitive with TMR when leakage power is taken into account. The fact that VHOP and TMR have comparable power usages, despite the former's additional parity computation complexity, highlights the impact of capacitive loads and transistor count on overall power consumption.

In summary, the fault-tolerant coding techniques each have their own trade-offs regarding complexity and power efficiency. The Hamming Code offers a sophisticated error correction capability at the cost of higher power consumption, while TMR, despite its simplicity, can still consume a significant amount of power due to capacitive loading effects. VHOP presents a middle ground, balancing complexity and power usage, with its performance being comparable to TMR when leakage power is factored in. These findings are crucial for students and practitioners in the field as they consider the design of fault-tolerant systems, highlighting the need to optimize both circuit design and layout to minimize power consumption while maintaining the desired level of fault tolerance.
