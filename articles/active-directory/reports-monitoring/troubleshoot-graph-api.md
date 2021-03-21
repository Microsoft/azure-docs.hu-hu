---
title: Hibák elhárítása Azure Active Directory jelentési API-ban | Microsoft Docs
description: A Azure Active Directory jelentési API-k meghívása során felmerülő hibák megoldását teszi lehetővé.
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
ms.openlocfilehash: abc8badf261e631dd6ceb7af9a6a0cb3676ae25d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017593"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Hibák elhárítása Azure Active Directory jelentési API-ban

Ez a cikk azokat a gyakori hibaüzeneteket sorolja fel, amelyekkel a Microsoft Graph API-val és a megoldásuk lépéseivel férhet hozzá a tevékenységek jelentéseihez.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 HTTP belső kiszolgálóhiba a Microsoft Graph v2-végpont elérésekor

Jelenleg nem támogatjuk a Microsoft Graph v2-végpontot – ügyeljen arra, hogy a Microsoft Graph v1-végpont használatával hozzáférhessen a tevékenység naplóihoz.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Hiba: egyik bérlő sem a B2C, sem a bérlő nem rendelkezik prémium szintű licenccel

A bejelentkezési jelentések eléréséhez Azure Active Directory Premium 1 (P1) licenc szükséges. Ha ez a hibaüzenet jelenik meg a bejelentkezések elérésekor, győződjön meg arról, hogy a bérlője Azure AD P1 licenccel rendelkezik.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Hiba: a felhasználó nem szerepel az engedélyezett szerepkörökben 

Ha ez a hibaüzenet akkor jelenik meg, amikor az API-val megpróbál hozzáférni a naplókhoz vagy a bejelentkezésekhez, győződjön meg arról, hogy a fiókja a Azure Active Directory-bérlő **biztonsági olvasójának** vagy a **jelentéskészítő olvasó** szerepkörének része. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Hiba: az alkalmazásból hiányzik a "HRE olvasása" engedély. 

Kövesse az előfeltételekben ismertetett lépéseket a [Azure Active Directory jelentési API eléréséhez](howto-configure-prerequisites-for-reporting-api.md) , hogy az alkalmazás a megfelelő engedélyekkel legyen fut. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Hiba: az alkalmazás hiányzik Microsoft Graph API "a naplózási napló összes adatszolgáltatásának olvasása" engedély

Kövesse az előfeltételekben ismertetett lépéseket a [Azure Active Directory jelentési API eléréséhez](howto-configure-prerequisites-for-reporting-api.md) , hogy az alkalmazás a megfelelő engedélyekkel legyen fut. 

## <a name="next-steps"></a>Következő lépések

[A naplózási API-referenciák használata](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
 [A bejelentkezési tevékenység jelentésének API-referenciájának használata](/graph/api/resources/signin?view=graph-rest-beta)