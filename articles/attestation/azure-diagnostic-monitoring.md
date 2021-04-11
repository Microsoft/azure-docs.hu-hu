---
title: Azure diagnosztikai monitorozás az Azure-hoz – igazolás
description: Azure diagnosztikai monitorozás az Azure-hoz – igazolás
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d2773be4bc67e125c18d5d38c951685e4f4fceaf
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168348"
---
# <a name="set-up-diagnostics-with-a-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Diagnosztika beállítása az Azure igazolás platformmegbízhatósági modul (TPM) végpontján

Ebből a cikkből megtudhatja, hogyan hozhat létre és konfigurálhat diagnosztikai beállításokat a platform metrikáinak és a platformok naplóinak különböző célhelyekre küldéséhez. Az Azure [platform-naplói](/azure/azure-monitor/platform/platform-logs-overview) , beleértve az Azure-tevékenység naplóját és az erőforrás-naplókat, részletes diagnosztikai és naplózási információkat biztosítanak az Azure-erőforrásokról és az azoktól függő Azure-platformról. A rendszer alapértelmezés szerint gyűjti a [platform metrikáit](/azure/azure-monitor/platform/data-platform-metrics) , és azokat a Azure monitor metrikai adatbázisban tárolja.

Mielőtt elkezdené, győződjön meg róla, hogy [beállította az Azure-igazolást Azure PowerShell](quickstart-powershell.md).

A platformmegbízhatósági modul (TPM) végponti szolgáltatás engedélyezve van a diagnosztikai beállításokban, és a tevékenységek figyelésére használható. Állítsa be a TPM szolgáltatás végpontjának [Azure-figyelését](/azure/azure-monitor/overview) a következő kód használatával.

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```

A Tevékenységnaplók a Storage- **fiók tárolók szakaszában találhatók** . További információ: [erőforrás-naplók összegyűjtése és elemzése Azure-erőforrásokból](/azure/azure-monitor/learn/tutorial-resource-logs).
