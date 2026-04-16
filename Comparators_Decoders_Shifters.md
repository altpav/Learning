For A `==` B:
The silicon has no concept of number and to check if two 4 bit numbers are equal, for exmaple, we must use an `XNOR` gate.
It outputs 1 only if both inputs are the same 0,0 or 1,1.

In 64 bit CPU we cannot built a 64 bit `AND` gate. It would be slow so engineers built an `AND Tree`. It compares pairs of bits and then pairs of pairs.
The time it takes for electricity to ripple through this tree is called Critical Path. If its too long the CPU's clock speed will drop.

To check `>` the hardware actually substracts. It checks borrow out bit of the adder.

```
   $a[3:0] = $rand_a[3:0];
   $b[3:0] = $rand_b[3:0];
   
   // Step A: Bitwise XNOR (no ==)
   $xnor_bits[3:0] = ~($a ^ $b);
   
   // Step B: The AND Tree (Are ALL bits identical?)
   // In real silicon, this avoids massive fan in(no. of inputs feeding into the gate) gates.
   $eq_stage1_0 = $xnor_bits[0] & $xnor_bits[1];
   $eq_stage1_1 = $xnor_bits[2] & $xnor_bits[3];
   
   // Output
   $is_equal_gate_level = $eq_stage1_0 & $eq_stage1_1;
```
------------------------------------------------


Barrel Shifter: If we shift a constant number like 1, it requires zero logic gates and zero delay. It is pure wire routing.
It is a matrix of maultiplexers (MUX). Stage 1 MUx, Stage 2 MUX, Stage 3 MUX. If we want to shift by 3, hardware tell Stage 1 to shift by 1, Stage 2 to shift by 2.

```
   // How to shift a 4-bit number by a variable 0, 1, 2, or 3.
   $shift_amt[1:0] = $rand_shift[1:0];
   
   // Stage 0: Shift by 1? (Controlled by shift_amt bit 0)
   // ? : is our physical Multiplexer (MUX)
   $stg0_out[3:0] = $shift_amt[0] ? { $a[2:0], 1'b0 }  // Shifted by 1
                                  : $a[3:0];           // Passed through (Shift by 0)

   // Stage 1: Shift by 2? (Controlled by shift_amt bit 1)
   // Takes the output of Stage 0, and potentially shifts it by 2 more.
   $stg1_out[3:0] = $shift_amt[1] ? { $stg0_out[1:0], 2'b00 } // Shifted by 2
                                  : $stg0_out[3:0];           // Passed through
                                  
   // The final output dynamically shifted by 0, 1, 2, or 3!
   $dynamic_shift_result[3:0] = $stg1_out;
```
-----------------------------------------------------

It works if we want to compare A and B but what if we want to compare A to one million different B's and we have 1 clock cycle in total?

Standard RAM works as we give it an address and it returns the data whereas Content Addressable Memory (CAM) compares data against every single row in memory simulatenously using a massive arrary of harware comparators and when it finds a match it reutns the address. Used in high speed cpu caches.

Also Leading Zero Counters (LZC0 is a massive tree of comparators that scans a 64 bit number and outputs the index of the highest `1`.

Macro Op Fusion : If it sees a Compare instruction followed immediately by a Jump instruction, the hardware Decoder fuses them physically into a single Compare-and-Jump operation on the fly. This saves a massive amount of power.

Priority Encoders

Funnel Shifters: nstead of just taking a 64-bit number and shifting it, a Funnel Shifter takes two 64-bit numbers, glues them together into a 128-bit number, and allows the hardware to extract any 64-bit "sliding window" from the middle in a single instant.

The Crossbar Switch (The Ultimate Shifter): A crossbar is a matrix of multiplexers that can connect any input to any output. In modern Chiplet designs (like AMD Ryzen or NVIDIA Blackwell GPUs), thousands of cores need to talk to each other. A giant hardware crossbar allows Core 1 to talk to Core 5, while Core 2 talks to Core 8, simultaneously, without blocking each other.

Pipelining

Clock Domain Crossing

--------------------------------------------------------

