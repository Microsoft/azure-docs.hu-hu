---
title: Azure-fájlmegosztás – nem sikerült a fájlok törlése valamelyik Azure-fájlmegosztásban
description: Azonosítsa és hárítsa el a fájlok Azure-fájlmegosztásból való törlésének sikertelenségére adott hibát.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: d8cc0cb7df4bb7bfff5a6b9d2f159cb674532927
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789752"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure-fájlmegosztás – nem sikerült a fájlok törlése valamelyik Azure-fájlmegosztásban

A fájlok Azure-fájlmegosztásból való törlése több tünetet is okozhat:

**1. tünet:**

Nem sikerült törölni egy fájlt az Azure-fájlmegosztásban az alábbi két probléma egyike miatt:

* A törlésre megjelölt fájl
* A megadott erőforrást egy SMB-ügyfél is használhatja

**2. jelenség:**

Nincs elég kvóta a parancs feldolgozásához

## <a name="cause"></a>Ok

Az 1816-os hiba akkor fordul elő, ha eléri a fájlhoz engedélyezett egyidejű nyitott leírók felső korlátját a fájlmegosztást csatlakoztató számítógépen. További információt az Azure Storage teljesítmény- és méretezhetőségi [ellenőrzőlistája tartalmaz.](../blobs/storage-performance-checklist.md)

## <a name="resolution"></a>Feloldás

Néhány fogópont bezárásával csökkentheti az egyidejű nyitott leírók számát.

## <a name="prerequisite"></a>Előfeltétel

### <a name="install-the-latest-azure-powershell-module"></a>A legújabb Azure PowerShell telepítése

* [Az Azure PowerShell-modul telepítése](/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Csatlakozás az Azure-hoz:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Válassza ki a cél tárfiók előfizetését:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Hozza létre a cél tárfiók környezetét:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Szerezze be a fájlmegosztás aktuálisan megnyitott leíróit:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Példa eredmény:

|HandleId (Leíróazonosító)|Elérési út|ClientIp (Ügyfél IP-címe)|ClientPort (Ügyfélport)|OpenTime|LastReconnectTime (Legutóbbi kapcsolat ideje)|FileId (Fájlazonosító)|ParentId (Szülőazonosító)|Munkamenet|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Új mappa/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Zárja be a megnyitott leírót:

Egy megnyitott leírót a következő paranccsal zárhatja be:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Következő lépések

* [Hibaelhárítás Azure Files Windowsban](storage-troubleshoot-windows-file-connection-problems.md)
* [Hibaelhárítás Azure Files Linux rendszeren](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure File Sync – hibaelhárítás](../file-sync/file-sync-troubleshoot.md)