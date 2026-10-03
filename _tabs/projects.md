---
icon: fas fa-microchip
order: 3
---

## Personal Projects

Hands-on projects I'm building in the open. Code, design notes and progress write-ups will be linked here as each stage lands.

### Smart Home Voice Assistant & IoT Nodes — *in progress*
A home-control system built in stages, starting from embedded nodes and working up to a voice assistant.

- **Display node:** Zephyr RTOS firmware on an STM32F407 Discovery board, driving a Nextion 4.3" resistive touch HMI over UART. The Nextion's SD-card update path failed on this module, so I wrote a Python serial uploader to flash the UI directly over a USB-UART adapter.
- **Control nodes:** ESP32-WROOM-32 firmware in C on ESP-IDF (FreeRTOS), using BLE and Wi-Fi. Nodes switch lights through a relay and control the air conditioner over IR.
- **Voice pipeline:** wake word and conversation handled on a PC first, sending commands to the nodes.
- **Next:** a battery-powered node version, applying the low-power techniques from my cellular metering work.

**Stack:** Zephyr RTOS · ESP-IDF / FreeRTOS · STM32F407 · ESP32 · BLE · Wi-Fi · UART · Embedded C · Python

### Linux Device Drivers, ARM Cortex-A & RISC-V — *ongoing*
Self-directed study to extend from Cortex-M microcontrollers into application-class processors.

- Writing Linux kernel modules and device drivers
- Studying the ARMv8-A / AArch64 architecture
- Learning the RISC-V ISA

## Professional Work

These are professional projects rather than public repos — happy to walk through implementation details in an interview.

### Cellular IoT Battery Endpoint — Landis+Gyr
LTE-M/NB-IoT firmware for a battery-powered smart gas-meter endpoint. Redesigned the cellular communication task so the modem completes all pending work in a single wake-up, cutting radio-on time per session by up to 85%. Built a battery-life model from real current measurements to confirm the device exceeds its 20-year battery requirement, and fixed modem power bugs (including a sleep-entry issue) using current profiling and AT command traces.

### Secure OTA Firmware Download Client — Landis+Gyr
A CoAP Block2 OTA firmware download client on TI CC1314R (embOS) over UDP cellular transport, RFC 7252-compliant with retransmission, Uri-Path/Uri-Query handling and flash offset management. Fail-safe session teardown and recovery ensure interrupted or aborted updates never brick the device or resume stale sessions.

### Wi-SUN RF Mesh Networking — Landis+Gyr
Wi-SUN RF mesh networking firmware for large-scale AMI smart-metering deployments, including DHCPv6/IPv6/InterNiche TCP/IP stack integration and EAP-PSK (CEAP) authentication flows for secure node admission — improving end-to-end communication reliability in production.

### DJI Drone Flight Automation — Chetu, Inc.
Flight-automation features built with the DJI OSDK on embedded Linux: GPS waypoint navigation, altitude control, and multi-sensor fusion.

### POS Terminal Firmware — Chetu, Inc.
NFC, MSR, and touchscreen point-of-sale terminal firmware, delivered by a five-engineer team under my technical leadership, shipped on schedule against client-facing requirements.

### PBX / Telephony Call Management — Copper Connections Ltd
New call-management features for PBX/telephony firmware; refactored legacy C across multiple device platforms to reduce defect rates.

### Water Conductivity Meter — Emtech Foundation
Took a lab-instrumentation device from prototype to production, alongside firmware for surgical equipment controllers, with a focus on firmware-level power optimization.

<!--
Add more entries here as new projects come up — copy the pattern above:
### Project Name — Company/Context
One or two lines on what it does and the tech used.
-->
