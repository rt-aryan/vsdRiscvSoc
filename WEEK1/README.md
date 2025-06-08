### Task1 : Install & Sanity-Check the Toolchain
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
### Task2: Compile “Hello, RISC-V”

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

### Task3: From C to Assembly
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
📘 Optional: Annotated Assembly with C Source

To get annotated assembly with interleaved C code, use:
```bash
riscv32-unknown-elf-gcc -march=rv32imc -mabi=ilp32 -S -fverbose-asm hello.c -o hello_verbose.s
```
