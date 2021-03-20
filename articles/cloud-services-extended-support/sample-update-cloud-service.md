---
title: Azure PowerShell-minták – Azure-felhőszolgáltatás (kiterjesztett támogatás) frissítése
description: Példa parancsfájlok az Azure Cloud Service (bővített támogatás) telepítésének frissítéséhez
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: fcb92ec3aa8f360107f0e7220415c57344c6d83a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98881469"
---
# <a name="update-an-azure-cloud-service-extended-support"></a>Azure-felhőszolgáltatás (kiterjesztett támogatás) frissítése

Ezek a minták különböző módszereket mutatnak be egy meglévő Azure Cloud Service-szolgáltatás (kiterjesztett támogatás) központi telepítésének frissítésére.

## <a name="add-an-extension-to-existing-cloud-service"></a>Bővítmény hozzáadása a meglévő Cloud Service-hez
```powershell
# Create RDP extension object
$rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name "RDPExtension" -Credential $credential -Expiration $expiration -TypeHandlerVersion "1.2.1"
# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Add RDP extension to existing Cloud Service extension object
$cloudService.ExtensionProfileExtension = $cloudService.ExtensionProfileExtension + $rdpExtension
# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="remove-all-extensions-from-a-cloud-service"></a>Összes bővítmény eltávolítása egy felhőalapú szolgáltatásból
```powershell
# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Set extension to empty list
$cloudService.ExtensionProfileExtension = @()
# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="remove-the-remote-desktop-extension-from-cloud-service"></a>Távoli asztali bővítmény eltávolítása a Cloud Service-ből
```powershell
# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Remove extension by name RDPExtension
$cloudService.ExtensionProfileExtension = $cloudService.ExtensionProfileExtension | Where-Object { $_.Name -ne "RDPExtension" }
# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="scale-out--scale-in-role-instances"></a>Kibővíthető/méretezhető szerepkör-példányok
```powershell
# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Scale-out all role instance count by 1
$cloudService.RoleProfileRole | ForEach-Object {$_.SkuCapacity += 1}

# Scale-in ContosoFrontend role instance count by 1
$role = $cloudService.RoleProfileRole | Where-Object {$_.Name -eq "ContosoFrontend"}
 $role.SkuCapacity -= 1

# Update Cloud Service configuration as per the new role instance count
$cloudService.Configuration = $configuration

# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="next-steps"></a>Következő lépések
Az Azure Cloud Services (bővített támogatás) szolgáltatással kapcsolatos további információkért lásd: [azure Cloud Services (bővített támogatás) – áttekintés](overview.md).
- Látogasson el a [Cloud Services (bővített támogatás) minták tárházára](https://github.com/Azure-Samples/cloud-services-extended-support)