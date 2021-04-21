---
title: Kötet szolgáltatási szintjének dinamikus módosítása a Azure NetApp Files | Microsoft Docs
description: Leírja, hogyan lehet dinamikusan módosítani egy kötet szolgáltatási szintjét.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/14/2021
ms.author: b-juche
ms.openlocfilehash: 3409387adb1e722d8368907d731e02983dd287fc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830159"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Kötetek szolgáltatásszintjének dinamikus módosítása

> [!IMPORTANT] 
> A replikációs célkötet szolgáltatási szintjének dinamikus módosítása jelenleg nem támogatott.

Egy meglévő kötet szolgáltatási szintjét úgy módosíthatja, hogy a kötetet a kötethez kívánt szolgáltatási szintet használó másik kapacitáskészletbe mozgatja. [](azure-netapp-files-service-levels.md) A kötet szolgáltatásszint-módosítása nem igényli az adatok áttelepítését. A kötethez való hozzáférést sem befolyásolja.  

Ezzel a funkcióval igény szerint megfelelhet a számítási feladatok igényeinek.  Módosíthatja a meglévő köteteket, hogy magasabb szolgáltatási szintet használjanak a jobb teljesítmény érdekében, vagy alacsonyabb szolgáltatási szintet használjanak a költségoptimalizáláshoz. Ha például a kötet jelenleg egy Standard szolgáltatási  szintet használó kapacitáskészletben található, és azt szeretné, hogy a kötet a *Prémium* szolgáltatási szintet használja, dinamikusan áthelyezheti a kötetet egy, a *Prémium* szolgáltatási szintet használó kapacitáskészletbe.  

A kapacitáskészletnek, amelybe a kötetet át szeretné áthelyezni, már léteznie kell. A kapacitáskészlet más köteteket is tartalmazhat.  Ha egy teljesen új kapacitáskészletbe szeretné áthelyezni a [](azure-netapp-files-set-up-capacity-pool.md) kötetet, a kötet áthelyezése előtt létre kell hoznia a kapacitáskészletet.  

## <a name="considerations"></a>Megfontolandó szempontok

* Miután a kötetet áthelyezte egy másik kapacitáskészletbe, többé nem lesz hozzáférése az előző kötettevékenység-naplókhoz és kötetmetrikákhoz. A kötet az új kapacitáskészletben található új tevékenységnaplókból és metrikákból indul ki.

* Ha egy kötetet magasabb szolgáltatási szinthez (például Standard vagy  *Prémium* vagy *Ultra* szolgáltatási szinthez) áthelyez egy kapacitáskészletbe,  akkor legalább hét napot várnia kell, mielőtt ismét  áthelyezheti a kötetet egy alacsonyabb szolgáltatási szintű kapacitáskészletbe (például az *Ultra* szintről Prémiumra vagy *Standardra).*  

## <a name="register-the-feature"></a>A funkció regisztrálása

A kötetek másik kapacitáskészletbe való áthelyezésének funkciója jelenleg előzetes verzióban érhető el. Ha először használja ezt a funkciót, első lépésként regisztrálnia kell a funkciót.

1. A funkció regisztrálása: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. Ellenőrizze a szolgáltatásregisztráció állapotát: 

    > [!NOTE]
    > A **RegistrationState** állapotra váltása akár `Registering` 60 percig is `Registered` lehet. A folytatás előtt várja meg, amíg **az állapot Regisztrálva** lesz.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
Használhatja az Azure CLI és [parancsát](/cli/azure/feature) is a funkció regisztrálására és a regisztráció `az feature register` `az feature show` állapotának megjelenítésére. 
 
## <a name="move-a-volume-to-another-capacity-pool"></a>Kötet áthelyezése másik kapacitáskészletbe

1.  A Kötetek lapon kattintson a jobb gombbal arra a kötetre, amelynek szolgáltatási szintjét módosítani szeretné. Válassza **a Készlet módosítása lehetőséget.**

    ![Kattintson a jobb gombbal a kötetre](../media/azure-netapp-files/right-click-volume.png)

2. A Készlet módosítása ablakban válassza ki azt a kapacitáskészletet, amelybe a kötetet át szeretné áthelyezni. 

    ![Készlet módosítása](../media/azure-netapp-files/change-pool.png)

3.  Kattintson az **OK** gombra.


## <a name="next-steps"></a>Következő lépések  

* [Az Azure NetApp Files szolgáltatásszintjei](azure-netapp-files-service-levels.md)
* [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
* [Kötet kapacitáskészletének módosításával kapcsolatos problémák elhárítása](troubleshoot-capacity-pools.md#issues-when-changing-the-capacity-pool-of-a-volume)
