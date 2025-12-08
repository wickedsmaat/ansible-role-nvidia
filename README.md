# ansible-role-nvidia
Overview

This role installs and configures NVIDIA proprietary GPU drivers on RHEL-based systems.
It disables the open-source Nouveau driver, installs required kernel headers, and deploys CUDA-enabled NVIDIA driver packages.

It is intended for homelab nodes running GPU workloads such as Plex transcoding, AI inference, or CUDA-accelerated applications.

## Task-by-Task Breakdown
Detecting the Presence of an NVIDIA GPU

This task inspects PCI devices for supported NVIDIA models (e.g., Quadro, RTX).
If no supported GPU is found, the driver installation tasks are skipped.
This avoids unnecessary configuration attempts on non-GPU systems.

Disabling the Nouveau Driver

The open-source Nouveau driver conflicts with NVIDIA’s proprietary driver.
This task removes the module from the running system and blacklists it permanently.
Disabling Nouveau is required before the official NVIDIA driver can load correctly.

Deploying Blacklist Files for Nouveau

Blacklist configuration files are placed in both /usr/lib/modprobe.d/ and /etc/modprobe.d/.
These block Nouveau from loading during boot, ensuring the NVIDIA kernel module is used instead.
Doing this in two locations covers variance across distributions.

Installing Kernel Development Packages

The NVIDIA driver must compile a kernel module against the running kernel version.
This task installs kernel headers, kernel-devel, and other necessary build dependencies.
Without matching kernel sources, the driver installation will fail.

Installing NVIDIA Driver Packages

This step installs the full NVIDIA driver suite, including:

Core driver

CUDA libraries

OpenGL libraries

NVENC/NVDEC supporting components

These packages enable GPU compute, hardware transcoding, and general GPU acceleration.

Installing libglvnd and Supporting Components

This task installs the GL Vendor Neutral Dispatch libraries.
These libraries ensure that OpenGL and Vulkan applications route graphics calls to the correct driver implementation.
They are required for a stable multi-driver graphics environment.

Installing GPU Monitoring Tools

Utilities like nvtop are installed to provide real-time visibility into GPU utilization.
This is helpful for confirming the driver is working and for monitoring workloads such as Plex transcoding.
This improves operational observability for homelab usage.

Configuring DNF to Prefer IPv4 (Optional)

Some networks do not use IPv6 reliably.
This task configures the package manager to use IPv4 only to avoid repository connection issues.
While minor, it improves reliability on certain home networks.

## Summary

The NVIDIA role completely configures a system for GPU acceleration.
It disables conflicting drivers, installs kernel components, deploys proprietary driver packages, and ensures the system can use NVIDIA hardware for compute or transcoding workloads.