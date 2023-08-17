---
title: Lsv3-series - Azure Virtual Machines 
description: Specifications for the Lsv3-series of Azure Virtual Machines (Azure VMs). 
author: sasha-melamed 
ms.service: virtual-machines 
ms.subservice: sizes
ms.topic: conceptual 
ms.date: 06/01/2022
ms.author: sasham
--- 

# Lsv3-series 

**Applies to:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: Flexible scale sets :heavy_check_mark: Uniform scale sets 

The Lsv3-series of Azure Virtual Machines (Azure VMs) features high-throughput, low latency, directly mapped local NVMe storage. These VMs run on the 3rd Generation Intel&reg; Xeon&reg; Platinum 8370C (Ice Lake) processor in a [hyper-threaded configuration](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html). This new processor features an all-core turbo clock speed of 3.5 GHz with [Intel&reg; Turbo Boost Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Advanced-Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) and [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html).  

The Lsv3-series VMs are available in sizes from 8 to 80 vCPUs. There are 8 GiB of memory allocated per vCPU, and one 1.92TB NVMe SSD device allocated per 8 vCPUs, with up to 19.2TB (10x1.92TB) available on the L80s_v3 size. 

> [!NOTE] 
> The Lsv3-series VMs are optimized to use the local disk on the node attached directly to the VM rather than using [durable data disks](disks-types.md). This method allows for greater IOPS and throughput for your workloads. The Lsv3, Lasv3, Lsv2, and Ls-series VMs don't support the creation of a host cache to increase the IOPS achievable by durable data disks. 
> 
> The high throughput and IOPS of the local disk makes the Lsv3-series VMs ideal for NoSQL stores such as Apache Cassandra and MongoDB. These stores replicate data across multiple VMs to achieve persistence in the event of the failure of a single VM. 
> 
> To learn more, see how to optimize performance on the Lsv3-series [Windows-based VMs](../virtual-machines/windows/storage-performance.md) or [Linux-based VMs](../virtual-machines/linux/storage-performance.md).   

- [Premium Storage](premium-storage-performance.md): Supported 
- [Premium Storage caching](premium-storage-performance.md): Not Supported 
- [Live Migration](maintenance-and-updates.md): Not Supported 
- [Memory Preserving Updates](maintenance-and-updates.md): Supported 
- [VM Generation Support](generation-2.md): Generation 1 and 2 
- [Accelerated Networking](../virtual-network/create-vm-accelerated-networking-cli.md): Supported 
- [Ephemeral OS Disks](ephemeral-os-disks.md): Supported  
- [Nested Virtualization](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): Supported  

| Size | vCPU | Memory (GiB) | Temp disk (GiB) | NVMe Disks | NVMe Disk throughput (Read IOPS/MBps) | Uncached data disk throughput (IOPS/MBps) | Max burst uncached data disk throughput (IOPS/MBps) | Max Data Disks | Max NICs | Expected network bandwidth (Mbps) | 
|---|---|---|---|---|---|---|---|---|---|---| 
| Standard_L8s_v3   |  8 |  64 |  80 |  1x1.92 TB  | 400000/2000  | 12800/290  | 20000/1200 | 16 | 4 | 12500 | 
| Standard_L16s_v3  | 16 | 128 | 160 |  2x1.92 TB  | 800000/4000  | 25600/600  | 40000/1600 | 32 | 8 | 12500 | 
| Standard_L32s_v3  | 32 | 256 | 320 |  4x1.92 TB  | 1.5M/8000    | 51200/865  | 80000/2000 | 32 | 8 | 16000 | 
| Standard_L48s_v3  | 48 | 384 | 480 |  6x1.92 TB  | 2.2M/14000   | 76800/1315 | 80000/3000 | 32 | 8 | 24000 | 
| Standard_L64s_v3  | 64 | 512 | 640 |  8x1.92 TB  | 2.9M/16000   | 80000/1735 | 80000/3000 | 32 | 8 | 30000 | 
| Standard_L80s_v3 | 80 | 640 | 800 | 10x1.92TB | 3.8M/20000 | 80000/2160 | 80000/3000 | 32 | 8 | 32000 | 

1. **Temp disk**: Lsv3-series VMs have a standard SCSI-based temp resource disk for use by the OS paging or swap file (`D:` on Windows, `/dev/sdb` on Linux). This disk provides 80 GiB of storage, 4,000 IOPS, and 80 MBps transfer rate for every 8 vCPUs. For example, Standard_L80s_v3 provides 800 GiB at 40000 IOPS and 800 MBPS. This configuration ensures the NVMe drives can be fully dedicated to application use. This disk is ephemeral, and all data is lost on stop or deallocation. 
2. **NVMe Disks**: NVMe disk throughput can go higher than the specified numbers. However, higher performance isn't guaranteed. Local NVMe disks are ephemeral. Data is lost on these disks if you stop or deallocate your VM. 
3. **NVMe Disk encryption** Lsv3 VMs created or allocated on or after 1/1/2023 have their local NVMe drives encrypted by default using hardware-based encryption with a Platform-managed key, except for the regions listed below. 

> [!NOTE]
> Central US and Qatar Central Lsv3 VMs created or allocated on or after 4/1/2023 have their local NVMe Drives Encrypted. 

4. **NVMe Disk throughput**: Hyper-V NVMe Direct technology provides unthrottled access to local NVMe drives mapped securely into the guest VM space. Lsv3 NVMe disk throughput can go higher than the specified numbers, but higher performance isn't guaranteed. To achieve maximum performance, see how to optimize performance on the Lsv3-series [Windows-based VMs](../virtual-machines/windows/storage-performance.md) or [Linux-based VMs](../virtual-machines/linux/storage-performance.md).   Read/write performance varies based on IO size, drive load, and capacity utilization. 
5. **Max burst uncached data disk throughput**: Lsv3-series VMs can [burst their disk performance](./disk-bursting.md) for up to 30 minutes at a time. 

> [!NOTE]
> Lsv3-series VMs don't provide host cache for data disk as it doesn't benefit the Lsv3 workloads. 

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

## Next steps 

Learn more about how [Azure compute units (ACU)](acu.md) can help you compare compute performance across Azure SKUs. 
