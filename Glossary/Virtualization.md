---
alias: VM
---

- Allows for the creation of virtual representations of physical computing resources, including servers, desktops, storage devices, and networks.
- Enables multiple isolated environments to run on a single physical machine, optimizing resource utilization and reducing costs.
- Involves creating virtual machines (VMs) that emulate the functionality of physical hardware.
    - Each VM operates independently with its own operating system and applications while sharing the underlying physical resources of the host machine.
    - This allows for more efficient use of hardware resources and supports various IT operations, including cloud computing services and enterprise IT architecture.

> [!quote]- Virtualization Architecture
> ![Diagram](virtualization-1.png)
>
> ![Diagram](virtualization-2.png)
> **Source**: ByteByteGo

- **Hypervisor** - a crucial software layer that enables virtualization by managing VMs on a host machine.
    - Allocates physical resources such as CPU, memory, and storage to each VM while ensuring isolation between them.
    - There are two main types of hypervisors:
        - **Type 1 (Bare-Metal Hypervisors)**: Installed directly on the hardware (e.g., VMware ESXi).
        - **Type 2 (Hosted Hypervisors)**: Installed on an existing operating system (e.g., Oracle VirtualBox).
- **Virtual Machine (VM)** - a software emulation of a physical computer that runs its own operating system and applications as if it were running on dedicated hardware.
    - Each VM is unaware of its virtual nature and behaves like a standalone computer.
- **Host** - the physical machine that runs the hypervisor.
- **Guests** - the VMs operating on a host.

## Types

- **Server Virtualization** - allows multiple virtual servers to run on a single physical server, maximizing resource utilization and simplifying management.
    - Each VM can operate with its own OS and applications independently.
- **Network Virtualization** - abstracts networking components into software-based solutions, enabling the creation of programmable networks that improve flexibility and security.
- **Storage Virtualization** - pools physical storage from multiple devices into a single logical storage unit managed centrally.
    - Enhances efficiency in data management and simplifies operations.
- **Application Virtualization** - decouples applications from their underlying operating systems, allowing them to run in isolated environments regardless of the OS they were designed for.
- **Desktop Virtualization** - centralizes desktop management in data centers, allowing users to access their desktops remotely from various devices
    - also known as Virtual Desktop Infrastructure (VDI).

![[Containers vs. VMs]]
