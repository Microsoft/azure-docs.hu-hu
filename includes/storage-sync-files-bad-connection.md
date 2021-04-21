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
ms.openlocfilehash: 4407ffb9ab16a720ef00a288d72b0fb4c2dbced1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774564"
---
Ez a hiba akkor következhet be, ha az Azure File Sync nem érhető el a kiszolgálóról. A hiba elhárításához végezze el a következő lépéseket:

1. Ellenőrizze, hogy a tűzfal `FileSyncSvc.exe` nem blokkolja-e a Windows-szolgáltatást.
2. Ellenőrizze, hogy a 443-as port nyitva van-e a Azure File Sync számára. Ezt a parancsmaggal `Test-NetConnection` tudja megtenni. Az alábbi `<azure-file-sync-endpoint>` helyőrző URL-címe [Az Azure File Sync proxy- és tűzfalbeállításai](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#firewall) című dokumentumban található. 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Ügyeljen arra, hogy a proxykonfiguráció a vártnak megfelelően legyen beállítva. Ez a `Get-StorageSyncProxyConfiguration` parancsmaggal végezhető el. Az Azure File Sync proxykonfigurációjának beállításáról [Az Azure File Sync proxy- és tűzfalbeállításai](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#firewall) című dokumentumban talál további információt.

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. A Test-StorageSyncNetworkConnectivity parancsmag használatával ellenőrizze a szolgáltatásvégpontokkal való hálózati kapcsolatot. További információ: [Szolgáltatásvégpontokkal való hálózati kapcsolat tesztelése.](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints)    

5. A hálózati kapcsolat hibaelhárításához további segítségért forduljon a hálózati rendszergazdához.