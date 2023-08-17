---
title: Overview of share snapshots for Azure Files
description: A share snapshot is a read-only version of an Azure file share that's taken at a point in time, as a way to back up the share.
author: khdownie
ms.service: azure-file-storage
ms.topic: conceptual
ms.date: 06/07/2023
ms.author: kendownie
---

# Overview of share snapshots for Azure Files
Azure Files provides the capability to take snapshots of SMB file shares. Share snapshots capture the share state at that point in time. This article describes the capabilities that file share snapshots provide and how you can take advantage of them in your use case.

## Applies to
| File share type | SMB | NFS |
|-|:-:|:-:|
| Standard file shares (GPv2), LRS/ZRS | ![Yes](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Standard file shares (GPv2), GRS/GZRS | ![Yes](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Premium file shares (FileStorage), LRS/ZRS | ![Yes](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |

## When to use share snapshots

### Protection against application error and data corruption

Applications that use file shares perform operations such as writing, reading, storage, transmission, and processing. If an application is misconfigured or an unintentional bug is introduced, accidental overwrite or damage can happen to a few blocks. To help protect against these scenarios, you can take a share snapshot before you deploy new application code. If a bug or application error is introduced with the new deployment, you can go back to a previous version of your data on that file share. 

### Protection against accidental deletions or unintended changes

Imagine that you're working on a text file in a file share. After the text file is closed, you lose the ability to undo your changes. In these cases, you then need to recover a previous version of the file. You can use share snapshots to recover previous versions of the file if it's accidentally renamed or deleted.

### General backup purposes

After you create a file share, you can periodically create a share snapshot of the file share to use it for data backup. A share snapshot, when taken periodically, helps maintain previous versions of data that can be used for future audit requirements or disaster recovery. We recommend using [Azure file share backup](../../backup/azure-file-share-backup-overview.md) for taking and managing snapshots. You can also take and manage snapshots yourself, using the [Azure portal](storage-files-quick-create-use-windows.md#create-a-share-snapshot), [Azure PowerShell](/powershell/module/az.storage/new-azrmstorageshare), or [Azure CLI](/cli/azure/storage/share#az-storage-share-snapshot).

## Capabilities

A share snapshot is a point-in-time, read-only copy of your data. Share snapshot capability is provided at the file share level. Retrieval is provided at the individual file level, to allow for restoring individual files. You can restore a complete file share by using SMB, the REST API, the Azure portal, the client library, or PowerShell/CLI.

You can view snapshots of a share by using the REST API or SMB. You can retrieve the list of versions of the directory or file, and you can mount a specific version directly as a drive (only available on Windows - see [Limits](#limits)). 

After a share snapshot is created, it can be read, copied, or deleted, but not modified. You can't copy a whole share snapshot to another storage account. You have to do that file by file, by using AzCopy or other copying mechanisms.

A share snapshot of a file share is identical to its base file share. The only difference is that a **DateTime** value is appended to the share URI to indicate the time at which the share snapshot was taken. For example, if a file share URI is http:\//storagesample.core.file.windows.net/myshare, the share snapshot URI is similar to:

```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Share snapshots persist until they are explicitly deleted. A share snapshot can't outlive its base file share. You can enumerate the snapshots associated with the base file share to track your current snapshots. 

When you create a share snapshot of a file share, the files in the share's system properties are copied to the share snapshot with the same values. The base files and the file share's metadata are also copied to the share snapshot, unless you specify separate metadata for the share snapshot when you create it.

You can't delete a share that has share snapshots unless you first delete all the snapshots for that share.

## Space usage

Share snapshots are incremental in nature. Only the data that has changed after your most recent share snapshot is saved. This minimizes the time required to create the share snapshot and saves on storage costs. Any write operation to the object or property or metadata update operation is counted toward "changed content" and is stored in the share snapshot. 

To conserve space, you can delete the share snapshot for the period when the churn was highest.

Even though share snapshots are saved incrementally, you need to retain only the most recent share snapshot in order to restore the share. When you delete a share snapshot, only the data unique to that share snapshot is removed. Active snapshots contain all the information that you need to browse and restore your data (from the time the share snapshot was taken) to the original location or an alternate location. You can restore at the item level.

Snapshots don't count towards the maximum share size limit, which is 100 TiB for premium file shares and standard file shares with large file shares enabled. There's no limit to how much space share snapshots occupy in total. Storage account limits still apply.

## Limits

The maximum number of share snapshots that Azure Files allows today is 200 per share. After 200 share snapshots, you have to delete older share snapshots in order to create new ones. You can retain snapshots for up to 10 years.

There's no limit to the simultaneous calls for creating share snapshots. There's no limit to amount of space that share snapshots of a particular file share can consume. 

Taking snapshots of NFS Azure file shares isn't currently supported.

## Copying data back to a share from share snapshot

Copy operations that involve files and share snapshots follow these rules:

You can copy individual files in a file share snapshot over to its base share or any other location. You can restore an earlier version of a file or restore the complete file share by copying file by file from the share snapshot. The share snapshot is not promoted to base share. 

The share snapshot remains intact after copying, but the base file share is overwritten with a copy of the data that was available in the share snapshot. All the restored files count toward "changed content."

You can copy a file in a share snapshot to a different destination with a different name. The resulting destination file is a writable file and not a share snapshot. In this case, your base file share will remain intact.

When a destination file is overwritten with a copy, any share snapshots associated with the original destination file remain intact.

## General best practices

Automate backups for data recovery whenever possible. Automated actions are more reliable than manual processes, helping to improve data protection and recoverability. You can use Azure file share backup, the REST API, the Client SDK, or scripting for automation.

Before you deploy the share snapshot scheduler, carefully consider your share snapshot frequency and retention settings to avoid incurring unnecessary charges.

Share snapshots provide only file-level protection. Share snapshots don't prevent fat-finger deletions on a file share or storage account. To help protect a storage account from accidental deletions, you can either [enable soft delete](storage-files-prevent-file-share-deletion.md), or lock the storage account and/or the resource group.

## See also
- Working with share snapshots in:
    - [Azure file share backup](../../backup/azure-file-share-backup-overview.md)
    - [Azure PowerShell](/powershell/module/az.storage/new-azrmstorageshare)
    - [Azure CLI](/cli/azure/storage/share#az-storage-share-snapshot)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Share snapshot FAQ](storage-files-faq.md#share-snapshots)
