# ClankerOS

ClankerOS: The Open System for AI & Robotics

ClankerOS is a full-stack, open-source ecosystem for advanced AI and robotics, from custom silicon to high-level software.

Our mission is to provide an end-to-end, transparent, and reproducible alternative to the closed, proprietary technology stacks that dominate the industry. We cover everything from chip design (CPUs, NPUs, GPUs) and advanced packaging to firmware, operating systems, compilers, and application frameworks.

The core values of ClankerOS are openness and sovereignty. Every component, whether hardware or software, has an accessible and auditable counterpart, empowering developers, researchers, and nations to build and control their own technological future.

Vision: From Atoms to Apps

ClankerOS is not just an operating system or a processor design. It is a unified, vertically integrated system designed for the demands of modern AI and robotics workloads.



The ecosystem includes:

Hardware (W1-W5): A family of open, synthesizable IP cores including RISC-V CPUs, specialized NPUs, vector GPUs, and more.

System Integration (W11): Reference SoC designs, Network-on-Chip (NoC) fabrics, and memory subsystems that tie the hardware together.

Platforms (W10): Reference implementations on FPGAs and open silicon (e.g., SkyWater SKY130) for development and validation.

Firmware & OS (W6): Secure boot, firmware, and a dedicated microkernel-based operating system optimized for the ClankerOS hardware architecture.

Acceleration & Runtimes (W7-W8): Compilers (LLVM-based), runtimes, and libraries that expose the full power of the hardware to applications.

Tooling & DevEx (W9): A cohesive, reproducible development environment based on Bazel and Nix.


Project Status

ClankerOS is currently in the v0.1 Bootstrap phase. The immediate focus is on establishing governance, defining core architectural interfaces, and setting up the foundational CI/CD and development infrastructure.

See our public roadmap for more details: docs/roadmap/MILESTONES.md.

Quickstart

1. Clone the Repository:

This is a large monorepo. For focused development, a sparse checkout is recommended.

See docs/build/SPARSE-CHECKOUT.md for details.

Example: Checkout system integration and build tools




