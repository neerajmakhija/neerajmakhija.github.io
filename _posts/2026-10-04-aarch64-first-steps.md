---
title: "AArch64 First Steps: Exception Levels, Registers and Booting Multiple Cores"
date: 2026-10-04 10:00:00 +0530
categories: [ARM, AArch64]
tags: [armv8, aarch64, cortex-a, exception-levels, trustzone, multicore, boot]
image:
  path: /assets/img/posts/aarch64-first-steps/cover.png
  alt: AArch64 exception levels EL0 to EL3
---

I've spent most of my career on Cortex-M parts (STM32, TI CC13xx) running embOS and FreeRTOS. Moving to Cortex-A and 64-bit Armv8-A is a real shift: there are privilege levels, two security worlds, a hypervisor layer and several cores that all wake up at once. These are my cleaned-up notes from the first week, written for anyone making the same jump from microcontrollers.

## 1. The core and the buses

At the simplest level, an Armv8-A core looks like any CPU: an ALU, a register file, a status register (`PSTATE`) and buses to memory. Instructions come in over the instruction side, data moves over the data side with read/write control.

One detail worth knowing early: Cortex-A cores have **separate L1 instruction and data caches** but a **single, unified memory map** behind them. That's often called a *modified Harvard* design. From software you see one address space.

## 2. Exception levels (EL0–EL3)

AArch64 has four **Exception Levels**. (I kept writing "execution levels" in my notes; the correct term is *exception* levels.) A higher number means more privilege.

![AArch64 exception levels in the Normal and Secure worlds](/assets/img/posts/aarch64-first-steps/exception-levels.png){: w="540" h="675" }


| EL  | Typically runs | Example |
|-----|----------------|---------|
| EL0 | User applications | Chrome, WhatsApp, a Linux process |
| EL1 | OS kernel | Linux, Android, a trusted OS like OP-TEE |
| EL2 | Hypervisor | KVM, Xen — shares one machine between several OSes |
| EL3 | Secure monitor | Trusted Firmware-A (BL31) — switches between worlds |

On top of that, there are two **security states** (TrustZone):

- **Non-secure (Normal) world**: EL0 apps, EL1 rich OS, EL2 hypervisor.
- **Secure world**: Secure EL0 trusted apps, Secure EL1 trusted OS.
- **EL3** is always secure. It is the gatekeeper that moves the CPU between the two worlds (via `SMC` calls).

Things I got slightly wrong at first:

- **Secure EL2 does exist — just not in base Armv8.0.** It was added in **Armv8.4** (`FEAT_SEL2`) so a hypervisor can run in the Secure world too. Most of the classic material (and the Cortex-A53 era) predates it, which is why older diagrams cross it out.
- **EL0 is the least privileged level in *both* worlds**, not only the secure one. Privilege comes from the EL; the security state decides which memory and peripherals you can reach.
- **"EL3 can access everything"** is a good first approximation: it can reach both secure and non-secure physical memory. But it still runs with its own MMU/translation setup, and SoC-level firewalls (e.g. a TZASC) can still restrict bus masters. With Armv9 RME, EL3 actually lives in a new *Root* world.

## 3. Registers

**General-purpose registers:** 31 of them, `X0`–`X30`, each 64 bits. Every `Xn` also has a 32-bit view, `Wn`, which is its lower half.

- Reading `Wn` ignores the upper 32 bits.
- **Writing `Wn` zeros the upper 32 bits** of `Xn`. Writing `0xFFFFFFFF` to `W0` leaves `X0 = 0x00000000FFFFFFFF`. This trips people up coming from AArch32.
- `X29` is the frame pointer and `X30` is the link register (return address after `BL`).

**Special registers:**

- `XZR`/`WZR` — the zero register. Reads as 0, writes are discarded.
- `PC` — not a general-purpose register in AArch64. You can't write it directly like `R15` on 32-bit ARM; it changes through branches, exceptions and `ERET`.
- `SP_ELx` — a **separate stack pointer per exception level**.
- `SPSR_ELx` — saved copy of `PSTATE` when an exception is taken *to* ELx.
- `ELR_ELx` — the return address for an exception taken *to* ELx.

### Why EL0 has no SPSR or ELR

My first note said "EL0 has no interrupts/exceptions". That's wrong. Exceptions **happen** at EL0 all the time — a system call (`SVC`), a page fault, an IRQ arriving while a user process runs. What's true is that an exception is **never taken *to* EL0**. It always goes to EL1, EL2 or EL3 (same level or higher, never lower). So EL0 has no `SPSR` or `ELR` of its own, because nothing ever needs to return *into* an EL0 handler.

### Per-level stacks

Each level having its own `SP` means an exception handler always lands on a known-good stack, even if the code that faulted had a broken one. It's not about "managing tasks" — task switching is the OS's job. At EL1 and above, you can also choose to keep using `SP_EL0` via the `SPSel` register (that's the "EL1t" vs "EL1h" naming you see in vector tables).

## 4. What happens when an exception is taken

Say a user program at EL0 makes a system call:

1. Hardware saves the current `PC` into `ELR_EL1`.
2. Hardware saves `PSTATE` into `SPSR_EL1`.
3. The core switches to EL1, masks interrupts per the rules, and selects `SP_EL1`.
4. It jumps to `VBAR_EL1` + an offset in the vector table (16 entries, 0x80 bytes apart, table aligned to 2 KB).
5. The handler does its work and executes `ERET`, which copies `ELR_EL1` back to `PC` and `SPSR_EL1` back to `PSTATE`, returning to EL0.

![Exception entry on Cortex-M vs AArch64](/assets/img/posts/aarch64-first-steps/exception-entry.png){: w="540" h="675" }


Coming from Cortex-M, the big difference is that **the hardware doesn't push registers to the stack for you**. On M-profile, exception entry automatically stacks R0–R3, R12, LR, PC and xPSR. On A-profile, saving general-purpose registers is the handler's responsibility.

## 5. Endianness

- **Little-endian:** least significant byte at the lowest address. `0x12345678` is stored as `78 56 34 12`.
- **Big-endian:** most significant byte first: `12 34 56 78`.

AArch64 is bi-endian for **data** (controlled by `SCTLR_ELx.EE` and `SCTLR_EL1.E0E`), but **instruction fetches are always little-endian**. Practically every Linux/Android system runs little-endian. Endianness matters most when you parse network packets (big-endian on the wire) — something I deal with daily in CoAP/IPv6 stacks.

## 6. Multicore boot and `MPIDR_EL1`

On a multicore SoC, after reset **every core starts at the same reset address**, all running the same boot code. A few corrections to my original notes:

- The reset address is **implementation-defined** (`RVBAR_ELx`), not necessarily `0x00000000`.
- On many real SoCs, secondary cores are held in reset or powered down until the primary core wakes them — via **PSCI `CPU_ON`** (the usual Linux method through Trusted Firmware) or a **spin-table**.

To run cores concurrently, each core needs **its own stack** (and its own per-core data). Boot code typically reads `MPIDR_EL1`, lets core 0 do global initialization, and parks the others in a low-power `WFE` loop until they're released.

![Multicore boot flow on AArch64](/assets/img/posts/aarch64-first-steps/multicore-boot.png){: w="540" h="675" }


```asm
_start:
    mrs   x0, mpidr_el1
    and   x0, x0, #0xFF          // Aff0 = core number on A53/A72-style clusters
    cbz   x0, primary

secondary_park:
    wfe                          // sleep until an event (SEV) wakes us
    b     secondary_park         // real code checks a release flag here

primary:
    ldr   x1, =stack_top         // give core 0 its own stack
    mov   sp, x1
    bl    main
```

Giving each core a separate stack is just an offset from a shared top:

```asm
    // x0 = core number
    ldr   x1, =stack_top
    mov   x2, #0x4000            // 16 KB per core
    mul   x3, x0, x2
    sub   x1, x1, x3
    mov   sp, x1
```

### `MPIDR_EL1` is not just "core 0, 1, 2, 3"

`MPIDR_EL1` gives an **affinity hierarchy**, not a flat index:

![MPIDR_EL1 affinity fields on Cortex-A53 vs Cortex-A55](/assets/img/posts/aarch64-first-steps/mpidr.png){: w="540" h="675" }


- **Aff0**: core within a cluster (on Cortex-A53/A57/A72)
- **Aff1**: cluster number
- **Aff2/Aff3**: higher levels in big systems

Watch out: on newer DynamIQ cores like **Cortex-A55/A76**, the `MT` bit is set, **Aff0 is the thread** (always 0) and **the core number is in Aff1**. Code that blindly masks `& 0xFF` will think every core is core 0. Always check the TRM for your exact core.

One more note: the Arm Cortex-A Programmer's Guide mentions an "MPIDR_EL3" holding the unchangeable physical ID. As far as I can tell from the architecture reference, there is no such register. The real mechanism is **`VMPIDR_EL2`**: a hypervisor sets it, and non-secure EL1 reads of `MPIDR_EL1` return that virtual value, while EL2/EL3 see the real physical one.

## What's next

Next I'm going to write a minimal bare-metal AArch64 image for QEMU (`virt` machine): set up a vector table, drop from EL2 to EL1, and bring up the secondary cores through PSCI. I'll post that here.

*References: Arm Cortex-A Series Programmer's Guide for Armv8-A; Arm Architecture Reference Manual for A-profile.*
