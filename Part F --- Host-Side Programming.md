# Part F — Host-Side Programming

## 58. Host XRP Architecture

The host-side XRP library provides the interface through which a host application communicates with a remote DSP processor.

It manages device access, command queues, buffer handling, command submission, and result retrieval.

### Host-Side Architecture

```text
          Host Application
                 |
                 v
          XRP Host Library
                 |
        +--------+--------+
        |        |        |
      Device   Queue    Buffer
     Manager  Manager   Manager
        |        |        |
        +--------+--------+
                 |
          Host XRP Driver
                 |
          Shared Memory
                 |
                 v
             DSP Runtime
                 |
          DSP Application
```

The host library hides much of the low-level communication complexity and provides APIs for submitting work to the DSP.

---

## 59. Host Application Initialization

Host application initialization prepares the environment required for XRP communication.

Typical steps include:
- Initializing application resources.
- Opening the XRP device.
- Creating command queues.
- Preparing input and output buffers.
- Initializing synchronization objects.

### Initialization Flow

```text
       Host Application
              |
              v
      Initialize Resources
              |
              v
       Open XRP Device
              |
              v
       Create Command Queue
              |
              v
       Prepare Buffers
              |
              v
        Ready for DSP
```

Initialization should complete successfully before commands are submitted.

---

## 60. Opening DSP Devices

Opening a DSP device establishes a connection between the host application and a particular remote-processing device.

The application first identifies an available device and then opens it using the supported XRP device API.

A device handle is typically used for subsequent operations such as queue creation and resource management.

### Device Access

```text
        Host Application
               |
               v
       Discover DSP Device
               |
               v
       Select Target Device
               |
               v
        Open Device
               |
               v
        Device Handle
               |
               v
       Create Command Queue
```

The device identifier and discovery mechanism depend on the operating system and platform integration.

---

## 61. Creating Command Queues

A command queue provides a communication channel through which the host submits commands to the DSP.

A queue organizes command submission and completion tracking. Applications can use one or more queues depending on their workload and the capabilities of the XRP implementation.

### Queue Creation

```text
        Host Application
               |
               v
          Open Device
               |
               v
       Create Command Queue
               |
               v
          Queue Handle
               |
               v
       Submit DSP Commands
```

The queue handle identifies the communication channel used by later command operations.

---

## 62. Sending Commands

Sending commands is the main mechanism through which the host requests DSP processing.

A command typically contains:
- A namespace identifier.
- A command identifier.
- Input data or parameters.
- References to associated buffers.

The host submits the request through an XRP command queue.

### Command Submission

```text
         Host Application
                |
                v
         Prepare Command
                |
                v
         Add Input Data
                |
                v
         Submit to Queue
                |
                v
          Shared Memory
                |
                v
          DSP Runtime
                |
                v
         Execute Handler
```

The host should validate command parameters and ensure that all required buffers are ready before submission.

---

## 63. Sending Input Data

Input data is the information that the DSP needs to perform the requested operation.

Small parameters may be passed as command input data, while larger datasets are commonly supplied through shared buffers.

Examples include:
- Audio samples.
- Image data.
- Sensor readings.
- Processing parameters.
- Model input tensors.

### Input Data Flow

```text
        Host Application
               |
               v
         Prepare Input
               |
               v
        Allocate Buffer
               |
               v
         Write Data
               |
               v
       Submit Command
               |
               v
          DSP Reads
             Input
```

The host must ensure that the input data remains valid for the period required by the command and follows the buffer ownership rules of the implementation.

---

## 64. Receiving Results

After processing a command, the DSP makes its output available to the host.

The host retrieves the result from the appropriate output buffer or command response.

Typical results include:
- Processed signal data.
- Image-processing output.
- Numerical results.
- Status information.
- Error codes.

### Result Retrieval

```text
           DSP Processor
                 |
                 v
          Execute Command
                 |
                 v
          Generate Output
                 |
                 v
          Write Result Data
                 |
                 v
          Signal Completion
                 |
                 v
          Host Application
                 |
                 v
          Read DSP Result
```

The host should check the command status before using output data to ensure that processing completed successfully.

---

## 65. Asynchronous Processing

Asynchronous processing allows the host to submit a command without waiting for the DSP to finish immediately.

After submitting the command, the host can perform other tasks while the DSP processes the request.

This improves host-side responsiveness and can allow multiple operations to overlap.

### Asynchronous Execution

```text
       Host Application              DSP
              |                       |
              | Submit Command        |
              |---------------------->|
              |                       |
              | Continue Other Work   |
              |                       |
              |                 Process Command
              |                       |
              |                       |
              |<----------------------|
              | Completion Event      |
              |                       |
              v                       |
         Read Result                  |
```

Asynchronous submission is useful for applications that need to remain responsive or manage several outstanding DSP operations.

---

## 66. Event Synchronization

Event synchronization allows the host to determine when an asynchronous command has completed.

Instead of continuously checking the command state, the application can wait for an event associated with the operation.

The event provides a synchronization point between command submission and result processing.

### Event-Based Synchronization

```text
        Submit Command
              |
              v
       Continue Host Work
              |
              v
        Wait for Event
              |
              v
      DSP Completes Work
              |
              v
       Signal Completion
              |
              v
       Host Wakes Up
              |
              v
       Retrieve Result
```

Waiting for completion is important before reading output buffers or releasing resources that are still being used by the DSP.

---

## 67. Multiple Commands

A host application may submit multiple commands to perform a sequence of DSP operations.

For example, an audio application might:
1. Submit audio input.
2. Apply filtering.
3. Perform signal analysis.
4. Retrieve the processed output.

Command ordering matters when one operation depends on the result of another.

### Sequential Command Processing

```text
       Host Application
              |
              v
        Command 1
              |
              v
        Command 2
              |
              v
        Command 3
              |
              v
        Command 4
              |
              v
         DSP Queue
              |
              v
       DSP Execution
```

The application should not assume that commands submitted through different queues execute in a particular global order unless the implementation explicitly guarantees it.

---

## 68. Multiple Queues

Multiple command queues allow a host application to maintain separate streams of DSP work.

For example, an application may use:
- One queue for audio processing.
- Another queue for image processing.
- A separate queue for background computation.

### Multiple Queue Architecture

```text
              Host Application
                     |
          +----------+----------+
          |          |          |
          v          v          v
       Queue 1    Queue 2    Queue 3
          |          |          |
          v          v          v
       Audio      Image     Background
      Commands   Commands    Commands
          |          |          |
          +----------+----------+
                     |
                     v
                 DSP Runtime
```

Multiple queues can help organize workloads and may allow independent operations to progress concurrently, depending on the DSP runtime and hardware support.

---

## 69. Multiple DSP Devices

Some systems contain more than one DSP device.

A host application can open multiple devices and distribute processing tasks among them when supported by the platform.

For example:
- DSP 0 may process audio.
- DSP 1 may perform image processing.
- DSP 2 may handle additional signal-processing tasks.

### Multiple DSP Architecture

```text
                 Host Application
                        |
             +----------+----------+
             |          |          |
             v          v          v
          Device 0   Device 1   Device 2
             |          |          |
             v          v          v
           DSP 0      DSP 1      DSP 2
             |          |          |
             v          v          v
          Audio      Image     Signal
        Processing Processing Processing
```

Each device generally requires its own device handle and associated resources.

The application must manage resource allocation, command completion, and device-specific failures independently.

---

## 70. Host Error Handling

Host-side error handling detects and responds to failures that occur during device access, command submission, communication, or DSP execution.

Common error conditions include:
- Device unavailable.
- Device initialization failure.
- Queue creation failure.
- Invalid command parameters.
- Buffer allocation or mapping failure.
- DSP execution errors.
- Command timeouts.
- Communication failures.

### Error Handling Flow

```text
        Host Application
               |
               v
        Submit Command
               |
               v
        Check Status
               |
          Successful?
           /       \
         Yes        No
          |          |
          v          v
      Get Result  Identify Error
                     |
                     v
               Handle Failure
                     |
              +------+------+
              |             |
              v             v
           Retry       Report Error
              |
              v
         Continue
```

A robust application should:
- Check return values from XRP API calls.
- Use timeouts when waiting for completion.
- Avoid reading output buffers after failed commands.
- Release resources safely.
- Handle device disconnection or DSP failure.
- Log useful diagnostic information.

Not every error is recoverable. The application should distinguish between retryable errors and failures that require device or application reinitialization.

---

# Part F Summary

Host-side XRP programming focuses on controlling DSP devices and managing the communication lifecycle from the host application.

The typical workflow is:

```text
        Host Application
               |
               v
       Initialize Resources
               |
               v
         Open DSP Device
               |
               v
       Create Command Queue
               |
               v
        Prepare Input Data
               |
               v
         Submit Command
               |
               v
       DSP Executes Command
               |
               v
       Wait for Completion
               |
               v
        Retrieve Results
               |
               v
        Handle Errors
               |
               v
        Release Resources
```

These tutorials explain how to initialize the host XRP environment, communicate with DSP devices, submit synchronous or asynchronous commands, manage multiple queues, retrieve results, and handle communication failures.

Together, these concepts form the foundation for developing host applications that use DSP acceleration through XRP.