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
echo 'export PATH=$HOME/riscv/bin:$PATH' \>\> \~/.bashrc source
\~/.bashrc
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
