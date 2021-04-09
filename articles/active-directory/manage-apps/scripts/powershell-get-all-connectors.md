---
title: PowerShell-minta – az összes alkalmazásproxy-összekötő csoport listázása
description: PowerShell-példa, amely felsorolja az összes Azure Active Directory-(Azure AD-) alkalmazásproxy-összekötő csoportot és összekötőt a címtárban.
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
ms.openlocfilehash: 9ece8ab2f48981669347ea41badd3e9fbf05dc6c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548983"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>Az összes összekötő-csoport és összekötő beolvasása a címtárban

Ez a PowerShell-parancsfájl például felsorolja az összes Azure Active Directory (Azure AD) alkalmazásproxy-összekötő csoportját és összekötőjét a címtárban.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ehhez a mintához a Graph modul (AzureAD) [AzureAD v2 PowerShell](/powershell/azure/active-directory/install-adv2) -je, vagy a [Graph modul előzetes verziójának (AzureADPreview) AzureAD v2 PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) szükséges.

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>Szkript ismertetése

| Parancs | Jegyzetek |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Lekéri az összes összekötő csoport listáját, vagy ha meg van adva, a megadott összekötő-csoport részleteit. |
| [Get-AzureADApplicationProxyConnectorGroupMembers](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers) | Az egyes összekötő csoportokhoz társított összes alkalmazásproxy-összekötő beolvasása.|

## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modulról az [Azure ad PowerShell-modul áttekintése](/powershell/azure/active-directory/overview)című témakörben olvashat bővebben.

További PowerShell-példák az alkalmazásproxy esetében: [Azure ad PowerShell-példák az azure ad Application proxy](../application-proxy-powershell-samples.md).
