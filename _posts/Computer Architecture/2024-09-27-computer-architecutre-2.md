---
title:  "2. Instructions: Language of the Computer"
excerpt: "&nbsp;&nbsp; " 
date:   2024-09-27 21:35:57 +0900
categories: Computer Architecture
permalink: posts/2-instructions-language-of-the-computer
published: true
# Data Structure / Algorithm / Computer Architecture / System Programming / Computer Network / Database / Design Pattern / Web Programming / JavaScript / Java
# ![Light Mode Image](){: class="light-mode-img" height="90%" width="90%"}

# ![Dark Mode Image](){: class="dark-mode-img" height="90%" width="90%"}

# abstration <img class="lazy" data-src="https://github.com/user-attachments/assets/88804cf0-05c4-4f03-8319-3161edd6f5b7#right" alt="image" height="5%" width="5%">**
---
# 2.1 Introduction

&nbsp;&nbsp; The words of a computer's language are called *instructions*, and its vocabulary is called an **instruction sets**[^1]. In this chapter, you will see the instruction set of a real computer, both in the form written by people and in the form read by the computer.

<div class="bg"></div>

# 2.2 Operations of the Computer Hardware

&nbsp;&nbsp; Every computer must be able to perform arithmetic. The MIPS assembly language notation

```nasm
add a, b, c
```

&nbsp;&nbsp; instructs a computer to add the two variables `b` and `c` and to put their sum in `a`.

&nbsp;&nbsp; This notation is rigid in that each MIPS arithmetic instruction performs only one operation and must have exactly three variables. For example, suppose we want place the sum of four variables `b`, `c`, `d`, and `e` into vairiable `a`.

&nbsp;&nbsp; The following sequence of instructions adds the four variables:

```nasm
add a, b, c     # The sum of b and c is placed in a
add a, a, d     # The sum of b, c and d is now in a
add a, a, e     # The sum of b, c, d and e is now in a
```

&nbsp;&nbsp; Thus, it takes three instructions to sum the four variables.

&nbsp;&nbsp; The natural number of operands for an operation like addition is three: the two numbers being added together and a place to put the sum. Requiring every instruction to have exactly three operands, no more and no less, conforms to the philosophy of keeping the hardware simple: hardware for a variable number of operands is more complicated than hardware for a fixed number. This situation illustrates the first of three underlying of hardware design:

<p style="text-align: center; font-size: 1.25rem"><em>Design Principle 1: <strong>Simplicity favors regularity.</strong></em></p>

<div class="bg"></div>

# 2.3 Operands of the Computer Hardware

&nbsp;&nbsp; Unlike programs in high-level languages, the operands of arithmetic instructions are restricted; they must be from a limited number of special locations built directly in hardware called *registers*. Registers are primitives used in hardware design that are also visible to the programmer when the computer is completed, so you can think of registers as the bricks of computer construction. The size of a register in the MIPS architecture is 32 bits; groups of 32 bits occur so frequently that they are given the name **word**[^2] in the MIPS architecture.

&nbsp;&nbsp; The three operands of MIPS arithmetic instructions must each be chosen from one of the 32 32-bit registers. The reason for the limit of 32 registers may be found in the second of our three underlying design principles of hardware technology:

<p style="text-align: center; font-size: 1.25rem"><em>Design Principle 2: <strong>Smaller is faster.</strong></em></p>

&nbsp;&nbsp; A very large number of registers may increase the clock cycle time simply because it takes electronic signals longer when they must travel farther. Guidlines such as "smaller is faster" are not absolutes; 31 registers may not be faster than 32.

&nbsp;&nbsp; The MIPS convention is to use two-character names following a dollar sign to represent a register. [Section 2.8](/posts/2-instructions-language-of-the-computer#28-supporting-procedures-in-computer-hardware) will explain the reasons behind these names. For now, we will use `$s0`, `$s1`, ... for registers that correspond to variables in C and Java programs and `$t0`, `$t1`, ... for temporary registers needed to compile the program into MIPS instructions.

<div class="bg"></div>

> **Compiling a C Assignment Using Registers**
> 
> &nbsp;&nbsp The assignment statement from our ealier example:
>
> $$
    \textrm{f}=(\textrm{g}+\textrm{h})-(\textrm{i}+\textrm{j});
> $$
>
> &nbsp;&nbsp; The variable `f`, `g`, `h`, `i` and `j` are assigned to the registers `$s0`, `$s1`, `$s2`, `$s3` and `$s4` respectively. What is the compiled MIPS code?
>
> <details><summary><strong>Answer</strong></summary>
> 
> <div class="bg"></div>
>
> <pre><code class="language-asm">add $t0, $s1, $s2   # register $t0 contains g + h
add $t1, $s3, $s4   # register $t1 contains i + j
sub $s0, $t0, $t1   # f gets $t0 - $t1, which is (g + h) - (i + j)
> </code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
>
> </details> 

## a. Memory Operands

&nbsp;&nbsp; The processor can keep only a small amount of data in registers, but computer memory contains billions of data elements. Hence, data structures (arrays and structures) are kept in memory.

&nbsp;&nbsp; The arithmetic operations occur only on registers in MIPS instructions; thus, MIPS must include instructions that transfer data between memory and registers. Such instructions are called **data transfer instructions**[^3]. To access a word in memory, the instruction must supply the memory **address**[^4].

&nbsp;&nbsp; The data transfer instruction that copies data from memory to register is traditionally called *load*. The format of the load instruction is the name of the operation followed by the register to be loaded, then a constant and register used to access memory. The sum of the constant portionof the instruction and the contents of the second register forms the memory address. The actual MIPS name for this instruction is ***lw***, standing for ***load word***.

<div class="bg"></div>

> **Compiling an Assignment When an Operand Is in Memory**
> 
> &nbsp;&nbsp Let's assume that `A` is an array of 100 words and that the compiler has associated the variables `g` and `h` with the registers `$s1` and `$s2` as before. Let's also assume that the starting address, or *base address*, of the array is in `$3`. Compile this C assignment statement:
>
> $$
    \textrm{g}=\textrm{h}+\textrm{A[8]};
> $$
>
> <details><summary><strong>Answer</strong></summary>
> 
> &nbsp;&nbsp; Although there is a single operation in this assignment statement, one of the operands is in memory, so we must first transfer <code>A[8]</code> to a register. The address of this array element is the sum of the base of the array <code>A</code>, found in register <code>$s3</code>, plus the number to select element 8. The data should be placed in a temporary register for use in the next instruction. The first compiled instruction is
>
> <pre><code class="language-asm">lw $t0, 8($s3)       # Temporary reg $t0 gets A[8] $s4
> </code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
> 
> &nbsp;&nbsp; The following instruction can operate on the value in <code>$t0</code> since it is in a register. The instruction must add <code>h</code> to <code>A[8]</code> and put the sum in the register corresponding to <code>g</code>:
>
> <pre><code class="language-asm">add $s1, $s2, $t0    # g = h + A[8]
> </code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
>
> &nbsp;&nbsp; The constant in a data transfer instruction (8) is called the <em>offset</em>, and the register added to form the address (<code>$s3</code>) is called the <em>base register</em>.
>
> </details>

<div class="bg"></div>

> **Hardware/Software Interface**
>
> In addition to associating variables with registers, the compiler allocate data structure like arrays and structures to locations in memory. The compiler can then place the proper starting address into the data transfer instructions.
>
> <img class="lazy" data-src="https://github.com/user-attachments/assets/817f66ad-fa6a-4396-b270-003998166ab9#center" alt="image" height="50%" width="50%">
> 
> &nbsp;&nbsp; In MIPS, words must start at addresses that are multiples of 4. This requirement is called and **alignment restriction**, and many architectures have it.

<div class="bg"></div>

> **alignment restriction**
>
> &nbsp;&nbsp; A requirement that data be aligned in memory on natural boundaries.
>
> &nbsp;&nbsp; Computers divide into those that use the address of the leftmost or "big end" byte as the word address versus those that use the rightmost or "little end" byte. MIPS is in the *big-endian* camp. 
>
> &nbsp;&nbsp; Byte addressing also affects the array index. To get the proper byte address in the code above, *the offset to be added to the base register `$s3` must be 4 x 8, or 32, so that the load address will select `A[8]` and not `A[8/4]`.

<div class="bg"></div>

&nbsp;&nbsp; The instruction complementary to load is traditionally called ***store***; it copies data from a register to memory. The format of a store is similar to that of a load: the name of the operation, followed by the register to be stored, then offset to select the array element, and finally the base register. The actual MIPS name is ***sw***, standing for ***store word***.

<div class="bg"></div>

> **Hardware/Software Interface**
>
> &nbsp;&nbsp; As the addresses in loads and stores are binary numbers, we can see why the DRAM for main memory comes in binary sizes rather than in decimal sizes. That is, in gebibytes ($2^{30}$) of tebibytes ($2^{40}$), not in gigabytes ($10^{9}$) or terabytes ($10^{12}$).
>

<div class="bg"></div>

> **Compiling Using Load and Store**
>
> &nbsp;&nbsp; Assume variable `h` is associated with register `$s2` and the base address of the array `A` is in `$s3`. What is the MIPS assembly code for the C assignment statement below?
>
> $$
> \textrm{A[12]}=\textrm{h}+\textrm{A[8]};
> $$
> 
> <details><summary><strong>Answer</strong></summary>
>
> &nbsp;&nbsp; The first two instructions are the same as the prior example, except this time we use the proper offset of 32 for byte addressing in the load word instruction to select <code>A[8]</code>, and the add instruction places the sum in <code>$t0</code>:
>
> <pre><code class="language-asm">lw $t0, 32($s3)      # Temporary reg $t0 gets A[8]
> add $t0, $s2, $t0    # Temporary reg $t0 gets h + A[8]
> </code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
>
> &nbsp;&nbsp; The final instruction stores the sum into <code>A[12]</code>, using 48 (4 x 12) as the offset and register <code>$s3</code> as the base register.
>
> <pre><code class="language-asm">sw $t0, 48($s3)      # Stores h + A[8] back into A[12]
> </code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
>
> </details>

<div class="bg"></div>

> **Hardware/Software Interface**
>
> &nbsp;&nbsp; Many programs have more variable than computers have registers. Consequently, the compiler tries to keep the most frequently used variables in registers and places the rest in memory, using load and stores to move variables between registers and memory. The process of putting less commonly used variables (or those needed later) into memory is called ***spilling*** registers.
>
> &nbsp;&nbsp; To achieve highest performance and conserve energy, an instruction set architecture must have a sufficient number of registers, and compilers must use registers effectively.

## b. Constant or Immediate Operands

&nbsp;&nbsp; Many times a program will use a constant in an operation—for example, incrementing an index to point to the next element of an array. Using only the instructions we have seen so far, we would have to load a constant from memory to use one. For example, to add the constant 4 to register `$s3`, we could use the code

```asm
lw $t0, AddrConstant4($s1)    # $t0 = constant 4
add $s3, $s3, $t0             # $s3 = $s3 + $t0 ($t0 == 4)
```

&nbsp;&nbsp; assuming that `$s1 + AddrConstant4` is the memory address of the constant 4.

&nbsp;&nbsp; An alternative that avoids the load instruction is to offer versions of the arithmetic instructions in which one operand is a constant. This quick add instruction with one constant operand is called ***add immediate*** or ***addi***. To add 4 to register `$s3`, we just write

```asm
addi $s3, $s3, 4    # $s3 = $s3 + 4
```

&nbsp;&nbsp; Constant operands occur frequently, and by including constants inside arithmetic instructions, operations are much faster and useless energy than if constants were loaded from memory.

<img class="lazy" data-src="https://github.com/user-attachments/assets/d564b128-6384-454a-a8fd-182afbe5ebcb#right" alt="image" height="5%" width="5%">*&nbsp;&nbsp; The constant zero has another role, which is to simplify the instruction set by offering useful variations. For example, the move operation is just an add instruction where on operand is zero. Hence, MIPS dedicates a register `$zero` to be hard-wired to the value zero. Using frequency to justify the inclusions of constants is another example of the great idea of making the **common case fast**.*

<div class="bg"></div>

# 2.4 Signed and Unsigned Numbers

&nbsp;&nbsp; Numbers are kept in computer hardware as a series of high and low electronic signals, and so they are considered base 2 numbers. (Just as base 10 numbers are called *decimal* numbers, base 2 numbers are called *binary* numbers.)

&nbsp;&nbsp; A single digit of a binary number is thus the "atom" of computing, since all information is composed of **binary digits**[^5] or ***bits***. This fundamental building block can be one of two values, which can be thought of as several alternatives: high and low, on or off, true or false, or 1 or 0.

<img class="lazy" data-src="https://github.com/user-attachments/assets/ce19a559-8ba7-4d92-8cf4-3c53909f1a0f#center" alt="image" height="95%" width="95%">

&nbsp;&nbsp; Since words are drawn vertically as well as horizontally, leftmost and rightmost may be unclear. Hence, the phrase **least significant bit** is used to refer to the right-most bit (bit 0 above) and **most significant bit** to the leftmost bit (bit 31).

&nbsp;&nbsp; The MIPS word is 32 bits long, so it can represent $2^{32}$ different 32-bit patterns. It is natural to let these combinations represent the numbers from 0 to 2<sup>32</sup>-1 (4,294,967,295<sub>10</sub>):

<img class="lazy" data-src="https://github.com/user-attachments/assets/a44c2ee0-8616-4772-81bc-ecbb94852bac#center" alt="image" height="95%" width="95%">

&nbsp;&nbsp; These positive numbers are called **unsigned numbers**.

<div class="bg"></div>

&nbsp;&nbsp; Computer programs calculate both positive and negative numbers, so we need a representation that distinguishes the positive from the negative. The most obvious solution is to add a separate sign, which conveniently can be represented in a single bit; the name for this representation is ***sign and magnitude***.

&nbsp;&nbsp; However, sign and magnitude representation has several shortcomings. First, it's not obvious where to put the sign bit. Second, adders for sign and magnitude may need an extra step to set the sign because we can't know in advance what the proper sign will be. Finally, a seperate sign bit means that sign and magnitude has both a positive and a negative zero, which can lead to problems for inattentive programmers. As a result of these shortcomings, sign and magnitude representation was soon abandoned.

&nbsp;&nbsp; The final solution was to pick the representation that made the hardware simple: leading 0s mean positive, and leading 1s mean negative. This convention for representing signed binary numbers is called ***two's complement*** representation:

<img class="lazy" data-src="https://github.com/user-attachments/assets/930872cb-3082-440c-aec4-f276b2c73be3#center" alt="image" height="95%" width="95%">

&nbsp;&nbsp; The positive half of the numbers, from 0 to 2,147,483,647<sub>10</sub> (2<sup>31</sup>-1), use the same representation as before. The following bit pattern (1000 ... 0000<sub>2</sub>) represents the most negative number -2,147,483,648<sub>10</sub> (-2<sup>31</sup>). It is followed by a declining set of negative numbers: -2,147,483,647<sub>10</sub> (1000 ... 0001<sub>2</sub>) down to -1<sub>10</sub> (1111 ... 1111<sub>2</sub>).

&nbsp;&nbsp; Two's complement representation has the advantage that all negative numbers have a 1 in the most significant bit. This bit is often called the ***sign bit***. By recognizing the role of the sign bit, we can represent positive and negative 32-bit numbers in terms of the bit value times a power of 2:

$$
(x31 \times -2^{31}) + (x30 \times 2^{30}) + \cdots + (x1 \times 2^1) + (x0 \times 2^0)
$$

> **Binary to Decimal Conversion**
> 
> &nbsp;&nbsp; What is the decimal value of this 32-bit two's complement number?
>
> &nbsp;&nbsp;&nbsp;&nbsp; 1111 1111 1111 1111 1111 1111 1111 1100<sub>2</sub>
>
> <details><summary><strong>Answer</strong></summary>
> 
> <div class="bg"></div>
>
> &nbsp;&nbsp; Substituting the number's bit values into the formula above:
>
> $$
> \begin{aligned}
> &(1 \times -2^{31}) + (1 \times 2^{30}) + (1 \times 2^{29}) + \cdots + (1 \times 2^2) \times (0 \times 2^1) + (0 \times 2^0) \\
> &= -2^{31} + 2^{30} + 2^{29} + \cdots + 2^2 + 0 + 0 \\
> &= -2,147,483,648_{10} + 2,147,483,644_{10} \\
> &= -4_{10}
> \end{aligned}
> $$
>
> </details>

<div class="bg"></div>

&nbsp;&nbsp; Overflow occurs when the leftmost retained bit of the binary bit pattern is not the same as the infinite number of digits to the left (the sign bit is incorrect): a 0 on the left of the bit pattern when the number is negative or a 1 when the number is positive.

> **Hardware/Software Interface**
> 
> &nbsp;&nbsp; The *function* of signed load is to copy the sign repeatedly to fill the rest of the register—called *sign extension*—but its *purpose* is to place a correct representation of the number within that register. Unsigned loads simply fill with 0s to the left of the data, since the number represented by the bit pattern is unsigned.
>
> &nbsp;&nbsp; When loading a 32-bit word into a 32-bit register, the point is moot; signed and unsigned are identical. MIPS does offer two flavors of byte loads: ***load byte*** (`lb`) treats the byte as a signed number and thus sign-extends to fill the 24 left-most bits of the register, while ***load byte unsigned*** (`lbu`) works with unsigned integers. Since C programs almost always use bytes to represent characters, `lbu` is used practically exclusively for byte loads.

<div class="bg"></div>

&nbsp;&nbsp; Let's examine two useful shorcuts when working with two's complement numbers. The first shortcut is a quick way to negate a two's complement binary number. Simple invert every 0 to 1 and every 1 to 0, then add one to result. This shortcut is based on the observation that the sum of a number and its inverted representation must be 111 ... 111<sub>2</sub>, which represents -1.

> **Negative Shortcut**
> 
> Negate 2<sub>10</sub>, and then check the result by negating -2<sub>10</sub>.
>
> &nbsp;&nbsp; 2<sub>10</sub> = 0000 0000 0000 0000 0000 0000 0000 0010<sub>2</sub>
>
> <details><summary><strong>Answer</strong></summary>
> 
> Negating this number by inverting the bits and adding one:
>
> <pre><code class="language-cmd">    1111 1111 1111 1111 1111 1111 1111 1101
+                                         1
————————————————————————————————————————————
=   1111 1111 1111 1111 1111 1111 1111 1110
=   -2
</code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
> 
> &nbsp;&nbsp; Going the other direction,
> <br>
> 1111 1111 1111 1111 1111 1111 1111 1110<sub>2</sub>
> <br>
> is first inverted and then incremented:
>
> <pre><code class="language-cmd">    0000 0000 0000 0000 0000 0000 0000 0001
+                                         1
————————————————————————————————————————————
=   0000 0000 0000 0000 0000 0000 0000 0010
=   2
</code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
>
> </details>

<div class="bg"></div>

&nbsp;&nbsp; Our next shortcut tells us how to convert a binary number represented in *n* bits to a number represented with more than *n* bits. To add a 16-bit register to a 32-bit register, the computer must convert that 16-bit number to its 32-bit equivalent. The shortcut is to take the most significant bit from the smaller quantity—**the sign bit**—and replicate it to fill the new bits of the larger quantity. The old nonsign bits are simply copied into the right portion of the new word. This shortcut is called ***sign extension***.

> **Sign Extension Shortcut**
> 
> &nbsp;&nbsp; Convert 16-bit binary versions of 2<sub>10</sub> and -2<sub>10</sub> to 32-bit binary numbers.
>
> <details><summary><strong>Answer</strong></summary>
> 
> &nbsp;&nbsp; The 16-bit binary version of the number 2 is
>
> <pre><code class="language-cmd">0000 0000 0000 0010 = 2
</code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
> 
> <div class="bg"></div>
> 
> &nbsp;&nbsp; It is converted to a 32-bit number by making 16 copies of the value in the most significant bit (0) and placing that in the left-hand half of the word. The right half gets the old value:
>
> <pre><code class="language-cmd">0000 0000 0000 0000 0000 0000 0000 0010 = 2
</code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
> 
> <div class="bg"></div>
> 
> &nbsp;&nbsp; Let's negate the 16-bit binary version of 2 using the earlier shortcut. Thus,
>
> <pre><code class="language-cmd">0000 0000 0000 0010
</code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
> &nbsp;&nbsp; becomes
>
> <pre><code class="language-cmd">1111 1111 1111 1101
+                      1
————————————————————————
=   1111 1111 1111 1110
</code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
>
> &nbsp;&nbsp; Creating a 32-bit version of the negative number means copying the sign but 16 times and placing it on the left:
>
> <pre><code class="language-cmd">1111 1111 1111 1111 1111 1111 1111 1110 = -2
</code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
>
> </details>

<div class="bg"></div>

# 2.5 Representing Instructions in the Computer

<p align="center">
<table>
  <thead>
    <tr>
      <th>Register name</th>
      <th>Number</th>
      <th>Usage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>$zero</td>
      <td>0</td>
      <td rowspan="1">constant 0</td>
    </tr>
    <tr>
      <td>$at</td>
      <td>1</td>
      <td rowspan="1">reserved for assembler</td>
    </tr>
    <tr>
      <td>$v0</td>
      <td>2</td>
      <td rowspan="2">expression evaluation and results of a function</td>
    </tr>
    <tr>
      <td>$v1</td>
      <td>3</td>
    </tr>
    <tr>
      <td>$a0</td>
      <td>4</td>
      <td rowspan="4">argument</td>
    </tr>
    <tr>
      <td>$a1</td>
      <td>5</td>
    </tr>
    <tr>
      <td>$a2</td>
      <td>6</td>
    </tr>
    <tr>
      <td>$a3</td>
      <td>7</td>
    </tr>
    <tr>
      <td>$t0</td>
      <td>8</td>
      <td rowspan="8">temporary (not preserved across call)</td>
    </tr>
    <tr>
      <td>$t1</td>
      <td>9</td>
    </tr>
    <tr>
      <td>$t2</td>
      <td>10</td>
    </tr>
    <tr>
      <td>$t3</td>
      <td>11</td>
    </tr>
    <tr>
      <td>$t4</td>
      <td>12</td>
    </tr>
    <tr>
      <td>$t5</td>
      <td>13</td>
    </tr>
    <tr>
      <td>$t6</td>
      <td>14</td>
    </tr>
    <tr>
      <td>$t7</td>
      <td>15</td>
    </tr>
    <tr>
      <td>$s0</td>
      <td>16</td>
      <td rowspan="8">saved temporary (preserved across call)</td>
    </tr>
    <tr>
      <td>$s1</td>
      <td>17</td>
    </tr>
    <tr>
      <td>$s2</td>
      <td>18</td>
    </tr>
    <tr>
      <td>$s3</td>
      <td>19</td>
    </tr>
    <tr>
      <td>$s4</td>
      <td>20</td>
    </tr>
    <tr>
      <td>$s5</td>
      <td>21</td>
    </tr>
    <tr>
      <td>$s6</td>
      <td>22</td>
    </tr>
    <tr>
      <td>$s7</td>
      <td>23</td>
    </tr>
    <tr>
      <td>$t8</td>
      <td>24</td>
      <td rowspan="2">temporary (not preserved across call)</td>
    </tr>
    <tr>
      <td>$t9</td>
      <td>25</td>
    </tr>
    <tr>
      <td>$k0</td>
      <td>26</td>
      <td rowspan="2">reserved for OS kernel</td>
    </tr>
    <tr>
      <td>$k1</td>
      <td>27</td>
    </tr>
    <tr>
      <td>$gp</td>
      <td>28</td>
      <td rowspan="1">pointer to global area</td>
    </tr>
    <tr>
      <td>$sp</td>
      <td>29</td>
      <td rowspan="1">stack pointer</td>
    </tr>
    <tr>
      <td>$fp</td>
      <td>30</td>
      <td rowspan="1">frame pointer</td>
    </tr>
    <tr>
      <td>$ra</td>
      <td>31</td>
      <td rowspan="1">return address (used by function call)</td>
    </tr>
  </tbody>
</table>
</p>

<div class="bg"></div>

&nbsp;&nbsp; Since registers are referred to by almost all instructions, there must be a convention to map register names into numbers. In MIPS assembly language, registers `$s0` to `$s7` map onto registers 16 to 23, and registers `$t0` to `$t7` map onto registers 8 to 15. Hence, `$s0` means register 16, `$s1` means register 17, `$s2` means register 18,..., `$t0` means register 8, `$t1` means register 9, and so on. 

> **Translating a MIPS Assembly Instruction into a Machine Instruction**
> 
> <pre><code class="language-asm">add $t0, $s1, $s2
</code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
> 
> <details><summary><strong>Answer</strong></summary>
> 
> The decimal representation is
> 
> <pre><code class="language-text hljs language-plaintext" data-highlighted="yes">⏐     0     ⏐     17    ⏐     18    ⏐     8     ⏐     0     ⏐     32    ⏐
</code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
> 
> <div class="bg"></div>
> 
> &nbsp;&nbsp; Each of these segments of an instruction is called a <em><strong>field</strong></em>. The first and last fields (containing 0 and 32 in this case) in combination tell the MIPS computer that this instruction performs addition. The second field gives the number of the register that is the first source operand of the addition operation (17 = <code>$s1</code>), and the third field gives the other source operand (18 = <code>$s2</code>). The fourth field contains the number of the register that is to receive the sum (8 = <code>$t0</code>). The fifth field is unused in this instruction, so it is set to 0. Thus, this instruction adds register <code>$s1</code> to register <code>$s2</code> and places the sum in register <code>$t0</code>. <br>
> &nbsp;&nbsp; This instruction can also be represented as fields of binary numbers as opposed to decimal:
>
> <pre><code class="language-text hljs language-plaintext" data-highlighted="yes">⏐   000000  ⏐   10001   ⏐   10010   ⏐   01000   ⏐   00000   ⏐   100000  ⏐
     6bits      5bits       5bits       5bits       5bits        6bits
</code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>

<div class="bg"></div>

&nbsp;&nbsp; This layout of the instruction is called the **instruction format**[^6]. As you can see from counting the number of bits, this MIPS instruction takes exactly 32 bits—the same size as a data word.

&nbsp;&nbsp; To distinguish it from assembly language, we call the numberic version of instructions **machine language**[^7] and a sequence of such instructions *machine code*.

<div class="bg"></div>

&nbsp;&nbsp; Since almost all computer data sizes are multiples of 4, **hexadecimal**[^8] (base 16) numbers are popular. Since base 16 is a power of 2, we can convert by replacing each group of four binary digits by a single hexadecimal digit, and vice versa.

> **Binary to Hexadecimal and Back**
>
> &nbsp;&nbsp; Convert the following hexadecimal and binary numbers into the other base:
>
> <pre><code class="language-text">eca8 6420
0001 0011 0101 0111 1001 1011 1101 1111
</code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
> 
> <details><summary><strong>Answer</strong></summary>
> 
> <pre><code class="language-text">[0] [1] [2] [3] [4] [5] [6] [7] [8] [9] [10] [11] [12] [13] [14] [15]
 0   1   2   3   4   5   6   7   8   9    a    b    c    d    e    f
</code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
>
> <div class="bg"></div>
> 
> <pre><code class="language-text">e    c    a    8    6    4    2    0
1110 1100 1010 1000 0110 0100 0010 0000
</code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
> 
> <div class="bg"></div>
> 
> <pre><code class="language-text">0001 0011 0101 0111 1001 1011 1101 1111
> 1    3    5    7    9    b    d    f
> 
> 1357 9bdf
> </code><button class="copy" type="button" aria-label="Copy code to clipboard"><i class="fa-regular fa-clone"></i></button></pre>
>

## a. MIPS Fields

&nbsp;&nbsp; MIPS fields are given names to make them easier to discuss:

<img class="lazy" data-src="https://github.com/user-attachments/assets/7742339a-6abd-4e92-b39b-52aae40e7da5#center" alt="image" height="95%" width="95%">

&nbsp;&nbsp; Here is the meaning of each name of the fields in MIPS instructions:

* ***op***: Basic operation of the instruction, traditionally called the **opcode**[^9].
* ***rs***: The first register source operand.
* ***rt***: The second register source operand.
* ***rd***: The register destination operand. It gets the result of the operation.
* ***shamt***: Shift amount. A constant value for shift instructions.
* ***funct***: Function. This field, often called the ***function code***, selects the specific variant of the operation in the op field.

<div class="bg"></div>

<p style="text-align: center; font-size: 1.25rem"><em>Design Principle 3: <strong>Good design demands good compormises.</strong></em></p>

&nbsp;&nbsp; The compromise chosen by the MIPS designers is to keep all instructions the same length, thereby requiring different kinds of instruction formats for different kinds of instructions. For example, the format above is called ***R-type*** (for register) or ***R-format***. A second type of instruction format is called ***I-type*** (for immediate) or ***I-format*** and is used by the immediate and data transfer instructions. The fields of I-format are

<img class="lazy" data-src="https://github.com/user-attachments/assets/74470109-f013-4ed6-a279-3e44684e130e#center" alt="image" height="95%" width="95%">

&nbsp;&nbsp; The 16-bit address means a load word instruction can load any word within a region of ±2<sup>15</sup> or 32,768 bytes (±2<sup>13</sup> or 8192 words) of the address in the base register `rs`. Similarily, add immediate is limited to constants no larger than ±2<sup>15</sup>.

<div class="bg"></div>


# 2.6 Logical Operations

<div class="bg"></div>

# 2.7 Instructions for Making Decisions

<div class="bg"></div>

# 2.8 Supporting Procedures in Computer Hardware

<div class="bg"></div>

# 2.9 Communicating with People

<div class="bg"></div>

# 2.10 MIPS Addressing for 32-Bit Immediates and Addresses

<div class="bg"></div>

# 2.11 Parallelism and Instructions: Synchronization

<div class="bg"></div>

# 2.12 Translating and Starting a Program

<div class="bg"></div>

# 2.13 A C Sort Example to Put It All Together

<div class="bg"></div>

# 2.14 Arrays versus Pointers

<div class="bg"></div>

# 2.15 Advanced Material: Compiling C and Interpreting Java

<div class="bg"></div>

# 2.16 Real Stuff: ARM v7 (32-bit) Instructions

<div class="bg"></div>

# 2.17 Real Stuff: ARM v8 (64-bit) Instructions

<div class="bg"></div>

# 2.18 Real Stuff: RISC-V Instructions

<div class="bg"></div>

# 2.19 Real Stuff: x86 Instructions

<div class="bg"></div>

# 2.20 Going Faster: Matrix Multiply in C

<div class="bg"></div>

# 2.21 Fallacies and Pitfalls

<div class="bg"></div>

# 2.22 Concluding Remarks

<div class="bg"></div>

# 2.23 Historical Perspectives and Further Reading

<div class="bg"></div>

---

[^1]: The vocabulary of commands understood by a given architecture.
[^2]: The natural unit of access in a computer, usually a group of 32 bits; corresponds to the size of a register in the MIPS architecture.
[^3]: A command that moves data between memory and registers.
[^4]: A value used to delineate the location of a specific data element within a memory array.
[^5]: Also called **binary bit**. One of the two numbers in base 2, 0 or 1, that are the components of inforamtion.
[^6]: A form of representation of an instruction composed of fields of binary numbers.
[^7]: Binary representation used for communication within a computer system.
[^8]: Numbers in base 16.
[^9]: The field that denotes the operation and format of an instruction.
