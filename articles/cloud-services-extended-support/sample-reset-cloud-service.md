---
title: Azure PowerShell minták – Azure Cloud Services alaphelyzetbe állítása (bővített támogatás)
description: Az Azure Cloud Service (bővített támogatás) üzembe helyezésének alaphelyzetbe állítására szolgáló parancsfájlok
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 5c43d61b1e7cd98674eab4c6d857cc1114a06013
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475320"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Azure Cloud Service alaphelyzetbe állítása (bővített támogatás) 
Ezek a minták a meglévő Azure Cloud Service (bővített támogatás) üzembe helyezésének különböző módjait fedik le.

## <a name="reimage-role-instances-of-cloud-service"></a>Cloud Service-példányok rendszerkép-példányainak átállítása
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Ez a parancs alaphelyzetbe állítja a 2 szerepkör-példányt ContosoFrontEnd_IN_0 és ContosoBackEnd_IN_1 a ContosoCS nevű Cloud Service-t, amely a ContosOrg nevű erőforráscsoporthoz tartozik.

## <a name="reimage-all-roles-of-cloud-service"></a>A Cloud Service összes szerepkörének rendszerképének alaphelyzetbe állítása
```powershell
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Ez a parancs alaphelyzetbe állítja a ContosoCS nevű Cloud Service összes szerepkör-példányát, amely a ContosOrg nevű erőforráscsoporthoz tartozik.

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Egy felhőalapú szolgáltatás egyetlen szerepkör-példányának rendszerképének alaphelyzetbe állítása
```powershell
Invoke-AzCloudServiceRoleInstanceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0"
```
Ez a parancs alaphelyzetbe állítja a (z) ContosoCS nevű Cloud Service ContosoFrontEnd_IN_0 nevű szerepkör-példányát, amely a ContosOrg nevű erőforráscsoporthoz tartozik.

## <a name="rebuild-role-instances-of-cloud-service"></a>A Cloud Service szerepkör-példányainak újraépítése
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Ez a parancs újraépíti a 2 szerepkör-példányt ContosoFrontEnd_IN_0 és ContosoBackEnd_IN_1 a ContosoCS nevű Cloud Service-t, amely a ContosOrg nevű erőforráscsoporthoz tartozik.

## <a name="rebuild-all-roles-of-cloud-service"></a>A Cloud Service összes szerepkörének újraépítése
```powershell
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Ezzel a paranccsal a ContosoCS nevű Cloud Service összes szerepkör-példánya újraépíthető, amely a ContosOrg nevű erőforráscsoporthoz tartozik.

## <a name="restart-role-instances-of-cloud-service"></a>Cloud Service-példányok újraindítása
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Ez a parancs újraindítja a 2 szerepkör-példányt ContosoFrontEnd_IN_0 és ContosoBackEnd_IN_1 a ContosoCS nevű Cloud Service-t, amely a ContosOrg nevű erőforráscsoporthoz tartozik.

## <a name="restart-all-roles-of-cloud-service"></a>A Cloud Service összes szerepkörének újraindítása
```powershell
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Ezzel a paranccsal a ContosoCS nevű Cloud Service összes szerepkör-példánya újraindítható, amely a ContosOrg nevű erőforráscsoporthoz tartozik.

## <a name="next-steps"></a>Következő lépések

- Az Azure Cloud Services (bővített támogatás) szolgáltatással kapcsolatos további információkért lásd: [azure Cloud Services (bővített támogatás) – áttekintés](overview.md).
- Látogasson el a [Cloud Services (bővített támogatás) minták tárházára](https://github.com/Azure-Samples/cloud-services-extended-support)
