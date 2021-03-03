---
title: Azure diagnosztikai monitorozás – Azure-igazolás
description: Azure diagnosztikai monitorozás az Azure-hoz – igazolás
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d01e7817906927295591353b710afe2899aacdf1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726478"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Diagnosztika beállítása az Azure igazolás platformmegbízhatósági modul (TPM) végpontján

Az Azure [platform-naplói](../azure-monitor/essentials/platform-logs-overview.md) , beleértve az Azure-tevékenység naplóját és az erőforrás-naplókat, részletes diagnosztikai és naplózási információkat biztosítanak az Azure-erőforrásokhoz és az Azure-platformtól függenek. A [platform metrikáit](../azure-monitor/essentials/data-platform-metrics.md) a rendszer alapértelmezés szerint gyűjti, és általában a Azure monitor metrikai adatbázisban tárolja. Ez a cikk a diagnosztikai beállítások létrehozásával és konfigurálásával kapcsolatos részletes információkat tartalmaz a platform metrikáinak és a platformok naplóinak különböző célhelyekre küldéséhez. 

A TPM-végpont szolgáltatás diagnosztikai beállítással van engedélyezve, és a tevékenységek figyelésére használható. Ha a TPM szolgáltatás végpontjának [Azure-figyelését](../azure-monitor/overview.md) a PowerShell használatával szeretné beállítani, kövesse az alábbi lépéseket. 

Az Azure igazolási szolgáltatás beállítása. 

[Azure-igazolás beállítása Azure PowerShell](./quickstart-powershell.md)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
A tevékenységi naplók a Storage-fiók tárolók szakaszában találhatók. Részletes információ: az erőforrás- [naplók összegyűjtése egy Azure-erőforrásból, és a Azure monitor-Azure monitor elemzése](../azure-monitor/essentials/tutorial-resource-logs.md)
