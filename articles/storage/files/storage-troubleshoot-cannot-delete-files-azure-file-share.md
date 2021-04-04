---
title: Azure-fájlmegosztás – nem sikerült a fájlok törlése valamelyik Azure-fájlmegosztásban
description: Azonosíthatja és elháríthatja az Azure-fájlmegosztás fájljainak törlési hibáját.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 3d4f10745d90ccd83e7251af40d3e92a230f2fcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629682"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure-fájlmegosztás – nem sikerült a fájlok törlése valamelyik Azure-fájlmegosztásban

A fájlok Azure-Fájlmegosztásből való törlésének sikertelensége több tünettel is rendelkezhet:

**1. tünet:**

Az alábbi két probléma egyike miatt nem sikerült törölni egy fájlt az Azure file share szolgáltatásban:

* A törlésre kijelölt fájl
* Lehetséges, hogy a megadott erőforrást SMB-ügyfél használja

**2. tünet:**

Nem áll rendelkezésre elegendő kvóta a parancs feldolgozásához

## <a name="cause"></a>Ok

1816-as hiba történik, amikor eléri a fájlhoz engedélyezett egyidejű nyitott kezelők felső korlátját azon a számítógépen, amelyen a fájlmegosztás csatlakoztatva van. További információ: az [Azure Storage teljesítmény-és méretezhetőségi ellenőrzőlistája](../blobs/storage-performance-checklist.md).

## <a name="resolution"></a>Feloldás

Csökkentse az egyidejű megnyitott fogópontok számát néhány leíró bezárásával.

## <a name="prerequisite"></a>Előfeltétel

### <a name="install-the-latest-azure-powershell-module"></a>A legújabb Azure PowerShell modul telepítése

* [Az Azure PowerShell-modul telepítése](/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Kapcsolódás az Azure-hoz:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Válassza ki a cél Storage-fiók előfizetését:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Környezet létrehozása a cél Storage-fiókhoz:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>A fájlmegosztás aktuálisan megnyitott leíróinak beolvasása:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Példa eredménye:

|HandleId|Elérési út|ClientIp (Ügyfél IP-címe)|ClientPort|OpenTime|LastReconnectTime|FileId|ParentId|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Új mappa/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Megnyitott leíró lezárása:

A megnyitott leíró bezárásához használja a következő parancsot:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Következő lépések

* [A Windows Azure Files hibáinak megoldása](storage-troubleshoot-windows-file-connection-problems.md)
* [A Linux Azure Files hibáinak megoldása](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure File Sync – hibaelhárítás](storage-sync-files-troubleshoot.md)