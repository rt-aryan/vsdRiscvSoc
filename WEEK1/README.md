### Task 1 : Install & Sanity-Check the Toolchain
🧰 Step 1: Install Required Dependencies

Open your terminal and run:
```bash
sudo dnf groupinstall "Development Tools" sudo dnf install gmp-devel
mpfr-devel libmpc-devel zlib-devel texinfo curl \\ python3 gcc-c++
isl-devel libstdc++-static
```
📥 Step 2: Clone the Toolchain Source
```bash
git clone https://github.com/riscv-collab/riscv-gnu-toolchain cd
riscv-gnu-toolchain
```
🛠️ Step 3: Configure the Build

We’ll build a minimal embedded toolchain targeting RV32IMC with Newlib
(suitable for baremetal systems).
```bash
./configure --prefix=$HOME/riscv --with-arch=rv32imc --with-abi=ilp32
```
--prefix=$HOME/riscv is where the tools will be installed. You can
change it if needed.

🔧 Step 4: Build the Toolchain

This step takes some time (\~10–30 minutes depending on your system):
```bash
make newlib
```
🧭 Step 5: Add Toolchain to Your PATH

Once done, make the toolchain accessible system-wide:
```bash
echo 'export PATH=$HOME/riscv/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```
✅ Step 6: Verify the Installation
```bash
riscv32-unknown-elf-gcc --version
```
You should see the version info if everything is correctly installed. 

📝Optional: Build RV64GC Toolchain

If you want to compile programs for Linux-capable RISC-V cores:
```bash
./configure --prefix=$HOME/riscv --with-arch=rv64gc --with-abi=lp64d
make linux
```
### Task 2: Compile “Hello, RISC-V”

📝 Step 1: Create a C File

Create a file called hello.c with this minimal C code:
```c
#include <stdio.h>

int main() {
    printf("Hello, RISC-V!\n");
    return 0;
}
```
You can create the file using nano, vim, or any text editor:
```bash
nano hello.c
```
Paste the code, save (Ctrl+O), and exit (Ctrl+X).
⚙️ Step 2: Compile with RISC-V GCC

Use the toolchain to compile the program for RV32IMC:

```bash
riscv32-unknown-elf-gcc -march=rv32imc -mabi=ilp32 -o hello.elf hello.c
```

✅ Explanation:

-march=rv32imc → target architecture

-mabi=ilp32 → 32-bit integer ABI

-o hello.elf → output file in ELF format

🔍 Step 3: Check the Output ELF

To confirm it compiled correctly:
```bash
file hello.elf
```
Expected output:
```bash
hello.elf: ELF 32-bit LSB executable, UCB RISC-V ...
```

### Task 3: From C to Assembly
🔧 Step 1: Generate Assembly Code

Use the -S flag to stop after generating the assembly output:
```bash
riscv32-unknown-elf-gcc -march=rv32imc -mabi=ilp32 -S hello.c -o hello.s
```
This will generate a file hello.s containing the RISC-V assembly code corresponding to your hello.c.
📂 Step 2: View the Assembly Output

You can open the .s file in any text editor or use:
```bash
less hello.s
```
You’ll see instructions like addi, call, lui, etc., in RISC-V assembly.

**To exit from viewing the file in less, simply press: q**

📘 Optional: Annotated Assembly with C Source

To get annotated assembly with interleaved C code, use:
```bash
riscv32-unknown-elf-gcc -march=rv32imc -mabi=ilp32 -S -fverbose-asm hello.c -o hello_verbose.s
```
### Task 4: Hex Dump & Disassembly

Disassemble the ELF File

Run:
```bash
riscv32-unknown-elf-objdump -d hello.elf
```
This will show you the disassembled machine code, i.e., the actual RISC-V instructions with addresses and opcodes.

### Task 5 : ABI & Register Cheat-Sheet
🧠 RISC-V ABI Basics (RV32I / RV32IMC)
| Register | ABI Name | Description                        |
| -------- | -------- | ---------------------------------- |
| x0       | zero     | Always zero                        |
| x1       | ra       | Return address                     |
| x2       | sp       | Stack pointer                      |
| x3       | gp       | Global pointer                     |
| x4       | tp       | Thread pointer                     |
| x5–x7    | t0–t2    | Temporaries                        |
| x8       | s0/fp    | Saved register / Frame pointer     |
| x9       | s1       | Saved register                     |
| x10–x11  | a0–a1    | Function arguments / return values |
| x12–x17  | a2–a7    | Function arguments                 |
| x18–x27  | s2–s11   | Saved registers                    |
| x28–x31  | t3–t6    | More temporaries                   |

📦 Stack and Function Call Convention
🔁 Function Call Rules

    Arguments: Passed in a0 to a7

    Return values: a0, a1

    Caller-saved: t0–t6, a0–a7

    Callee-saved: s0–s11

📥 Example:

If you call a function with:
```c
int sum(int a, int b);
```
    a → passed in a0

    b → passed in a1

    Result comes back in a0

✅ How This Helps You

When you run:
```bash
riscv32-unknown-elf-objdump -d -S hello.elf
```
You’ll now recognize things like:

    mv a0, zero → set return value to 0

    jal ra, printf → jump to printf, saving return address

    addi sp, sp, -16 → stack allocation
### Task 6 : Stepping with GDB
🧰 Step 1: Start GDB

Launch GDB with your ELF:
```bash
riscv32-unknown-elf-gdb hello.elf
```
You’ll now be in the GDB prompt ((gdb)).
⛔ Step 2: Set a Breakpoint at main

At the GDB prompt:
```gdb
(gdb) break main
```
▶️ Step 3: Start Program Execution

Run the program with:
```gdb
(gdb) run
```
⚠️ Note: Since this is a bare-metal ELF, it won't actually "run" on your Fedora system unless you're using QEMU as a virtual CPU backend (we'll get to that soon).
However, you can still inspect and simulate program flow with stepping.
🐾 Step 4: Step Through the Program

Use these commands to go instruction-by-instruction or line-by-line:
```gdb
(gdb) stepi      # Step 1 assembly instruction
(gdb) next       # Step 1 C line
(gdb) continue   # Continue until next breakpoint
```
🧠 Step 5: Inspect Registers

To view all general-purpose registers:
```gdb
(gdb) info registers
```
To view a specific register (e.g. a0):
```gdb
(gdb) print $a0
```
🚪 Step 6: Exit GDB
```gdb
(gdb) quit
```
