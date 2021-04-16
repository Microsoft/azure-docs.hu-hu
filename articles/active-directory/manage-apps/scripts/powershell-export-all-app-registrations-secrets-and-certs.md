---
title: PowerShell-minta – Titkos kulcsok és tanúsítványok exportálása alkalmazásregisztrációkhoz Azure Active Directory bérlőben.
description: PowerShell-példa, amely exportálja a bérlőben a megadott alkalmazásregisztrációk összes titkos Azure Active Directory tanúsítványát.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: b5cbb6b3843e81d9265405dcea24a092e57bf65e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377042"
---
# <a name="export-secrets-and-certificates-for-app-registrations"></a>Titkos kulcsok és tanúsítványok exportálása alkalmazásregisztrációkhoz

Ez a PowerShell-példaszkvóta a megadott alkalmazásregisztrációk összes titkos adatát és tanúsítványát exportálja egy CSV-fájlba a címtárból.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Ehhez a mintához az [AzureAD V2 PowerShell for Graph](/powershell/azure/active-directory/install-adv2) modulra (AzureAD) vagy az [AzureAD V2 PowerShell for Graph-modul](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) előzetes verziójára (AzureADPreview) van szükség.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-app-registrations-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified app registrations in your directory.")]

## <a name="script-explanation"></a>Szkript ismertetése

Az "Add-Member" parancs felelős a CSV-fájlban lévő oszlopok létrehozásáért.
A "$Path" változót közvetlenül a PowerShellben módosíthatja CSV-fájl elérési út használatával arra az esetre, ha az exportálás nem interaktív lenne.

| Parancs | Jegyzetek |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Lekér egy alkalmazást a címtárból. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner) | Lekéri egy alkalmazás tulajdonosait a címtárból. |

## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modullal kapcsolatos további információkért lásd: [Az Azure AD PowerShell-modul áttekintése.](/powershell/azure/active-directory/overview)

Az alkalmazáskezelésre vonatkozó egyéb PowerShell-példákért lásd: [Azure AD PowerShell-példák az alkalmazáskezeléshez.](../app-management-powershell-samples.md)
