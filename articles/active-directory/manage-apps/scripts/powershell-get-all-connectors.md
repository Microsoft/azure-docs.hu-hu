---
title: PowerShell-minta – Az összes alkalmazásproxy listája
description: PowerShell-példa, amely felsorolja az Azure Active Directory (Azure AD) alkalmazásproxy összekötőcsoportokat és összekötőket a címtárban.
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
ms.openlocfilehash: 032d4930e29126ec9636e710f016d9612dc2cda0
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377564"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>A címtárban található összes összekötőcsoport és összekötő lekérte

Ez a PowerShell-példaszk Azure Active Directory (Azure AD) alkalmazásproxy a címtárban található összes összekötőcsoportot és összekötőt.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához az [AzureAD V2 PowerShell for Graph](/powershell/azure/active-directory/install-adv2) modulra (AzureAD) vagy az [AzureAD V2 PowerShell for Graph-modul](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) előzetes verziójára (AzureADPreview) van szükség.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Lekéri az összes összekötőcsoport listáját, vagy ha meg van adva, a megadott összekötőcsoport részleteit. |
| [Get-AzureADApplicationProxyConnectorGroupMembers](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers) | Le alkalmazásproxy összekötőkhöz társított összes összekötőt.|

## <a name="next-steps"></a>Következő lépések

További információ az Azure AD PowerShell-modulról: [Az Azure AD PowerShell-modul áttekintése.](/powershell/azure/active-directory/overview)

További PowerShell-példák a alkalmazásproxy: [Azure AD PowerShell-példák az Azure AD alkalmazásproxy.](../application-proxy-powershell-samples.md)
