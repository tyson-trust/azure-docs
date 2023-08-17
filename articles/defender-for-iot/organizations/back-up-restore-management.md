---
title: Back up and restore the on-premises management console - Microsoft Defender for IoT
description: Learn how to back up and restore the Microsoft Defender for IoT on-premises management console.
ms.date: 07/31/2023
ms.topic: how-to
---

# Back up and restore the on-premises management console

Back up and restore your on-premises management console to help protect against hard drive failures and data loss. In this article, learn how to:

- Define backup and restore settings
- Run an unscheduled backup via CLI
- Use an SMB server to save your backup files to an external server
- Restore the on-premises management console from the latest backup via CLI

## Define automated backup and restore settings

The on-premises management console is automatically backed up daily to the `/var/cyberx/backups` directory. Backup files do *not* include PCAP or log files, which must be manually backed up if needed.

We recommend that you configure your on-premises management console to automatically transfer backup files to your own, internal network.

> [!NOTE]
> Backup files can be used to restore an on-premises management console only if the on-premises management console's current software version is the same as the version in the backup file.

## Manually extract a system backup file

This procedure describes how to manually extract an on-premises management console backup file. Exported backup files can't be used by customers to restore an on-premises management console, and are only meant to be sent to customer support for analysis while troubleshooting.

1. Sign into the on-premises management console as an admin user.
1. Select **System settings** > **Export**.
1. Under **Export Options**, select **System backup** and then select **Export**.

    The exported file is created, and listed in the **Archived Files** grid at the bottom of the page.

1. Click the filename to download your exported file. Make sure to view the OTP (One Time Password) to send it to support with your exported file.

## Start an immediate, unscheduled backup via CLI

You may want to create a manual backup file, such as just after updating your OT sensor software.

To run a manual backup from the CLI:

1. Sign into the on-premises management console as a privileged user via SSH/Telnet.

1. Run:

   ```bash
   sudo cyberx-management-backup -full
   ```

## Save your backup file to an external server (SMB)

We recommend saving your on-premises management console sensor backup files on your internal network. To do this, you may want to use an SMB server. For example:

1. Create a shared folder on the external SMB server, and make sure that you have the folder's path and the credentials required to access the SMB server.

1. Sign into your on-premises management console via SFTP and create a directory for your backup files. Run:

   ```bash
   sudo mkdir /<backup_folder_name_on_ server>
   sudo chmod 777 /<backup_folder_name_on_c_server>/
   ```

1. Edit the `fstab` file with details about your backup folder. Run:

   ```bash
   sudo nano /etc/fstab

   add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0
   ```

1. Edit and create credentials to share for the SMB server. Run:

   ```bash
   sudo nano /etc/samba/user
   ```

1. Add your credentials as follows:

   ```bash
   username=<user name>
   password=<password>
   ```

1. Mount the backup directory. Run:

   ```bash
   sudo mount -a
   ```

1. Configure your backup directory on the SMB server to use the shared file on the OT sensor. Run:

    ```bash
    sudo nano /var/cyberx/properties/backup.properties`
    ```

    Set the `backup_directory_path` to the folder on your OT sensor where you want to save your backup files.

## Restore from the latest backup via CLI

To restore your OT sensor from the latest backup file via CLI:

1. Sign into the on-premises management console as a privileged user via SSH/Telnet.

1. Run:

    ```bash
    $ sudo cyberx-management-system-restore -b <file path/file name>
    ```

## Next steps

[Maintain the on-premises management console](how-to-manage-the-on-premises-management-console.md)
