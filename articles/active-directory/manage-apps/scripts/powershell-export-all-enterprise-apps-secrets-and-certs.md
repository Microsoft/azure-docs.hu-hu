---
title: PowerShell-minta – a titkok és tanúsítványok exportálása Azure Active Directory bérlő vállalati alkalmazásaihoz.
description: PowerShell-példa, amely a Azure Active Directory bérlőben a megadott vállalati alkalmazásokhoz tartozó összes titkot és tanúsítványt exportálja.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: 5c0b661f80bf7100215b25a66e5b5337af01ba75
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584383"
---
# <a name="export-secrets-and-certificates-for-enterprise-apps"></a>A titkok és a tanúsítványok exportálása a vállalati alkalmazásokhoz
Ez a PowerShell-parancsfájl egy CSV-fájlba exportálja a megadott vállalati alkalmazások összes titkát és tanúsítványát a címtárból.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Ehhez a mintához a Graph modul (AzureAD) [AzureAD v2 PowerShell](/powershell/azure/active-directory/install-adv2) -je, vagy a [Graph modul előzetes verziójának (AzureADPreview) AzureAD v2 PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) szükséges.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-enterprise-apps-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified enterprise apps in your directory.")]

## <a name="script-explanation"></a>Szkript ismertetése

A "tag hozzáadása" parancs feladata az oszlopok létrehozása a CSV-fájlban.
A "$Path" változót közvetlenül a PowerShellben, a CSV-fájl elérési útjával módosíthatja, ha azt szeretné, hogy az exportálás nem interaktív.

| Parancs | Jegyzetek |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Beolvas egy alkalmazást a címtárból. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | Egy alkalmazás tulajdonosait kérdezi le a címtárból. |

## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modulról az [Azure ad PowerShell-modul áttekintése](/powershell/azure/active-directory/overview)című témakörben olvashat bővebben.

További PowerShell-példák az alkalmazások kezeléséhez: [Azure ad PowerShell-példák az alkalmazások kezeléséhez](../app-management-powershell-samples.md).
