---
title: Alkalmazás klónozása a PowerShell használatával
description: Megtudhatja, hogyan klónozható a App Service egy új alkalmazásba a PowerShell használatával. Számos klónozási forgatókönyvet lefednek, beleértve a Traffic Manager is.
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 63ab20b16ae41aa48822f1b5c8e733c93d97f581
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833183"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Azure App Service alkalmazások klónozása a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A Microsoft Azure PowerShell 1.1.0-s verziójával egy új lehetőséggel bővült, amely lehetővé teszi egy meglévő App Service-alkalmazás klónozását egy másik régióban vagy régióban újonnan létrehozott `New-AzWebApp` alkalmazásba. Ezzel a lehetőséggel az ügyfelek gyorsan és egyszerűen helyezhetnek üzembe több alkalmazást különböző régiókban.

Az alkalmazás klónozása a Standard, a Prémium, a Prémium V2 és az Izolált App Service-csomagok esetén támogatott. Az új funkció ugyanazokra a korlátozásokra vonatkozik, mint App Service Backup szolgáltatás, lásd: Alkalmazások biztonsági mentése [a Azure App Service.](manage-backup.md)

## <a name="cloning-an-existing-app"></a>Meglévő alkalmazás klónozása
Forgatókönyv: Egy meglévő alkalmazás az USA déli középső régiójában, és a tartalmát egy új alkalmazásba szeretné klónozni az USA északi középső régiójában. Ez a PowerShell-parancsmag Azure Resource Manager használatával valósítható meg egy új alkalmazás létrehozásához a `-SourceWebApp` beállítással.

Ha ismeri a forrásalkalmazást tartalmazó erőforráscsoport-nevet, a következő PowerShell-paranccsal lekérte a forrásalkalmazás adatait (ebben az esetben a `source-webapp` neve):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Új csomag létrehozásához App Service parancsot használhatja, `New-AzAppServicePlan` ahogyan az alábbi példában is

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

Az paranccsal létrehozhatja az új alkalmazást az USA északi középső régiójában, és összekapcsolhatja egy meglévő App Service `New-AzWebApp` tervvel. Emellett használhatja ugyanazt az erőforráscsoportot, mint a forrásalkalmazás, vagy definiálhat egy új erőforráscsoportot az alábbi parancsban látható módon:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Egy meglévő alkalmazás klónozáshoz, beleértve az összes társított üzembe helyezési üzembehelyhelyet is, a paramétert kell `IncludeSourceWebAppSlots` használnia.  Vegye figyelembe, hogy a paraméter csak teljes alkalmazás klónozása esetén támogatott, beleértve `IncludeSourceWebAppSlots` az összes tárolóhelyet is. A következő PowerShell-parancs bemutatja, hogyan használható ez a paraméter az `New-AzWebApp` paranccsal:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Egy azonos régióban lévő meglévő alkalmazás klónozásához létre kell hoznia egy új erőforráscsoportot és egy új App Service-tervet ugyanabban a régióban, majd a következő PowerShell-paranccsal klónoznia kell az alkalmazást:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Meglévő alkalmazás klónozása egy App Service Environment
Forgatókönyv: Egy meglévő alkalmazás az USA déli középső régiójában, és a tartalmat egy új alkalmazásba szeretné klónozni egy meglévő App Service Environment (ASE) használatával.

A forrásalkalmazást tartalmazó erőforráscsoport nevének ismerete után a következő PowerShell-paranccsal lekérte a forrásalkalmazás adatait (ebben az esetben a neve `source-webapp` ):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Ha ismeri az ASE nevét és az erőforráscsoport nevét, amelyhez az ASE tartozik, létrehozhatja az új alkalmazást a meglévő ASE-ban az alábbi parancsban látható módon:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

A paraméterre örökölt okból van szükség, de a rendszer figyelmen kívül hagyja, amikor `Location` ASE-beli alkalmazást hoz létre. 

## <a name="cloning-an-existing-app-slot"></a>Meglévő alkalmazáshely klónozása
Forgatókönyv: Egy alkalmazás meglévő üzembe helyezési tárolóhelyét szeretné klónozni egy új vagy egy új üzembehelyre. Az új alkalmazás lehet ugyanabban a régióban, mint az eredeti alkalmazáshely, vagy egy másik régióban.

A forrásalkalmazást tartalmazó erőforráscsoport nevének ismerete után a következő PowerShell-paranccsal lekérte a forrásalkalmazás pontinformációját (ebben az esetben a neve `source-appslot` : `source-app` ).

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

A következő parancs bemutatja, hogyan lehet létrehozni a forrásalkalmazás klónozását egy új alkalmazásba:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Az Traffic Manager konfigurálása alkalmazás klónozása közben
A többrépontos alkalmazások létrehozása és Azure Traffic Manager konfigurálása, hogy az összes alkalmazáshoz irányítsa a forgalmat, fontos forgatókönyv annak biztosításához, hogy az ügyfelek alkalmazásai magas rendelkezésre állásban állnak. Meglévő alkalmazás klónozása esetén mindkét alkalmazást csatlakoztathatja egy új Traffic Manager-profilhoz vagy egy meglévőhöz. A Azure Resource Manager csak Traffic Manager verziója támogatott.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Új Traffic Manager létrehozása alkalmazás klónozása közben
Forgatókönyv: Ha egy alkalmazást egy másik régióba szeretne klónozni, miközben konfigurál egy Azure Resource Manager mindkét alkalmazást tartalmazó Traffic Manager-profilt. A következő parancs bemutatja, hogyan lehet létrehozni a forrásalkalmazás klónját egy új alkalmazásba egy új Traffic Manager konfigurálása során:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Új klónozott alkalmazás hozzáadása egy meglévő Traffic Manager profilhoz
Forgatókönyv: Már rendelkezik egy Azure Resource Manager Traffic Manager-profillal, és mindkét alkalmazást végpontként szeretné hozzáadni. Először össze kell állítania a meglévő Traffic Manager-profilazonosítót. Szüksége lesz az előfizetés-azonosítóra, az erőforráscsoport nevére és a meglévő Traffic Manager-profil nevére.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

A Traffic Manger-azonosítót követően a következő parancs bemutatja, hogyan lehet klónozni a forrásalkalmazást egy új alkalmazásba, miközben hozzáadja őket egy meglévő Traffic Manager profilhoz:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```
> [!NOTE]
> Ha "A Traffic Manager-gazdagépnév SSL-ellenőrzése sikertelen" hibaüzenetet kap, javasoljuk, hogy használja az -IgnoreCustomHostNames attribútumot a PowerShell-parancsmagban a klónozási művelet végrehajtásakor, vagy használja a portált.

## <a name="current-restrictions"></a>Aktuális korlátozások
Az alkalmazás klónozásának ismert korlátozásai a következőek:

* Az automatikus méretezési beállítások klónozása nem történt meg
* A biztonsági mentés ütemezési beállításai nincsenek klónozva
* A virtuális hálózat beállításai nincsenek klónozva
* Az App Insights nem lesz automatikusan beállítva a célalkalmazásban
* Az Easy Auth-beállítások nincsenek klónozva
* A Kudu-bővítmény nincs klónozva
* A TiP-szabályok nincsenek klónozva
* Az adatbázis tartalma nincs klónozva
* A kimenő IP-címek megváltoznak, ha egy másik skálázású egységbe klónoznak
* Linux-alkalmazásokhoz nem érhető el
* A felügyelt identitások nincsenek klónozva

### <a name="references"></a>Hivatkozások
* [App Service klónozás](app-service-web-app-cloning.md)
* [Alkalmazás biztonsági Azure App Service](manage-backup.md)
* [Azure Resource Manager előzetes verzió Azure Traffic Manager támogatása](../traffic-manager/traffic-manager-powershell-arm.md)
* [Az App Service Environment bemutatása](environment/intro.md)
* [Az Azure PowerShell használata az Azure Resource Managerrel](../azure-resource-manager/management/manage-resources-powershell.md)

