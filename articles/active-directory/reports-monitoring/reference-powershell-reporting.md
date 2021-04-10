---
title: Azure AD PowerShell-parancsmagok jelentéskészítéshez | Microsoft Docs
description: Az Azure AD PowerShell-parancsmagok referenciája a jelentéskészítéshez.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 329036f3ed815eaaba94f441e372f4be86edd629
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558083"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Az Azure AD PowerShell-parancsmagjai jelentéskészítéshez

> [!NOTE] 
> Ezek a PowerShell-parancsmagok jelenleg csak az [Azure ad Preview](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#directory_auditing) modullal működnek. Vegye figyelembe, hogy az előzetes verziójú modul éles használatra nem ajánlott. 

A nyilvános előzetes kiadás telepítéséhez használja az alábbi parancsot. 

```powershell
Install-module AzureADPreview
```

Ha többet szeretne megtudni arról, hogyan csatlakozhat az Azure AD-hez a PowerShell használatával, tekintse meg az [Azure ad PowerShell a graphhoz](/powershell/azure/active-directory/install-adv2)című cikket.  

A Azure Active Directory-(Azure AD-) jelentésekkel részletes információkat kaphat az összes írási műveletről (napló) és a hitelesítési adatokról (bejelentkezési naplók). Bár az adatok az MS Graph API használatával érhetők el, mostantól az Azure AD PowerShell-parancsmagokkal is lekérheti ugyanezeket az adatokat a jelentéskészítéshez.

Ez a cikk áttekintést nyújt a naplókhoz és a bejelentkezési naplókhoz használandó PowerShell-parancsmagokról.

## <a name="audit-logs"></a>Naplók

A [naplók nyomon követést biztosítanak](concept-audit-logs.md) az Azure ad különböző funkciói által végzett összes módosítás naplójában. A naplók például a felhasználók, alkalmazások, csoportok, szerepkörök és házirendek hozzáadásával vagy eltávolításával kapcsolatos, az Azure AD-ban található összes erőforráson végrehajtott módosítások.

A naplókhoz a Get-AzureADAuditDirectoryLogs parancsmag használatával férhet hozzá.


| Eset                      | PowerShell-parancs |
| :--                           | :--                |
| Alkalmazás megjelenítendő neve      | Get-AzureADAuditDirectoryLogs szűrő: "initiatedBy/app/displayName EQ" Azure AD Cloud Sync "" |
| Kategória                      | Get-AzureADAuditDirectoryLogs szűrő "Category EQ" ApplicationManagement "" |
| Tevékenység dátumának időpontja            | Get-AzureADAuditDirectoryLogs szűrő: "activityDateTime gt 2019-04-18" |
| A fentiek mindegyike              | Get-AzureADAuditDirectoryLogs Filter "initiatedBy/app/displayName EQ" Azure AD Cloud Sync "és category EQ" ApplicationManagement "és activityDateTime gt 2019-04-18"|


Az alábbi képen egy példa látható erre a parancsra. 

![A képernyőképen a D naplózási könyvtár naplójának Get-Azure eredménye látható.](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Bejelentkezési naplók

A [bejelentkezési](concept-sign-ins.md) naplók a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról nyújtanak információt.

A bejelentkezési naplókhoz a Get-AzureADAuditSignInLogs parancsmag használatával férhet hozzá.


| Eset                      | PowerShell-parancs |
| :--                           | :--                |
| Felhasználó megjelenítendő neve             | Get-AzureADAuditSignInLogs szűrő "userDisplayName EQ" Timothy Perkins "" |
| Létrehozás dátuma és időpontja              | Get-AzureADAuditSignInLogs szűrő "createdDateTime gt 2019-04-18T17:30:00.0 Z" (minden, 5:30 PM, 4/18) |
| Állapot                        | Get-AzureADAuditSignInLogs szűrő "Status/errorCode EQ 50105" |
| Alkalmazás megjelenítendő neve      | Get-AzureADAuditSignInLogs szűrő "appDisplayName EQ" StoreFrontStudio [wsfed enabled] "" |
| A fentiek mindegyike              | Get-AzureADAuditSignInLogs szűrő "userDisplayName EQ" Timothy Perkins "és status/errorCode ne 0 és appDisplayName EQ" StoreFrontStudio [wsfed enabled] "" |


Az alábbi képen egy példa látható erre a parancsra. 

![A képernyőképen a D naplózási naplók parancs Get-Azure eredménye látható.](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Következő lépések

- [Azure ad-jelentések – áttekintés](overview-reports.md).
- [Naplózott jelentés](concept-audit-logs.md). 
- [Programozott hozzáférés az Azure AD-jelentésekhez](concept-reporting-api.md)
