# SPI DTC Loopback

## Overview

This software building block (SBB) demonstrates typical use of the Renesas FSP SPI HAL APIs with SPI and Data Transfer Controller (DTC) modules.

The reference application configures DTC support for two SPI channels, channel 0 and channel 1, operating in Master and Slave modes. After initialization, the Master and Slave transmit and receive data in response to commands entered through a serial terminal. SPI data is moved through the DTC module.

## Supported Boards

This document covers only the following boards:

- FPB-RA4E2
- FPB-RA6E2

## What Use Case Does This SBB Demonstrate?

The SBB demonstrates how to:

1. Configure two SPI channels in Master and Slave modes.
2. Configure DTC support for SPI transmission and reception.
3. connect the Master and Slave SPI signals with three jumper wires.
4. Accept transfer commands and data from a serial terminal.
5. Transmit and receive SPI data through the DTC module.
6. Exercise supported write, read, and WriteRead behavior from a reference application.

## What Does the SBB Core Code Do?

According to the source README, the SBB initializes the SPI and DTC modules, configures SPI channel 0 and channel 1 as Master and Slave, and performs terminal-directed data transfers through the DTC.

The source `readme.txt` does not identify the public API names, callbacks, source filenames, command syntax, channel-to-role assignment, chip-select handling, timeout behavior, or error recovery. These details must be documented from the implementation before release.

## Reusable SBB Code and Reference Application Code

### Reusable SBB code

The reusable portion should contain implementation that is independent of the selected FPB board and terminal interface, including:

- SPI Master and Slave initialization.
- DTC configuration for SPI transmit and receive operations.
- Transfer setup and execution.
- Transfer-completion and error handling.
- Validation of transfer sizes and operation constraints.
- Status reporting through a defined interface.

### Reference application code

The following behavior is reference-application or demonstration behavior:

- Reading commands and transfer data from a serial terminal.
- Displaying menus, prompts, results, and errors.
- Applying the documented input restrictions.
- Connecting the two SPI channels on one board with jumper wires.
- Selecting board-specific SPI pins and connectors.
- Using the J-Link On-Board serial port for terminal output.

A product application may replace the terminal command interface and physical loopback arrangement while retaining reusable SPI and DTC transfer logic.

## Public Functions to Review First

The source `readme.txt` does not list public function names. Before integration, identify and document the functions responsible for:

1. Initializing the SBB.
2. Opening the SPI Master and Slave instances.
3. Configuring or enabling DTC transfers.
4. Starting write, read, and WriteRead operations.
5. Processing terminal commands and input data.
6. Handling transfer-completion callbacks.
7. Reporting SPI or DTC errors.
8. Closing or resetting the SBB, if supported.

Use the actual function names exported by the delivered source and header files. Do not derive API names from this document.

## Hardware Requirements

- One supported Renesas RA FPB board.
- One USB cable suitable for programming and debugging the selected board.
- Three jumper wires for MISO, MOSI, and RSPCK connections.
- A host PC with a serial-terminal application, such as Tera Term.

## General Hardware Setup

1. Connect the RA board USB debug port to the host PC with the appropriate USB cable.
2. Connect the Master-side and Slave-side MISO signals with a jumper wire.
3. Connect the Master-side and Slave-side MOSI signals with a jumper wire.
4. Connect the Master-side and Slave-side RSPCK signals with a jumper wire.
5. Program and debug the reference application through the board debug connection.

The source does not document a chip-select jumper connection. Verify chip-select handling in the project configuration and implementation before adapting the example.

## Board Configuration and Connections

### FPB-RA4E2

| Signal | First SPI endpoint | Second SPI endpoint |
|---|---|---|
| MISO | P110, J4:3 | P100, J4:16 |
| MOSI | P109, J4:2 | P101, J4:15 |
| RSPCK | P111, J4:4 | P102, J4:14 |

The source does not explicitly state which endpoint is the Master and which is the Slave.

### FPB-RA6E2

| Signal | First SPI endpoint | Second SPI endpoint |
|---|---|---|
| MISO | P110, J4:3 | P100, J4:16 |
| MOSI | P109, J4:2 | P101, J4:15 |
| RSPCK | P111, J4:4 | P102, J4:14 |

The source does not explicitly state which endpoint is the Master and which is the Slave.

## Transfer and Input Constraints

The source README specifies the following constraints:

- User-entered data must be less than 64 bytes.
- SPI bit width is configured as 32 bits.
- Each 4-byte block of user data is transferred in one SPI transmission.
- For a WriteRead operation, the number of bytes sent for the Slave must be less than the number of bytes sent for the Master.
- Operation is not guaranteed for terminal input other than integer or character data. Examples called out as unsupported include floating-point values and special characters.

The source does not explain how non-multiple-of-four input lengths are padded, rejected, or transferred. Verify this behavior in the implementation.

## Serial Terminal Configuration

Use the serial port provided by the J-Link On-Board interface.

| Setting | Value |
|---|---|
| Speed | 115200 baud |
| Data | 8 bits |
| Parity | None |
| Stop bits | 1 |
| Flow control | None |

For Tera Term:

1. Open **Setup > Terminal**.
2. Under **New-line**, set both **Receive** and **Transmit** to **CR**.
3. Enable **Local echo** to display entered characters.

## Running the Reference Application

1. Apply the three SPI jumper connections for the selected board.
2. Connect the board debug port to the host PC.
3. Program and start the reference application.
4. Open the J-Link On-Board virtual COM port in a serial terminal.
5. Configure the terminal for 115200 baud, 8 data bits, no parity, 1 stop bit, and no flow control.
6. Enter a supported command and data using the terminal interface provided by the application.
7. Observe the reported SPI transfer result.

The source does not provide the terminal menu, command syntax, or example command sequence.

## What Must Change for Another Board or Application?

### SPI channels and pins

- Select two SPI instances that support the required Master and Slave configuration.
- Assign valid MISO, MOSI, RSPCK, and chip-select pins.
- Update the physical connection instructions.
- Confirm the Master and Slave role assigned to each channel.

### DTC configuration

- Configure DTC support for the selected SPI transmit and receive operations.
- Verify transfer width, source and destination addressing, activation sources, and transfer counts against the implementation requirements.
- Confirm that DTC resources do not conflict with other application modules.

The source states that DTC supports the SPI transfers but does not provide the DTC instance names or detailed configuration.

### Clocks and timing

- Recalculate the SPI bit rate for the target peripheral clock.
- Confirm compatible clock polarity, clock phase, and bit order on both SPI instances.
- Review timeout and completion handling in the implementation.

The source does not specify SPI frequency, clock polarity, clock phase, bit order, chip-select timing, timeout values, or measured timing margins.

### Data handling

- Preserve or deliberately revise the less-than-64-byte input restriction.
- Account for the configured 32-bit SPI width and 4-byte transfer blocks.
- Define behavior for input lengths that are not multiples of four.
- Validate WriteRead Master and Slave byte-count requirements.
- Define accepted and rejected terminal input formats.

### Memory, task priorities, and middleware

The source does not specify buffer allocation, alignment requirements, cache handling, memory usage, RTOS requirements, task priorities, stack sizes, or synchronization behavior. Review these areas when integrating the SBB into a larger application.

## Dependencies

The demonstrated project explicitly depends on:

- Renesas FSP SPI HAL modules.
- DTC modules supporting SPI data transmission and reception.
- Two SPI channels configured in Master and Slave modes.
- Board-specific SPI pin configuration.
- Three jumper wires for MISO, MOSI, and RSPCK.
- A J-Link On-Board serial connection.
- A serial-terminal application.

The source does not specify the FSP version, toolchain version, IDE version, exact SPI and DTC instance names, transfer module configuration, or other middleware dependencies.

## Demonstrated Behavior and Performance

The source documents the following behavior:

- SPI channel 0 and channel 1 are configured in Master and Slave modes.
- The Master and Slave transmit and receive data in response to terminal commands.
- SPI data is transmitted and received through the DTC module.
- The SPI bit width is 32 bits.
- Each 4-byte user-data block is handled in one SPI transmission.

The source does not provide SPI frequency, throughput, latency, CPU utilization, DTC savings, memory usage, error rate, transfer count, test duration, or other measured performance. The example therefore demonstrates functional behavior rather than quantified performance.

## File Ownership and Modification Guidance

The source `readme.txt` does not list the project files. Classify the delivered files before modifying them.

### Safe places for customer changes

- Customer application source files.
- Customer-owned board configuration.
- SBB wrapper or adaptation files.
- Clearly identified user-editable regions in generated integration files.

### Reusable SBB files

Keep SPI and DTC transfer logic separate from board pin assignments, terminal presentation, and physical loopback instructions. Track any local changes made directly to reusable SBB files.

### Generated or preset files

Treat FSP-generated source, pin configuration, stacks, and configuration artifacts as generated unless the project explicitly identifies them as hand-maintained. Apply configuration changes through the supported FSP configuration rather than editing generated output directly.

### Demo-only files

Treat terminal menus, user-command processing, board-specific loopback wiring, and result presentation as demo-only unless the product application explicitly adopts them.

## Updating to a Newer SBB Release After Local Modification

To reduce upgrade conflicts:

1. Keep released reusable SBB files unchanged where possible.
2. Put product-specific behavior in wrapper, adaptation, or application files.
3. Keep board configuration separate from reusable SPI and DTC logic.
4. Record the adopted SBB version and all local modifications.
5. Compare the original release, local version, and newer release during an upgrade.
6. Reapply only the required local changes.
7. Regenerate FSP-managed files using the FSP version required by the newer release.
8. Rebuild and repeat write, read, WriteRead, boundary-input, and error-path tests on each supported target board.

Direct edits to reusable or generated files increase the effort required to adopt a newer SBB release.

## Expected Result

After the board is configured, wired, programmed, and started:

1. The terminal provides the reference application's transfer interface.
2. The user enters a supported command and data.
3. The SPI Master and Slave perform the requested operation.
4. DTC transfers data for the SPI operation.
5. The application reports the result through the terminal.

