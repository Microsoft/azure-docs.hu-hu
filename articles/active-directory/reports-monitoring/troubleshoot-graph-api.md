---
title: A jelentéskészítési API Azure Active Directory hibáinak elhárítása | Microsoft Docs
description: Megoldást kínál a Reporting API-k Azure Active Directory során előforduló hibákra.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 445514297550210d80cd50895201d1129fac4f20
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533711"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Az Azure Active Directory Reporting API hibáinak elhárítása

Ez a cikk azokat a gyakori hibaüzeneteket sorolja fel, amelyek akkor jelenhetnek meg, amikor a Microsoft Graph API-val fér hozzá a tevékenységjelentéshez, valamint a megoldásuk lépéseit.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500-as belső HTTP-kiszolgálóhiba a V2 Microsoft Graph elérésekor

Jelenleg nem támogatjuk a Microsoft Graph v2 végpontot – győződjön meg arról, hogy a tevékenységnaplókat a v1 Microsoft Graph használja.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Hiba: Egyik bérlő sem B2C, vagy a bérlő nem rendelkezik prémium szintű licenccel

A bejelentkezési jelentések eléréséhez prémium Azure Active Directory 1-es (P1) licenc szükséges. Ha ezt a hibaüzenetet látja a bejelentkezések elérésekor, győződjön meg arról, hogy a bérlője rendelkezik Azure AD P1 licenccel.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Hiba: A felhasználó nem az engedélyezett szerepkörökben van 

Ha ezt a hibaüzenetet látja, amikor az API-val próbál hozzáférni az auditnaplókhoz  vagy  bejelentkezésekhez, győződjön meg arról, hogy a fiókja része a biztonsági olvasó vagy a jelentésolvasó szerepkörnek a Azure Active Directory bérlőben. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Hiba: Az alkalmazásból hiányzik az AAD "Címtáradatok olvasása" engedélye 

Kövesse az Előfeltételek lépéseit a [Azure Active Directory Reporting API](howto-configure-prerequisites-for-reporting-api.md) eléréséhez, hogy az alkalmazás a megfelelő engedélyekkel fut-e. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Hiba: Az alkalmazásból hiányzik Microsoft Graph API "Az összes auditnapló-adat olvasása" engedély

Kövesse az Előfeltételek lépéseit a [Azure Active Directory Reporting API](howto-configure-prerequisites-for-reporting-api.md) eléréséhez, hogy az alkalmazás a megfelelő engedélyekkel fut-e. 

## <a name="next-steps"></a>Következő lépések

[Az audit API-referencia használata](/graph/api/resources/directoryaudit) 
 [A bejelentkezési tevékenység jelentésének API-referenciájának használata](/graph/api/resources/signin)