## mbr-boot-code-analysis
A Practical Study of Reading and Analyzing the MBR Boot Code from a USB Drive Using Linux
## Table of Contents
- [Objective](#objective)
- [Introduction](#introduction)
- [Requirements](#objective)
- [Introduction to Disk and MBR](#introduction-to-disk-and-mbr)
- [Functions of MBR](#functions-of-mbr)
- [Structure of MBR](#structure-of-mbr)
- [Practical Experiment](#practical-experiment)
- [Observations](#observations)
- [Conclusion](#conclusion)
  

## Objective

To explain the Master Boot Record (MBR), and demonstrate how to read and analyze the first 512 bytes of a USB drive’s MBR using Linux tools.

## Introduction
This documentation explains what the Master Boot Record (MBR) is and demonstrates how to read the first 512 bytes of a USB drive for analysis.

## Requirements 
- Linux PC
- USB drive
- Basic terminal knowledge

  <img width="1920" height="1080" alt="Screenshot from 2026-01-02 10-13-44" src="https://github.com/user-attachments/assets/f1bc71f5-a0d1-45f1-b142-8cc0e50e64c5" />

## Introduction to Disk and MBR
### What is a Disk
A disk is a permanent storage device used to store data even after the power is turned OFF.
#### Examples
- Hard Disk (HDD)
- Solid State Drive (SSD)
- USB drive
- SD card
#### What a Disk Stores
- Files
- Folders
- Operating systems
> A disk stores only binary data (0 and 1).

### What is MBR
* MBR stands for Master Boot Record
* MBR is the first 512 bytes of a disk

### It is located at:
> Disk → Sector 0 → 512 bytes

<img width="1117" height="598" alt="Screenshot from 2025-12-31 16-21-02" src="https://github.com/user-attachments/assets/62acc15a-75c4-4ce4-abf3-09c2ddf669e9" />


### Why MBR Exists
- System Startup Sequence
- Power ON
- BIOS executes

*  BIOS needs to determine:
- Where the operating system is located
- Which partition should be booted

* Key Points
- BIOS does not understand filesystems
- BIOS understands only raw disk sectors

* BIOS requests:
> “Read the first sector of the disk”

> The first sector read by BIOS is the MBR.

### Functions of MBR
* MBR performs two primary functions:
* Boot decision
* Identifies the bootable partition
* Locates the partition bootloader
* Disk layout information
* Describes how the disk is partitioned
=> Important Notes

### MBR is not:
- a filesystem
- a partition
- an operating system

> MBR exists before all higher-level disk structures.

### MBR Location
- [ Sector 0 ]  → MBR (512 bytes)
-[ Sector 1 ]
- [ Sector 2 ]
- [   ...    ]

### MBR can exist even if:
- The disk is empty
- The disk has no filesystem
- Legacy BIOS Boot Behavior
### In BIOS-based systems:
- Boot always starts from MBR
- Absence of MBR results in boot failure

>(UEFI and GPT are outside the scope of this document)

## Why MBR is 512 Bytes
* MBR occupies exactly one disk sector
* Traditional disk sector size is 512 bytes
* MBR resides in Sector 0
* Disk Design Constraints
* Smallest readable unit: one sector

> Fixed sector size: 512 bytes

- BIOS Constraints
* BIOS can read only full sectors
* BIOS cannot read partial sectors
* BIOS cannot interpret filesystems

### Therefore, BIOS reads:
> Sector 0 → 512 bytes → MBR
- Power-On Limitations
- At startup:
- No operating system
- No drivers
- Very limited memory
- Minimal execution environment
# BIOS behavior:
# Read the first sector and execute it.

## Implications
* MBR must fit entirely within 512 bytes
* No additional space is available
*  Components That Must Fit
* Boot code
* Partition table
* Boot signature
* Historical Context
* Storage capacity was limited
* RAM was minimal
* CPUs were slow
* 512 bytes provided:
* Fast access
* Simplicity
* Hardware compatibility
* Once standardized, this size remained unchanged for backward compatibility.

## Structure of MBR

-The 512-byte MBR is divided as follows:
* Offset Range	Component	Size
* 0 – 445	Boot Code	446 bytes
* 446 – 509	Partition Table	64 bytes
* 510 – 511	Boot Signature	2 bytes
* Boot Code (446 bytes)
* Small machine-level program
* Executed directly by BIOS
* Identifies the active partition
* Loads the partition bootloader
* Due to size constraints, it cannot load the operating system directly.
* Partition Table (64 bytes)
* Defines disk partitioning
* Contains four partition entries
* Each entry is 16 bytes
* Supports a maximum of four primary partitions
* Each entry stores:
* Boot flag
* Starting sector
* Partition size
* Partition type
* Boot Signature (2 bytes)
* Fixed value: 55 AA
* Indicates a valid boot sector to BIOS
> If the signature is missing, BIOS ignores the disk.

## Practical Experiment

### Step 1: Identify the USB Drive
```bash
   lsblk

```
<img width="1920" height="1080" alt="Screenshot from 2026-01-01 17-57-44" src="https://github.com/user-attachments/assets/debb4fae-590a-4383-af9d-345eb31ba0d9" />

### Step 2: Read the First 512 Bytes (MBR)

```bash
   sudo dd if=/dev/sdb bs=512 count=1 | hexdump -C

```

### Command Details
* if=/dev/sdb → input device
* bs=512 → sector size
* count=1 → read only the first sector
* hexdump -C → formatted hexadecimal output

  
<img width="1920" height="1080" alt="Screenshot from 2026-01-01 17-58-24" src="https://github.com/user-attachments/assets/04d55168-d79a-4ce1-8399-17a0a2d91a78" />


### Step 3: Verify the Boot Signature
* The final two bytes of the output should be:
" 55 aa "

### Step 4: Interpretation if Signature is Missing
* Condition	Interpretation
* 55 aa absent	Disk does not use MBR
* Random data	Disk may be GPT or partially wiped
* All zeros	Disk is empty or uninitialized

## Observations
* Disk sector size is 512 bytes
* MBR is located at sector 0
* Presence of 55 AA confirms a valid MBR
* BIOS relies entirely on this sector during boot

## Conclusion
> The Master Boot Record is raw, disk-level code that forms the bridge between system firmware and the operating system. Proper understanding of MBR requires analyzing disk data at the sector level, rather than at the filesystem level.























