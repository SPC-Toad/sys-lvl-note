# Operating Systems Notes (Beginner-Friendly OS Dev)

## What is an Operating System?

- A software layer between user applications and hardware.
- Responsibilities:
  - Execute programs
  - Provide **protection**, **reliability**, **efficiency**, **predictability**, **convenience**
  - Enforce privilege levels: Ring 0 (kernel) to Ring 3 (user)

---

## System Organization

### CPU and Device Controllers

- All connect via **bus interconnect** to shared memory.
- Enables concurrent execution and resource contention.

### Northbridge (High-Speed Communication)

- Connects CPU to RAM, GPU, and Southbridge.
- Examples: PCIe, memory controller, graphics interface.

### Southbridge (Peripheral Hub)

- Connects CPU to low-speed I/O devices.
- Examples: SATA/NVMe, USB, Audio, BIOS, legacy I/O.

---

## Boot Process Overview

### BIOS Boot (Legacy)

1. Power-on: POST (Power-On Self-Test)
2. CPU starts at `0xFFFFFFF0`, jumps to `0xF0000` (BIOS ROM)
3. BIOS loads the first sector (MBR) of the boot device to `0x7C00`
4. MBR must end with signature `0xAA55` at offset 510 (0x1FE)

### Real Mode → Protected Mode

- System boots in **Real Mode** (20-bit addressing)
- Transition to **Protected Mode** requires:
  - Global Descriptor Table (GDT) and GDTR setup
  - Loading segment registers
  - Enabling paging and memory protection
- 64-bit OS boot uses GRUB2 or UEFI to enter **Long Mode**

---

## Memory Map (First 1MB, 32-bit)

```
0x00000 - 0x003FF: Interrupt Vector Table (IVT)
0x00400 - 0x004FF: BIOS Data Area
0x00500 - 0x07BFF: Free RAM
0x07C00 - 0x07DFF: Boot Sector
0x07E00 - 0x9FFFF: Conventional Memory
0xA0000 - 0xBFFFF: Legacy Video RAM
0xC0000 - 0xFFFFF: ROM BIOS / Reserved
```

---

## GRUB2 Bootloader (x86\_64)

- Loads kernel ELF binary
- Switches to long mode and provides memory map via Multiboot
- Even with GRUB2:
  - You must still manually declare and load GDT
  - Set GDTR and segment selectors
  - Set up page tables and enter protected/long mode
- **Main benefit over GRUB Legacy**: GRUB2 automatically loads stages (no need to manually load 1, 1.5, 2 for disk image changes)

---

## Kernel Layout and Virtual Memory

### Kernel Virtual Start Address

- Typical ELF kernel base: `0x400000` (4MB)
- Physical memory may be mapped to start at `0x100000`
- GDT, IDT, and paging set up in early kernel

### Why `0x7FFFFFFF` for 32-bit limit?

- 2^31 - 1 = 2,147,483,647 = `0x7FFFFFFF` (signed 32-bit max)

### Virtual Memory in 64-bit Linux

- Uses 48-bit canonical addresses: 2^48 = 256 TB
- Higher bits (48–63) must mirror bit 47 (canonical form)
- Hardware currently supports up to 52-bit addresses, but most OSes stick to 48 for practicality

---

## Interrupts and Exceptions

### Types

- **Hardware (IRQs)**: Timer, Keyboard, Disk, etc.
- **Software**: System calls (`int 0x80`, `syscall`)
- **Exceptions**: Divide-by-zero, Page fault, Invalid Opcode

### Handling

- IDT (Interrupt Descriptor Table) maps to ISR
- Kernel manages exception and context switching

---

## Paging and Virtual Memory

- VM is a conceptual array backed by disk
- Main memory (RAM) caches working set pages
- **Page Tables** map virtual → physical addresses
- **TLB**: Hardware cache for page table entries
  - Managed by CPU automatically

### Dynamic Allocation

- Heap and stack grow in opposite directions
- Page faults trigger demand paging or swapping

### Fragmentation

- **Internal**: Block > payload due to alignment (e.g., malloc 13B allocates 16B)
- **External**: Enough total free space, but not contiguous blocks

---

## File System (Very Simple FS)

- Block-based: 512B, 1KB, or 4KB per block
- Has Superblock, Inode table, Directory entries
- Must handle paths, inodes, free blocks, and file contents

---

## Device Drivers and DMA

- Kernel interacts with hardware via drivers
- Drivers often live in kernel space (Ring 0)
- `ioctl()` used for device-specific commands
- **DMA**: Direct access to memory for fast I/O

---

## System Calls and Context Switching

- User → kernel via syscall or interrupt
- Save context (registers, stack pointer)
- Use `fork()`, `execve()`, and `wait()` for process control
- **Copy-On-Write (COW)** used in `fork()` to delay memory duplication

---

## GCC and ELF Format

### Object File Types

- `.o` - relocatable
- `.so` - dynamic library (`-shared -fpic`)
- `.a` - static library (`ar rcs libname.a`)

### `-fpic` flag

- Generates Position Independent Code
- Allows `.text` section to be mapped anywhere in memory
- Required for shared libraries

### Sections

- `.text`: Code
- `.data`: Initialized globals
- `.bss`: Zero-initialized globals (no disk space used)
- `.rodata`: Constants
- `.symtab`, `.rel.text`, `.rel.data`, `.debug`: Metadata

### Linking

- **Static**: All code baked into executable
- **Dynamic**:
  - **Lazy Binding**: Symbol resolved at first use (faster startup)
  - **Fast Binding**: All symbols resolved at load (more secure)

---

## Useful Tools

- `nm`, `objdump`, `readelf`, `strip`, `size`, `ar`
- `ldd`: show dynamic dependencies
- `strace`: trace syscalls
- `/proc`: virtual FS for kernel and process introspection
- `ps`, `top`, `pmap`: show system and memory usage

---

## Assembly Segment Setup Example

```asm
mov $0x10, %ax        ; GDT segment selector (index 2 * 8 = 0x10)
mov %ax, %ds
mov %ax, %es
mov %ax, %fs
mov %ax, %gs
mov %ax, %ss
```

- 0x10 = binary `00010000`
- Used to load data segment registers

---

## General OSDev Roadmap

- Initial Boot: GRUB loads ELF to memory
- Setup:
  - GDT, IDT
  - Page tables (map virtual/physical)
  - Basic exception handlers
  - Kernel memory allocator (malloc/free)
- Add modules incrementally:
  - Timer, Keyboard, Serial, Syscalls
  - Shell or init process

---

## Advanced Topics (Optional/Future Work)

- I/O Virtualization (virtio, vhost)
- USB controller support (e.g., xHCI)
- Preemptive multitasking (RR, MLFQ, CFS)
- SMP support (Multi-core)
- Hyperthreading (Simultaneous Multi-Threading)

---

> This document will be continuously updated alongside my OS project. The goal is to help beginners understand key decisions, architecture, and development flow from scratch.

### Acknowledgments

- Prof. Rich West (CS410, CS552 – BU)
- Prof. Renato Mancuso (CS350 – BU)
- "Computer Systems: A Programmer’s Perspective" – Bryant & O’Hallaron
- "Operating Systems: Three Easy Pieces" – Arpaci-Dusseau

