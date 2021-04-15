---
title: PowerShell-minta – Titkos kulcsok és tanúsítványok exportálása vállalati alkalmazásokhoz Azure Active Directory bérlőben.
description: PowerShell-példa, amely a bérlőben található megadott vállalati alkalmazások összes titkos Azure Active Directory tanúsítványát exportálja.
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
ms.openlocfilehash: 536197ebc5df94447f3937773e0447e47961bd92
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378601"
---
# <a name="export-secrets-and-certificates-for-enterprise-apps"></a>Titkos kulcsok és tanúsítványok exportálása vállalati alkalmazásokhoz
Ez a PowerShell-példaszkvóta a megadott vállalati alkalmazások összes titkos adatát, tanúsítványát és tulajdonosát exportálja a címtárból egy CSV-fájlba.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Ehhez a mintához az [AzureAD V2 PowerShell for Graph](/powershell/azure/active-directory/install-adv2) modulra (AzureAD) vagy az [AzureAD V2 PowerShell for Graph-modul](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) előzetes verziójára (AzureADPreview) van szükség.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-enterprise-apps-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified enterprise apps in your directory.")]

## <a name="script-explanation"></a>Szkript ismertetése

Az "Add-Member" parancs felelős a CSV-fájlban lévő oszlopok létrehozásáért.
A "$Path" változót közvetlenül a PowerShellben, egy CSV-fájl elérési útjában módosíthatja arra az esetre, ha az exportálás nem interaktív.

| Parancs | Jegyzetek |
|---|---|
| [Get-AzureADServicePrincipal](/powershell/module/azuread/Get-azureADServicePrincipal?view=azureadps-2.0&preserve-view=true) | Lekér egy vállalati alkalmazást a címtárból. |
| [Get-AzureADServicePrincipalOwner](/powershell/module/azuread/Get-AzureADServicePrincipalOwner?view=azureadps-2.0&preserve-view=true) | Lekéri egy vállalati alkalmazás tulajdonosait a címtárból. |


## <a name="next-steps"></a>Következő lépések

További információ az Azure AD PowerShell-modulról: [Az Azure AD PowerShell-modul áttekintése.](/powershell/azure/active-directory/overview)

További PowerShell-példák az alkalmazáskezeléshez: [Azure AD PowerShell-példák az alkalmazáskezeléshez.](../app-management-powershell-samples.md)
