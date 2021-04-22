---
title: Karbantartási konfigurációhoz társított erőforrások áthelyezése másik régióba
description: Megtudhatja, hogyan helyezze át a virtuális gép karbantartási konfigurációjának erőforrásait egy másik Azure-régióba
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 4427071edf237d82e8a99d44678d77d23e180fff
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865242"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>A karbantartásvezérlési konfiguráció erőforrásainak áthelyezése egy másik régióba

Ebben a cikkben a karbantartásvezérlési konfigurációhoz társított erőforrásokat egy másik Azure-régióba helyezze át. Több okból is érdemes lehet áthelyezni egy konfigurációt. Például egy új régió előnyeinek kihasználásához, egy adott régióban elérhető funkciók vagy szolgáltatások üzembe helyezéséhez, a belső szabályzati és irányítási követelményekhez való megfeleléshez vagy a kapacitástervezéshez.

[A karbantartásvezérlés](maintenance-control.md)testreszabott karbantartási konfigurációkkal lehetővé teszi annak szabályozását, hogy a rendszer hogyan alkalmazza a platformfrissítéseket a virtuális gépekre és az Azure dedikált gazdagépeire. A karbantartási vezérlés régiók közötti áthelyezésének több forgatókönyve is van:

- Ha a karbantartási konfigurációhoz társított erőforrásokat át kell áthelyezni, magát a konfigurációt azonban nem, kövesse ezt a cikket.
- A karbantartásvezérlési konfiguráció áthelyezését, de a konfigurációhoz társított erőforrásokat ne kövesse az [alábbi utasítások szerint.](move-region-maintenance-configuration.md)
- A karbantartási konfiguráció és a hozzá tartozó erőforrások áthelyezéshez először kövesse az [alábbi utasításokat.](move-region-maintenance-configuration.md) Ezután kövesse az ebben a cikkben található utasításokat.

## <a name="prerequisites"></a>Előfeltételek

A karbantartásvezérlési konfigurációhoz társított erőforrások áthelyezésének megkezdése előtt:

- Mielőtt hozzákezd, győződjön meg arról, hogy az átköltöztött erőforrások léteznek az új régióban.
- Ellenőrizze az áthelyezni kívánt Azure-beli virtuális gépekhez és Azure-beli dedikált gazdagéphez társított karbantartásvezérlési konfigurációkat. Ellenőrizze az egyes erőforrásokat egyenként. Jelenleg nem lehet több erőforrás konfigurációit lekérni.
- Erőforrás konfigurációjának lekért száma:
    - Győződjön meg arról, hogy a fiók előfizetés-azonosítóját használja, nem pedig Azure Dedicated Host azonosítóját.
    - CLI: A --output táblaparaméter csak olvashatósághoz használható, és törölhető vagy módosítható.
    - PowerShell: A Format-Table Name paraméter csak olvashatósághoz használható, és törölhető vagy módosítható.
    - Ha a PowerShellt használja, hibaüzenetet kap, ha olyan erőforrás konfigurációit próbálja listába sorolni, amelyekhez nem tartozik konfiguráció. A hiba a következő lesz: "A művelet a következő állapottal meghiúsult: "Nem található". Részletek: 404 Ügyfélhiba: Nem található az URL-címhez".

    
## <a name="prepare-to-move"></a>Felkészülés az áthelyezésre

1. Mielőtt elkezdené, határozza meg ezeket a változókat. Mindegyikhez biztosítottunk egy példát.

    **Változó** | **Részletek** | **Példa**
    --- | ---
    $subId | A karbantartási konfigurációkat tartalmazó előfizetés azonosítója | "our-subscription-ID"
    $rsrcGroupName | Erőforráscsoport neve (Azure-beli virtuális gép) | "VMResourceGroup"
    $vmName | Virtuális gép erőforrásneve |  "myVM"
    $adhRsrcGroupName |  Erőforráscsoport (dedikált gazdagépek) | "HostResourceGroup"
    $adh | Dedikált gazdagépnév | "myHost"
    $adhParentName | Szülőerőforrás neve | "HostGroup"
    
2. A karbantartási konfigurációk lekérése a PowerShell [Get-AZConfigurationAssignment parancsával:](/powershell/module/az.maintenance/get-azconfigurationassignment)

    - Azure-beli dedikált gazdagépek számára futtassa a következőt:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Azure-beli virtuális gépeken futtassa a következőt:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. A karbantartási konfigurációk lekérése a cli [az maintenance assignment paranccsal:](/cli/azure/maintenance/assignment)

    - Azure-beli dedikált gazdagépek:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Azure-beli virtuális gépekhez:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Áthelyezés 

1. [Kövesse ezeket az utasításokat](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) az Azure-beli virtuális gépek új régióba való áthelyezéshez.
2. Az erőforrások mozgatása után szükség szerint újra kell alkalmazni a karbantartási konfigurációkat az új régióban található erőforrásokon attól függően, hogy áthelyezte-e a karbantartási konfigurációkat. Karbantartási konfigurációt a [PowerShell](../virtual-machines/maintenance-control-powershell.md) vagy a parancssori felület használatával alkalmazhat egy [erőforrásra.](../virtual-machines/maintenance-control-cli.md)


## <a name="verify-the-move"></a>Az áthelyezés ellenőrzése

Ellenőrizze az új régióban található erőforrásokat, és ellenőrizze az új régióban található erőforrások társított konfigurációit. 

## <a name="clean-up-source-resources"></a>Forráserőforrások megtisztítása

Az áthelyezés után fontolja meg az áthelyezott erőforrások törlését a forrás régióban.


## <a name="next-steps"></a>Következő lépések

Ha [karbantartási konfigurációkat](move-region-maintenance-configuration.md) kell áthelyezni, kövesse ezeket az utasításokat. 
