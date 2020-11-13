---
title: SCIM-szinkronizálás Azure Active Directory
description: Építészeti útmutató a SCIM-szinkronizálás megvalósításához Azure Active Directory használatával.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f957070ec94fc4c61089f31fe91261a2f52c4ee4
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578858"
---
# <a name="scim-synchronization-with-azure-active-directory"></a>SCIM-szinkronizálás Azure Active Directory

A rendszer a tartományok közötti Identitáskezelés (SCIM) egy nyílt szabványú protokoll, amely automatizálja az identitási tartományok és az informatikai rendszerek közötti felhasználói azonosító információk cseréjét. A SCIM biztosítja, hogy a humántőke-felügyeleti (HCM) rendszerbe felvett alkalmazottak automatikusan rendelkezzenek a Azure Active Directory (Azure AD) vagy a Windows Server Active Directory-ben létrehozott fiókokkal. A felhasználói attribútumok és profilok szinkronizálása a két rendszer között történik, így a felhasználók a felhasználói állapot vagy a szerepkör módosítása alapján frissülnek.

A SCIM a két végpont szabványosított definíciója: egy/Users végpont és egy/groups végpont. Általános REST-műveleteket használ objektumok létrehozásához, frissítéséhez és törléséhez. Egy előre definiált sémát is használ a gyakori attribútumokhoz, például a csoportnév, a Felhasználónév, az utónév, a vezetéknév és az e-mail. A SCIM 2,0 REST APIt kínáló alkalmazások csökkenthetik vagy megszüntethetik a védett felhasználói felügyeleti API-kkal vagy termékekkel való munkavégzést okozó fájdalmat. A SCIM-kompatibilis ügyfelek például létrehozhatnak egy JSON-objektum HTTP-BEJEGYZÉSét a/Users-végponton egy új felhasználói bejegyzés létrehozásához. Ahelyett, hogy némileg eltérő API-ra lenne szüksége ugyanahhoz az alapszintű műveletekhez, a SCIM szabványnak megfelelő alkalmazások azonnal kihasználhatják a meglévő ügyfeleket, eszközöket és kódokat. 

## <a name="use-when"></a>A következő esetekben használja: 

Automatikusan szeretné kiépíteni a felhasználói adatokat egy HCM rendszerből az Azure AD-be és a Windows Server-Active Directoryba, majd szükség esetén megcélozhatja a rendszereket. 

![építészeti diagram](./media/authentication-patterns/scim-auth.png)


## <a name="components-of-system"></a>A System összetevői 

* **HCM rendszer** : olyan alkalmazások és technológiák, amelyek lehetővé teszik a humántőke-felügyeleti folyamatokat, valamint a HR-folyamatokat támogató és automatizálható eljárásokat az alkalmazottak életciklusa során. 

* **Azure ad-kiépítési szolgáltatás** : a scim 2,0 protokollt használja az automatikus kiépítés számára. A szolgáltatás a SCIM-végponthoz csatlakozik az alkalmazáshoz, és a SCIM felhasználói objektum sémáját és a REST API-kat használja a felhasználók és csoportok kiépítése és kiépítése automatizálására.  

* **Azure ad** : felhasználói adattár, amely az identitások és a hozzájuk tartozó jogosultságok életciklusának kezelésére szolgál. 

* **Célrendszer** : az scim-végponttal rendelkező alkalmazás vagy rendszer, amely az Azure ad-kiépítés használatával lehetővé teszi a felhasználók és csoportok automatikus kiépítét.  

## <a name="implement-scim-with-azure-ad"></a>A SCIM megvalósítása az Azure AD-vel 

* [A kiépítés működése az Azure AD-ben ](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works)

* [A vállalati alkalmazások felhasználói fiókkal való üzembe helyezésének kezelése a Azure Portal ](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

* [SCIM-végpont létrehozása és a felhasználók üzembe helyezésének konfigurálása az Azure AD-vel  ](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups)

* [Az Azure AD-kiépítési szolgáltatás SCIM 2,0 protokolljának megfelelősége](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-scim-compatibility)

