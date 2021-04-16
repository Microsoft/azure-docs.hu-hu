---
title: PowerShell-minta – alkalmazásproxy tanúsítványt nem tartalmazó alkalmazások telepítése
description: PowerShell-példa, amely felsorolja Azure Active Directory (Azure AD) alkalmazásproxy egyéni tartományokat használó, de nem feltöltött érvényes TLS/SSL-tanúsítvánnyal.
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
ms.openlocfilehash: 00c2aa65c727bc614441b59c0021846855fcaf68
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377140"
---
# <a name="get-all-application-proxy-apps-published-with-no-certificate-uploaded"></a>Az összes alkalmazásproxy alkalmazás közzététele tanúsítvány feltöltése nélkül

Ez a PowerShell-példaszkvóta felsorolja az Azure Active Directory (Azure AD) alkalmazásproxy alkalmazásokat, amelyek egyéni tartományokat használnak, de nem töltöttek fel érvényes TLS-/SSL-tanúsítványt.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához az [AzureAD V2 PowerShell for Graph](/powershell/azure/active-directory/install-adv2) modulra (AzureAD) vagy az [AzureAD V2 PowerShell for Graph-modul](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) előzetes verziójára (AzureADPreview) van szükség.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-custom-domain-no-cert.ps1 "Get all Azure AD Proxy application apps published with no certificate uploaded")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Lekért egy szolgáltatásnév. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Lekért egy Azure AD-alkalmazást. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Lekér egy Azure AD-alkalmazásproxy konfigurált alkalmazást. |

## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modullal kapcsolatos további információkért lásd: [Az Azure AD PowerShell-modul áttekintése.](/powershell/azure/active-directory/overview)

További PowerShell-példák alkalmazásproxy Azure [AD PowerShell-példák az Azure AD alkalmazásproxy.](../application-proxy-powershell-samples.md)
