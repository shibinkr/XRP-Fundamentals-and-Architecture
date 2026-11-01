# Part B — Development Environment Setup

## Quick Description

This tutorial explains how to prepare a Linux development environment for XRP. It covers Xtensa toolchain installation, target core configuration, environment variables, source code retrieval, build dependencies, cross-compilation and build verification.

---

## 11. Development Environment

### Overview

XRP development requires a Linux-based development environment capable of compiling host applications, DSP firmware and Linux kernel components.

The development machine typically contains:
- Linux operating system.
- Host compiler (GCC).
- Xtensa cross-compiler.
- GNU Make.
- Linux kernel headers.
- XRP source repository.
- Required libraries and development utilities.

### Development Environment Architecture

```text
+--------------------------------------+
|         Linux Development PC         |
|                                      |
|  +--------------------------------+  |
|  |       XRP Source Code          |  |
|  +--------------------------------+  |
|                                      |
|  +---------------+----------------+  |
|  | Host Compiler | Xtensa Compiler|  |
|  +---------------+----------------+  |
|                                      |
|  +--------------------------------+  |
|  |          GNU Make              |  |
|  +--------------------------------+  |
|                                      |
|  +--------------------------------+  |
|  |       Build Dependencies       |  |
|  +--------------------------------+  |
+--------------------------------------+
                  |
                  v
         Generated Build Outputs
                  |
        +---------+---------+
        |                   |
        v                   v
    Host Binaries       DSP Firmware
```

---

## 12. Xtensa Toolchain

### Overview

The Xtensa toolchain provides the compiler and utilities required to build applications for Xtensa DSP processors.

Unlike normal host compilation, DSP applications must be compiled using a toolchain configured for the target Xtensa architecture.

### Important Tools

| Tool | Purpose |
|---|---|
| Xtensa GCC | Compiles DSP source code |
| Xtensa Assembler | Converts assembly into object files |
| Xtensa Linker | Links DSP object files |
| Xtensa objcopy | Converts executable formats |
| Xtensa objdump | Examines compiled binaries |

### Toolchain Workflow

```text
+----------------------+
|   DSP Source Code    |
|       (.c/.S)        |
+----------+-----------+
           |
           v
+----------------------+
|   Xtensa Compiler    |
+----------+-----------+
           |
           v
+----------------------+
|   Object Files       |
|       (.o)           |
+----------+-----------+
           |
           v
+----------------------+
|    Xtensa Linker     |
+----------+-----------+
           |
           v
+----------------------+
|   DSP Executable     |
|   / Firmware Image   |
+----------------------+
```

The exact compiler executable and installation path depend on the Xtensa toolchain distribution.

---

## 13. Xtensa Core Configuration

### Overview

Xtensa processors are configurable architectures. Different DSP implementations may have different instruction sets, hardware features, memory configurations and processor options.

The correct Xtensa core configuration must be selected before compiling DSP software.

The configuration determines:
- Supported instruction set.
- Processor architecture.
- DSP extensions.
- Memory configuration.
- Compiler and linker settings.
- Core-specific libraries.

### Configuration Relationship

```text
       Xtensa Toolchain
              |
              v
      Select Xtensa Core
              |
              v
     Load Core Configuration
              |
       +------+------+
       |             |
       v             v
  Compiler       Linker
 Configuration   Settings
       |             |
       +------+------+
              |
              v
       DSP Compilation
```

Using an incorrect core configuration may result in compilation errors, unsupported instructions or incompatible DSP binaries.

---

## 14. Environment Variables

### Overview

Environment variables configure the Xtensa development tools and identify the target processor configuration.

Two important variables are:

**XTENSA_SYSTEM**

Specifies the Xtensa system configuration directory containing processor-specific configuration information.

**XTENSA_CORE**

Specifies the name of the target Xtensa processor core.

Example:

```bash
export XTENSA_SYSTEM=/path/to/xtensa/config
export XTENSA_CORE=<target_core>
```

The actual paths and core name must match the installed toolchain.

### Environment Configuration Flow

```text
+---------------------------+
|      Linux Shell          |
+-------------+-------------+
              |
              v
+---------------------------+
|      XTENSA_SYSTEM        |
|                           |
| Core Configuration Path  |
+-------------+-------------+
              |
              v
+---------------------------+
|       XTENSA_CORE         |
|                           |
| Target Processor Name    |
+-------------+-------------+
              |
              v
+---------------------------+
|     Xtensa Toolchain      |
+-------------+-------------+
              |
              v
       DSP Compilation
```

To verify the variables:

```bash
echo $XTENSA_SYSTEM
echo $XTENSA_CORE
```

These variables should be configured in the shell environment before building DSP components.

---

## 15. Repository Download

### Overview

The XRP source repository contains the host library, DSP implementation, Linux kernel driver, common components, examples and documentation.

The source code can be obtained using Git.

### Clone the Repository

```bash
git clone https://github.com/foss-xtensa/xrp.git
```

Enter the repository:

```bash
cd xrp
```

Check the repository status:

```bash
git status
```

### Repository Download Workflow

```text
+-------------------------+
|      GitHub Repository  |
|                         |
|   foss-xtensa/xrp       |
+------------+------------+
             |
             v
+-------------------------+
|       git clone         |
+------------+------------+
             |
             v
+-------------------------+
|      Local XRP Source   |
|                         |
|  xrp-common/            |
|  xrp-dsp/               |
|  xrp-host/              |
|  xrp-kernel/            |
|  xrp-example/           |
|  doc/                   |
+-------------------------+
```

---

## 16. Repository Structure

### Overview

The XRP repository is organized into directories according to functionality.

Understanding the source structure helps developers identify where host, DSP and kernel components are implemented.

| Directory / File | Purpose |
|---|---|
| `doc/` | Documentation and porting guides |
| `xrp-common/` | Common XRP implementation components |
| `xrp-dsp/` | DSP-side implementation |
| `xrp-host/` | Host-side implementation |
| `xrp-kernel/` | Linux kernel driver |
| `xrp-example/` | Example applications and build system |
| `Makefile.src` | Source and build configuration |
| `xrp_api.h` | Public XRP API declarations |

### Source Organization

```text
                 XRP Repository
                       |
       +---------------+---------------+
       |               |               |
       v               v               v
  xrp-host/        xrp-dsp/       xrp-common/
       |               |               |
       v               v               v
  Host Library    DSP Library     Common Code
       |               |               |
       +---------------+---------------+
                       |
             +---------+---------+
             |                   |
             v                   v
        xrp-kernel/         xrp-example/
             |                   |
             v                   v
        Kernel Driver       Applications
```

---

## 17. Build Dependencies

### Overview

XRP requires different development tools and dependencies depending on which components are being built.

Host applications, DSP firmware and Linux kernel modules may require separate compiler environments.

### Common Dependencies

| Dependency | Purpose |
|---|---|
| GCC | Host-side compilation |
| Xtensa GCC | DSP-side compilation |
| GNU Make | Build automation |
| Git | Source code management |
| Linux kernel headers | Kernel module compilation |
| Standard C libraries | Host application dependencies |
| Xtensa libraries | DSP runtime dependencies |

### Dependency Relationship

```text
+--------------------------+
|       XRP Build          |
+------------+-------------+
             |
    +--------+--------+
    |        |        |
    v        v        v
  Host     DSP     Kernel
 Build    Build    Build
    |        |        |
    v        v        v
  GCC    Xtensa    Kernel
         GCC       Headers
```

Missing dependencies may cause compilation or linking failures.

---

## 18. Cross-Compilation

### Overview

Cross-compilation means building software on one architecture for execution on another architecture.

In XRP, the development machine may use an x86 or x86_64 processor, while the target system uses an ARM host processor and an Xtensa DSP.

Therefore, different compiler toolchains may be required.

### Cross-Compilation Architecture

```text
          Development PC
             x86_64
                |
        +-------+-------+
        |               |
        v               v
   Host Compiler   Xtensa Compiler
        |               |
        v               v
   ARM Host         Xtensa DSP
   Binary           Firmware
        |               |
        v               v
   Host CPU         DSP Core
```

### Compilation Targets

| Target | Compiler | Output |
|---|---|---|
| Linux x86_64 host | Native GCC | Host binary |
| ARM64 host | ARM64 cross-compiler | ARM64 binary |
| Xtensa DSP | Xtensa GCC | DSP executable |

The actual target architecture depends on the XRP hardware platform.

---

## 19. Build Configuration

### Overview

Build configuration determines how XRP components are compiled and linked.

Configuration parameters may specify:
- Compiler executable paths.
- Target architecture.
- Xtensa core configuration.
- Include directories.
- Library paths.
- Build flags.
- Output directories.
- Kernel build directory.

The XRP Makefile and associated configuration files define the available build options.

### Build Configuration Flow

```text
+----------------------------+
|       Makefile             |
+-------------+--------------+
              |
              v
+----------------------------+
|    Build Configuration     |
|                            |
| Compiler Settings          |
| Target Architecture        |
| Include Paths              |
| Library Paths              |
| Build Flags                |
+-------------+--------------+
              |
              v
+----------------------------+
|       Build Process        |
+-------------+--------------+
              |
       +------+------+
       |             |
       v             v
   Host Build     DSP Build
       |             |
       v             v
   Host Binary   DSP Binary
```

Before building, verify that compiler paths, target settings and environment variables are correct.

---

## 20. Build Verification

### Overview

After compilation, generated files should be checked to ensure the build completed successfully.

Build verification helps identify missing libraries, incorrect target binaries and compilation failures.

### Verification Steps

**1. Check build output**

Review the terminal output for compiler or linker errors.

**2. Check generated files**

Use:

```bash
find . -type f -name "*.o"
find . -type f -name "*.a"
find . -type f -name "*.so"
```

**3. Check executable files**

```bash
find . -type f -executable
```

**4. Verify binary architecture**

For Linux host binaries, use:

```bash
file <binary_name>
```

For Xtensa binaries, use the appropriate Xtensa binary inspection tools supplied with the toolchain.

**5. Check libraries and dependencies**

Verify that the expected libraries and build outputs are present.

### Build Verification Workflow

```text
+---------------------------+
|       Start Build         |
+-------------+-------------+
              |
              v
+---------------------------+
|    Compilation Complete?  |
+-------------+-------------+
              |
        +-----+-----+
        |           |
       No          Yes
        |           |
        v           v
   Check Errors  Check Outputs
                    |
                    v
             Verify Libraries
                    |
                    v
             Verify Binaries
                    |
                    v
             Build Successful
```

---

## 21. Summary

Part B explains how to prepare a Linux development environment for XRP development.

The main concepts covered are:

1. Setting up a Linux development machine.
2. Installing and configuring the Xtensa toolchain.
3. Selecting the correct Xtensa DSP core.
4. Configuring `XTENSA_SYSTEM` and `XTENSA_CORE`.
5. Downloading the XRP source repository.
6. Understanding the repository directory structure.
7. Installing the required build dependencies.
8. Cross-compiling host and DSP components.
9. Configuring Makefiles and compiler parameters.
10. Verifying generated libraries and binaries.

### Overall Development Workflow

```text
       Linux Development PC
                |
                v
     Install Development Tools
                |
                v
      Configure Xtensa Tools
                |
                v
       Select Xtensa Core
                |
                v
       Set Environment Variables
                |
                v
        Clone XRP Repository
                |
                v
        Install Dependencies
                |
                v
        Configure Build System
                |
                v
          Cross-Compile
                |
                v
        Verify Build Outputs
                |
                v
        Ready for XRP Development
```

**Conclusion:** A correctly configured development environment is essential for successful XRP development. The Xtensa toolchain, target core configuration, compiler dependencies and build settings must all match the intended hardware platform before proceeding to host and DSP application development.