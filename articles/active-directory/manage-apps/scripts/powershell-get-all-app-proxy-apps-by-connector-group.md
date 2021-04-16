---
title: Az Azure AD alkalmazásproxy összekötőcsoportokat listába sorolja az alkalmazásokhoz
description: PowerShell-példa, amely felsorolja az Azure Active Directory (Azure AD) alkalmazásproxy összekötőcsoportokat a hozzárendelt alkalmazásokkal.
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
ms.openlocfilehash: 1adb576e751d93dc4a9d240546325d7e671d0892
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377009"
---
# <a name="get-all-application-proxy-apps-and-list-by-connector-group"></a>Az összes alkalmazásproxy és lista összekötőcsoport szerint

Ez a PowerShell-példaszkent az összekötők összes Azure Active Directory (Azure AD) alkalmazásproxy a hozzárendelt alkalmazásokkal.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához az [AzureAD V2 PowerShell for Graph](/powershell/azure/active-directory/install-adv2) modulra (AzureAD) vagy az [AzureAD V2 PowerShell for Graph-modul](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) előzetes verziójára (AzureADPreview) van szükség.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-by-connectorgroup.ps1 "Get all Application Proxy Connector groups with the assigned applications")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Lekért egy szolgáltatásnév. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Lekért egy Azure AD-alkalmazást. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Lekér egy Azure AD-alkalmazásproxy konfigurált alkalmazást. |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Lekéri az összes összekötőcsoport listáját, vagy ha meg van adva, a megadott összekötőcsoport részleteit. |


## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modullal kapcsolatos további információkért lásd: [Az Azure AD PowerShell-modul áttekintése.](/powershell/azure/active-directory/overview)

További PowerShell-példák alkalmazásproxy Azure [AD PowerShell-példák az Azure AD alkalmazásproxy.](../application-proxy-powershell-samples.md)
