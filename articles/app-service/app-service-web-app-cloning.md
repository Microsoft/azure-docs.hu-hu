---
title: Alkalmazás klónozása a PowerShell használatával
description: Megtudhatja, hogyan klónozott App Service alkalmazást egy új alkalmazásba a PowerShell használatával. Számos klónozási forgatókönyvet érintenek, beleértve a Traffic Manager integrációt is.
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.custom: seodec18
ms.openlocfilehash: e3ae342e7cbd8a9c2e126de7666d07f0664be407
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573642"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Alkalmazások klónozásának Azure App Service a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A Microsoft Azure PowerShell 1.1.0-es verziójának megjelenése után új beállítás lett hozzáadva, `New-AzWebApp` amely lehetővé teszi egy meglévő app Service alkalmazás klónozását egy másik régióban vagy ugyanabban a régióban található újonnan létrehozott alkalmazásba. Ez a beállítás lehetővé teszi, hogy az ügyfelek gyorsan és egyszerűen helyezzen üzembe számos alkalmazást különböző régiókban.

Az alkalmazások klónozása a standard, a Premium, a Premium v2 és az elkülönített app Service-csomagok esetében támogatott. Az új szolgáltatás ugyanazokat a korlátozásokat használja, mint a App Service Backup szolgáltatás, lásd: [alkalmazás biztonsági mentése Azure app Serviceban](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Meglévő alkalmazás klónozása
Forgatókönyv: egy meglévő alkalmazás az USA déli középső régiójában, és szeretné klónozott tartalmat egy új alkalmazásba venni az USA északi középső régiójában. Ez a PowerShell-parancsmag Azure Resource Manager-verziójának használatával végezhető el egy új alkalmazás létrehozásához a `-SourceWebApp` kapcsolóval.

A forrásoldali alkalmazást tartalmazó erőforráscsoport nevének ismeretében a következő PowerShell-paranccsal kérheti le a forrásoldali alkalmazás adatait (ebben az esetben a neve `source-webapp` ):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Új App Service terv létrehozásához `New-AzAppServicePlan` az alábbi példában szereplő parancsot használhatja.

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

A `New-AzWebApp` parancs használatával létrehozhatja az új alkalmazást az USA északi középső régiójában, és összekapcsolhatja azt egy meglévő app Service-csomaggal. Emellett használhatja ugyanazt az erőforráscsoportot, mint a forrásoldali alkalmazás, vagy definiálhat egy új erőforráscsoportot az alábbi parancsban látható módon:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Meglévő alkalmazás klónozásához, beleértve az összes kapcsolódó telepítési tárolóhelyet, a paramétert kell használnia `IncludeSourceWebAppSlots` .  Vegye figyelembe, hogy a `IncludeSourceWebAppSlots` paraméter csak a teljes alkalmazás klónozására támogatott, beleértve az összes tárolóhelyét is. A következő PowerShell-parancs a paraméter használatát mutatja be a `New-AzWebApp` paranccsal:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Egy meglévő alkalmazás ugyanazon a régión belüli klónozásához létre kell hoznia egy új erőforráscsoportot és egy új App Service-csomagot ugyanabban a régióban, majd a következő PowerShell-paranccsal kell megadnia az alkalmazás klónozását:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Meglévő alkalmazás klónozása egy App Service Environmentba
Forgatókönyv: egy meglévő alkalmazás az USA déli középső régiójában, és szeretné klónozott tartalmat egy új alkalmazásba egy meglévő App Service Environment (betanító).

A forrásoldali alkalmazást tartalmazó erőforráscsoport nevének ismeretében a következő PowerShell-paranccsal kérheti le a forrásoldali alkalmazás adatait (ebben az esetben a neve `source-webapp` ):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Annak ismerete, hogy a kiegészítő csomag neve és az erőforráscsoport neve, amelyhez a betekintő tartozik, létrehozhatja az új alkalmazást a meglévő bevezetésben, ahogy az a következő parancsban látható:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

A `Location` paramétert örökölt okok miatt kell megadni, de a rendszer figyelmen kívül hagyja, ha az alkalmazást egy központilag hozza létre. 

## <a name="cloning-an-existing-app-slot"></a>Meglévő alkalmazás-tárolóhely klónozása
Forgatókönyv: egy alkalmazás meglévő üzembe helyezési pontját szeretné klónozott új alkalmazásra vagy új tárolóhelyre. Az új alkalmazás ugyanabban a régióban lehet, mint az eredeti alkalmazás vagy egy másik régió.

A forrásoldali alkalmazást tartalmazó erőforráscsoport nevének ismeretében a következő PowerShell-paranccsal kérheti le a forrásoldali alkalmazás tárolóhelyének adatait (ebben az esetben a neve) a következőhöz `source-appslot` kötve `source-app` :

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

A következő parancs bemutatja, hogyan hozhat létre egy klónt a forrásprogram egy új alkalmazásba:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Traffic Manager konfigurálása az alkalmazások klónozása során
A többrégiós alkalmazások létrehozása és az Azure Traffic Manager konfigurálása az összes alkalmazás forgalmának irányításához fontos forgatókönyv annak biztosítása érdekében, hogy az ügyfelek alkalmazásai nagy rendelkezésre álljanak. Egy meglévő alkalmazás klónozásakor lehetősége van arra, hogy mindkét alkalmazást egy új Traffic Manager-profilhoz vagy egy meglévőhöz kapcsolódjon. Csak a Traffic Manager Azure Resource Manager verziója támogatott.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Új Traffic Manager-profil létrehozása az alkalmazások klónozása során
Forgatókönyv: egy alkalmazás egy másik régióba való klónozására van szükség, miközben Azure Resource Manager Traffic Manager-profilt konfigurál, amely mindkét alkalmazást magában foglalja. Az alábbi parancs bemutatja, hogyan hozható létre a forrás-alkalmazás egy új alkalmazás egy új Traffic Manager profil konfigurálásakor:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Új klónozott alkalmazás hozzáadása meglévő Traffic Manager profilhoz
Forgatókönyv: már rendelkezik Azure Resource Manager Traffic Manager-profillal, és mindkét alkalmazást végpontként szeretné hozzáadni. Ehhez először össze kell állítania a Traffic Manager-profil meglévő AZONOSÍTÓját. Szüksége lesz az előfizetés-AZONOSÍTÓra, az erőforráscsoport nevére és a meglévő Traffic Manager-profil nevére.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

A Traffic Manger AZONOSÍTÓjának megadását követően a következő parancs bemutatja, hogyan hozhat létre egy klónt a forrásprogram egy új alkalmazásba, amikor hozzáadja őket egy meglévő Traffic Manager profilhoz:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```
> [!NOTE]
> Ha a következő hibaüzenetet kapja: "a Traffic Manager-állomásnév SSL-ellenőrzése sikertelen", javasoljuk, hogy használja a-IgnoreCustomHostNames attribútumot a PowerShell-parancsmagban a klónozási művelet végrehajtása közben, vagy használja a portált.

## <a name="current-restrictions"></a>Jelenlegi korlátozások
Az alkalmazások klónozásának ismert korlátozásai:

* Az automatikus skálázási beállítások klónozása nem történik meg
* A biztonsági mentési ütemterv beállításainak klónozása nem történik meg
* A VNET-beállítások klónozása nem történik meg
* Az alkalmazás-felismerések nem lesznek automatikusan beállítva a célként megadott alkalmazásban
* Az egyszerű hitelesítési beállítások klónozása nem történik meg
* A kudu-bővítmény klónozása nem történik meg
* A TiP-szabályok klónozása nem történik meg
* Az adatbázis tartalmának klónozása nem történik meg
* A kimenő IP-címek akkor változnak, ha egy másik méretezési egység klónozása
* Linux-alkalmazásokhoz nem érhető el
* A felügyelt identitások nincsenek klónozott

### <a name="references"></a>Hivatkozások
* [App Service klónozás](app-service-web-app-cloning.md)
* [Alkalmazás biztonsági mentése Azure App Service](manage-backup.md)
* [Az Azure Traffic Manager előzetes verziójának Azure Resource Manager támogatása](../traffic-manager/traffic-manager-powershell-arm.md)
* [Az App Service Environment bemutatása](environment/intro.md)
* [Az Azure PowerShell használata az Azure Resource Managerrel](../azure-resource-manager/management/manage-resources-powershell.md)

