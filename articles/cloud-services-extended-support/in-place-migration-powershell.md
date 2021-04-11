---
title: Migrálás az Azure Cloud Servicesba (bővített támogatás) a PowerShell használatával
description: Migrálás az Azure Cloud Services (klasszikus) rendszerről az Azure Cloud Servicesra (bővített támogatás) a PowerShell használatával
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: eea49a41e81e7e580becce815ff91aff6aa430d6
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286919"
---
# <a name="migrate-to-azure-cloud-services-extended-support-using-powershell"></a>Migrálás az Azure Cloud Servicesba (bővített támogatás) a PowerShell használatával

Ezek a lépések bemutatják, hogyan használhatók a Azure PowerShell parancsok [Cloud Services (klasszikus)](../cloud-services/cloud-services-choose-me.md) rendszerről [Cloud Servicesra (kiterjesztett támogatás)](overview.md)való áttelepítésre.

> [!IMPORTANT]
> Az áttelepítési eszköz jelenleg nyilvános előzetes verzióban érhető el a Cloud Services (klasszikus) rendszerről a Cloud Servicesra (kiterjesztett támogatás). Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="1-plan-for-migration"></a>1) a Migrálás megtervezése
A sikeres áttelepítéshez a tervezés a legfontosabb lépés. Az áttelepítési lépések megkezdése előtt tekintse át a [Cloud Services (bővített támogatás) áttekintését](overview.md) és a [IaaS-erőforrások klasszikusról Azure Resource Managerra történő áttelepítésének megtervezését](../virtual-machines/migration-classic-resource-manager-plan.md) . 

## <a name="2-install-the-latest-version-of-powershell"></a>2.) telepítse a PowerShell legújabb verzióját
Két fő lehetőség van a Azure PowerShell telepítésére: [PowerShell-Galéria](https://www.powershellgallery.com/profiles/azure-sdk/) vagy [webplatform-telepítő (WebPI)](https://aka.ms/webpi-azps). A WebPI havi frissítéseket fogad. A PowerShell-galéria folyamatosan fogadja a frissítéseket. Ez a cikk a Azure PowerShell 2.1.0 verzióján alapul.

A telepítési utasításokért lásd: [Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0&preserve-view=true).

## <a name="3-ensure-admin-permissions"></a>3.) a rendszergazdai jogosultságok biztosítása
Az áttelepítés elvégzéséhez hozzá kell adnia az előfizetéshez tartozó [Azure Portal](https://portal.azure.com).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A **központi** menüben válassza az **előfizetés** lehetőséget. Ha nem látja, válassza a **minden szolgáltatás** lehetőséget.
3. Keresse meg a megfelelő előfizetési bejegyzést, majd tekintse meg a **saját szerepkör** mezőt. A rendszergazdák számára az értéknek a *fiók* rendszergazdája kell lennie.

Ha nem tud felvenni egy társ-rendszergazda szerepkört, forduljon a szolgáltatás rendszergazdájához vagy a társ-rendszergazdához, és kérje a hozzáadást.

## <a name="4-register-the-classic-provider-and-cloudservice-feature"></a>4. a klasszikus szolgáltató és a felhőszolgáltatás szolgáltatás regisztrálása
Először indítson el egy PowerShell-parancssort. Az áttelepítéshez állítsa be a környezetet a klasszikus és a Resource Managerhez.

Jelentkezzen be a fiókjába a Resource Manager-modellhez.

```powershell
Connect-AzAccount
```

Szerezze be az elérhető előfizetéseket a következő paranccsal:

```powershell
Get-AzSubscription | Sort Name | Select Name
```

Állítsa be az Azure-előfizetését az aktuális munkamenethez. Ez a példa az alapértelmezett előfizetés nevét állítja be az **Azure-előfizetésre**. Cserélje le a példában szereplő előfizetés nevét a saját nevére.

```powershell
Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

Regisztrálja az áttelepítési erőforrás-szolgáltatót a következő parancs használatával:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```
> [!NOTE]
> A regisztráció egyszeri lépés, de csak egyszer kell megtennie az áttelepítés megkísérlése előtt. A regisztráció nélkül a következő hibaüzenet jelenik meg:
>
> *BadRequest: az előfizetés nincs regisztrálva áttelepítésre.*

Regisztrálja az előfizetéshez tartozó CloudServices szolgáltatást. A regisztráció elvégzése több percet is igénybe vehet. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

Várjon öt percet, amíg a regisztráció befejeződik. 

A következő parancs használatával keresse meg a klasszikus szolgáltató jóváhagyásának állapotát:

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

A regisztráció állapotát a következő paranccsal ellenőrizhető:  
```powershell
Get-AzProviderFeature -FeatureName CloudServices
```

`Registered`A folytatás előtt győződjön meg arról, hogy a RegistrationState.

A klasszikus üzemi modellre való áttérés előtt győződjön meg arról, hogy elegendő Azure Resource Manager vCPU kvóta van az aktuális üzembe helyezés vagy a virtuális hálózat Azure-régiójában. A következő PowerShell-paranccsal ellenőrizhető, hogy az aktuálisan hány vCPU Azure Resource Manager. További információ a vCPU-kvótákkal kapcsolatban: [korlátok és a Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

Ez a példa az **USA nyugati** régiójában lévő rendelkezésre állást ellenőrzi. Cserélje le a példában szereplő régió nevét a saját nevére.

```powershell
Get-AzVMUsage -Location "West US"
```

Most jelentkezzen be a fiókjába a klasszikus üzemi modellhez.

```powershell
Add-AzureAccount
```

Szerezze be az elérhető előfizetéseket a következő paranccsal:

```powershell
Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Állítsa be az Azure-előfizetését az aktuális munkamenethez. Ez a példa az alapértelmezett előfizetést állítja be az **Azure-előfizetésre**. Cserélje le a példában szereplő előfizetés nevét a saját nevére.

```powershell
Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="5-migrate-your-cloud-services"></a>5.) telepítse át a Cloud Services 
* [Felhőalapú szolgáltatás migrálása nem virtuális hálózatban](#51-option-1---migrate-a-cloud-service-not-in-a-virtual-network)
* [Felhőalapú szolgáltatás migrálása virtuális hálózatban](#51-option-2---migrate-a-cloud-service-in-a-virtual-network)

> [!NOTE]
> Az itt leírt összes művelet idempotens. Ha a nem támogatott funkció vagy a konfigurációs hiba nem megfelelő, javasoljuk, hogy próbálkozzon újra az előkészítés, a megszakítás vagy a végrehajtás művelettel. A platform ezután újra próbálkozik a művelettel.


### <a name="51-option-1---migrate-a-cloud-service-not-in-a-virtual-network"></a>5,1) 1. lehetőség – nem virtuális hálózatban lévő felhőalapú szolgáltatás áttelepítése
Szerezze be a Cloud Services listáját a következő parancs használatával. Ezután válassza ki az áttelepíteni kívánt felhőalapú szolgáltatást.

```powershell
Get-AzureService | ft Servicename
```

Szerezze be a felhőalapú szolgáltatás központi telepítési nevét. Ebben a példában a szolgáltatás neve a **saját szolgáltatás**. Cserélje le a példában szereplő szolgáltatásnevet a saját szolgáltatás nevére.

```powershell
$serviceName = "My Service"
$deployment = Get-AzureDeployment -ServiceName $serviceName
$deploymentName = $deployment.DeploymentName
```

Először is ellenőrizze, hogy a következő parancsok segítségével áttelepítheti-e a felhőalapú szolgáltatást:

```powershell
$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
$validate.ValidationMessages
 ```

A következő parancs megjeleníti az áttelepítést tiltó figyelmeztetéseket és hibákat. Ha az érvényesítés sikeres, átléphet az előkészítési lépésre.

```powershell
Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```

### <a name="51-option-2---migrate-a-cloud-service-in-a-virtual-network"></a>5,1) 2. lehetőség – felhőalapú szolgáltatás áttelepítése virtuális hálózaton

Ha egy felhőalapú szolgáltatást virtuális hálózaton szeretne áttelepíteni, telepítse át a virtuális hálózatot. A felhőalapú szolgáltatás automatikusan áttelepíti a virtuális hálózatot.

> [!NOTE]
> Lehet, hogy a virtuális hálózat neve eltér az új portálon láthatótól. Az új Azure Portal a nevet jeleníti meg `[vnet-name]` , de a tényleges virtuális hálózat neve típusú `Group [resource-group-name] [vnet-name]` . Az áttelepítés megkezdése előtt keresse meg a tényleges virtuális hálózat nevét a parancs használatával, `Get-AzureVnetSite | Select -Property Name` vagy tekintse meg a régi Azure Portal. 

Ez a példa a virtuális hálózat nevét állítja be a **myVnet**. Cserélje le a példában szereplő virtuális hálózat nevét a saját nevére.

```powershell
$vnetName = "myVnet"
```

Először is ellenőrizze, hogy a következő paranccsal telepítheti-e át a virtuális hálózatot:

```powershell
Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

A következő parancs megjeleníti az áttelepítést tiltó figyelmeztetéseket és hibákat. Ha az ellenőrzés sikeres, folytassa a következő előkészítési lépéssel:

```powershell
Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Az előkészített felhőalapú szolgáltatás konfigurációját Azure PowerShell vagy a Azure Portal használatával vizsgálhatja meg. Ha nem áll készen az áttelepítésre, és vissza szeretne térni a régi állapotra, használja a következő parancsot:

```powershell
Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Ha az előkészített konfiguráció jól néz ki, a következő parancs használatával továbbíthatja és véglegesítheti az erőforrásokat:

```powershell
Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```


## <a name="next-steps"></a>Következő lépések
Tekintse át az [áttelepítés utáni változások](in-place-migration-overview.md#post-migration-changes) szakaszt a központi telepítési fájlok, az automatizálás és az új Cloud Services (bővített támogatás) központi telepítésének egyéb attribútumaival kapcsolatos változások megtekintéséhez. 
