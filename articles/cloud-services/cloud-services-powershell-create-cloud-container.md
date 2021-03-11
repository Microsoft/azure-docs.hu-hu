---
title: Cloud Service-(klasszikus) tároló létrehozása a PowerShell használatával | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan hozható létre felhőalapú szolgáltatás-tároló a PowerShell-lel. A tároló webes és feldolgozói szerepköröket üzemeltet.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 39fe439e37b1af4e833396ef83205729af8c7ad3
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610415"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-classic-container"></a>Üres Cloud Service-(klasszikus) tároló létrehozása Azure PowerShell parancs használatával

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

Ez a cikk azt ismerteti, hogyan hozhat létre gyorsan Cloud Services-tárolót Azure PowerShell-parancsmagok használatával. Kövesse az alábbi lépéseket:

1. Telepítse a Microsoft Azure PowerShell parancsmagot a [Azure PowerShell letöltések](https://aka.ms/webpi-azps) lapról.
2. Nyissa meg a PowerShell-parancssort.
3. Jelentkezzen be az [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount) használatával.

   > [!NOTE]
   > Az Azure PowerShell parancsmag telepítésével és az Azure-előfizetéshez való csatlakozással kapcsolatos további útmutatásért tekintse meg a [Azure PowerShell telepítése és konfigurálása című témakört](/powershell/azure/).
   >
   >
4. A **New-AzureService** parancsmag használatával hozzon létre egy üres Azure Cloud Service-tárolót.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Kövesse ezt a példát a parancsmag meghívásához:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Az Azure Cloud Service létrehozásával kapcsolatos további információkért futtassa a következőt:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Következő lépések

* A felhőalapú szolgáltatás központi telepítésének kezeléséhez tekintse meg a [Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService), a [Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService)és a [set-AzureService](/powershell/module/servicemanagement/azure.service/set-azureservice) parancsokat. További információért tekintse meg a [Cloud Services konfigurálását](cloud-services-how-to-configure-portal.md) is.
* Ha közzé szeretné tenni a Cloud Service-projektet az Azure-ban, tekintse meg az [archivált Cloud Services-tárház](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery) **PublishCloudService.ps1** kód mintáját.