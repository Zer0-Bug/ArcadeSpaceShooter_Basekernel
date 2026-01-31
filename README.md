<h1 align="center">Space Shooter: Kernel-Integrated Arcade</h1>

<p align="center">
  <a href="https://en.wikipedia.org/wiki/C_(programming_language)">
    <img src="https://img.shields.io/badge/C-A8B9CC?style=for-the-badge&logo=c&logoColor=white" alt="C">
  </a>
  <a href="https://github.com/dthain/basekernel">
    <img src="https://img.shields.io/badge/Basekernel-OS-orange?style=for-the-badge" alt="Basekernel">
  </a>
  <a href="https://www.qemu.org/">
    <img src="https://img.shields.io/badge/QEMU-FF6600?style=for-the-badge&logo=qemu&logoColor=white" alt="QEMU">
  </a>
  <a href="https://en.wikipedia.org/wiki/VESA_BIOS_Extensions">
    <img src="https://img.shields.io/badge/VESA-Graphics-blue?style=for-the-badge" alt="VESA">
  </a>
  <a href="LICENSE">
    <img src="https://img.shields.io/badge/license-MIT-darkred?style=for-the-badge" alt="License">
  </a>
</p>

<p align="center">
  An extraordinary 2D <b>Space Shooter</b> implemented at the <b>Kernel Level</b>. Built within the <b>Basekernel</b> research operating system, this project replaces the standard OS shell with a high-speed arcade game loop, utilizing custom low-level drivers for <b>VESA BIOS</b> graphics and direct hardware interrupts.
</p>

<p align="center">
  <a href="#kernel-architecture">
    <img src="https://img.shields.io/badge/Architecture-222222?style=flat" />
  </a>
  <span> ° </span>
  <a href="#project-structure">
    <img src="https://img.shields.io/badge/Structure-222222?style=flat" />
  </a>
  <span> ° </span>
  <a href="#system-mechanics">
    <img src="https://img.shields.io/badge/Mechanics-222222?style=flat" />
  </a>
  <span> ° </span>
  <a href="#technical-specifications">
    <img src="https://img.shields.io/badge/Specs-222222?style=flat" />
  </a>
  <span> ° </span>
  <a href="#deployment--installation">
    <img src="https://img.shields.io/badge/Deploy-222222?style=flat" />
  </a>
</p>

---
<br>

<h2 align="center">Kernel Architecture</h2>

Unlike standard games that run as user-space applications, this version is baked directly into the **Operating System Kernel**, providing zero-latency hardware access:

1.  **Monolithic Game Loop:** The game logic is integrated into `kernel_main()`, executing immediately after system initialization (interrupts, memory paging, and RTC setup).
2.  **Hardware Level Input:** Bypasses traditional OS APIs by using `keyboard_read(1)` to poll the hardware interrupt controller for real-time key states.
3.  **VESA Framebuffer Pipeline:** Renders directly to the video memory using the kernel's `graphics_create_root()` driver, supporting primitives like `graphics_line` and `graphics_rect`.
4.  **Deterministic Timing:** Implements a custom busy-wait `delay()` system to ensure consistent gameplay speed across different processor clock frequencies in a non-preemptive environment.

---
<br>

<h2 align="center">Project Structure</h2>

```
ArcadeSpaceShooter_Basekernel/
├── LICENSE                                   # MIT License
├── README.md                                 # Project documentation
│
├── basekernel/                               # Core Operating System Source
│   ├── kernel/
│   │   ├── main.c                            # Integrated Game Logic & OS Entry
│   │   ├── graphics.c                        # VESA Driver Implementation
│   │   └── interrupt.c                       # Low-level IRQ Management
│   ├── library/                              # Kernel common utilities
│   ├── include/                              # System headers (VESA, RTC, ATA)
│   ├── Makefile                              # Kernel build system
│   └── basekernel.iso                        # Bootable OS Image
│
└── Screenshots/                              # QEMU Gameplay Previews
    ├── basekernel_v1.PNG
    ├── basekernel_v2.PNG
    └── basekernel_v3.PNG
```

---
<br>

<h2 align="center">System Mechanics</h2>

- **Triangle-Mesh Spaceship**: A custom-drawn white triangle entity rendered using iterative line primitives.
- **AABB Collision Grid**: Kernel-side intersection logic for bullet-to-enemy and enemy-to-player detection.
- **Pseudo-Random Spawning**: Utilizes a 32-bit linear congruential generator (LCG) for deterministic enemy placement.
- **1020x700 Viewport**: Utilizes high-resolution VESA graphics modes for a crisp, hardware-accelerated interface.
- **Binary HUD**: Real-time score rendering via a custom `printInt` routine that parses integers directly to the framebuffer.

### Operating Controls
<table align="center">
  <tr>
    <th align="center">Command</th>
    <th align="center">Physical Key</th>
  </tr>
  <tr>
    <td align="center">Coordinate Increment (UP)</td>
    <td align="center"><b>W</b></td>
  </tr>
  <tr>
    <td align="center">Coordinate Decrement (DOWN)</td>
    <td align="center"><b>S</b></td>
  </tr>
  <tr>
    <td align="center">Initialize Projectile</td>
    <td align="center"><b>Space</b></td>
  </tr>
</table>

---
<br>

<h2 align="center">Technical Specifications</h2>

<table align="center">
  <tr>
    <th align="center">Layer</th>
    <th align="center">Specification</th>
  </tr>
  <tr>
    <td align="center"><b>Operating Mode</b></td>
    <td align="center">32-bit Protected Mode (x86)</td>
  </tr>
  <tr>
    <td align="center"><b>Graphics Mode</b></td>
    <td align="center">VESA Linear Frame Buffer</td>
  </tr>
  <tr>
    <td align="center"><b>Memory Layout</b></td>
    <td align="center">Custom Paged Memory Model</td>
  </tr>
  <tr>
    <td align="center"><b>Executable Format</b></td>
    <td align="center">ELF (Internal) / Bootable ISO</td>
  </tr>
  <tr>
    <td align="center"><b>Target Emulator</b></td>
    <td align="center">QEMU / VirtualBox</td>
  </tr>
</table>

---
<br>

<h2 align="center">Deployment & Installation</h2>

### 1. Build Requirements
You will need a cross-compiler setup for `i386-elf` if you are not running on a native 32-bit Linux host.

### 2. Acquisition
```bash
git clone https://github.com/Zer0-Bug/ArcadeSpaceShooter_Basekernel.git
```
```bash
cd ArcadeSpaceShooter_Basekernel/basekernel
```

### 3. Compilation
Build the bootable kernel image using the provided Makefile:
```bash
make
```

### 4. Running the OS
Launch the game image directly in QEMU:
```bash
qemu-system-i386 -cdrom basekernel.iso
```

---
<br>

<h2 align="center">Contribution</h2>

Contributions are always appreciated. Open-source projects grow through collaboration, and any improvement—whether a bug fix, new feature, documentation update, or suggestion—is valuable.

To contribute, please follow the steps below:

1. Fork the repository.
2. Create a new branch for your change:  
   `git checkout -b feature/your-feature-name`
3. Commit your changes with a clear and descriptive message:  
   `git commit -m "Add: brief description of the change"`
4. Push your branch to your fork:  
   `git push origin feature/your-feature-name`
5. Open a Pull Request describing the changes made.
<br>
All contributions are reviewed before being merged. Please ensure that your changes follow the existing code style and include relevant documentation or tests where applicable.

---
<br>
<p align="center">
  <a href="mailto:777eerol.exe@gmail.com">
    <img src="https://cdn.simpleicons.org/gmail/D14836" width="40" alt="Email">
  </a>
  <span> × </span>
  <a href="https://www.linkedin.com/in/eerolexe/">
    <img src="https://upload.wikimedia.org/wikipedia/commons/c/ca/LinkedIn_logo_initials.png"
         width="40"
         alt="LinkedIn">
  </a>
</p>

---

<p align="center" style="margin-top:10px; letter-spacing:4px;">
  ∞
</p>
