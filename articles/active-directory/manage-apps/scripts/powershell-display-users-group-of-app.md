---
title: PowerShell-minta – Felhasználók és & felsorolása alkalmazásproxy alkalmazáshoz
description: PowerShell-példa egy adott alkalmazáshoz (Azure AD Azure Active Directory rendelt összes felhasználó és csoport alkalmazásproxy felsorolásához.
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
ms.openlocfilehash: 0fdbf2ab7d33978a72e7455f3e2f5ba591cc4ab7
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375491"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>Egy alkalmazáshoz hozzárendelt felhasználók és csoportok alkalmazásproxy megjelenítése

Ez a PowerShell-példaszkértő példa egy adott alkalmazáshoz (Azure AD Azure Active Directory rendelt felhasználókat és csoportokat alkalmazásproxy sorolja fel.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához az [AzureAD V2 PowerShell for Graph](/powershell/azure/active-directory/install-adv2) modulra (AzureAD) vagy az [AzureAD V2 PowerShell for Graph-modul](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) előzetes verziójára (AzureADPreview) van szükség.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
| [Get-AzureADUser](/powershell/module/AzureAD/get-azureaduser)| Lekért egy felhasználót. |
| [Get-AzureADGroup](/powershell/module/AzureAD/get-azureadgroup)| Lekért egy csoportot. |
| [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Lekért egy szolgáltatásnév. |
| [Get-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/get-azureaduserapproleassignment) | Felhasználói alkalmazás szerepkör-hozzárendelésének lekérte. |
| [Get-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/get-azureadgroupapproleassignment) | Csoportalkalmazás szerepkör-hozzárendelésének lekérte. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure AD PowerShell-modulról: [Az Azure AD PowerShell-modul áttekintése.](/powershell/azure/active-directory/overview)

További PowerShell-példák a alkalmazásproxy: [Azure AD PowerShell-példák az Azure AD alkalmazásproxy.](../application-proxy-powershell-samples.md)
