# XRP Fundamentals and Architecture
## Tutorials 1–10

## 1. XRP Overview

### 1.1 What is XRP?

XRP (Xtensa Remote Processing) is a software framework designed to enable communication between a host processor and an Xtensa DSP (Digital Signal Processor).

It allows applications running on a host CPU, such as an ARM processor, to submit computational tasks to an Xtensa DSP. The DSP executes these tasks and returns the results to the host.

The main objective is to offload suitable processing workloads from the host CPU to the DSP.

### 1.2 Why is XRP needed?

Modern embedded systems often contain multiple processing units. Each processor is designed for specific workloads.

For example:
- Host CPU: Runs Linux, application software and system services.
- Xtensa DSP: Performs signal processing, audio processing and other DSP-intensive operations.
- Shared memory: Provides a communication area between the processors.

Without a communication framework, applications would need to implement processor communication, memory sharing and synchronization independently.

XRP provides a common software interface for these operations.

### 1.3 Key Features

- Host-to-DSP command submission.
- DSP-to-host result delivery.
- Shared-memory-based data transfer.
- Buffer and buffer-group management.
- Command queues.
- Synchronous and asynchronous execution models.
- Interrupt-based or polling-based communication, depending on hardware support.
- Support for different deployment architectures.

### 1.4 Typical Use Cases

| Application | DSP Responsibility |
|---|---|
| Audio processing | Filtering, mixing and noise reduction |
| Voice processing | Echo cancellation and speech processing |
| Image processing | Image filtering and transformation |
| Sensor processing | Signal analysis and filtering |
| Communications | Signal modulation and demodulation |
| Embedded AI | Suitable signal-processing or inference workloads |

### 1.5 Basic Processing Flow

```text
+---------------------------+
|       Host Application    |
|                           |
|  Prepare input data       |
|  Submit processing task   |
+-------------+-------------+
              |
              v
+---------------------------+
|        XRP Host API       |
|                           |
|  Create command           |
|  Prepare buffers          |
|  Submit request           |
+-------------+-------------+
              |
              v
+---------------------------+
|       Communication       |
|                           |
|  Shared memory            |
|  Message exchange         |
|  Synchronization          |
+-------------+-------------+
              |
              v
+---------------------------+
|         Xtensa DSP        |
|                           |
|  Receive command          |
|  Process input data       |
|  Generate result          |
+-------------+-------------+
              |
              v
+---------------------------+
|       Host Application    |
|                           |
|  Receive result           |
|  Process output           |
+---------------------------+
```

---

## 2. XRP Architecture

### 2.1 High-Level Architecture

XRP follows a host–worker architecture.

The host processor acts as the controller, while the DSP acts as the worker responsible for executing submitted commands.

The architecture consists of three major components:

1. Host-side software.
2. Inter-processor communication infrastructure.
3. DSP-side software.

### 2.2 Architecture Diagram

```text
             HOST PROCESSOR
+------------------------------------+
|           Linux System             |
|                                    |
|  +------------------------------+  |
|  |      Host Application        |  |
|  +--------------+---------------+  |
|                 |                  |
|  +--------------v---------------+  |
|  |         XRP Host API         |  |
|  +--------------+---------------+  |
|                 |                  |
|  +--------------v---------------+  |
|  |       Linux Kernel Driver    |  |
|  +--------------+---------------+  |
+-----------------|------------------+
                  |
                  |
        Inter-Processor Communication
                  |
       +----------v-----------+
       |     Shared Memory    |
       |                      |
       | Commands and Buffers |
       | Synchronization Data |
       +----------+-----------+
                  |
                  |
             XTENSA DSP
+-----------------|------------------+
|                 v                  |
|  +------------------------------+  |
|  |          XRP DSP API         |  |
|  +--------------+---------------+  |
|                 |                  |
|  +--------------v---------------+  |
|  |      Command Handlers        |  |
|  +--------------+---------------+  |
|                 |                  |
|  +--------------v---------------+  |
|  |       DSP Application        |  |
|  +------------------------------+  |
|                                    |
+------------------------------------+
```

### 2.3 Host-Side Components

The host-side components are responsible for:

- Initializing XRP.
- Discovering and opening DSP devices.
- Creating command queues.
- Preparing input and output buffers.
- Sending commands.
- Waiting for command completion.
- Retrieving processing results.

### 2.4 DSP-Side Components

The DSP-side components are responsible for:

- Initializing the XRP DSP environment.
- Registering command namespaces.
- Registering command handlers.
- Receiving commands.
- Accessing input buffers.
- Executing processing operations.
- Returning results.

### 2.5 Shared Memory

Shared memory is an important part of XRP communication.

Instead of copying large amounts of data through a message channel, the host and DSP can access an agreed memory region.

For example:

```text
             SHARED MEMORY
+--------------------------------+
| Command Information            |
+--------------------------------+
| Input Buffer                   |
|                                |
| Audio / Image / Sensor Data    |
+--------------------------------+
| Output Buffer                  |
|                                |
| Processing Results             |
+--------------------------------+
| Synchronization Information    |
+--------------------------------+
```

The processors must follow the appropriate memory-access, cache-coherency and synchronization rules defined by the platform.

---

## 3. XRP Terminology

Understanding XRP terminology is important before developing host or DSP applications.

### 3.1 Device

A device represents an available DSP processing endpoint that the host can access through XRP.

The host application opens a device before submitting commands.

### 3.2 Buffer

A buffer represents a memory region used to transfer input or output data between the host and DSP.

Example:

```text
+--------------------------+
|        Input Buffer      |
+--------------------------+
| Sample 1                 |
| Sample 2                 |
| Sample 3                 |
| Sample 4                 |
+--------------------------+
```

### 3.3 Buffer Group

A buffer group combines multiple buffers into one logical collection.

This is useful when a command requires multiple input and output data regions.

```text
             Buffer Group
                   |
       +-----------+-----------+
       |           |           |
       v           v           v
  +---------+ +---------+ +---------+
  | Buffer  | | Buffer  | | Buffer  |
  | Input 1 | | Input 2 | | Output  |
  +---------+ +---------+ +---------+
```

### 3.4 Queue

A queue is a communication channel used to submit commands to the DSP.

The host creates a queue and uses it to submit processing requests.

```text
Host Application
       |
       v
+----------------+
| Command Queue  |
+----------------+
       |
       +---- Command 1
       |
       +---- Command 2
       |
       +---- Command 3
       |
       v
    DSP Worker
```

### 3.5 Message

A message represents the command information exchanged between the host and DSP.

It may contain command identifiers, input data descriptions and other information required by the command handler.

### 3.6 Namespace

A namespace identifies a group of related commands.

It allows different applications or functional modules to register independent command handlers.

For example:

```text
             XRP Namespace
                   |
       +-----------+-----------+
       |           |           |
       v           v           v
   Audio NS    Image NS     Sensor NS
       |           |           |
       v           v           v
   Filter      Transform    Analysis
   Command     Command      Command
```

Namespaces help organize command handling and avoid command identifier conflicts.

---

## 4. Host–DSP Communication

### 4.1 Communication Model

XRP uses a controller-worker communication model.

The host acts as the controller:
- Creates processing requests.
- Supplies input data.
- Submits commands.
- Waits for completion.
- Retrieves results.

The DSP acts as the worker:
- Receives requests.
- Identifies the requested operation.
- Processes input data.
- Produces output data.
- Reports completion.

### 4.2 Communication Sequence

```text
HOST CPU                         XTENSA DSP
   |                                  |
   |  1. Prepare Input Data           |
   |                                  |
   |  2. Create Command               |
   |                                  |
   |  3. Submit Command               |
   |--------------------------------->|
   |                                  |
   |                     4. Receive Command
   |                                  |
   |                     5. Process Data
   |                                  |
   |                     6. Generate Output
   |                                  |
   |<---------------------------------|
   |  7. Completion Notification      |
   |                                  |
   |  8. Read Output Data             |
   |                                  |
   |  9. Continue Application         |
   |                                  |
```

### 4.3 Communication Mechanisms

Depending on the hardware implementation, XRP communication may use:

**Shared Memory**

Used for transferring command information and processing data.

**Interrupts**

An interrupt can notify the host or DSP that an operation requires attention.

**Polling**

A processor periodically checks communication status to determine whether a command has arrived or completed.

**Synchronization**

Synchronization mechanisms ensure that the host and DSP access shared resources in the correct order.

### 4.4 Synchronous and Asynchronous Processing

**Synchronous Processing**

The host waits until the DSP finishes processing.

```text
Host: Submit Command
          |
          v
      Wait for DSP
          |
          v
      DSP Processes
          |
          v
      Return Result
          |
          v
      Continue
```

**Asynchronous Processing**

The host submits a command and continues executing other operations while the DSP processes the request.

```text
Host: Submit Command
          |
          +-------> Continue Other Work
          |
          v
      DSP Processes
          |
          v
      Completion Event
          |
          v
      Host Retrieves Result
```

---

## 5. XRP Requirements

XRP requires compatible hardware, software and development tools.

### 5.1 Hardware Requirements

Typical hardware requirements include:

- A host processor capable of running the host-side XRP software.
- An Xtensa DSP core.
- A supported inter-processor communication mechanism.
- Shared memory accessible to both processors.
- Hardware interrupt support if interrupt-based communication is required.
- Platform-specific DSP reset and initialization controls.

### 5.2 Software Requirements

| Component | Purpose |
|---|---|
| Linux | Host operating system |
| Linux kernel headers | Kernel module compilation |
| Xtensa tools | DSP application compilation |
| GCC or compatible host compiler | Host application compilation |
| GNU Make | Build automation |
| XRP source code | Host, DSP and kernel implementation |
| Platform driver | Hardware-specific integration |

### 5.3 Development Environment

A typical development environment looks like this:

```text
+----------------------------------+
|        Linux Development PC      |
|                                  |
|  +----------------------------+  |
|  | Host Compiler              |  |
|  +----------------------------+  |
|                                  |
|  +----------------------------+  |
|  | Xtensa Cross Compiler      |  |
|  +----------------------------+  |
|                                  |
|  +----------------------------+  |
|  | GNU Make                   |  |
|  +----------------------------+  |
|                                  |
|  +----------------------------+  |
|  | XRP Source Repository      |  |
|  +----------------------------+  |
+----------------------------------+
                 |
                 v
        Target Embedded System
                 |
       +---------+---------+
       |                   |
       v                   v
   Host CPU            Xtensa DSP
```

### 5.4 Memory Requirements

Memory configuration must account for:

- DSP firmware memory.
- Shared communication memory.
- Input and output buffers.
- Command queues.
- DSP stack and heap.
- Alignment requirements.
- Cache-coherency requirements.

The exact memory layout depends on the target hardware and its memory map.

---

## 6. XRP Licensing

Before modifying or distributing XRP, developers should understand the licensing of the individual project components.

XRP contains multiple software components, including host libraries, DSP-side code and Linux kernel integration.

These components may have different licensing requirements.

### 6.1 Licensing Areas

| Component | Licensing Consideration |
|---|---|
| Host library | Review the license associated with host-side source files |
| DSP implementation | Review DSP source and firmware licensing |
| Linux kernel driver | Review kernel driver licensing and compatibility |
| Example applications | Check example-specific licensing |
| Third-party dependencies | Follow the licenses of included dependencies |

### 6.2 Important Considerations

- Review the repository's `LICENSE` and individual source-file headers.
- Check whether third-party libraries are included.
- Preserve required copyright notices.
- Understand redistribution requirements.
- Verify licensing compatibility before combining XRP with proprietary software.

**Note:** Do not assume that every component has identical licensing terms. The actual license declarations in the repository are authoritative.

---

## 7. XRP Implementations

XRP supports different implementation models depending on the target environment and communication architecture.

The main models covered here are:

1. Hosted implementation.
2. Standalone implementation.
3. Single-image implementation.

### 7.1 Hosted Implementation

The hosted model uses a Linux kernel driver between the host application and DSP.

```text
+-----------------------------+
|       Linux Userspace       |
|                             |
|     Host Application        |
|             |               |
|        XRP Host API         |
+-------------+---------------+
              |
+-------------v---------------+
|        Linux Kernel         |
|                             |
|       XRP Driver            |
+-------------+---------------+
              |
              v
       Shared Memory
              |
              v
+-----------------------------+
|          Xtensa DSP         |
|                             |
|       XRP DSP Code          |
|       Command Handlers      |
+-----------------------------+
```

**Characteristics:**
- Linux userspace integration.
- Kernel driver manages hardware communication.
- DSP firmware executes commands.
- Suitable for Linux-based embedded systems.

### 7.2 Standalone Implementation

The standalone model uses a more direct communication path between the host and DSP.

It does not rely on the same hosted Linux kernel-driver architecture.

```text
+-----------------------------+
|         Host CPU            |
|                             |
|     Host Application        |
|        XRP Library          |
+-------------+---------------+
              |
              |
       Shared Memory
              |
              |
+-------------v---------------+
|         Xtensa DSP          |
|                             |
|       DSP Firmware          |
|       Command Handlers      |
+-----------------------------+
```

**Characteristics:**
- Direct host-to-DSP communication.
- Platform-specific hardware configuration.
- Shared-memory communication.
- Useful in embedded environments with a suitable standalone host implementation.

### 7.3 Single-Image Implementation

The single-image model places host and DSP application code into a common application image or execution environment.

This model is intended for environments where the host and DSP software can be integrated into a single-image setup.

```text
+--------------------------------+
|       Single Application       |
|                                |
|  +--------------------------+  |
|  |      Host Code           |  |
|  +--------------------------+  |
|                                |
|  +--------------------------+  |
|  |      DSP Code            |  |
|  +--------------------------+  |
|                                |
|  +--------------------------+  |
|  |   XRP Communication      |  |
|  +--------------------------+  |
+--------------------------------+
```

**Characteristics:**
- Host and DSP application components are integrated.
- Deployment and startup are coordinated.
- Exact execution and memory arrangements depend on the platform.

---

## 8. Hosted Architecture

### 8.1 Overview

The hosted architecture is designed for systems where Linux runs on the host processor.

It separates the XRP implementation into three major layers:

1. Linux userspace.
2. Linux kernel driver.
3. DSP firmware.

### 8.2 Layer Responsibilities

**Linux Userspace**

The host application uses the XRP host library to:
- Open DSP devices.
- Create queues.
- Submit commands.
- Pass buffers.
- Retrieve results.

**Linux Kernel Driver**

The kernel driver handles platform-level operations such as:
- Device initialization.
- Shared-memory management.
- Interrupt handling.
- DSP reset and startup.
- Hardware communication.

**DSP Firmware**

The DSP firmware handles:
- Command reception.
- Namespace registration.
- Command execution.
- Buffer processing.
- Result generation.

### 8.3 Hosted Communication Flow

```text
       HOST PROCESSOR
+----------------------------+
|    Linux Application       |
+-------------+--------------+
              |
              v
+----------------------------+
|       XRP Host API         |
+-------------+--------------+
              |
              v
+----------------------------+
|     XRP Kernel Driver      |
+-------------+--------------+
              |
              v
+----------------------------+
|       Shared Memory        |
+-------------+--------------+
              |
              v
        XTENSA DSP
+----------------------------+
|       XRP DSP API          |
+-------------+--------------+
              |
              v
+----------------------------+
|      DSP Application      |
+----------------------------+
```

### 8.4 When to Use Hosted Architecture

Use the hosted model when:
- Linux is available on the host CPU.
- Kernel-level hardware integration is required.
- DSP firmware is loaded and managed by the platform.
- Applications need to access DSP processing through Linux userspace.

---

## 9. Standalone Architecture

### 9.1 Overview

The standalone architecture provides a direct communication model between the host application and DSP firmware.

It is useful when the system does not use the hosted Linux kernel-driver model for XRP communication.

### 9.2 Architecture

```text
+------------------------------+
|          Host CPU            |
|                              |
|    Standalone Application    |
|              |               |
|       XRP Host Library       |
+--------------+---------------+
               |
               v
+------------------------------+
|       Communication Layer    |
|                              |
|       Shared Memory           |
|       Message Exchange        |
|       Synchronization         |
+--------------+---------------+
               |
               v
+------------------------------+
|          Xtensa DSP           |
|                              |
|       XRP DSP Library         |
|              |               |
|       Command Handlers        |
|              |               |
|       DSP Processing          |
+------------------------------+
```

### 9.3 Important Components

**Host Library**

Provides the interface for creating commands and communicating with the DSP.

**DSP Firmware**

Contains the command handlers and processing functions.

**Shared Memory**

Provides the memory region used for exchanging data.

**Communication Configuration**

Defines communication addresses, memory regions and synchronization mechanisms.

### 9.4 Hardware Dependencies

Standalone implementations may require platform-specific configuration for:
- Shared-memory addresses.
- DSP startup and reset.
- Interrupt signaling.
- Cache management.
- Communication registers.
- Memory accessibility.

These dependencies must be implemented according to the target hardware.

---

## 10. Single-Image Architecture

### 10.1 Overview

The single-image architecture combines host-side and DSP-side application components into a common image or integrated execution environment.

The purpose is to simplify application deployment in systems that support this model.

Unlike the hosted model, which explicitly uses Linux userspace and a kernel driver, a single-image implementation can use a platform-specific communication mechanism.

### 10.2 Conceptual Architecture

```text
+-----------------------------------+
|          Single Image             |
|                                   |
|  +-----------------------------+  |
|  |        Host Application     |  |
|  +--------------+--------------+  |
|                 |                 |
|  +--------------v--------------+  |
|  |       XRP Interface         |  |
|  +--------------+--------------+  |
|                 |                 |
|  +--------------v--------------+  |
|  |     Communication Layer     |  |
|  +--------------+--------------+  |
|                 |                 |
|  +--------------v--------------+  |
|  |        DSP Application      |  |
|  +-----------------------------+  |
|                                   |
+-----------------------------------+
```

### 10.3 Key Characteristics

- Host and DSP software are integrated into a common deployment model.
- Startup and initialization can be coordinated.
- Communication still requires a valid host–DSP interface.
- Memory layout and execution details depend on the target platform.

### 10.4 Comparison of XRP Implementations

| Feature | Hosted | Standalone | Single-Image |
|---|---|---|---|
| Host environment | Linux userspace | Standalone host environment | Integrated application environment |
| Kernel driver | Used | Not required by the standalone model | Platform-dependent |
| DSP firmware | Separate DSP firmware | Separate DSP firmware | Integrated deployment model |
| Communication | Kernel-mediated hardware interface | Direct communication implementation | Platform-specific communication |
| Shared memory | Typically used | Typically used | Depends on implementation |
| Main purpose | Linux-based systems | Direct host–DSP integration | Integrated deployment |

---

## 11. Summary

The first ten XRP tutorials introduce the fundamental concepts required to understand the framework.

The key points are:

1. XRP enables host applications to offload processing tasks to an Xtensa DSP.
2. The host acts as the controller, while the DSP executes commands.
3. Shared memory provides an efficient mechanism for exchanging command data and processing buffers.
4. Queues organize command submission and execution.
5. Namespaces organize DSP command handlers.
6. Interrupts, polling and synchronization support communication between processors.
7. XRP development requires compatible hardware, Linux components where applicable, and Xtensa development tools.
8. Hosted, standalone and single-image models provide different integration approaches.
9. Hardware-specific memory, interrupt, reset and cache operations are important for correct communication.
10. Licensing must be checked at the individual component level.

### Overall XRP Processing Model

```text
       HOST APPLICATION
              |
              v
         XRP HOST API
              |
              v
       CREATE COMMAND
              |
              v
       PREPARE BUFFERS
              |
              v
      SUBMIT TO DSP QUEUE
              |
              v
      HOST-DSP COMMUNICATION
              |
              v
         XTENSA DSP
              |
              v
       COMMAND HANDLER
              |
              v
       PROCESS INPUT DATA
              |
              v
        GENERATE RESULT
              |
              v
       NOTIFY HOST CPU
              |
              v
       RETRIEVE RESULTS
              |
              v
       HOST APPLICATION
```

**Conclusion:** These ten topics establish the foundation for understanding XRP. They prepare developers for the next stages: setting up the development environment, configuring the Xtensa toolchain, exploring the source repository, building XRP components and developing host and DSP applications.
