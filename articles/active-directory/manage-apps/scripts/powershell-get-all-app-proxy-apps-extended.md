---
title: PowerShell-minta – alkalmazásproxy-alkalmazások részletes adatainak listázása
description: PowerShell-példa, amely felsorolja az összes Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazást, valamint az alkalmazás AZONOSÍTÓját (AppId), a nevet (DisplayName), a külső URL-t (Alkalmazásazonosítójának), a belső URL-címet (InternalUrl) és a hitelesítési típust (ExternalAuthenticationType).
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 62dae175b529bd54b2f139eece89bbde6f98cd38
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055008"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Az összes alkalmazásproxy-alkalmazás beolvasása és a kibővített információk listázása

Ez a PowerShell-parancsfájl az összes Azure Active Directory (Azure AD) alkalmazásproxy alkalmazásával kapcsolatos információkat listázza, beleértve az alkalmazás AZONOSÍTÓját (AppId), a nevet (DisplayName), a külső URL-t (Alkalmazásazonosítójának), a belső URL-címet (InternalUrl), a hitelesítési típust (ExternalAuthenticationType), az egyszeri bejelentkezés módját és a további beállításokat.

A $ssoMode változó értékének módosítása egyszeri bejelentkezéses módban szűrt kimenetet tesz lehetővé. További részletek a szkriptben olvashatók.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához a [AzureAD v2 PowerShell szükséges a Graph modulhoz](/powershell/azure/active-directory/install-adv2) (AzureAD).

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Egy egyszerű szolgáltatásnév beolvasása. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Beolvas egy Azure AD-alkalmazást. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Az Application proxyhoz konfigurált alkalmazást kérdezi le az Azure AD-ben. |

## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modulról az [Azure ad PowerShell-modul áttekintése](/powershell/azure/active-directory/overview)című témakörben olvashat bővebben.

További PowerShell-példák az alkalmazásproxy esetében: [Azure ad PowerShell-példák az azure ad Application proxy](../application-proxy-powershell-samples.md).
