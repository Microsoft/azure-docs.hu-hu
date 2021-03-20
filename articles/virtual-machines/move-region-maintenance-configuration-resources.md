---
title: Karbantartási konfigurációhoz társított erőforrások áthelyezése másik régióba
description: Megtudhatja, hogyan helyezhet át egy virtuális gép karbantartási konfigurációjához társított erőforrásokat egy másik Azure-régióba
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 88082c441dafdc7571f2b9775bfc07ebe3ca5aa4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730506"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Erőforrások áthelyezése egy karbantartási vezérlő konfigurációjában egy másik régióba

Ebből a cikkből megtudhatja, hogyan helyezhet át egy karbantartási vezérlési konfigurációhoz társított erőforrásokat egy másik Azure-régióba. Előfordulhat, hogy több okból is át kívánja helyezni a konfigurációt. Ha például egy új régió előnyeit szeretné kihasználni, egy adott régióban elérhető szolgáltatásokat vagy szolgáltatásokat kíván üzembe helyezni, a belső házirend-és irányítási követelmények teljesítésére, vagy a kapacitás megtervezésére reagálva.

A [karbantartási szabályozás](maintenance-control.md), amely testreszabott karbantartási konfigurációkkal rendelkezik, lehetővé teszi a platformok frissítéseinek a virtuális gépekre és az Azure dedikált gazdagépekre való alkalmazásának szabályozását. A karbantartási ellenőrzés több régióban is áthelyezhető:

- Ha a karbantartási konfigurációhoz társított erőforrásokat szeretné áthelyezni, a konfigurációt azonban nem, akkor kövesse ezt a cikket.
- Ha át szeretné helyezni a karbantartási vezérlő konfigurációját, de nem a konfigurációhoz társított erőforrásokat, kövesse az [alábbi utasításokat](move-region-maintenance-configuration.md).
- A karbantartási konfiguráció és a hozzá társított erőforrások áthelyezéséhez először kövesse [ezeket az utasításokat](move-region-maintenance-configuration.md). Ezután kövesse a cikk utasításait.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt megkezdené a karbantartási vezérlő konfigurációjával kapcsolatos erőforrások áthelyezését:

- Mielőtt elkezdené, győződjön meg róla, hogy az áthelyezett erőforrások léteznek az új régióban.
- Ellenőrizze az Azure-beli virtuális gépekhez és az áthelyezni kívánt Azure dedikált gazdagépekhez társított karbantartási vezérlési konfigurációkat. Mindegyik erőforrást egyenként kell megnéznie. Jelenleg nincs mód több erőforrás konfigurációjának lekérésére.
- Egy erőforrás konfigurációinak beolvasása során:
    - Győződjön meg arról, hogy a fiók előfizetés-AZONOSÍTÓját használja, nem pedig az Azure dedikált gazdagép-AZONOSÍTÓját.
    - CLI: a--output Table paraméter csak olvashatóként használható, és törölhető vagy módosítható.
    - PowerShell: a Format-Table Name paraméter csak olvashatóságra szolgál, és törölhető vagy módosítható.
    - Ha a PowerShellt használja, hibaüzenet jelenik meg, ha olyan erőforrás konfigurációit próbál meg kilistázni, amely nem rendelkezik társított konfigurációval. A hiba a következőhöz hasonló lesz: "a művelet meghiúsult állapottal:" nem található ". Részletek: 404 ügyfél-hiba: nem található az URL-címhez.

    
## <a name="prepare-to-move"></a>Felkészülés az áthelyezésre

1. Mielőtt elkezdené, adja meg ezeket a változókat. Mindegyikhez példát adtunk.

    **Változó** | **Részletek** | **Példa**
    --- | ---
    $subId | A karbantartási konfigurációkat tartalmazó előfizetés azonosítója | "a-előfizetés-azonosító"
    $rsrcGroupName | Erőforráscsoport neve (Azure VM) | "VMResourceGroup"
    $vmName | Virtuális gép erőforrásának neve |  MyVM
    $adhRsrcGroupName |  Erőforráscsoport (dedikált gazdagépek) | "HostResourceGroup"
    $adh | Dedikált állomásnév | "myHost"
    $adhParentName | Szülő erőforrás neve | HostGroup
    
2. A karbantartási konfigurációk beolvasása a PowerShell [Get-AZConfigurationAssignment](/powershell/module/az.maintenance/get-azconfigurationassignment) parancs használatával:

    - Azure dedikált gazdagépek esetén futtassa a következőt:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Azure-beli virtuális gépek esetén futtassa a következőt:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. A karbantartási konfigurációk beolvasása a parancssori felületről a [karbantartási hozzárendelés](/cli/azure/ext/maintenance/maintenance/assignment) paranccsal:

    - Azure dedikált gazdagépek esetén:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Azure-beli virtuális gépek esetén:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Áthelyezés 

1. [Kövesse ezeket az utasításokat](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) az Azure-beli virtuális gépek új régióba való áthelyezéséhez.
2. Az erőforrások áthelyezését követően a karbantartási konfigurációkat a megfelelő módon alkalmazza újra az új régióban lévő erőforrásokra, attól függően, hogy áthelyezte-e a karbantartási konfigurációkat. A [PowerShell](../virtual-machines/maintenance-control-powershell.md) vagy a [parancssori](../virtual-machines/maintenance-control-cli.md)felület használatával karbantartási konfigurációt alkalmazhat egy erőforrásra.


## <a name="verify-the-move"></a>Az áthelyezés ellenőrzése

Ellenőrizze az új régióban lévő erőforrásokat, és ellenőrizze az új régióban lévő erőforrások társított konfigurációit. 

## <a name="clean-up-source-resources"></a>Forrás erőforrásainak törlése

Az áthelyezést követően érdemes törölni az áthelyezett erőforrásokat a forrás régióban.


## <a name="next-steps"></a>Következő lépések

Kövesse [ezeket az utasításokat](move-region-maintenance-configuration.md) , ha a karbantartási konfigurációkat át kell helyeznie. 
