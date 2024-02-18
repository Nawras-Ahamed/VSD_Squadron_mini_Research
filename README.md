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
    <summary> TASK 1 </summary>

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
