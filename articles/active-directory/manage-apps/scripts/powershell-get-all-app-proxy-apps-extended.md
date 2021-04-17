---
title: PowerShell-minta – Bővített információ listázó alkalmazásproxy alkalmazásokról
description: PowerShell-példa, amely felsorolja az Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazásokat, valamint az alkalmazásazonosítót (AppId), a nevet (DisplayName), a külső URL-címet (ExternalUrl), a belső URL-címet (InternalUrl) és a hitelesítési típust (ExternalAuthenticationType).
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
ms.openlocfilehash: 1b3234a13a0e3fac760a899ce66e7faa7e7b642c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377361"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Az összes alkalmazásproxy lekért alkalmazás és a kiterjesztett információk listája

Ez a PowerShell-példaszkennelés felsorolja az összes Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazás adatait, beleértve az alkalmazásazonosítót (AppId), a nevet (DisplayName), a külső URL-címet (ExternalUrl), a belső URL-címet (InternalUrl), a hitelesítési típust (ExternalAuthenticationType), az SSO módot és a további beállításokat.

A kimeneti változó értékének $ssoMode lehetővé teszi a szűrt kimenetet SSO mód szerint. A további részletek a szkriptben vannak dokumentálva.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához az [AzureAD V2 PowerShell for Graph modulra](/powershell/azure/active-directory/install-adv2) (AzureAD) van szükség.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Lekért egy szolgáltatásnév. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Lekért egy Azure AD-alkalmazást. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Lekér egy Azure AD-alkalmazásproxy konfigurált alkalmazást. |

## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modullal kapcsolatos további információkért lásd: [Az Azure AD PowerShell-modul áttekintése.](/powershell/azure/active-directory/overview)

További PowerShell-példák alkalmazásproxy Azure [AD PowerShell-példák az Azure AD alkalmazásproxy.](../application-proxy-powershell-samples.md)
