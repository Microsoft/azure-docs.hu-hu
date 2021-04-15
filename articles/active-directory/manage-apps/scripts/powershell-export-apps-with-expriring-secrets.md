---
title: PowerShell-minta – Lejáró titkos adatokat és tanúsítványokat tartalmazó alkalmazások exportálása Azure Active Directory bérlőben.
description: PowerShell-példa, amely exportálja az összes alkalmazást, amely lejáró titkos okkal és tanúsítványokkal rendelkezik a bérlőben Azure Active Directory alkalmazásokhoz.
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
ms.openlocfilehash: 7f129e06904497b43eff8a3f0221fb57565ac112
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375406"
---
# <a name="export-apps-with-expiring-secrets-and-certificates"></a>Lejáró titkos adatokat és tanúsítványokat feljáró alkalmazások exportálása

Ez a PowerShell-példaszkvóta egy CSV-fájlba exportálja a címtárból az összes lejáró titkos okkal, tanúsítványokkal és azok tulajdonosával együtt az alkalmazásregisztrációkat.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Ehhez a mintához az [AzureAD V2 PowerShell for Graph](/powershell/azure/active-directory/install-adv2) modulra (AzureAD) vagy az [AzureAD V2 PowerShell for Graph-modul](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) előzetes verziójára (AzureADPreview) van szükség.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-expiring-secrets.ps1 "Exports all apps with expiring secrets and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript közvetlenül, módosítás nélkül használható. A rendszergazda a lejárati dátumról, valamint arról is megkérdezi, hogy szeretné-e látni a már lejárt titkos kulcsokat vagy tanúsítványokat.

Az "Add-Member" parancs felelős a CSV-fájlban lévő oszlopok létrehozásáért.
A "New-Object" parancs létrehoz egy objektumot, amely a CSV-fájl exportálási oszlopaihoz lesz használva.
A "$Path" változót közvetlenül a PowerShellben módosíthatja egy CSV-fájl elérési útjában, ha az exportálást nem interaktívnak szeretné részesíte előnyben.

| Parancs | Jegyzetek |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Lekér egy alkalmazást a címtárból. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | Lekéri egy alkalmazás tulajdonosait a címtárból. |

## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modullal kapcsolatos további információkért lásd: [Az Azure AD PowerShell-modul áttekintése.](/powershell/azure/active-directory/overview)

Az alkalmazáskezelésre vonatkozó egyéb PowerShell-példákért lásd: [Azure AD PowerShell-példák az alkalmazáskezeléshez.](../app-management-powershell-samples.md)
