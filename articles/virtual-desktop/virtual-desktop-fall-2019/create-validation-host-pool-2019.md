---
title: Windows rendszerű virtuális asztali (klasszikus) gazdagép-készlet szolgáltatás frissítései – Azure
description: Megtudhatja, hogyan hozhat létre egy érvényesítési gazdagépet a Windows Virtual Desktopban (klasszikus) a frissítések éles környezetben való kihelyezése előtt.
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 291f1e8b8870257c233dc32894ff49b26c0a3501
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323529"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates-in-windows-virtual-desktop-classic"></a>Oktatóanyag: a szolgáltatás frissítéseinek ellenőrzéséhez szükséges gazdagép létrehozása a Windows Virtual Desktopban (klasszikus)

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat. Ha Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../create-validation-host-pool.md).

A gazdagép-készletek egy vagy több azonos virtuális gép gyűjteményei a Windows rendszerű virtuális asztali bérlői környezetekben. Javasoljuk, hogy hozzon létre egy érvényesítési gazdagépet, amelyben a Service Updates alkalmazás először alkalmazza a szolgáltatást. Ez lehetővé teszi a szolgáltatások frissítéseinek figyelését, mielőtt a szolgáltatás alkalmazza azokat a standard vagy nem érvényesítési környezetbe. Érvényesítési címkészlet nélkül előfordulhat, hogy nem deríti fel azokat a módosításokat, amelyek hibákat vezetnek be, ami az éles környezetben lévő felhasználók számára állásidőt eredményezhet.

Annak biztosítása érdekében, hogy az alkalmazások a legújabb frissítésekkel működjenek, az érvényesítési gazdagépnek ugyanúgy kell lennie, mint amennyire csak lehetséges, a nem érvényesítési környezetben lévő gazdagépekhez hasonlóan. A felhasználóknak a szokásos gazdagép-készlethez hasonlóan gyakran kell csatlakozniuk az ellenőrzési gazdagéphez. Ha automatizált tesztelést végez a gazdagép-készleten, az automatikus tesztelést is tartalmaznia kell az ellenőrzési gazdagépen.

Az érvényesítési gazdagépen lévő hibák hibakereséséhez használhatja a [diagnosztikai szolgáltatást](diagnostics-role-service-2019.md) vagy a [Windows virtuális asztali hibaelhárítási cikkeit](troubleshoot-set-up-overview-2019.md).

>[!NOTE]
> Javasoljuk, hogy az összes jövőbeli frissítés teszteléséhez hagyja meg az ellenőrző gazdagép készletét.

Mielőtt elkezdené, [töltse le és importálja a Windows rendszerű virtuális asztali PowerShell-modult](/powershell/windows-virtual-desktop/overview/), ha még nem tette meg. Ezután futtassa a következő parancsmagot a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>A gazda készlet létrehozása

A következő cikkek utasításait követve hozhat létre egy gazdagépet:
- [Oktatóanyag: állomáslista létrehozása az Azure Marketplace-szel](create-host-pools-azure-marketplace-2019.md)
- [Gazdagépcsoport létrehozása Azure Resource Manager-sablonnal](create-host-pools-arm-template.md)
- [Gazdagépcsoport létrehozása a PowerShell-lel](create-host-pools-powershell-2019.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>A gazdagép-készlet definiálása érvényesítési gazdagépként

Futtassa az alábbi PowerShell-parancsmagokat az új címkészlet érvényesítési gazdagépként való definiálásához. Cserélje le az idézőjelek értékeit a munkamenetéhez tartozó értékekre:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

A következő PowerShell-parancsmag futtatásával ellenőrizze, hogy az érvényesítési tulajdonság be van-e állítva. Cserélje le az idézőjelek értékeit a munkamenetéhez tartozó értékekre.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

A parancsmag eredményeinek ehhez a kimenethez hasonlóan kell kinéznie:

```
    TenantName          : contoso
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Frissítési ütemterv

A szolgáltatás frissítései havonta történnek. Jelentős problémák esetén a kritikus frissítések gyakoribb ütemben lesznek elérhetők.

## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozott egy érvényesítési gazdagépet, megtudhatja, hogyan használhatja a Azure Service Health a Windows rendszerű virtuális asztali környezet figyelésére.

> [!div class="nextstepaction"]
> [Szolgáltatásriasztások beállítása](set-up-service-alerts-2019.md)
