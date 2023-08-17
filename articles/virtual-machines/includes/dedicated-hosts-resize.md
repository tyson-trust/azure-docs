---
title: Dedicated hosts include file
description: Include file for dedicated hosts resize functionality
services: virtual-machines
author: mattmcinnes
ms.topic: include
ms.service: virtual-machines
ms.subservice: azure-dedicated-host
ms.date: 07/12/2023
ms.author: mattmcinnes
ms.reviewer: vamckMS
ms.custom: include file
---

Moving a host and all associated VMs to newer generation hardware can be done through the host resize feature. Resize simplifies the migration process and avoids having to manually create new hosts and move all VMs individually.

> [!IMPORTANT]
> Host Resize is currently in PREVIEW.
> See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

Resize limitations:
- Host can only be resized to an ADH within the same VM family. A Dsv3-Type3 host can be resized to Dsv3-Type4 but **not** to an **E**sv3-Type4.
- You can only resize to newer generation of hardware. A Dsv3-Type3 host can be resized to Dsv3-Type4 but **not** Dsv3-Type2.
- Resizing changes the 'Host Asset ID'. The 'Host ID' remains the same.
- The host and all associated VMs becomes unavailable during the resize operation.

> [!Warning]
> The resize operation causes the loss of any non-persistent data such as temp disk data. Save all your work to persistent data storage before triggering resize.

> [!Note]
> During the public preview, hosts in host groups with Fault domain count of '1' might not support resize. This limitation is only temporary and will be removed as we announce general availability of host resize.
> 
> If the source host is already running on the latest hardware, 'Size' page would display an empty list. If you're looking for enhanced performance, consider switching to a different VM family.


### [Portal](#tab/portal)

1. Search for and select the host.
1. In the left menu under **Settings**, select **Size**.
1. Once on the size page from the list of SKUs, select the desired SKU to resize to.
1. Selecting a target size from the list would enable **Resize** button on the bottom on the page.
1. Click **Resize**, host's 'Provisioning State' changes from 'Provisioning Succeeded' to 'Updating'
1. Once the resizing is complete, the host's 'Provisioning State' reverts to 'Provisioning Succeeded'


### [CLI](#tab/cli)

First list the sizes that you can resize in case you are unsure which to resize to.

Use [az vm host list-resize-options](/cli/azure/vm#az-vm-host-list-resize-options) [Preview]

```azurecli-interactive
az vm host list-resize-options \
 --host-group myHostGroup \
 --host-name myHost \
 --resource-group myResourceGroup
```

Resize the host using [az vm host resize](/cli/azure/vm#az-vm-host-resize) [Preview].

```azurecli-interactive
az vm host resize \
 --host-group myHostGroup \
 --host-name myHost \
 --resource-group myResourceGroup \
 --sku Dsv3-Type4
```

### [PowerShell](#tab/powershell)

PowerShell support for host resize is coming soon.

---