---
title: 'Basv2 Series (preview)' #Required; page title is displayed in search results. 60 characters max.
description: Overview of AMD Bsv2 Virtual Machine Series; #Required; this appears in search as the short description
author: rishabv90 #Required. GitHub user alias, with correct capitalization.
ms.author: risverma #Required. Microsoft alias of author or team alias.
ms.service: virtual-machines #Required
ms.subservice: sizes #Required
ms.topic: conceptual #Required 
ms.date: 06/20/2022 #Required; mm/dd/yyyy format. Date the article was created or the last time it was tested and confirmed correct 

---

# Basv2-series (Public Preview)

Basv2-series virtual machines run on the AMD's 3rd Generation EPYCTM 7763v processor in a multi-threaded configuration with up to 256 MB L3 cache configuration, providing low cost CPU burstable general purpose virtual machines. Basv2-series virtual machines utilize a CPU credit model to track how much CPU is consumed - the virtual machine accumulates CPU credits when a workload is operating below the base CPU performance threshold and, uses credits when running above the base CPU performance threshold, until all of its credits are consumed. Upon consuming all the CPU credits, a Basv2-series virtual machine is throttled back to its base CPU performance until it accumulates the credits to CPU burst again.

Basv2-series virtual machines offer a balance of compute, memory, and network resources, and are a cost effective way to run a broad spectrum of general purpose workloads, including large scale micro-services, small and medium databases, virtual desktops, and business-critical applications; and are also an affordable option to run your code repositories and dev/test environments. Basv2-Series offers virtual machines of up-to 32 vCPU and 128 Gib of RAM, with max network bandwidth of upto 6250 Mbps and max uncached disk throughput of 600 Mbps. Basv2-series virtual machines also support attachments of Standard SSD, Standard HDD, Premium SSD disk types with a default Remote-SSD support, you can also attach Ultra Disk storage based on its regional availability. Disk storage is billed separately from virtual machines. [See pricing for disks](https://azure.microsoft.com/pricing/details/managed-disks/).    


[Premium Storage](premium-storage-performance.md): Supported<br>
[Premium Storage caching](premium-storage-performance.md): Supported<br>
[Live Migration](maintenance-and-updates.md): Supported<br>
[Memory Preserving Updates](maintenance-and-updates.md): Supported<br>
[VM Generation Support](generation-2.md): Generation 2<br>
[Accelerated Networking](../virtual-network/create-vm-accelerated-networking-cli.md)<sup>1</sup>: Supported<br>
[Ephemeral OS Disks](ephemeral-os-disks.md): Not Supported <br>
[Nested Virtualization](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): Not Supported <br>
<br> 

| Size               | vCPU | RAM | Base CPU Performance of VM (%) | Initial Credits (#) | Credits banked/hour | Max Banked Credits (#) | Max uncached disk throughput: IOPS/M8ps | Max burst uncached disk throughput: IOPS/MBps | Max Data Disks | Max Network Bandwidth (Gbps) | Max NICs |
|--------------------|------|-----|--------------------------------|---------------------|---------------------|------------------------|-----------------------------------------|-----------------------------------------------|----------------|------------------------------|----------|
| Standard_B2ats_v2  | 2    | 1   | 20%                            | 60                  | 24                  | 576                    | 3750/85                                 | 10,000/960                                    | 4              | 6.25                         | 2        |
| Standard_B2als_v2  | 2    | 4   | 30%                            | 60                  | 24                  | 576                    | 3750/85                                 | 10,000/960                                    | 4              | 6.25                         | 2        |
| Standard_B2as_v2   | 2    | 8   | 40%                            | 600                 | 24                  | 576                    | 3750/85                                 | 10,000/960                                    | 4              | 6.25                         | 2        |
| Standard_B4als_v2  | 4    | 8   | 30%                            | 120                 | 48                  | 1152                   | 6,400/145                               | 20,000/960                                    | 8              | 6.25                         | 2        |
| Standard_B4as_v2   | 4    | 16  | 40%                            | 120                 | 48                  | 1150                   | 6,400/145                               | 20,000/960                                    | 8              | 6.25                         | 2        |
| Standard_B8als_v2  | 8    | 16  | 30%                            | 240                 | 96                  | 2304                   | 12,800/290                              | 20,000/960                                    | 16             | 6.25                         | 2        |
| Standard_B8as_v2   | 8    | 32  | 40%                            | 240                 | 96                  | 2304                   | 12,800/290                              | 20,000/960                                    | 16             | 6.25                         | 2        |
| Standard_B16als_v2 | 16   | 32  | 30%                            | 480                 | 192                 | 4608                   | 25,600/600                              | 40,000/960                                    | 32             | 6.25                         | 4        |
| Standard_B16as_v2  | 16   | 64  | 40%                            | 480                 | 192                 | 4608                   | 25,600/600                              | 40,000/960                                    | 32             | 6.25                         | 4        |
| Standard_B32als_v2 | 32   | 64  | 60%                            | 960                 | 384                 | 9216                   | 25,600/600                              | 80,000/960                                    | 32             | 6.25                         | 4        |
| Standard_B32as_v2  | 32   | 128 | 40%                            | 960                 | 384                 | 9216                   | 25,600/600                              | 80,000/960                                    | 32             | 6.25                         | 4        |




[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## Other sizes and information

- [General purpose](sizes-general.md)
- [Memory optimized](sizes-memory.md)
- [Storage optimized](sizes-storage.md)
- [GPU optimized](sizes-gpu.md)
- [High performance compute](sizes-hpc.md)
- [Previous generations](sizes-previous-gen.md)

Pricing Calculator: [Pricing Calculator](https://azure.microsoft.com/pricing/calculator/)

More information on Disks Types: [Disk Types](./disks-types.md#ultra-disks)
