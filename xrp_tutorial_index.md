# Complete Tutorial Index: XRP -- Xtensa Remote Processing

**Project:** [foss-xtensa/xrp](https://github.com/foss-xtensa/xrp)\
**Full name:** Xtensa Remote Processing (XRP)\
**Primary language:** C\
**Platform:** Linux / Android / Xtensa DSP environments

> **Scope note:** This is a proposed learning curriculum based on the
> repository's documentation and source layout. It is not a claim that
> the repository contains 164 separate tutorials. Some topics are
> covered by the official user guide, while others require source-code
> study or are suggested practical exercises. Verify exact API names and
> build targets against the checked-out repository version.

## 1. Introduction

XRP (Xtensa Remote Processing) is a software framework for communication
between a host processor and an Xtensa DSP. Its components provide
mechanisms for device access, command submission, shared-buffer
exchange, synchronization, and DSP-side command handling.

Typical areas of study include:

-   Discovering and communicating with a remote DSP.
-   Loading and executing DSP-side applications.
-   Allocating and managing shared memory.
-   Exchanging commands and data between processors.
-   Synchronizing host and DSP execution.
-   Managing remote-processor resources.

**Terminology:** XRP in this repository means Xtensa Remote Processing,
not the XRP cryptocurrency or XRP Ledger.

## 2. Complete Tutorial Table of Contents

### Part A --- Fundamentals and Architecture

| No. | Tutorial | Topics Covered |
|---:|---|---|
| 1 | XRP Overview | Purpose, features, use cases and design goals |
| 2 | XRP Architecture | Host CPU, Xtensa DSP, shared memory and communication |
| 3 | XRP Terminology | Device, buffer, buffer group, queue, message and namespace |
| 4 | Host–DSP Communication | Controller-worker communication model |
| 5 | XRP Requirements | Hardware, Linux kernel, Xtensa tools and memory requirements |
| 6 | XRP Licensing | Host, DSP and Linux kernel licensing |
| 7 | XRP Implementations | Hosted, standalone and single-image implementations |
| 8 | Hosted Architecture | Linux userspace, kernel driver and DSP firmware |
| 9 | Standalone Architecture | Direct host-to-DSP communication |
|10 | Single-Image Architecture | Running host and DSP application code in one process  
                               
                                                        
  ------------------------------------------------------------------------

### Part B --- Development Environment Setup

| No. | Tutorial | Topics Covered |
|---:|---|---|
| 11 | Development Environment | Linux development environment |
| 12 | Xtensa Toolchain | Installing and configuring Xtensa tools |
| 13 | Xtensa Core Configuration | Selecting the target Xtensa core |
| 14 | Environment Variables | `XTENSA_SYSTEM`, `XTENSA_CORE` |
| 15 | Repository Download | Cloning the XRP source code |
| 16 | Repository Structure | Understanding source directories |
| 17 | Build Dependencies | Compiler, Make, kernel headers and libraries |
| 18 | Cross-Compilation | Building host and DSP binaries |
| 19 | Build Configuration | Configuring Makefile parameters |
| 20 | Build Verification | Checking generated libraries and binaries |

### Part C --- Understanding the XRP Source Code

| Directory / File | Purpose | Recommended Tutorial |
|---|---|---|
| `doc/` | User documentation and porting guides | Documentation walkthrough |
| `xrp-common/` | Common XRP implementation components | Common code architecture |
| `xrp-dsp/` | DSP-side XRP implementation | DSP library development |
| `xrp-example/` | Example applications and build system | Running example applications |
| `xrp-host/` | Host-side XRP implementation | Host library development |
| `xrp-kernel/` | Linux kernel driver | Kernel driver development |
| `Makefile.src` | Source/build configuration | Build-system walkthrough |
| `xrp_api.h` | Public XRP API | API reference and programming |
  -----------------------------------------------------------------------

Repository navigation:

-   [Complete source
    tree](https://github.com/foss-xtensa/xrp/tree/master)
-   [Documentation
    directory](https://github.com/foss-xtensa/xrp/tree/master/doc)
-   [Common
    implementation](https://github.com/foss-xtensa/xrp/tree/master/xrp-common)
-   [DSP
    implementation](https://github.com/foss-xtensa/xrp/tree/master/xrp-dsp)
-   [Example
    applications](https://github.com/foss-xtensa/xrp/tree/master/xrp-example)
-   [Host
    implementation](https://github.com/foss-xtensa/xrp/tree/master/xrp-host)
-   [Kernel
    implementation](https://github.com/foss-xtensa/xrp/tree/master/xrp-kernel)
-   [Public API
    header](https://github.com/foss-xtensa/xrp/blob/master/xrp_api.h)

> Repository paths and filenames can change between branches or
> releases. Check the current tree before relying on a path.

### Part D --- XRP API Programming Tutorials

| No. | Tutorial | Topics Covered |
|---:|---|---|
| 21 | XRP API Introduction | Public API architecture |
| 22 | Opening an XRP Device | `xrp_open_device()` |
| 23 | Closing an XRP Device | Device resource management |
| 24 | Creating a Command Queue | `xrp_create_queue()` |
| 25 | Destroying a Command Queue | Queue lifecycle |
| 26 | Synchronous Commands | `xrp_run_command_sync()` |
| 27 | Asynchronous Commands | `xrp_enqueue_command()` |
| 28 | Event Management | Waiting for command completion |
| 29 | Buffer Creation | `xrp_create_buffer()` |
| 30 | Buffer Mapping | `xrp_map_buffer()` |
| 31 | Buffer Unmapping | `xrp_unmap_buffer()` |
| 32 | Buffer Groups | Creating and managing buffer groups |
| 33 | Buffer Access Permissions | Read-only and read/write access |
| 34 | Shared Memory | Host and DSP memory sharing |
| 35 | Memory Allocation | Device-specific and system memory |
| 36 | Memory Mapping | Mapping buffers into address spaces |
| 37 | Data Transfer | Input and output buffer management |
| 38 | Memory Synchronization | Cache coherency and ownership |
| 39 | Error Handling | XRP status and failure handling |
| 40 | Resource Cleanup | Releasing buffers, queues and devices |


Important API concepts:

-   A buffer is represented by an `xrp_buffer` object.
-   Multiple buffers can be grouped using `xrp_buffer_group`.
-   A queue manages command submission and ordering.
-   Commands may be executed synchronously or asynchronously, depending
    on the API.
-   Events can be used to wait for asynchronous completion.
-   Buffer mappings and other resources must be released according to
    the API's ownership rules.

Use the [XRP API
header](https://github.com/foss-xtensa/xrp/blob/master/xrp_api.h) as the
source of truth for declarations, structures, and supported functions.
Confirm function signatures against the version being built.

### Part E --- DSP-Side Programming

| No. | Tutorial | Topics Covered |
|---:|---|---|
| 41 | DSP XRP Architecture | DSP-side library structure |
| 42 | DSP Application Entry Point | Application entry point and lifecycle |
| 43 | DSP Initialization | Hardware initialization |
| 44 | Opening the DSP Device | Device initialization and access |
| 45 | Namespace Registration | Registering command handlers |
| 46 | Namespace Architecture | UUID-based command namespaces |
| 47 | Command Handler | Implementing command handlers |
| 48 | Input Data Processing | Reading host command data |
| 49 | Output Data Processing | Returning DSP results |
| 50 | Buffer Group Processing | Accessing host-provided buffers |
| 51 | Message Dispatching | Dispatching incoming messages |
| 52 | Message Polling | Polling for incoming work |
| 53 | Host–DSP Synchronization | Synchronization protocol |
| 54 | DSP Interrupt Handling | IRQ-based communication |
| 55 | DSP Polling Mode | Polling-based communication |
| 56 | DSP Idle Management | Waiting for incoming requests |
| 57 | DSP Error Handling | Failure and panic handling |

  ------------------------------------------------------------------------

A typical DSP application workflow is:

1.  Initialize the hardware and XRP platform layer.
2.  Open or initialize the XRP device using the implementation's
    supported interface.
3.  Register the required namespace or command handlers.
4.  Wait for incoming messages, using the configured polling or
    interrupt mechanism.
5.  Dispatch incoming messages.
6.  Execute the matching command handler.
7.  Return status and output data to the host.
8.  Wait for the next request.

The exact initialization and dispatch functions depend on the selected
XRP implementation and repository version.

### Part F --- Host-Side Programming

| No. | Tutorial | Topics Covered |
|---:|---|---|
| 58 | Host XRP Architecture | Host library architecture |
| 59 | Host Application Initialization | Initializing the application |
| 60 | Opening DSP Devices | Discovering and opening devices |
| 61 | Creating Command Queues | Establishing communication channels |
| 62 | Sending Commands | Sending messages to DSP |
| 63 | Sending Input Data | Passing input buffers |
| 64 | Receiving Results | Processing DSP output |
| 65 | Asynchronous Processing | Non-blocking command submission |
| 66 | Event Synchronization | Waiting for asynchronous operations |
| 67 | Multiple Commands | Command ordering and execution |
| 68 | Multiple Queues | Independent command streams |
| 69 | Multiple DSP Devices | Communicating with multiple DSPs |
| 70 | Host Error Handling | Handling DSP failures and timeouts |



### Part G --- Linux Kernel Driver Development

| No. | Tutorial | Topics Covered |
|---:|---|---|
| 71 | Linux Driver Architecture | XRP kernel driver design |
| 72 | Generic Kernel Driver | Generic XRP driver implementation |
| 73 | Hardware-Specific Driver | Hardware-specific driver implementation |
| 74 | Kernel Module Compilation | Building kernel modules |
| 75 | Driver Initialization | Driver registration |
| 76 | Device Tree Integration | XRP device nodes |
| 77 | Device Discovery | Linux device probing |
| 78 | DSP Reset | Reset and runstall management |
| 79 | Firmware Loading | Loading DSP firmware |
| 80 | Shared Memory Management | DSP memory allocation |
| 81 | Memory Mapping | Kernel and userspace mappings |
| 82 | DMA Operations | DMA synchronization |
| 83 | Cache Management | Cache flush and invalidation |
| 84 | Interrupt Management | Host-to-DSP and DSP-to-host IRQ |
| 85 | Power Management | Runtime suspend and resume |
| 86 | Character Device Interface | Userspace communication |
| 87 | IOCTL Interface | Kernel-userspace requests |
| 88 | Kernel Driver Debugging | Diagnosing driver failures |

 

### Part H — Standalone Implementation

| No. | Tutorial | Topics Covered |
|---:|---|---|
| 89 | Standalone Mode Introduction | Architecture and purpose |
| 90 | Standalone Host Library | Host-side implementation |
| 91 | Standalone DSP Firmware | DSP firmware configuration |
| 92 | Shared Memory Configuration | Communication memory |
| 93 | Device Tree Configuration | DSP device definitions |
| 94 | Communication Base Address | Communication address and shared-memory setup |
| 95 | DSP Communication Setup | Establishing shared communication |
| 96 | XTSC Integration | Connecting to Xtensa simulation |
| 97 | Shared Memory Files | Linux shared-memory integration, where supported |
| 98 | Standalone Execution | Running the complete example |

### Part I — Hosted Implementation

| No. | Tutorial | Topics Covered |
|---:|---|---|
| 99 | Hosted Mode Introduction | Hosted architecture |
| 100 | Hosted Host Library | Linux userspace implementation |
| 101 | Kernel Driver Integration | Connecting host and DSP |
| 102 | Firmware Management | Loading DSP firmware |
| 103 | Device Tree Configuration | Configuring hosted devices |
| 104 | Memory Pool Configuration | Configuring DSP memory |
| 105 | Host-to-DSP Communication | Message submission |
| 106 | DSP-to-Host Communication | Result delivery |
| 107 | Hosted Application Execution | Running hosted applications |

### Part J — Advanced XRP Features

| No. | Tutorial | Topics Covered |
|---:|---|---|
| 108 | Multiple DSP Devices | Multi-DSP systems |
| 109 | Multiple Namespaces | Independent command handlers |
| 110 | Multiple Hardware Queues | Hardware queue management |
| 111 | Queue Priorities | Priority-based processing, if supported |
| 112 | XOS Integration | Xtensa OS integration |
| 113 | Multiqueue Processing | Concurrent request handling |
| 114 | IRQ Communication | Interrupt-based operation |
| 115 | Polling Communication | Polling-based operation |
| 116 | DSP Power Management | Low-power operation |
| 117 | Shared Memory Optimization | Reducing unnecessary copies |
| 118 | Buffer Alignment | Memory alignment requirements |
| 119 | Cache Optimization | Cache-aware memory management |
| 120 | Performance Optimization | Improving communication efficiency |


### Part K --- Hardware Porting and Customization
| No. | Tutorial | Topics Covered |
|---:|---|---|
| 121 | Hardware Porting Introduction | Understanding hardware dependencies |
| 122 | DSP Hardware Porting | Implementing DSP hardware operations |
| 123 | Hardware Initialization | Platform-specific initialization |
| 124 | Synchronization Data | Configuring synchronization |
| 125 | Device IRQ Handling | Receiving interrupts |
| 126 | Host IRQ Signalling | Signalling the host |
| 127 | DSP Panic Handling | DSP panic notification |
| 128 | DSP Cache Porting | DSP-side cache management |
| 129 | Host Cache Porting | Host-side cache coherency |
| 130 | Kernel Hardware Porting | Developing hardware-specific drivers |
| 131 | Hardware Operations Interface | Implementing kernel hardware callbacks |
| 132 | DSP Reset and Runstall | Controlling DSP execution |
| 133 | DSP Firmware Loading | Hardware-specific firmware loading |
| 134 | IRQ Register Configuration | Configuring interrupt registers |
| 135 | DMA Integration | Integrating hardware DMA |
| 136 | Memory Copy Operations | Hardware-specific memory transfers |
| 137 | Crash Detection | Detecting DSP firmware crashes |
| 138 | Custom Hardware Driver | Developing a complete hardware port |

Treat names such as `xrp_hw_init()` and `xrp_hw_set_sync_data()` as
search terms to verify in the checked-out source, not as a guaranteed
complete or version-independent API list. Consult the actual hardware
interface header before implementing callbacks.

### Part L --- Build, Execute and Test

 | No. | Tutorial | Topics Covered |
|---:|---|---|
| 139 | Building XRP | Complete build process |
| 140 | Building DSP Libraries | Compiling DSP components |
| 141 | Building Standalone Example | Building the standalone example |
| 142 | Building DSP Libraries Only | Building DSP libraries separately |
| 143 | Building Hosted Example | Building hosted examples |
| 144 | Running Standalone Example | Running the standalone example |
| 145 | Cleaning Build Artifacts | Removing generated files |
| 146 | Building Linux Kernel Module | Compiling the XRP kernel module |
| 147 | Building Hardware Driver | Compiling the hardware-specific module |
| 148 | Cross-Compilation | Building for ARM64 and other hosts |
| 149 | Testing Host–DSP Communication | Verifying message exchange |
| 150 | Testing Shared Memory | Verifying buffer transfers |
| 151 | Testing Interrupts | Verifying IRQ communication |
| 152 | Testing Multiple Queues | Verifying multiqueue operation |
| 153 | Debugging DSP Firmware | Diagnosing firmware problems |
| 154 | Debugging Linux Driver | Diagnosing kernel problems |
| 155 | Performance Testing | Measuring communication overhead |

> Build targets and module names must be checked against the current
> Makefiles and kernel build instructions. Do not assume that
> illustrative targets such as `make run` or module filenames are
> available in every branch.

### Part M --- Android Integration

| No. | Tutorial | Topics Covered |
|---:|---|---|
| 156 | Android XRP Architecture | XRP in Android environments |
| 157 | Android Kernel Integration | Adding XRP to an Android kernel |
| 158 | Android Kernel Configuration | Enabling the required kernel configuration |
| 159 | Android Device Tree | Configuring DSP hardware |
| 160 | Android Kernel Compilation | Building XRP-enabled kernels |
| 161 | Android Emulator Integration | XRP with Android emulator environments |
| 162 | Android x86 Integration | Running XRP on Android x86 |
| 163 | Android x86_64 Integration | Running XRP on Android x86_64 |
| 164 | Android Kernel Debugging | Debugging XRP kernel integration |

Additional reference: [Updating an Android XRP-enabled
kernel](https://github-wiki-see.page/m/foss-xtensa/androidnn-goldfish/wiki/Updating-android-XRP-enabled-kernel-for-new-release).

## 3. Official Source Documentation and References
| Resource | Description |
|---|---|
| [XRP Repository](https://github.com/foss-xtensa/xrp) | Complete project |
| [XRP User Guide](https://raw.githubusercontent.com/foss-xtensa/xrp/master/doc/xrp_user_guide.dox) | Main technical documentation |
| [XRP API Header](https://github.com/foss-xtensa/xrp/blob/master/xrp_api.h) | API functions and structures |
| [DSP Source](https://github.com/foss-xtensa/xrp/tree/master/xrp-dsp) | DSP implementation |
| [Host Source](https://github.com/foss-xtensa/xrp/tree/master/xrp-host) | Host implementation |
| [Kernel Source](https://github.com/foss-xtensa/xrp/tree/master/xrp-kernel) | Linux kernel driver |
| [Example Source](https://github.com/foss-xtensa/xrp/tree/master/xrp-example) | Practical examples |
| [Common Source](https://github.com/foss-xtensa/xrp/tree/master/xrp-common) | Common implementation |


The repository README points readers toward `doc/xrp_user_guide.dox` and
the release's `xrp_rm.pdf`. The user guide is a useful starting point;
use the documentation and source corresponding to the exact branch or
release being studied.

## 6. Suggested Learning Sequence

    Phase Study                           Expected result
  ------- ------------------------------- -------------------------------------
        1 Introduction and architecture   Understand XRP
        2 Repository structure            Understand source organization
        3 Xtensa toolchain                Prepare the development environment
        4 Standalone example              Build and run XRP
        5 API programming                 Understand host APIs
        6 DSP programming                 Develop DSP command handlers
        7 Shared memory                   Transfer data efficiently
        8 Hosted implementation           Understand Linux integration
        9 Kernel driver                   Understand device management
       10 Interrupts and multiqueue       Understand advanced communication
       11 Hardware porting                Integrate with custom hardware
       12 Performance and debugging       Optimize and troubleshoot


