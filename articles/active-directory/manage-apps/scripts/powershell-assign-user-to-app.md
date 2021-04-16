---
title: PowerShell-minta – Felhasználó hozzárendelése alkalmazásproxy alkalmazáshoz
description: PowerShell-példa, amely egy felhasználót rendel egy Azure Active Directory (Azure AD) alkalmazásproxy alkalmazáshoz.
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
ms.openlocfilehash: 1b58a16363adefaca5f80e828e2bba5157c5da7b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375542"
---
# <a name="assign-a-user-to-a-specific-azure-ad-application-proxy-application"></a>Felhasználó hozzárendelése egy adott Azure AD-alkalmazásproxy alkalmazáshoz

Ez a PowerShell-példaszkret lehetővé teszi egy felhasználó hozzárendelését egy adott Azure AD-alkalmazásproxy alkalmazáshoz.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához az [AzureAD V2 PowerShell for Graph](/powershell/azure/active-directory/install-adv2) modulra (AzureAD) vagy az [AzureAD V2 PowerShell for Graph-modul](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) előzetes verziójára (AzureADPreview) van szükség.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-user-to-app.ps1 "Assign a user to an application")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
| [New-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/new-azureaduserapproleassignment) | Hozzárendel egy felhasználót egy alkalmazás-szerepkörhöz. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure AD PowerShell-modulról: [Az Azure AD PowerShell-modul áttekintése.](/powershell/azure/active-directory/overview)

További PowerShell-példák a alkalmazásproxy: [Azure AD PowerShell-példák az Azure AD alkalmazásproxy.](../application-proxy-powershell-samples.md)
