---
title: What's new in Update management center (Preview)
description: Learn about what's new and recent updates in the Update management center (Preview) service.
ms.service: update-management-center
ms.topic: overview
author: SnehaSudhirG
ms.author: sudhirsneha
ms.date: 07/05/2023
---

# What's new in Update management center (Preview)

[Update management center (preview)](overview.md) helps you manage and govern updates for all your machines. You can monitor Windows and Linux update compliance across your deployments in Azure, on-premises, and on the other cloud platforms from a single dashboard. This article summarizes new releases and features in Update management center (Preview).

## July 2023

### Dynamic scope (preview)

Dynamic scope (preview) is an advanced capability of schedule patching. You can now create a group of [machines based on a schedule and apply patches](dynamic-scope-overview.md) on those machines at scale. [Learn more](tutorial-dynamic-grouping-for-scheduled-patching.md).
 

## May 2023

### Customized image support

Update management center (preview) now supports [generalized](../virtual-machines/linux/imaging.md#generalized-images) custom images, and a combination of offer, publisher, and SKU for Marketplace/PIR images.See the [list of supported operating systems](support-matrix.md#supported-operating-systems). 

### Multi-subscription support

The limit on the number of subscriptions that you can manage to use the Update management center (preview) portal has now been removed. You can now manage all your subscriptions using the update management center (preview) portal.

## April 2023

### New prerequisite for scheduled patching

A new patch orchestration - **Customer Managed Schedules (Preview)** is introduced as a prerequisite to enable scheduled patching on Azure VMs. The new patch enables the *Azure-orchestrated* and *BypassPlatformSafteyChecksOnUserSchedule* VM properties on your behalf after receiving the consent. [Learn more](prerequsite-for-schedule-patching.md).

> [!IMPORTANT]
> For a seamless scheduled patching experience, we recommend that for all Azure VMs, you update the patch orchestration to **Customer Managed Schedules (Preview)** by **30th June 2023**. If you fail to update the patch orchestration by **30th June 2023**, you can experience a disruption in business continuity because the schedules will fail to patch the VMs.


## November 2022

### New region support

Update management center (Preview) now supports new five regions for Azure Arc-enabled servers. [Learn more](support-matrix.md#supported-regions).

## October 2022

### Improved on-boarding experience

You can now enable periodic assessment for your machines at scale using [Policy](periodic-assessment-at-scale.md) or from the [portal](manage-update-settings.md#configure-settings-on-single-vm).


## Next steps

- [Learn more](support-matrix.md) about supported regions.