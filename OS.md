# Operating Systems notes

## What is an Operating System?
- Definition: it is a program between user-level and hardware level.
    - Allows users to execute programs
    - Since a lot of users are dumb and like to break things, operating system will `aim` to provide protection, reliability, efficiency, predictability and convenience. (Ring 0 1 2 3)

### Computer System Organization
- One or more CPUs, device controllers connect through common bus providing access to `shared memory`. 
- Concurrent execution of CPUs and devices competing for memory cycles.

#### North Bridge
- Role: The Northbridge is responsible for handling high-speed communication between the CPU and performance-critical components.
- Purpose: Acts as a communication bridge between the CPU and memory (RAM), graphics processing unit (GPU), and sometimes the Southbridge.
- Examples of Things It Handles:
    - Memory Controller: Connects the CPU to the system RAM.
    - Graphics Interface: Manages communication between the CPU and GPU, usually via PCI Express (PCIe).
    - High-Speed Interconnects: Provides the link to the Southbridge.

#### South Bridge
- Role: The Southbridge manages lower-speed communication between the CPU and less performance-critical peripherals.
- Purpose: Acts as a hub for input/output (I/O) operations and connects various components of the motherboard to the CPU.
- Examples of Things It Handles:
    - Storage Controllers: Interfaces like SATA, IDE, or NVMe for hard drives and SSDs.
    - USB Ports: Manages USB controllers for external device communication.
    - Audio and Ethernet: Handles onboard audio and networking components.
    - BIOS/UEFI: Facilitates interaction between the system firmware and the CPU.
    - Peripheral Buses: Interfaces like PCI, PCIe (non-GPU), and legacy buses (e.g., ISA).

#### Bus Interconnect
- definition: A Bus interconnect is the hardware connection between multiple CPU sockets and memory banks, allowing them to share memory across physical boundaries.







## Bootstrap Program
- Initial Program run when system starts
    - Initializes system, CPU registers, memory, devices...
    - Loads OS kernel into main memory
    - There are going to use GRUB for Bootstrap. (But I will also talk about systemed-boot later)

### Boot Sector (GRUB)
- PC architecture assumes boot sector (or MBR) is 1<sup>st</sup> sector (S) on 1<sup>st</sup> track of a cylinder (C), under the first head (H).
    - CHS geometry: 001 = logical block address (LBA) 0
    - At power on, the basic I/O system (BIOS) performs a power-on self-test (or POST)
        - Modern x86: starts reset vector at `0xFFFFFFF0` then jumps to `0xF0000`
        - Drives (example: disk images) are checked for a valid boot sector having a signature 0xaa55 at offset 510 byte.
        ```s
        ...other asm setup...
        
        # Boot signature for MBR
        .org 0x1FE ; offset of 510 byte
        .byte 0x55
        .byte 0xaa
        ```
       - Remember the disk image is in `big endian` format.
       - BIOS reads valid boot sector from disk into memory address 0x7C00 

## BIOS Booting Procedure
- The system will start in Real Mode. Once the kernel is initialized, the CPU transitions to Protected Mode. 
- Order are the following
1. BIOS init (CPU is in real mode)
2. Master Boot Record
3. Bootloader (Stage 1, 1.5, 2)
4. Early Kernel Init (Starting to switch to `protected mode`)
5. Full Kernel init (CPU in protected mode)
6. First User-Mode Process

- Example BIOS functions using real-mode s/w interrupts:
    - INT 0x10 = Video display functions (including VESA/VBE)
    - INT 0x13 = mass storage (disk, floppy) access
    - INT 0x15 = memory size functions
    - INT 0x16 = keyboard functions

#### Wait but why does it start from the real mode and then switch to protected mode?
- This transition occurs because, during the kernel's initialization, the Global Descriptor Table (GDT) is set up. The GDT defines memory segments such as:
  - **Kernel Mode Segments**: Code Segment (CS) and Data Segment (DS).
  - **User Mode Segments**: Code Segment (CS) and Data Segment (DS).
- Each segment in the GDT specifies privilege levels (rings) to enforce access control:
  - **Ring 0**: For kernel mode (highest privilege).
  - **Ring 3**: For user mode (lowest privilege).
- Once these memory segments and privilege levels are defined, the CPU is switched to Protected Mode, allowing for advanced features like memory protection, multitasking, and access to the full addressable memory.

## 32-bit memory region in Boot
- (0x0 - 0xA0000): Accessible RAM memory
- (0xA0001 - 0xC0000): Legacy video card memory access
- (0xC0001 - 0xE0000): Expansion Area (Maps ROMS for old peripheral cards)
- (0xE0001 - 0xF0000): Extended System BIOS
- (0xF0001 - 0x100000): System BIOS (0x100000 is the limit in real mode)

## Memory map (lower 1MB)
<table border="1">
  <thead>
    <tr>
      <th>Start</th>
      <th>End</th>
      <th>Size</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x00000</td>
      <td>0x003FF</td>
      <td>1 KB</td>
      <td>RAM</td>
      <td>Real-mode IVT</td>
    </tr>
    <tr>
      <td>0x00400</td>
      <td>0x004FF</td>
      <td>256 bytes</td>
      <td>RAM</td>
      <td>BIOS Data Area</td>
    </tr>
    <tr>
      <td>0x00500</td>
      <td>0x07BFF</td>
      <td>30 KB</td>
      <td>RAM (Free for use)</td>
      <td>Conventional memory</td>
    </tr>
    <tr>
      <td>0x07C00</td>
      <td>0x07DFF</td>
      <td>512 bytes</td>
      <td>RAM</td>
      <td>OS boot sector</td>
    </tr>
    <tr>
      <td>0x07E00</td>
      <td>0x7FFFF</td>
      <td>480.5 KB</td>
      <td>RAM (Free for use)</td>
      <td>Conventional memory</td>
    </tr>
    <tr>
      <td>0x80000</td>
      <td>0x9FFFF</td>
      <td>128 KB</td>
      <td>RAM (partially usable)</td>
      <td>Extended BIOS Data Area</td>
    </tr>
    <tr>
      <td>0xA0000</td>
      <td>0xFFFFFF</td>
      <td>384 KB</td>
      <td>Various (unusable)</td>
      <td>Video memory, ROM area</td>
    </tr>
  </tbody>
</table>

### ISA device interrupt (IRQs)
<table border="1">
  <thead>
    <tr>
      <th>Index</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Description</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Programmable Interrupt Timer Interrupt</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Keyboard Interrupt</td>
    </tr>
    <tr>
      <td>3</td>
      <td>
        <p>Cascade (used internally by the two PICs, never raised)</p>
      </td>
    </tr>
    <tr>
      <td>4</td>
      <td>COM2 (if enabled)</td>
    </tr>
    <tr>
      <td>5</td>
      <td>COM1 (if enabled)</td>
    </tr>
    <tr>
      <td>6</td>
      <td>LPT2 (if enabled)</td>
    </tr>
    <tr>
      <td>7</td>
      <td>Floppy Disk</td>
    </tr>
    <tr>
      <td>8</td>
      <td>LPT1 / Unreliable spurious interrupt (usually)</td>
    </tr>
    <tr>
      <td>9</td>
      <td>CMOS real-time clock (if enabled)</td>
    </tr>
    <tr>
      <td>10</td>
      <td>Free for peripherals / legacy SCSI / NIC</td>
    </tr>
    <tr>
      <td>11</td>
      <td>Free for peripherals / SCSI / NIC</td>
    </tr>
    <tr>
      <td>12</td>
      <td>Free for peripherals / SCSI / NIC</td>
    </tr>
    <tr>
      <td>13</td>
      <td>PS2 Mouse</td>
    </tr>
    <tr>
      <td>14</td>
      <td></td>
    </tr>
    <tr>
      <td>15</td>
      <td></td>
    </tr>
  </tbody>
</table>


## Polling (interrupt)
- Polling devices: CPU repeatedly checks the status of the device by checking device register to see if the device is ready or busy.

## Direct Memory Access (DMA)
- Characters typed to 9600-baud (bits per second) terminal which is around 1200 characters per second.

## Caching
- copying information into faster storage system
    - CPU caches: These are hardware caches located close to the CPU and designed for ultra-fast access to frequently used instructions and data.
        - L1: Smallest and fastest, stores the most frequently accessed data.
        - L2: Larger but slower than L1, acts as a backup for L1 misses.
        - L3 (Last-Level Cache - LLC): Larger, shared across CPU cores, slower than L2 but still much faster than main memory.
        - When CPU didn't find the data that it needs, then we call it `miss`, if it finds the data, then we call it `hit`.


    - Main memory can be viewed as a last cache for secondary storage.
        - RAM as a Cache for Secondary Storage (Disk):
            - Main Memory (RAM) holds data that is frequently or recently used by applications.
            - When data is needed, the system first checks RAM. If the data isn't there, it retrieves it from slower secondary storage (e.g., SSD or HDD).

## Process Management
- Process: A program in some state of execution.
    - Includes the text + data areas of a program plus runtime state info including a process control block, a stack and heap memory area
    
