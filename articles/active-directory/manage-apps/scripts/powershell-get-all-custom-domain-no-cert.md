---
title: PowerShell-minta – alkalmazásproxy-alkalmazások tanúsítvány nélkül
description: PowerShell-példa, amely felsorolja az összes olyan Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazást, amely egyéni tartományokat használ, de nincs feltöltve érvényes TLS/SSL-tanúsítvány.
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
ms.openlocfilehash: 49a7c0729fae8eb009bfcbe0661ecaca600dd3ec
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861663"
---
# <a name="get-all-azure-ad-proxy-application-apps-published-with-no-certificate-uploaded"></a>A tanúsítvány feltöltése nélkül közzétett összes Azure AD-alkalmazásproxy letöltése

Ez a PowerShell-parancsfájl például felsorolja az összes olyan Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazást, amely egyéni tartományokat használ, de nincs feltöltve érvényes TLS/SSL-tanúsítvány.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához a Graph modul (AzureAD) [AzureAD v2 PowerShell](/powershell/azure/active-directory/install-adv2) -je, vagy a [Graph modul előzetes verziójának (AzureADPreview) AzureAD v2 PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) szükséges.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-custom-domain-no-cert.ps1 "Get all Azure AD Proxy application apps published with no certificate uploaded")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Egy egyszerű szolgáltatásnév beolvasása. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Beolvas egy Azure AD-alkalmazást. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Az Application proxyhoz konfigurált alkalmazást kérdezi le az Azure AD-ben. |

## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modulról az [Azure ad PowerShell-modul áttekintése](/powershell/azure/active-directory/overview)című témakörben olvashat bővebben.

További PowerShell-példák az alkalmazásproxy esetében: [Azure ad PowerShell-példák az azure ad Application proxy](../application-proxy-powershell-samples.md).
