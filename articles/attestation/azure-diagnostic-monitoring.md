---
title: Azure diagnosztikai monitorozás Azure Attestation
description: Azure diagnosztikai monitorozás Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b0cd0402348e4aa45b291f30b677fc9e4bbdb98
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833633"
---
# <a name="set-up-diagnostics-with-a-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Diagnosztikai eszközök platformmegbízhatósági modul (TPM) végponttal Azure Attestation

Ez a cikk segítséget nyújt a diagnosztikai beállítások létrehozásában és konfigurálásában, hogy platformmetrikákat és platformnaplókat küldjön különböző célokra. [Az](/azure/azure-monitor/platform/platform-logs-overview) Azure platformnaplói, beleértve az Azure-tevékenységnaplót és az erőforrásnaplókat, részletes diagnosztikai és naplózási információkat biztosítanak az Azure-erőforrásokról és az azure-platformról, amelyektől függenek. [A platformmetrikákat](/azure/azure-monitor/platform/data-platform-metrics) a rendszer alapértelmezés szerint gyűjti, és a Azure Monitor Metrikák adatbázisban tárolja.

Mielőtt hozzákezd, győződjön meg arról, hogy beállította a Azure Attestation [a Azure PowerShell.](quickstart-powershell.md)

A platformmegbízhatósági modul (TPM) végpontszolgáltatás engedélyezve van a diagnosztikai beállításokban, és használható a tevékenységek figyelése érdekében. Állítsa be [az Azure Monitoringot](/azure/azure-monitor/overview) a TPM-szolgáltatásvégponthoz az alábbi kóddal.

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```

A tevékenységnaplók a tárfiók **Tárolók** szakaszában találhatóak. További információ: Erőforrásnaplók gyűjtése és elemzése [Egy Azure-erőforrásból.](/azure/azure-monitor/learn/tutorial-resource-logs)
