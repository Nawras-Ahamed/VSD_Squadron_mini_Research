# A 4-week Research Internship on RISC-V using VSDSquadron Mini RISC-V Dev Board

[Click here for board link](https://www.vlsisystemdesign.com/vsdsquadronmini/)


![NewMini224-removebg-preview](https://github.com/Nawras-Ahamed/VSD_Squadron_mini_Research/assets/50738659/02693492-c8b9-44a6-90ef-6c6281721042)



BOARD SPECS:

| CH32V003F4U6 chip with 32-bit RISC-V core based on RV32EC instruction set |
| ------------------------------------------------------------------------- 
| SRAM                                                                       2kb onchip volatile sram     16kb external program memory                                    |
| Processor                                                                  24 MHz                                                                                       |
| Sink Current per I/O Pin                                                   8 mA                                                                                         |
| Source Current per I/O Pin                                                 8 mA                                                                                         |
| Input voltage (nominal)                                                    5 V                                                                                          |
| I/O voltage                                                                3.3 V                                                                                        |
| Programmer/debugger                                                        Onboard RISC-V programmer/debugger, USB to TTL serial port support                           |
| SPI                                                                        1x, PC5(SCK), PC1(NSS), PC6(MOSI), PC7(MISO)                                                 |
| I2C                                                                        1x, PC1(SDA), PC2(SCL)                                                                       |
| USART                                                                      1x, PD6(RX), PD5(TX)                                                                         |
| External interrupts                                                        8 external interrupt edge detectors, but it only maps one external interrupt to 18 I/O ports |
| PWM pins                                                                   14X                                                                                          |
| Analog I/O pins                                                            10-bit ADC, PD0-PD7, PA1, PA2, PC4                                                           |
| Digital I/O pins                                                           15                                                                                           |
| Built-in LED Pin                                                           1X onboard user led (PD6)                                                                    |
| USB 2.0 Type-C                                                            
   

This repo is intended to document the weekly progress.

### The first online meet was held on 16th of Feb 2024 @6PM

<details>
    <summary> TASK 1 - INSTALLING TOOLS</summary>

1) install RISC-V GNU Toolchain 

2) install Yosys 

3) install iverilog 

4) install gtkwave

### CLONING RISC-V GNU TOOLCHAIN

```sudo apt install git-all```   # To install git

```sudo apt-get install autoconf automake autotools-dev curl python3 libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev``` *make sure to install the dependencies*

![gnu_dependencies](https://github.com/Nawras-Ahamed/VSD_Squadron_mini_Research/assets/50738659/3a354063-2d87-44c5-8b54-e2f13d2b1965)

```git clone https://github.com/riscv/riscv-gnu-toolchain```

![gnu_toolchain_clone](https://github.com/Nawras-Ahamed/VSD_Squadron_mini_Research/assets/50738659/760e3e42-8c07-4254-80a3-050e489ac42d)


## Create a opt dir
```mkdir /opt/riscv```  *try sudo incase of permission denial*

In my case I created a driectory ```mkdir riscv``` and ``` chmod 777 home/nawras/riscv ```

## Config and make inside the risc-v gnu toolchain dir 

```./configure --prefix=/opt/riscv```  

In my case ```./configure --prefix=/home/nawras/riscv```  

Then
```make``` **(Have patience)**

### Troubleshooting

**ERROR 1**: "gcc not found"
try ```sudo apt-get install build-essential```
see if gcc is in /usr/bin/

**ERROR 2**: "no acceptable c compiler found in $PATH"
Open the .bashrc by any editors like vim,emacs,nano,gedit ```nano ~/.bashrc``` 
Add the below line at the end of .bashrc and save it
```export PATH="$PATH:/usr/bin/gcc```

**ERROR 3**: Even after installing gcc g++ sometimes it shows 'gcc' command not found ,though it suggest to ```sudo apt install gcc``` which again will cause the same error. I figured this by ```ls```'ing the /usr/bin directory to find the gcc g++ cc to be in red text with black background indicates broken link or missing file.


Better purge it at **YOUR OWN RISK** and reinstall it again.
```sudo apt-get purge gcc```

or **REINSTALL** ```sudo apt-get install --reinstall gcc``` (didn't work for me)



### INSTALLING IVERILOG GTKWAVE & YOSYS

### YOSYS

```bash
git clone https://github.com/YosysHQ/yosys.git
cd yosys 
sudo apt-get install build-essential clang bison flex \libreadline-dev gawk tcl-dev libffi-dev git \ graphviz xdot pkg-config python3 libboost-system-dev\libboost-python-dev libboost-filesystem-dev zlib1g-dev
make config-gcc
make 
sudo make install
```

![yosys_make](https://github.com/Nawras-Ahamed/VSD_Squadron_mini_Research/assets/50738659/e722e508-0802-4f50-9cb6-02e9c6bafe48)


![buildsuccess_yosys](https://github.com/Nawras-Ahamed/VSD_Squadron_mini_Research/assets/50738659/5e10e5b8-19dd-4460-994f-2759e9b942b1)


### iVerilog

```
sudo apt-get install iverilog
```


### GTkWave
``` sudo apt-get install gtkwave ```

![iverilog_gtkwave](https://github.com/Nawras-Ahamed/VSD_Squadron_mini_Research/assets/50738659/344a4225-c6bb-4728-a325-ac66d1621b28)

</details>


<details>
  <summary> TASK 2 - RISC-V ISA AND INSTRUCTION FORMAT </summary>
  
  ### THE RISC-V ISA

[Instruction Set Manual](https://riscv.org/wp-content/uploads/2017/05/riscv-spec-v2.2.pdf)


The RISC-V ISA is defined as a base integer ISA, which must be present in any implementation, plus optional extensions to the base ISA.
The base integer instruction set, also known as the "RV32I" or "RV64I" instruction set, depending on the address space size, provides the core functionality required for general-purpose computing. 
It includes instructions for arithmetic, logical and control,memory access and manipulation <br>

The instruction format of an operation in binary is known as its instruction format. RISC-V employs six core instruction formats, each encoded in a fixed-length 32-bit format for streamlined decoding and execution. These formats fall into six types:

R-type: For register-to-register operations like arithmetic and logical operations, utilizing three register operands. <br>
I-type: For short immediate operations involving arithmetic and logical operations with a 12-bit immediate value, employing two register operands. <br>
S-type: For store operations transferring data from a register to memory, involving two register operands and a 12-bit immediate value for memory address offset. <br>
B-type: For conditional branch operations directing control flow based on a condition, with two register operands and a 12-bit immediate value for branch target address. <br>
U-type: For operations with a 20-bit immediate(long) value, such as loading a constant or setting the upper 20 bits of a register. <br>
J-type: For unconditional jump operations transferring control to a different instruction unconditionally, with one register operand and a 20-bit immediate value for the jump target address. <br>

![image](https://github.com/Nawras-Ahamed/VSD_Squadron_mini_Research/assets/50738659/c5ee17d3-5017-41ba-bdbb-7c3acead31d8)


Instruction 1 : ``` add r6, r2, r1``` <br>
Instruction Type : **R-TYPE ARITHMETIC** <br>
Instruction Specification : Performs addition operation on the contents of registers r2 and r1 and stores the result in register r6. <br>
Instruction Format :


Instruction 2 : ``` sub r7, r1, r2 ``` <br>
Instruction Type : **R-TYPE ARITHMETIC** <br>
Instruction Specification : Performs subtraction operation on the contents of registers r2 and r1 and stores the result in register r7. <br>
Instruction Format :

Instruction 3 : ``` and r8, r1, r3``` <br>
Instruction Type : **R-TYPE LOGICAL** <br>
Instruction Specification : Performs bitwise AND operation between the contents of registers r1 and r3 and stores the result in register r8. <br>
Instruction Format :

Instruction 4 : ```or r9, r2, r5``` <br>
Instruction Type : **R-TYPE LOGICAL** <br>
Instruction Specification : Performs bitwise OR operation between the contents of registers r2 and r5 and stores the result in register r9. <br>
Instruction Format :

Instruction 5 : ```xor r10, r1, r4``` <br>
Instruction Type : **R-TYPE LOGICAL** <br>
Instruction Specification : Performs bitwise XOR operation between the contents of registers r1 and r4 and stores the result in register r10. <br>
Instruction Format :

Instruction 6 : ```slt r11, r2, r4``` <br>
Instruction Type : **R-TYPE LOGICAL** <br>
Instruction Specification : . It stands for "Set Less Than", and it compares the values in registers r2 and r4. If the value in r2 is less than the value in r4, it sets the value of r11 to 1; otherwise, it sets it to 0 <br>
Instruction Format :

Instruction 7 : ```addi r12, r4, 5``` <br>
Instruction Type : **I-Type** <br>
Instruction Specification : adds the immediate value 5 to the value in register r4 and stores the result in register r12 <br>
Instruction Format :

Instruction 8 : ```sw r3, r1, 2``` <br>
Instruction Type : **S-TYPE** <br>
Instruction Specification : stores the value from register r3 into the memory address formed by adding the immediate offset 2 to the value in register r1 <br>
Instruction Format :

Instruction 9 : ```lw r13, r1, 2``` <br>
Instruction Type : **S-TYPE** <br>
Instruction Specification : used to load a 32-bit value from memory into a register <br>
Instruction Format :

Instruction 10 : ```beq r0, r0, 15``` <br>
Instruction Type : **B-TYPE** <br>
Instruction Specification :checks if the values in registers r0 and r0 are equal. Since r0 is typically the zero register (hardwired to zero) this always evaluates TRUE <br>
Instruction Format :

Instruction 11 : ```bne r0, r1, 20``` <br>
Instruction Type : **B-TYPE** <br>
Instruction Specification :checks if the values in registers r0 and r1 are not equal If they are not equal the program will branch by adding the immediate offset of 20 to the PC <br>
Instruction Format :

Instruction 12 : ```sll r15, r1, r2(2)``` <br>
Instruction Type : **R-TYPE** <br>
Instruction Specification :logical left shift on the value in register r1, shifting it left by a number of bits specified by the value in register r2 which is 2 in this case and stores the result in register r15 <br>
Instruction Format :

Instruction 13 : ```srl r16, r14, r2(2)``` <br>
Instruction Type : **R-TYPE** <br>
Instruction Specification :logical right shift on the value in register r14, shifting it left by a number of bits specified by the value in register r2 which is 2 in this case and stores the result in register r16 <br>
Instruction Format :


</details>


<details>
    <summary> TASK 3 - COMPILING A C FILE & VIEWING THE OBJDUMP</summary>
   I just created a C program that sorts an array.

      #include <stdio.h
      void main()

    {

        int i, j, a, n, x[30];

        printf("Enter the value of N \n");

        scanf("%d", &n);

        printf("Enter the numbers \n");

        for (i = 0; i < n; ++i)

            scanf("%d", &x[i]);

        for (i = 0; i < n; ++i)

        {
             for (j = i + 1; j < n; ++j)

            {

                if (x[i] > x[j])

                {

                   a =  x[i];

                    x[i] = x[j];

                    x[j] = a;

                }
            }
     } 
     }


```riscv64-unknown-elf-gcc -o1 -o sorti.o sorti.```
![riscv_compile](https://github.com/Nawras-Ahamed/VSD_Squadron_mini_Research/assets/50738659/b7e607e7-9cf9-4129-bbb0-401b85ef644a)


The obj file can be seen after running this 

while i can also see the riscv assembly 
```riscv64-unknown-elf-objdump -d sorti.o | less```

![main](https://github.com/Nawras-Ahamed/VSD_Squadron_mini_Research/assets/50738659/54aab80e-5bb5-4448-a04b-f81d87b61810)


    
   
</details>
<details>
    <summary> TASK 4 - Optimization of C code and Intro To SPIKE sim </summary>

**Why do we need Optimization?**
[Optimize-options in gcc](https://gcc.gnu.org/onlinedocs/gcc/Optimize-Options.html)

Turning on optimization flags makes the compiler attempt to improve the performance and/or code size at the expense of compilation time and possibly the ability to debug the program

With ```-O1```, the compiler tries to reduce code size and execution time, without performing any optimizations.
Optimize even more. GCC performs nearly all supported optimizations that do not involve a space-speed tradeoff. As compared to -O, this option increases both compilation time and the performance of the generated code. 

With ```-Ofast``` it enables all -O3(optimize yet more) optimizations. It also enables optimizations that are not valid for all standard-compliant programs.

____________________
**ASM FOR SORTING AN ARRAY**

```bash
riscv64-unknown-elf-gcc -O1 -o sort.o sorti.c
riscv64-unknown-elf-objdump -d sort.o | less
```

```asm
00000000000101a4 <main>:
   101a4:       7135                    addi    sp,sp,-160
   101a6:       ed06                    sd      ra,152(sp)
   101a8:       00020537                lui     a0,0x20
   101ac:       5c050513                addi    a0,a0,1472 # 205c0 <__clzdi2+0x42>
   101b0:       434000ef                jal     105e4 <puts>
   101b4:       18ec                    addi    a1,sp,124
   101b6:       00020537                lui     a0,0x20
   101ba:       5d850513                addi    a0,a0,1496 # 205d8 <__clzdi2+0x5a>
   101be:       42e000ef                jal     105ec <scanf>
   101c2:       00020537                lui     a0,0x20
   101c6:       5e050513                addi    a0,a0,1504 # 205e0 <__clzdi2+0x62>
   101ca:       41a000ef                jal     105e4 <puts>
   101ce:       57f6                    lw      a5,124(sp)
   101d0:       08f05a63                blez    a5,10264 <main+0xc0>
   101d4:       e922                    sd      s0,144(sp)
   101d6:       e526                    sd      s1,136(sp)
   101d8:       e14a                    sd      s2,128(sp)
   101da:       848a                    mv      s1,sp
   101dc:       4401                    li      s0,0
   101de:       00020937                lui     s2,0x20
   101e2:       85a6                    mv      a1,s1
   101e4:       5d890513                addi    a0,s2,1496 # 205d8 <__clzdi2+0x5a>
   101e8:       404000ef                jal     105ec <scanf>
   101ec:       2405                    addiw   s0,s0,1
   101ee:       57f6                    lw      a5,124(sp)
   101f0:       0491                    addi    s1,s1,4
   101f2:       fef448e3                blt     s0,a5,101e2 <main+0x3e>
   101f6:       06f05063                blez    a5,10256 <main+0xb2>
   101fa:       004c                    addi    a1,sp,4
   101fc:       fff7889b                addiw   a7,a5,-1
   10200:       1882                    slli    a7,a7,0x20
   10202:       0208d893                srli    a7,a7,0x20
   10206:       8e3e                    mv      t3,a5
   10208:       4501                    li      a0,0
   1020a:       ffe7881b                addiw   a6,a5,-2
   1020e:       00810313                addi    t1,sp,8
   10212:       a00d                    j       10234 <main+0x90>
   10214:       0791                    addi    a5,a5,4
   10216:       00c78b63                beq     a5,a2,1022c <main+0x88>
   1021a:       ffc5a703                lw      a4,-4(a1)
   1021e:       4394                    lw      a3,0(a5)
   10220:       fee6dae3                bge     a3,a4,10214 <main+0x70>
   10224:       fed5ae23                sw      a3,-4(a1)
   10228:       c398                    sw      a4,0(a5)
   1022a:       b7ed                    j       10214 <main+0x70>
   1022c:       0505                    addi    a0,a0,1
   1022e:       0591                    addi    a1,a1,4
   10230:       01c50f63                beq     a0,t3,1024e <main+0xaa>
   10234:       0005079b                sext.w  a5,a0
   10238:       03150363                beq     a0,a7,1025e <main+0xba>
   1023c:       40f8063b                subw    a2,a6,a5
   10240:       1602                    slli    a2,a2,0x20
   10242:       9201                    srli    a2,a2,0x20
   10244:       962a                    add     a2,a2,a0
   10246:       060a                    slli    a2,a2,0x2
   10248:       961a                    add     a2,a2,t1
   1024a:       87ae                    mv      a5,a1
   1024c:       b7f9                    j       1021a <main+0x76>
   1024e:       644a                    ld      s0,144(sp)
   10250:       64aa                    ld      s1,136(sp)
   10252:       690a                    ld      s2,128(sp)
   10254:       a801                    j       10264 <main+0xc0>
   10256:       644a                    ld      s0,144(sp)
   10258:       64aa                    ld      s1,136(sp)
   1025a:       690a                    ld      s2,128(sp)
   1025c:       a021                    j       10264 <main+0xc0>
   1025e:       644a                    ld      s0,144(sp)
   10260:       64aa                    ld      s1,136(sp)
   10262:       690a                    ld      s2,128(sp)
   10264:       60ea                    ld      ra,152(sp)
   10266:       610d                    addi    sp,sp,160
   10268:       8082                    ret
```
____________________

```bash
riscv64-unknown-elf-gcc -Ofast -o sort.o sorti.c
riscv64-unknown-elf-objdump -d sort.o | less
```

```asm
0000000000010104 <main>:
   10104:       00020537                lui     a0,0x20
   10108:       7135                    addi    sp,sp,-160
   1010a:       55050513                addi    a0,a0,1360 # 20550 <__clzdi2+0x3e>
   1010e:       ed06                    sd      ra,152(sp)
   10110:       e14a                    sd      s2,128(sp)
   10112:       466000ef                jal     10578 <puts>
   10116:       00020937                lui     s2,0x20
   1011a:       004c                    addi    a1,sp,4
   1011c:       56890513                addi    a0,s2,1384 # 20568 <__clzdi2+0x56>
   10120:       460000ef                jal     10580 <scanf>
   10124:       00020537                lui     a0,0x20
   10128:       57050513                addi    a0,a0,1392 # 20570 <__clzdi2+0x5e>
   1012c:       44c000ef                jal     10578 <puts>
   10130:       4792                    lw      a5,4(sp)
   10132:       02f05263                blez    a5,10156 <main+0x52>
   10136:       e922                    sd      s0,144(sp)
   10138:       e526                    sd      s1,136(sp)
   1013a:       4401                    li      s0,0
   1013c:       0024                    addi    s1,sp,8
   1013e:       85a6                    mv      a1,s1
   10140:       56890513                addi    a0,s2,1384
   10144:       43c000ef                jal     10580 <scanf>
   10148:       4792                    lw      a5,4(sp)
   1014a:       2405                    addiw   s0,s0,1
   1014c:       0491                    addi    s1,s1,4
   1014e:       fef448e3                blt     s0,a5,1013e <main+0x3a>
   10152:       644a                    ld      s0,144(sp)
   10154:       64aa                    ld      s1,136(sp)
   10156:       60ea                    ld      ra,152(sp)
   10158:       690a                    ld      s2,128(sp)
   1015a:       610d                    addi    sp,sp,160
   1015c:       8082                    ret
```
______________________
**INSTALLING SPIKE**

[SPIKE RISCV ISA SIM](https://github.com/riscv-software-src/riscv-isa-sim)
  
  ```bash
 git clone https://github.com/riscv-software-src/riscv-isa-sim.git
 sudo apt-get install device-tree-compiler libboost-regex-dev
 mkdir build
 cd build
 ../configure --prefix=/home/nawras/riscv
 make
 sudo make install

 ```
The ```--prefix=/home/nawras/riscv``` is where the path is set to.

**INSTALLING RISCV PROXY KERNEL (PK)**

```bash
git clone https://github.com/riscv-software-src/riscv-pk.git
mkdir build
cd build
../configure --prefix=/home/nawras/riscv --host=riscv64-unknown-elf
make
make install
```

[**TROUBLESHOOT 1 -  HOST COMPILER , riscv-unknown-elf & PATH**](https://github.com/riscv-software-src/riscv-pk/issues/204)

![image](https://github.com/Nawras-Ahamed/VSD_Squadron_mini_Research/assets/50738659/29b8b342-f2fd-45f4-9392-8227509e8fb9)

**ERROR 2** 
![image](https://github.com/Nawras-Ahamed/VSD_Squadron_mini_Research/assets/50738659/bd31c5d7-1b43-4082-9db3-fcd12714ac29)

[**TROUBLESHOOT 2 - Error: unrecognized opcode fence.i, extension zifencei required**](https://github.com/riscv-software-src/riscv-pk/issues/260) <br>

Looks like the fence instruction is needed and I have to build a seperate riscv gnu toolchain for this by 
```bash
cd riscv-gnu-toolchain
mkdir build
cd build
../configure --prefix=/home/nawras/riscv --with-arch=rv64gc_zfencei
make
```
</details>

