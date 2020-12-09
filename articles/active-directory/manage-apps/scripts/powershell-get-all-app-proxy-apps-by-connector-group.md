---
title: Azure AD Application Proxy-összekötő csoportok listázása alkalmazásokhoz
description: PowerShell-példa, amely felsorolja az összes Azure Active Directory-(Azure AD-) alkalmazásproxy-összekötő csoportot a hozzárendelt alkalmazásokkal.
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
ms.openlocfilehash: 008959c3e22f026314ec28b42b649e6c2dabefee
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861619"
---
# <a name="get-all-application-proxy-apps-and-list-by-connector-group"></a>Az összes alkalmazásproxy-alkalmazás és-lista lekérése összekötő csoport szerint

Ez a PowerShell-parancsfájl a hozzárendelt alkalmazásokkal rendelkező összes Azure Active Directory (Azure AD) alkalmazásproxy-összekötő csoport adatait listázza.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához a Graph modul (AzureAD) [AzureAD v2 PowerShell](/powershell/azure/active-directory/install-adv2) -je, vagy a [Graph modul előzetes verziójának (AzureADPreview) AzureAD v2 PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) szükséges.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-by-connectorgroup.ps1 "Get all Application Proxy Connector groups with the assigned applications")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Egy egyszerű szolgáltatásnév beolvasása. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Beolvas egy Azure AD-alkalmazást. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Az Application proxyhoz konfigurált alkalmazást kérdezi le az Azure AD-ben. |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Lekéri az összes összekötő csoport listáját, vagy ha meg van adva, a megadott összekötő-csoport részleteit. |


## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modulról az [Azure ad PowerShell-modul áttekintése](/powershell/azure/active-directory/overview)című témakörben olvashat bővebben.

További PowerShell-példák az alkalmazásproxy esetében: [Azure ad PowerShell-példák az azure ad Application proxy](../application-proxy-powershell-samples.md).
