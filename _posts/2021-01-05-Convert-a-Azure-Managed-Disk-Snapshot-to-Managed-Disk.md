---
layout: post
title: Convert a Azure Managed Disk Snapshot to Managed Disk
---
In the last few days I have had a number of users wanting to take VM snapshots and restoring them into a new Virtual Network for testing. Sure there is a way in the UI to do it, but lets do it in PowerShell. 

```powershell
$snapshot = Get-AzSnapshot -ResourceGroupName "RG1" -SnapshotName "VM1-SNAP"
$diskConfig = New-AzDiskConfig -Location $snapshot.Location -SourceResourceId $snapshot.Id -CreateOption Copy
New-AzDisk -Disk $diskConfig -ResourceGroupName "RG1"-DiskName "VM1-RESTORED-DISK"
```
This is a quick way to convert a snapshot, and because its PowerShell, you can use the switch -AsJob to make everything happen in parallel.

Have fun making test environments!