---
title: Erőforrás-naplózás engedélyezése az Azure-ban Traffic Manager
description: Megtudhatja, hogyan engedélyezheti az erőforrás-naplózást a Traffic Manager profiljához, és hogyan férhet hozzá az eredményként létrehozott naplófájlokhoz.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: 4cf3709574e2055f40759fd2d7026c93ac9db098
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608019"
---
# <a name="enable-resource-logging-in-azure-traffic-manager"></a>Erőforrás-naplózás engedélyezése az Azure-ban Traffic Manager

Ez a cikk azt ismerteti, hogyan engedélyezhető a diagnosztikai erőforrás-naplók gyűjtése, és hogyan férhet hozzá a Traffic Manager profilhoz tartozó naplózási adatnaplóhoz.

Az Azure Traffic Manager erőforrás-naplói betekintést nyerhetnek a Traffic Manager profil erőforrásának viselkedésére. A profil naplójának használatával például megadhatja, hogy az egyes mintavételek miért nem voltak időtúllépések egy végponton.

## <a name="enable-resource-logging"></a>Erőforrás-naplózás engedélyezése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbi parancsokat futtathatja a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy futtathatja a PowerShellt a számítógépről. A Azure Cloud Shell egy ingyenes interaktív rendszerhéj. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a PowerShellt a számítógépről futtatja, szüksége lesz a Azure PowerShell modulra, a 1.0.0-ra vagy az újabb verzióra. A futtatásával `Get-Module -ListAvailable Az` megkeresheti a telepített verziót. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, az Azure-ba való bejelentkezéshez is futtatnia kell `Login-AzAccount` .

1. **Traffic Manager profil beolvasása:**

    Az erőforrás-naplózás engedélyezéséhez szükség van egy Traffic Manager-profil AZONOSÍTÓJÁRA. Kérje le azt a Traffic Manager-profilt, amely számára engedélyezni szeretné az erőforrás-naplózást a [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)használatával. A kimenet tartalmazza a Traffic Manager profil AZONOSÍTÓjának adatait.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Erőforrás-naplózás engedélyezése a Traffic Manager profilhoz:**

    Engedélyezze az erőforrás-naplózást a Traffic Manager profilhoz az előző lépésben a [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting)használatával BEszerzett azonosítóval. A következő parancs a Traffic Manager profilhoz tartozó részletes naplókat tárolja egy megadott Azure Storage-fiókban. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Diagnosztikai beállítások ellenőrzése:**

      Ellenőrizze a Traffic Manager-profil diagnosztikai beállításait a [Get-AzDiagnosticSetting](/powershell/module/az.monitor/get-azdiagnosticsetting)használatával. A következő parancs megjeleníti az adott erőforráshoz naplózott kategóriákat.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Győződjön meg arról, hogy az Traffic Manager profil erőforrásához társított összes naplózási kategória engedélyezve van. Ellenőrizze azt is, hogy a Storage-fiók megfelelően van-e beállítva.

## <a name="access-log-files"></a>Hozzáférési naplófájlok
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Navigáljon az Azure Storage-fiókjához a portálon.
2. Az Azure Storage-fiók **Áttekintés** lapján a **szolgáltatások** területen válassza a **Blobok** elemet.
3. A **tárolók** területen válassza az elemzések **-naplók-probehealthstatusevents** lehetőséget, majd lépjen a fájl PT1H.jsra, és kattintson a **Letöltés** gombra a naplófájl másolatának letöltéséhez és mentéséhez.

    ![A Traffic Manager profilhoz tartozó naplófájlok elérése blob Storage-ból](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Traffic Manager naplózási séma

A Azure Monitoron keresztül elérhető összes erőforrás-napló közös legfelső szintű sémával rendelkezik, és minden szolgáltatás számára rugalmasságot biztosít, hogy egyedi tulajdonságokat bocsát ki a saját eseményeihez. A legfelső szintű erőforrás-naplók sémája: [támogatott szolgáltatások, sémák és kategóriák az Azure-erőforrások naplóihoz](../azure-monitor/essentials/resource-logs-schema.md).

Az alábbi táblázat az Azure Traffic Manager-profil erőforrásához tartozó naplók sémáját tartalmazza.

|Mezőnév|Mező típusa|Definíció|Példa|
|----|----|---|---|
|Végpontneve|Sztring|Annak az Traffic Manager végpontnak a neve, amelynek az állapotát rögzíti a rendszer.|*myPrimaryEndpoint*|
|Állapot|Sztring|A kipróbált Traffic Manager végpont állapota. Az állapot lehet akár **felfelé** , akár **lefelé** is.|**Fel**|
|||||

## <a name="next-steps"></a>Következő lépések

* További információ a [Traffic Manager figyelésről](traffic-manager-monitoring.md)