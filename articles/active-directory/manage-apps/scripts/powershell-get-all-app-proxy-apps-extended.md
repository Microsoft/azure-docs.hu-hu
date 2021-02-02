---
title: PowerShell-minta – alkalmazásproxy-alkalmazások részletes adatainak listázása
description: PowerShell-példa, amely felsorolja az összes Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazást, valamint az alkalmazás AZONOSÍTÓját (AppId), a nevet (DisplayName), a külső URL-t (Alkalmazásazonosítójának), a belső URL-címet (InternalUrl) és a hitelesítési típust (ExternalAuthenticationType).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: ccd0c7be7fd0dd533028faa0dc2bbdad30d74c79
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258693"
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
