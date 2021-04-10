---
title: PowerShell-minta – alkalmazásproxy-alkalmazások alapszintű adatainak listázása
description: PowerShell-példa, amely felsorolja Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazásait, valamint az alkalmazás AZONOSÍTÓját (AppId), a nevet (DisplayName) és az Object ID-t (ObjId).
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
ms.openlocfilehash: 0a5ee23a16c10a579c9b39ce3589028603ea7a3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550620"
---
# <a name="get-all-application-proxy-apps-and-list-basic-information"></a>Az összes alkalmazásproxy-alkalmazás beolvasása és alapvető információk listázása

Ez a PowerShell-parancsfájl az összes Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazással kapcsolatos információkat listázza, beleértve az alkalmazás AZONOSÍTÓját (AppId), a nevet (DisplayName) és az Object ID-t (ObjId).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához a Graph modul (AzureAD) [AzureAD v2 PowerShell](/powershell/azure/active-directory/install-adv2) -je, vagy a [Graph modul előzetes verziójának (AzureADPreview) AzureAD v2 PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) szükséges.
## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-basic.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Egy egyszerű szolgáltatásnév beolvasása. |

## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modulról az [Azure ad PowerShell-modul áttekintése](/powershell/azure/active-directory/overview)című témakörben olvashat bővebben.

További PowerShell-példák az alkalmazásproxy esetében: [Azure ad PowerShell-példák az azure ad Application proxy](../application-proxy-powershell-samples.md).
