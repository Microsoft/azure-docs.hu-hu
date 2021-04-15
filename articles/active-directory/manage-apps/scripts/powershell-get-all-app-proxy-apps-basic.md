---
title: PowerShell-minta – Alapvető információk felsorolása alkalmazásproxy alkalmazásokhoz
description: PowerShell-példa a Azure Active Directory (Azure AD) alkalmazásproxy alkalmazásazonosítóval (AppId), névvel (DisplayName) és objektumazonosítóval (ObjId).
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: b9381d6f345888abe3f6314e253ae580155b97df
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375440"
---
# <a name="get-all-application-proxy-apps-and-list-basic-information"></a>Az összes alkalmazásproxy lekért alkalmazás és az alapvető információk felsorolása

Ez a PowerShell-példaszkprogram az összes Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazás adatait listázza, beleértve az alkalmazásazonosítót (AppId), a nevet (DisplayName) és az objektumazonosítót (ObjId).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához az [AzureAD V2 PowerShell for Graph](/powershell/azure/active-directory/install-adv2) modulra (AzureAD) vagy az [AzureAD V2 PowerShell for Graph-modul](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) előzetes verziójára (AzureADPreview) van szükség.
## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-basic.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Lekért egy szolgáltatásnév. |

## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modullal kapcsolatos további információkért lásd: [Az Azure AD PowerShell-modul áttekintése.](/powershell/azure/active-directory/overview)

További PowerShell-példák alkalmazásproxy Azure [AD PowerShell-példák az Azure AD alkalmazásproxy.](../application-proxy-powershell-samples.md)
