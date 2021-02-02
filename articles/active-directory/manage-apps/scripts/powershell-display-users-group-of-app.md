---
title: PowerShell-minta – az alkalmazásproxy-alkalmazáshoz tartozó felhasználók & csoportok listázása
description: PowerShell-példa, amely felsorolja az adott Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazáshoz rendelt összes felhasználót és csoportot.
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
ms.openlocfilehash: 6fc94a7e9c8ed222b7612db691c687b8daa85d9a
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258422"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>Alkalmazásproxy-alkalmazáshoz rendelt felhasználók és csoportok megjelenítése

Ez a PowerShell-parancsfájl egy adott Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazáshoz rendelt felhasználókat és csoportokat sorolja fel.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához a Graph modul (AzureAD) [AzureAD v2 PowerShell](/powershell/azure/active-directory/install-adv2) -je, vagy a [Graph modul előzetes verziójának (AzureADPreview) AzureAD v2 PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) szükséges.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
| [Get-AzureADUser](/powershell/module/AzureAD/get-azureaduser)| Felhasználó beolvasása. |
| [Get-AzureADGroup](/powershell/module/AzureAD/get-azureadgroup)| Egy csoport beolvasása. |
| [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Egy egyszerű szolgáltatásnév beolvasása. |
| [Get-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/get-azureaduserapproleassignment) | Felhasználói alkalmazás szerepkör-hozzárendelésének beolvasása. |
| [Get-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/get-azureadgroupapproleassignment) | Csoportbeli alkalmazás szerepkör-hozzárendelésének beolvasása. |

## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modulról az [Azure ad PowerShell-modul áttekintése](/powershell/azure/active-directory/overview)című témakörben olvashat bővebben.

További PowerShell-példák az alkalmazásproxy esetében: [Azure ad PowerShell-példák az azure ad Application proxy](../application-proxy-powershell-samples.md).
