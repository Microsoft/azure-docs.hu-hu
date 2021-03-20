---
title: Karbantartási konfiguráció áthelyezése másik Azure-régióba
description: Megtudhatja, hogyan helyezhet át egy virtuális gép karbantartási konfigurációját egy másik Azure-régióba
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 91a6adecc9cf0db56fa4c433f388b05aa1bdef6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98202912"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Karbantartási vezérlőelemek konfigurációjának áthelyezése másik régióba

Ezt a cikket követve áthelyezheti a karbantartási vezérlők konfigurációját egy másik Azure-régióba. Előfordulhat, hogy több okból is át kívánja helyezni a konfigurációt. Ha például egy új régió előnyeit szeretné kihasználni, egy adott régióban elérhető szolgáltatásokat vagy szolgáltatásokat kíván üzembe helyezni, a belső házirend-és irányítási követelmények teljesítésére, vagy a kapacitás megtervezésére reagálva.

A [karbantartási szabályozás](maintenance-control.md), amely testreszabott karbantartási konfigurációkkal rendelkezik, lehetővé teszi a platformok frissítéseinek a virtuális gépekre és az Azure dedikált gazdagépekre való alkalmazásának szabályozását. A karbantartási ellenőrzés több régióban is áthelyezhető:

- Ha át szeretné helyezni a karbantartási vezérlő konfigurációját, de nem a konfigurációhoz társított erőforrásokat, kövesse a cikk utasításait.
- Ha a karbantartási konfigurációhoz társított erőforrásokat szeretné áthelyezni, de nem maga a konfiguráció, kövesse az [alábbi utasításokat](move-region-maintenance-configuration-resources.md).
- A karbantartási konfiguráció és a hozzá társított erőforrások áthelyezéséhez először kövesse a cikkben található utasításokat. Ezután kövesse [az alábbi utasításokat](move-region-maintenance-configuration-resources.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt megkezdené a karbantartási vezérlők konfigurációjának áthelyezését:

- A karbantartási konfigurációk az Azure-beli virtuális gépekhez vagy az Azure dedikált gazdagépekhez vannak társítva. Mielőtt elkezdené, győződjön meg arról, hogy a virtuális gép/gazdagép erőforrásai léteznek az új régióban.
- Azonosítani 
    - Meglévő karbantartási vezérlési konfigurációk.
    - Azon erőforráscsoportok, amelyekben jelenleg a meglévő konfigurációk találhatók. 
    - Azok az erőforráscsoportok, amelyekhez a konfigurációk hozzá lesznek adva az új régióba való áthelyezés után. 
    - Az áthelyezni kívánt karbantartási konfigurációhoz társított erőforrások.
    - Győződjön meg arról, hogy az új régióban lévő erőforrások megegyeznek az aktuális karbantartási konfigurációkhoz társított erőforrásokkal. A konfigurációk ugyanazokat a neveket használhatják az új régióban, mint a régiben, de ez nem kötelező.

## <a name="prepare-and-move"></a>Előkészítés és áthelyezés 

1. Az egyes előfizetésekben lévő összes karbantartási konfiguráció beolvasása. Ehhez futtassa a CLI az [karbantartási konfigurációs lista](/cli/azure/ext/maintenance/maintenance/configuration#ext-maintenance-az-maintenance-configuration-list) parancsát, és cserélje le a $subIdt az előfizetés-azonosítójával.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Tekintse át az előfizetésen belüli konfigurációs rekordok visszaadott táblázatának listáját. Íme egy példa. A lista az adott környezet értékeit fogja tartalmazni.

    **Név** | **Hely** | **Erőforráscsoport**
    --- | --- | ---
    Karbantartás kihagyása | eastus2 | konfiguráció – erőforrás-csoport
    IgniteDemoConfig | eastus2 | konfiguráció – erőforrás-csoport
    defaultMaintenanceConfiguration – eastus | eastus | tesztelés – konfiguráció
    

3. Mentse a listát a hivatkozáshoz. A konfigurációk áthelyezésekor a segítségével ellenőrizheti, hogy minden áthelyezték-e.
4. Hivatkozásként az egyes konfigurációkat/erőforráscsoportokat az új régióban lévő új erőforráscsoporthoz rendelje.
5. Hozzon létre új karbantartási konfigurációkat az új régióban a [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)vagy a [parancssori](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)felület használatával.
6. Társítsa a konfigurációkat az új régió erőforrásaihoz a [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)vagy a [CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration)használatával.


## <a name="verify-the-move"></a>Az áthelyezés ellenőrzése

A konfigurációk áthelyezése után hasonlítsa össze az új régióban lévő konfigurációkat és erőforrásokat az előkészített táblázatos listával.


## <a name="clean-up-source-resources"></a>Forrás erőforrásainak törlése

Az áthelyezést követően érdemes törölni az áthelyezett karbantartási konfigurációkat a forrás régióban, a [PowerShellben](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)vagy a [parancssori](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration)felületen.


## <a name="next-steps"></a>Következő lépések

Kövesse [ezeket az utasításokat](move-region-maintenance-configuration-resources.md) , ha a karbantartási konfigurációkhoz társított erőforrásokat kell áthelyeznie. 
