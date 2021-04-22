---
title: Karbantartási konfiguráció áthelyezése másik Azure-régióba
description: Megtudhatja, hogyan helyezze át a virtuális gép karbantartási konfigurációját egy másik Azure-régióba
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: f833f3eb9e3d94da6178a0a9a9cf4f95ec0682e7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865368"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Karbantartásvezérlési konfiguráció áthelyezése másik régióba

Kövesse ezt a cikket a karbantartásvezérlési konfiguráció másik Azure-régióba való áthelyezéshez. Több okból is érdemes lehet áthelyezni egy konfigurációt. Például egy új régió előnyeinek kihasználásához, egy adott régióban elérhető funkciók vagy szolgáltatások üzembe helyezéséhez, a belső szabályzati és irányítási követelmények követelményeknek való megfeleléshez vagy a kapacitástervezésre válaszul.

[A testreszabott](maintenance-control.md)karbantartási konfigurációkkal a karbantartásvezérlés lehetővé teszi annak szabályozását, hogy a rendszer hogyan alkalmazza a platformfrissítéseket a virtuális gépekre és az Azure-beli dedikált gazdagépekre. A karbantartási vezérlés régiók közötti áthelyezésének több forgatókönyve is van:

- A karbantartásvezérlési konfiguráció áthelyezéshez kövesse az ebben a cikkben található utasításokat, de a konfigurációhoz társított erőforrásokat ne.
- Ha a karbantartási konfigurációhoz társított erőforrásokat át kell áthelyezni, magát a konfigurációt azonban nem, kövesse az [alábbi utasításokat.](move-region-maintenance-configuration-resources.md)
- A karbantartási konfiguráció és a hozzá tartozó erőforrások áthelyezéshez először kövesse az ebben a cikkben található utasításokat. Ezután kövesse ezeket [az utasításokat.](move-region-maintenance-configuration-resources.md)

## <a name="prerequisites"></a>Előfeltételek

A karbantartásvezérlési konfiguráció áthelyezésének megkezdése előtt:

- A karbantartási konfigurációk Azure-beli virtuális gépekhez vagy azure-beli dedikált gazdagéphez vannak társítva. Mielőtt hozzákezd, győződjön meg arról, hogy a virtuális gép/gazdagép erőforrásai léteznek az új régióban.
- Azonosítani: 
    - Meglévő karbantartásvezérlési konfigurációk.
    - Az erőforráscsoportok, amelyekben jelenleg a meglévő konfigurációk találhatók. 
    - Azok az erőforráscsoportok, amelyekhez a konfigurációk hozzá lesznek adva az új régióba való áthelyezés után. 
    - Az áthelyezni kívánt karbantartási konfigurációhoz társított erőforrások.
    - Ellenőrizze, hogy az új régióban lévő erőforrások azonosak-e az aktuális karbantartási konfigurációkhoz társított erőforrásokkal. A konfigurációk neve lehet ugyanaz az új régióban, mint a régiben, de ez nem kötelező.

## <a name="prepare-and-move"></a>Előkészítés és áthelyezés 

1. Az egyes előfizetések összes karbantartási konfigurációjának lekérése. Ehhez futtassa a CLI [az maintenance configuration list](/cli/azure/maintenance/configuration#az_maintenance_configuration_list) parancsot, és cserélje le a $subId az előfizetés azonosítójára.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Tekintse át az előfizetésben található konfigurációs rekordok visszaadott táblalistát. Íme egy példa. A lista az adott környezet értékeit fogja tartalmazni.

    **Név** | **Hely** | **Erőforráscsoport**
    --- | --- | ---
    Karbantartás kihagyása | eastus2 | configuration-resource-group
    IgniteDemoConfig | eastus2 | configuration-resource-group
    defaultMaintenanceConfiguration-eastus | eastus | tesztkonfiguráció
    

3. Mentse a listát referenciaként. A konfigurációk áthelyezése során segít ellenőrizni, hogy minden át lett-e helyezték.
4. Referenciaként minden konfigurációt/erőforráscsoportot az új régióban található új erőforráscsoporthoz kell leképezni.
5. Hozzon létre új karbantartási konfigurációkat az új régióban a [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)vagy a [parancssori felület használatával.](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)
6. Társítsa a konfigurációkat az új régióban található erőforrásokhoz a [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)vagy a [parancssori felület használatával.](../virtual-machines/maintenance-control-cli.md#assign-the-configuration)


## <a name="verify-the-move"></a>Az áthelyezés ellenőrzése

A konfigurációk áthelyezését követően hasonlítsa össze az új régióban található konfigurációkat és erőforrásokat az előkészített táblalistával.


## <a name="clean-up-source-resources"></a>Forráserőforrások tisztítása

Az áthelyezés után érdemes lehet törölni az áthelyezve karbantartási konfigurációkat a forrás-régióban, a [PowerShellben](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)vagy a [parancssori felületben.](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration)


## <a name="next-steps"></a>Következő lépések

Kövesse [ezeket az utasításokat,](move-region-maintenance-configuration-resources.md) ha a karbantartási konfigurációkhoz társított erőforrásokat kell áthelyezni. 
