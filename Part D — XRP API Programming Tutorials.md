# Part D — XRP API Programming Tutorials

## Quick Description

This tutorial explains how to use the XRP public API to communicate between a host processor and an Xtensa DSP. It covers device initialization, command queues, synchronous and asynchronous execution, buffer management, shared memory, data transfer, synchronization, error handling and resource cleanup. It provides the foundation for developing host applications that offload processing tasks to the DSP.

---

## 21. XRP API Introduction

### Overview

The XRP API provides a programming interface that allows host applications to communicate with an Xtensa DSP without directly managing all low-level communication operations.

The API provides functions for:
- Opening and closing DSP devices.
- Creating and destroying command queues.
- Submitting synchronous and asynchronous commands.
- Creating and mapping buffers.
- Managing buffer groups.
- Synchronizing memory access.
- Handling errors and releasing resources.

### API Architecture

```text
+--------------------------------+
|       Host Application         |
+---------------+----------------+
                |
                v
+--------------------------------+
|          XRP Public API        |
|                                |
| Device Management              |
| Queue Management               |
| Command Execution              |
| Buffer Management              |
| Event Management               |
+---------------+----------------+
                |
                v
+--------------------------------+
|       XRP Host Library         |
+---------------+----------------+
                |
                v
+--------------------------------+
|     Kernel / Communication    |
|           Layer                |
+---------------+----------------+
                |
                v
+--------------------------------+
|       Shared Memory / DSP      |
+---------------+----------------+
                |
                v
|          Xtensa DSP            |
+--------------------------------+
```

The exact implementation of the communication layer depends on the selected XRP implementation and target platform.

---

## 22. Opening an XRP Device

### Overview

Before submitting commands, the host application must open an XRP device.

The function `xrp_open_device()` is used to obtain a device handle.

The returned handle represents the DSP device and is used by subsequent XRP API operations.

### Illustrative Example

```c
#include <xrp_api.h>

struct xrp_device *device;

device = xrp_open_device(0, &status);

if (!device) {
    printf("Failed to open XRP device\n");
    return -1;
}
```

The exact function signature and status declarations should be checked against the repository's `xrp_api.h`.

### Device Opening Flow

```text
+----------------------+
|   Host Application   |
+----------+-----------+
           |
           v
+----------------------+
|   xrp_open_device()  |
+----------+-----------+
           |
           v
+----------------------+
|   Device Available?  |
+----------+-----------+
           |
      +----+----+
      |         |
     Yes        No
      |         |
      v         v
 Device      Return
 Handle      Error
      |
      v
 Continue API Operations
```

---

## 23. Closing an XRP Device

### Overview

After completing all processing operations, the application should close the device.

Closing the device releases the associated device resource.

The application should first release dependent resources, such as queues and buffers, according to the API ownership rules.

### Resource Flow

```text
Open Device
     |
     v
Create Queue
     |
     v
Execute Commands
     |
     v
Destroy Queue
     |
     v
Close Device
     |
     v
Resources Released
```

Proper device management prevents resource leaks and unnecessary device usage.

---

## 24. Creating a Command Queue

### Overview

A command queue provides a communication channel through which the host submits processing requests to the DSP.

The host creates a queue associated with an opened device.

### Illustrative Example

```c
struct xrp_queue *queue;

queue = xrp_create_queue(device, &status);

if (!queue) {
    printf("Queue creation failed\n");
    return -1;
}
```

### Queue Architecture

```text
+---------------------+
|  Host Application   |
+----------+----------+
           |
           v
+---------------------+
|    XRP Queue        |
+----------+----------+
           |
     +-----+-----+
     |     |     |
     v     v     v
   CMD1  CMD2  CMD3
     |     |     |
     +-----+-----+
           |
           v
      Xtensa DSP
```

Queues organize command submission and execution.

---

## 25. Destroying a Command Queue

### Overview

When a queue is no longer required, it should be destroyed using the corresponding XRP queue-management function.

Queue destruction releases queue-related resources.

Before destroying a queue, ensure that pending operations have completed or have been handled according to the API's documented lifecycle rules.

### Queue Lifecycle

```text
+----------------------+
|   Create Queue       |
+----------+-----------+
           |
           v
+----------------------+
|   Submit Commands    |
+----------+-----------+
           |
           v
+----------------------+
|  Complete Operations |
+----------+-----------+
           |
           v
+----------------------+
|   Destroy Queue      |
+----------------------+
```

---

## 26. Synchronous Commands

### Overview

Synchronous execution means the host waits until the DSP completes the submitted command.

The function `xrp_run_command_sync()` is used for synchronous command execution.

### Illustrative Example

```c
xrp_run_command_sync(
    queue,
    input_data,
    input_size,
    output_data,
    output_size,
    &status
);
```

The parameters shown are illustrative. Refer to the actual API header for the exact function signature.

### Execution Flow

```text
HOST CPU                    DSP
   |                         |
   | Submit Command          |
   |------------------------>|
   |                         |
   |                    Process Data
   |                         |
   |                    Generate Result
   |                         |
   |<------------------------|
   | Receive Completion      |
   |                         |
   v                         |
Continue Execution           |
```

Synchronous execution is useful when the application requires the processing result immediately before continuing.

---

## 27. Asynchronous Commands

### Overview

Asynchronous execution allows the host to submit a command without waiting for the DSP to finish processing.

The function `xrp_enqueue_command()` is used to enqueue asynchronous operations.

The host can perform other tasks while the DSP processes the request.

### Illustrative Flow

```text
HOST CPU                    DSP
   |                         |
   | Enqueue Command         |
   |------------------------>|
   |                         |
   | Continue Other Work     |
   |                         |
   |                    Process Command
   |                         |
   |<------------------------|
   | Completion Notification |
   |                         |
   v                         |
Retrieve Result              |
```

Asynchronous execution can improve system utilization when multiple independent operations are available.

---

## 28. Event Management

### Overview

Events are used to determine whether an asynchronous command has completed.

The host can wait for an event before accessing the processing result.

Event management helps coordinate asynchronous execution and resource usage.

### Event Flow

```text
+----------------------+
|  Submit DSP Command  |
+----------+-----------+
           |
           v
+----------------------+
|  DSP Processing      |
+----------+-----------+
           |
           v
+----------------------+
| Completion Event     |
+----------+-----------+
           |
           v
+----------------------+
| Host Waits for Event |
+----------+-----------+
           |
           v
+----------------------+
| Retrieve Result      |
+----------------------+
```

The actual event functions and ownership rules should be verified in the XRP API header.

---

## 29. Buffer Creation

### Overview

Buffers represent memory regions used to transfer data between the host and DSP.

The function `xrp_create_buffer()` creates an XRP buffer.

Buffers can contain:
- Input data.
- Output data.
- Intermediate processing data.

### Illustrative Example

```c
struct xrp_buffer *buffer;

buffer = xrp_create_buffer(
    device,
    buffer_size,
    initial_data,
    &status
);
```

This is a conceptual example; use the exact parameter definitions from `xrp_api.h`.

### Buffer Architecture

```text
+--------------------------+
|      XRP Buffer          |
+--------------------------+
|                          |
|      Input Data          |
|                          |
+--------------------------+
|                          |
|      Output Data         |
|                          |
+--------------------------+
```

---

## 30. Buffer Mapping

### Overview

Buffer mapping provides access to buffer memory from the host application.

The function `xrp_map_buffer()` maps a buffer into the host address space.

The application can then access the mapped memory according to the specified access permissions.

### Mapping Flow

```text
+---------------------+
|    XRP Buffer       |
+----------+----------+
           |
           v
+---------------------+
|  xrp_map_buffer()   |
+----------+----------+
           |
           v
+---------------------+
| Host Virtual Address|
+----------+----------+
           |
           v
      Read / Write
      Buffer Data
```

Mapping permissions and synchronization requirements must be respected.

---

## 31. Buffer Unmapping

### Overview

After accessing a mapped buffer, the application should unmap it using `xrp_unmap_buffer()`.

Unmapping ends the application's mapped access to the buffer.

This is important for correct resource management and memory ownership.

### Buffer Lifecycle

```text
Create Buffer
     |
     v
Map Buffer
     |
     v
Access Data
     |
     v
Unmap Buffer
     |
     v
Release Buffer
```

The buffer should only be released after all operations using it have completed.

---

## 32. Buffer Groups

### Overview

A buffer group combines multiple buffers into one logical collection.

It is useful when a DSP command requires several input and output memory regions.

For example, an image-processing operation may require:
- Input image.
- Configuration parameters.
- Output image.

### Buffer Group Architecture

```text
             Buffer Group
                   |
       +-----------+-----------+
       |           |           |
       v           v           v
  +---------+ +---------+ +---------+
  | Input 1 | | Input 2 | | Output  |
  | Image   | | Config  | | Image   |
  +---------+ +---------+ +---------+
                   |
                   v
               DSP Command
```

Buffer groups simplify the organization and transfer of multiple data buffers.

---

## 33. Buffer Access Permissions

### Overview

Buffer access permissions determine whether a processor can read or modify a memory region.

Common access modes include:

| Permission | Description |
|---|---|
| Read-only | Data can be read but not modified |
| Read/write | Data can be read and modified |

### Access Model

```text
        XRP Buffer
             |
       +-----+-----+
       |           |
       v           v
   Read-only   Read/Write
       |           |
       v           v
   Read Data    Read Data
                  |
                  v
              Modify Data
```

Correct permissions prevent unintended modification of shared data.

---

## 34. Shared Memory

### Overview

Shared memory allows the host and DSP to access common memory regions.

It is particularly useful for transferring large data blocks without repeatedly copying them through a separate communication channel.

### Shared Memory Architecture

```text
+--------------------+       +--------------------+
|     Host CPU       |       |     Xtensa DSP     |
|                    |       |                    |
| Host Application   |       | DSP Application    |
|        |           |       |        |           |
+--------+-----------+       +--------+-----------+
         |                            |
         v                            v
+------------------------------------------------+
|                 Shared Memory                  |
|                                                |
|  +-------------+  +-------------+              |
|  | Input Data  |  | Output Data |              |
|  +-------------+  +-------------+              |
|                                                |
|  +------------------------------------------+  |
|  |       Command / Synchronization Data     |  |
|  +------------------------------------------+  |
+------------------------------------------------+
```

Both processors must agree on the memory layout and access rules.

---

## 35. Memory Allocation

### Overview

XRP applications may use different types of memory depending on the target platform.

Typical memory categories include:

**System Memory**

Memory allocated by the host operating system.

**Device-Specific Memory**

Memory configured or reserved for DSP access.

**Shared Memory**

Memory accessible to both processors.

The availability and allocation method depend on the hardware platform and XRP implementation.

### Memory Organization

```text
             System Memory
                   |
       +-----------+-----------+
       |           |           |
       v           v           v
   Host Data   DSP Memory   Shared Memory
                             |
                      +------+------+
                      |             |
                      v             v
                  Host Access   DSP Access
```

Memory allocation must account for physical accessibility, alignment and cache requirements.

---

## 36. Memory Mapping

### Overview

Memory mapping connects a physical or device memory region to a processor's virtual address space.

The host and DSP may use different virtual addresses to access the same shared physical memory.

### Mapping Concept

```text
          Physical Memory
          Shared Region
                |
       +--------+--------+
       |                 |
       v                 v
 Host Virtual       DSP Virtual
   Address            Address
       |                 |
       v                 v
   Host CPU          Xtensa DSP
```

Correct mapping is essential for safe access to shared memory.

---

## 37. Data Transfer

### Overview

Data transfer involves passing input buffers to the DSP and retrieving processed output buffers.

For example, an audio application may pass audio samples to the DSP for filtering and receive the processed samples.

### Data Transfer Flow

```text
+---------------------+
|  Host Input Data    |
+----------+----------+
           |
           v
+---------------------+
|   Input Buffer      |
+----------+----------+
           |
           v
+---------------------+
|   Submit Command    |
+----------+----------+
           |
           v
+---------------------+
|     Xtensa DSP      |
|                     |
|   Process Input     |
+----------+----------+
           |
           v
+---------------------+
|   Output Buffer     |
+----------+----------+
           |
           v
+---------------------+
|   Host Application  |
|   Read Result       |
+---------------------+
```

The host and DSP must agree on buffer size, format and ownership.

---

## 38. Memory Synchronization

### Overview

Memory synchronization ensures that the host and DSP observe the correct data when accessing shared memory.

It is particularly important on systems where processors have separate caches.

Potential synchronization operations include:
- Cache clean or flush.
- Cache invalidation.
- Memory barriers.
- Ownership transfer.
- Completion synchronization.

### Synchronization Flow

```text
       HOST CPU
           |
           v
    Write Input Data
           |
           v
     Cache Clean
           |
           v
    Notify DSP
           |
           v
       XTENSA DSP
           |
           v
    Read Input Data
           |
           v
    Process Data
           |
           v
    Write Output Data
           |
           v
    Cache Maintenance
           |
           v
    Notify Host CPU
           |
           v
      Read Result
```

The exact cache operations depend on the hardware cache architecture and memory attributes.

---

## 39. Error Handling

### Overview

Error handling allows the application to detect and respond to failures during XRP operations.

Potential errors include:
- Device opening failure.
- Queue creation failure.
- Buffer allocation failure.
- Invalid command parameters.
- DSP processing failure.
- Communication timeout.
- Memory mapping failure.

### Error Handling Flow

```text
+----------------------+
|   Execute XRP API    |
+----------+-----------+
           |
           v
+----------------------+
|    Check Status      |
+----------+-----------+
           |
      +----+----+
      |         |
   Success     Error
      |         |
      v         v
 Continue    Handle Error
      |         |
      v         v
 Process    Cleanup Resources
```

Applications should check the returned status after each operation and avoid using invalid handles or incomplete results.

---

## 40. Resource Cleanup

### Overview

Resource cleanup ensures that all allocated XRP resources are released after processing.

Resources may include:
- Device handles.
- Command queues.
- Buffer groups.
- Buffers.
- Mapped memory.
- Event objects.

Cleanup should follow the API's documented ownership and lifetime rules.

### Resource Lifecycle

```text
        Open Device
             |
             v
        Create Queue
             |
             v
        Create Buffers
             |
             v
         Map Buffers
             |
             v
        Submit Commands
             |
             v
       Wait for Completion
             |
             v
       Unmap Buffers
             |
             v
       Release Buffers
             |
             v
        Destroy Queue
             |
             v
         Close Device
             |
             v
       All Resources Released
```

Always ensure that pending DSP operations have completed before releasing resources they may still access.

---

## Summary

Part D introduces the XRP public API and explains how host applications interact with an Xtensa DSP.

The main concepts covered are:

1. XRP public API architecture and programming model.
2. Opening and closing DSP devices.
3. Creating and destroying command queues.
4. Synchronous and asynchronous command execution.
5. Event-based completion handling.
6. Creating, mapping and unmapping buffers.
7. Managing buffer groups and access permissions.
8. Shared-memory allocation and mapping.
9. Input and output data transfer.
10. Cache coherency and memory synchronization.
11. Error handling and resource cleanup.

### Complete XRP API Workflow

```text
       Host Application
              |
              v
       Open XRP Device
              |
              v
       Create Command Queue
              |
              v
        Create Buffers
              |
              v
         Map Buffers
              |
              v
        Prepare Input Data
              |
              v
        Submit DSP Command
              |
              v
        DSP Processes Data
              |
              v
       Wait for Completion
              |
              v
        Retrieve Output
              |
              v
         Unmap Buffers
              |
              v
        Release Resources
              |
              v
         Close Device
```

**Conclusion:** The XRP API provides the programming interface required to manage DSP devices, submit processing commands and exchange data between host and DSP processors. Understanding its device, queue, buffer and synchronization mechanisms is essential for developing reliable XRP-based applications.

**Reference:** [XRP GitHub Repository](https://github.com/foss-xtensa/xrp) — consult `xrp_api.h` and the user guide for exact API signatures, parameters, return values and resource ownership rules.