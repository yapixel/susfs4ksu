# susfs4ksu (Linux 3.10 / ARM64)

An addon root hiding kernel patches and userspace module for KernelSU, ported and backported to **Linux 3.10** (ARM64 Non-GKI).

## Features
- **SuSFS Version**: `v2.3.0`
- **Linux Kernel**: `3.10.y` (tested with Xiaomi Mi 4c MSM8992 libra, Greenforce Clang 22, ThinLTO)
- **KernelSU Support**: Compatible with `backslashxx/KernelSU` and non-GKI manual hook trees
- **De-inlined Architecture**: All internal functions de-inlined to prevent compiler bloat and inline-overflow under modern LLVM / ThinLTO
- **Full Features**:
  - `CONFIG_KSU_SUSFS_SUS_PATH`
  - `CONFIG_KSU_SUSFS_SUS_MOUNT`
  - `CONFIG_KSU_SUSFS_SUS_KSTAT`
  - `CONFIG_KSU_SUSFS_SPOOF_UNAME`
  - `CONFIG_KSU_SUSFS_ENABLE_LOG`
  - `CONFIG_KSU_SUSFS_HIDE_KSU_SUSFS_SYMBOLS`
  - `CONFIG_KSU_SUSFS_SPOOF_CMDLINE_OR_BOOTCONFIG`
  - `CONFIG_KSU_SUSFS_OPEN_REDIRECT`
  - `CONFIG_KSU_SUSFS_SUS_MAP`

## Repository Structure
```text
kernel_patches/
  ├── 51_deinlined_susfs_hooks_kernel-3.10.patch # Canonical de-inlined hooks for Linux 3.10
  ├── 50_add_susfs_in_kernel-3.10.patch       # Compatibility copy for existing workflows
  ├── KernelSU/
  │   ├── 11_enable_susfs_for_ksu.patch      # Canonical xxKSU driver patch
  │   └── 10_enable_susfs_for_ksu.patch      # Compatibility copy for KernelSU
  ├── fs/
  │   └── susfs.c                            # De-inlined SuSFS v2.3.0 engine
  └── include/linux/
      ├── susfs.h                            # Kernel header with 3.10 static key wrappers
      └── susfs_def.h                        # Structures and constants definition
ksu_susfs/                                   # Userspace ksu_susfs tool source
ksu_module_susfs/                            # Userspace module template
build_ksu_susfs_tool.sh                      # Build script for ksu_susfs tool
build_ksu_module.sh                          # Build script for flashable module
```

## How to Apply Patches

### 1. Copy SuSFS Core Source Files
```bash
cp kernel_patches/fs/susfs.c <KERNEL_ROOT>/fs/
cp kernel_patches/include/linux/susfs.h <KERNEL_ROOT>/include/linux/
cp kernel_patches/include/linux/susfs_def.h <KERNEL_ROOT>/include/linux/
```

### 2. Apply KernelSU Patch
If your KernelSU directory is `drivers/kernelsu`:
```bash
cd <KERNEL_ROOT>/drivers/kernelsu
patch -p1 < <PATH_TO_SUSFS4KSU>/kernel_patches/KernelSU/11_enable_susfs_for_ksu.patch
# Or use 10_enable_susfs_for_ksu.patch for compatibility
```

### 3. Apply Kernel VFS Patch
```bash
cd <KERNEL_ROOT>
patch -p1 < <PATH_TO_SUSFS4KSU>/kernel_patches/51_deinlined_susfs_hooks_kernel-3.10.patch
# Or use 50_add_susfs_in_kernel-3.10.patch for compatibility
```

### 4. Enable Kconfig Options
Add to your kernel `defconfig`:
```kconfig
CONFIG_KSU_SUSFS=y
CONFIG_KSU_SUSFS_SUS_PATH=y
CONFIG_KSU_SUSFS_SUS_MOUNT=y
CONFIG_KSU_SUSFS_SUS_KSTAT=y
CONFIG_KSU_SUSFS_SPOOF_UNAME=y
CONFIG_KSU_SUSFS_ENABLE_LOG=y
CONFIG_KSU_SUSFS_HIDE_KSU_SUSFS_SYMBOLS=y
CONFIG_KSU_SUSFS_SPOOF_CMDLINE_OR_BOOTCONFIG=y
CONFIG_KSU_SUSFS_OPEN_REDIRECT=y
CONFIG_KSU_SUSFS_SUS_MAP=y
```

## Credits
- [simonpunk/susfs4ksu](https://gitlab.com/simonpunk/susfs4ksu) for the original SuSFS implementation.
- [KernelSU](https://github.com/tiann/KernelSU) & [backslashxx/KernelSU](https://github.com/backslashxx/KernelSU)
