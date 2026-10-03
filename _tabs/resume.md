---
icon: fas fa-file-alt
order: 2
---

**Noida, India** · [neerajmakhija79@gmail.com](mailto:neerajmakhija79@gmail.com) · +91-9811573148 · [LinkedIn](https://www.linkedin.com/in/neeraj-makhija-bb1760129)

[Download PDF resume](/assets/files/Neeraj_Makhija_Resume.pdf){: .btn .btn-outline-primary }

## Summary

Senior embedded firmware engineer with 7+ years building production firmware for bare-metal and RTOS (FreeRTOS, embOS) environments on ARM Cortex-M (STM32, TI CC1314R). Owns cellular IoT firmware for a battery-powered smart gas-meter endpoint: LTE-M/NB-IoT modem subsystem architecture, AT-command and modem state-machine design, PSM duty-cycle and battery-life optimization, and secure CoAP OTA firmware download, validated with current-profiler and AT/UART traces. Strong in peripheral drivers (UART, SPI, I2C, RS232/RS485), networking/RF stacks (IPv6, DHCPv6, CoAP, Wi-SUN RF mesh), and low-level debugging (GDB, git bisect, logic analysis). Now extending into Linux device drivers, Cortex-A (AArch64), Zephyr, ESP32 BLE/Wi-Fi and RISC-V.

## Experience

### Senior Firmware Engineer — Landis+Gyr, Noida
*May 2023 – Present · Cellular IoT (LTE-M/NB-IoT) battery endpoint for smart gas metering · Wi-SUN RF mesh for AMI smart metering*

- Redesigned the cellular communication task so the modem finishes all pending work in a single wake-up, cutting radio-on time per session by up to 85% and significantly extending battery life
- Built a battery-life model from real current measurements, confirming the device comfortably exceeds its 20-year battery requirement and letting the team see the impact of any configuration change
- Diagnosed and fixed LTE-M/NB-IoT modem power issues using current profiling and AT command traces, including a sleep-entry bug that kept the modem drawing far more than its deep-sleep current
- Implemented a CoAP Block2 OTA firmware download client on TI CC1314R (embOS) over UDP cellular transport with RFC 7252-compliant retransmission, Uri-Path/Uri-Query handling and flash offset management; designed fail-safe session teardown and recovery so interrupted or aborted updates never brick the device or resume stale sessions
- Engineered and optimized Wi-SUN RF mesh firmware for large-scale AMI deployments; integrated DHCPv6, IPv6 and InterNiche TCP/IP flows (custom client-ID, solicit/advertise/request) and EAP-PSK node admission
- Root-caused a firmware crash via git bisect to a GPIO configuration regression and resolved critical field defects, hardening production stability

### Team Lead, Software Engineer (C/C++) — Chetu Inc., Noida
*Nov 2021 – Apr 2023*

- Led a five-engineer firmware team delivering NFC, MSR, and touchscreen POS terminal firmware; owned client-facing requirements and shipped client projects on schedule
- Built drone flight-automation features using DJI OSDK on embedded Linux, including GPS waypoint navigation, altitude control, and multi-sensor fusion

## Projects

Details on the [Projects](/projects/) tab.

- **Smart Home Voice Assistant & IoT Nodes** *(in progress)* — Zephyr RTOS on STM32F407 Discovery with a Nextion touch HMI; ESP32-WROOM-32 nodes on ESP-IDF (FreeRTOS) with BLE and Wi-Fi for lighting and AC control
- **Linux Device Drivers, ARM Cortex-A & RISC-V** *(ongoing)* — kernel modules and device drivers; ARMv8-A / AArch64 and the RISC-V ISA

## Early Experience

**R&D Embedded Engineer — Copper Connections Ltd, Delhi** · Apr 2021 – Oct 2021
PBX/telephony firmware — new call-management features; refactored legacy C across multiple device platforms, reducing defect rates.

**Embedded Design R&D Engineer — Emtech Foundation, Delhi** · Jun 2019 – Mar 2021
End-to-end firmware for surgical equipment controllers and lab instrumentation; took a Water Conductivity Meter from prototype to production; firmware-level power optimization.

## Education & Awards

**B.Tech, Electronics & Communication Engineering**
B.S. Anangpuria Institute of Technology & Management · 2015 – 2019

- Team of the Year 2024 (Landis+Gyr)
- IndiaSkills 2018 — Bronze Medal, Electronics (National) & 1st Place (Zonal)
- Faridabad Industrial Association Innovation Award

## Technical Skills

**Wireless & IoT:** LTE-M, NB-IoT, Wi-SUN RF Mesh, BLE, Wi-Fi, Cellular AT Commands, Power Saving Mode (PSM), Battery-Life Optimization
**Architectures & MCUs:** ARM Cortex-M3/M4/M33, ARM Cortex-A (ARMv8-A / AArch64), RISC-V (learning), STM32, TI CC1314R, ESP32 (ESP-IDF), 8-bit MCUs
**RTOS / Bare-Metal:** FreeRTOS, embOS, Zephyr RTOS, NuttX, Bare-Metal
**Interfaces & Drivers:** UART, SPI, I2C, USB, RS232, RS485, GPIO, DMA, Interrupts, Memory-Mapped I/O
**Embedded Linux:** Linux Device Drivers (kernel modules, in progress), Embedded Linux (Debian/Ubuntu), POSIX Threads, Linux Shell
**Networking & Protocols:** TCP/IP, UDP, IPv6, DHCPv6, CoAP (RFC 7252), EAP-PSK, InterNiche
**Firmware Security:** Secure Boot, Secure OTA, Bootloader Development, Embedded Cryptography (AES)
**Languages & Build:** Embedded C, C++, Python, Bash, GCC, GNU Make, GNU ARM Toolchain, IAR
**Debug, Test & Workflow:** GDB, JTAG/SWD, Power Profiling (PPK2), git bisect, Logic / Protocol Analysis, Git, JIRA / TFS, Code Review
