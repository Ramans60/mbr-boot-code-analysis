# MBR BOOT CODE ANALYSIS
 A Practical Study of Reading and Analyzing the MBR Boot Code from a USB Drive Using Linux

## Table of Contents
- [Objective](#objective)
- [Introduction](#introduction)
- [Requirements](#requirements)
- [Introduction to Disk and MBR](#introduction-to-disk-and-mbr)
- [Why MBR Exists](#why-mbr-exists)
- [Why MBR is 512 Bytes](#why-mbr-is-512-bytes)
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


## Why MBR Exists

- MBR exists to provide initial boot information to the system firmware.
- It identifies the bootable partition and describes how the disk is partitioned.
- MBR is accessed before any filesystem is loaded.


## Functions of MBR
- MBR performs two primary functions:
  - Boot decision
    - Identifies the bootable partition
    - Locates the partition bootloader
  - Disk layout information
    - Describes how the disk is partitioned


#### MBR is not:
- a filesystem
- a partition
- an operating system

> MBR exists before all higher-level disk structures.

### MBR Location
- Sector 0 → MBR (512 bytes)
- Sector 1
- Sector 2
- ...

### MBR can exist even if:
- The disk is empty
- The disk has no filesystem

=> In BIOS-based systems:
- Boot always starts from MBR
- Absence of MBR results in boot failure

>(UEFI and GPT are outside the scope of this document)

## Why MBR is 512 Bytes

- MBR occupies exactly one disk sector.
- Traditional disk sector size is 512 bytes.
- At startup, BIOS reads only the first sector of the disk.
> Therefore, the entire MBR must fit within 512 bytes.

## Structure of MBR

<img width="1536" height="1024" alt="ChatGPT Image Jan 2, 2026, 11_39_45 AM" src="https://github.com/user-attachments/assets/26120541-c056-4899-9e49-89f30aeaf505" />

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
- `55 aa`

> If the last two bytes are `55 aa`, the disk uses a valid MBR.

## Observations
* Disk sector size is 512 bytes
* MBR is located at sector 0
* Presence of 55 AA confirms a valid MBR
* BIOS relies entirely on this sector during boot

## Conclusion
> The Master Boot Record is raw, disk-level code that forms the bridge between system firmware and the operating system. Proper understanding of MBR requires analyzing disk data at the sector level, rather than at the filesystem level.

















