---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b26d4af29a92fb0f14c52e76a7eae1d0073a3aa0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005320"
---
Ez a hiba akkor következhet be, ha az Azure File Sync nem érhető el a kiszolgálóról. A hiba elhárításához végezze el a következő lépéseket:

1. Ellenőrizze, hogy a tűzfal nem blokkolja-e a Windows-szolgáltatást `FileSyncSvc.exe` .
2. Ellenőrizze, hogy a 443-es port nyitva van-e a kimenő kapcsolatokhoz a Azure File Sync szolgáltatással. Ezt a parancsmaggal teheti meg `Test-NetConnection` . Az alábbi `<azure-file-sync-endpoint>` helyőrző URL-címe [Az Azure File Sync proxy- és tűzfalbeállításai](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) című dokumentumban található. 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Ügyeljen arra, hogy a proxykonfiguráció a vártnak megfelelően legyen beállítva. Ez a `Get-StorageSyncProxyConfiguration` parancsmaggal végezhető el. Az Azure File Sync proxykonfigurációjának beállításáról [Az Azure File Sync proxy- és tűzfalbeállításai](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) című dokumentumban talál további információt.

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. A Test-StorageSyncNetworkConnectivity parancsmag használatával ellenőrizheti a szolgáltatás-végpontokkal létesített hálózati kapcsolatot. További információért lásd: [hálózati kapcsolat tesztelése a szolgáltatási végpontokkal](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints).    

5. További segítségért forduljon a hálózati rendszergazdához, és kérjen segítséget a hálózati kapcsolatról.