---
title: PowerShell-minta – alkalmazásproxy alapértelmezett tartományt használó alkalmazások telepítése
description: PowerShell-példa, amely felsorolja Azure Active Directory (Azure AD) alkalmazásproxy alapértelmezett tartományokat (.msappproxy.net).
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
ms.openlocfilehash: 02216711efdc88f3a5255bcdc67074da3f768534
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377955"
---
# <a name="get-all-application-proxy-apps-using-default-domains-msappproxynet"></a>Az összes alkalmazásproxy lekért alkalmazás alapértelmezett tartományokkal (.msappproxy.net)

Ez a PowerShell-példaszk Azure Active Directory alapértelmezett tartományokat (.msappproxy.net) használó összes alkalmazásproxy (Azure AD) msappproxy.net.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához az [AzureAD V2 PowerShell for Graph](/powershell/azure/active-directory/install-adv2) modulra (AzureAD) vagy az [AzureAD V2 PowerShell for Graph-modul](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) előzetes verziójára (AzureADPreview) van szükség.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-default-domain-apps.ps1 "Get all Application Proxy apps using default domains (.msappproxy.net)")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Lekért egy szolgáltatásnév. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Lekért egy Azure AD-alkalmazást. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Lekér egy Azure AD-alkalmazásproxy konfigurált alkalmazást. |

## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modullal kapcsolatos további információkért lásd: [Az Azure AD PowerShell-modul áttekintése.](/powershell/azure/active-directory/overview)

További PowerShell-példák a alkalmazásproxy: [Azure AD PowerShell-példák az Azure AD alkalmazásproxy.](../application-proxy-powershell-samples.md)
