---
title: Azure Cloud Service (klasszikus) méretezése a Windows PowerShellben | Microsoft Docs
description: klasszikus Ismerje meg, hogyan méretezheti a PowerShellt egy webes szerepkör vagy feldolgozói szerepkör az Azure-ban való méretezéséhez.
ms.topic: article
ms.service: cloud-services
ms.subservice: autoscale
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0b0a9cc8032cfd849f053e2d45e05f85e4216a23
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936614"
---
# <a name="how-to-scale-an-azure-cloud-service-classic-in-powershell"></a>Azure Cloud Service (klasszikus) méretezése a PowerShellben

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

A Windows PowerShell használatával a-példányok hozzáadásával vagy eltávolításával méretezheti a webes szerepköröket vagy feldolgozói szerepköröket.  

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Mielőtt műveleteket hajt végre az előfizetésen a PowerShellen keresztül, be kell jelentkeznie:

```powershell
Add-AzureAccount
```

Ha több előfizetése van társítva a fiókjához, előfordulhat, hogy módosítania kell a jelenlegi előfizetést attól függően, hogy hol található a felhőalapú szolgáltatás. Az aktuális előfizetés a következő futtatásával ellenőrizhető:

```powershell
Get-AzureSubscription -Current
```

Ha módosítania kell a jelenlegi előfizetést, futtassa a következőt:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>A szerepkör aktuális példányszámának keresése

A szerepkör aktuális állapotának vizsgálatához futtassa a következőt:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Vissza kell olvasnia a szerepkörre vonatkozó információkat, beleértve az operációs rendszer aktuális verzióját és a példányszámot. Ebben az esetben a szerepkör egyetlen példánnyal rendelkezik.

![A szerepkörre vonatkozó információk](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>A szerepkör felskálázása további példányok hozzáadásával

A szerepkör felskálázásához adja meg a példányok kívánt számát a **set-AzureRole** parancsmagnak a **Count** paraméterként:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

A parancsmag egy pillanatra blokkolja az új példányok üzembe helyezését és elindítását. Ebben az időszakban, ha megnyit egy új PowerShell-ablakot, és a korábban bemutatott módon hívja meg a **Get-AzureRole-** t, akkor megjelenik az új célként megadott példányok száma. Ha megvizsgálja a szerepkör állapotát a portálon, akkor az új példány indul:

![Virtuálisgép-példány indítása a portálon](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Az új példányok elindítása után a parancsmag sikeresen visszatér:

![A szerepkör-példány nagyobb sikert mutat](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Méretezés a szerepkörben a példányok eltávolításával

A szerepköröket úgy méretezheti, hogy a példányokat ugyanúgy távolítja el. Állítsa be a **Count** paramétert a **set-AzureRole** értékre azon példányok számának megadásához, amelyeket a művelet befejezését követően szeretne használni.

## <a name="next-steps"></a>Következő lépések

Nem lehet konfigurálni a Cloud Services automatikus méretezését a PowerShellből. Ehhez tekintse meg [a felhőalapú szolgáltatás automatikus méretezését](cloud-services-how-to-scale-portal.md)ismertető témakört.
