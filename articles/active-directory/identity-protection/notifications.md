---
title: Az Azure Active Directory Identity Protection-értesítések |} A Microsoft Docs
description: Ismerje meg, hogyan támogatják a különböző értesítések a vizsgálati tevékenységet.
services: active-directory
keywords: az Azure active directory identity protection a következőket cloud app discovery szolgáltatást, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend kezelése
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0065ec03695ee977133ae2ec43aafba7d5bfff78
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784348"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Az Azure Active Directory Identity Protection-értesítések

Az Azure AD Identity Protection küld automatizált értesítő e-mailek, amelyek segítségével kezelheti a felhasználói kockázat és a kockázati események két típusa:

- Érintett felhasználók e-mail észlelt
- Heti összefoglaló e-mail

Ez a cikk mindkét értesítő e-mailek áttekintést nyújt.


## <a name="users-at-risk-detected-email"></a>Érintett felhasználók e-mail észlelt

Egy észlelt kockázati-fiókot választ, az Azure AD Identity Protection az e-mailek riasztást állít elő **észlelt kockázatos felhasználókat** tulajdonosaként. Az e-mail tartalmaz egy hivatkozást a **[kockázatosként megjelölt felhasználók](../reports-monitoring/concept-user-at-risk.md)** jelentést. Ajánlott eljárásként azonnal kell vizsgálni az érintett felhasználók.

Ez a riasztás konfigurációját adja meg, milyen felhasználói kockázati szinten a riasztást generáló teszi lehetővé. Az e-mailt fog készülni, amikor a felhasználó kockázati szint eléri a mi megadott; azonban nem fog kapni, a felhasználó esetében észlelt kockázati e-mailes riasztásokhoz új felhasználók azokat a felhasználói kockázat szintjének való áthelyezése után. Például ha a riasztást a közepes felhasználói kockázat és John Közepes kockázatot jelentő helyezi át a felhasználói házirend, kapni fog e-mail észlelt kockázattal érintett felhasználókról John a. Azonban nem észlelt kockázati a riasztás egy második felhasználókról fog kapni, ha John majd magas kockázatú helyezi át, vagy további kockázati események.

![Érintett felhasználók e-mail észlelt](./media/notifications/01.png)


### <a name="configuration"></a>Konfiguráció

A rendszergazdák állíthatja be:

- **A felhasználói kockázat szintje, amely az e-mailt generációja** – alapértelmezés szerint a kockázati szintje "Nagy" kockázatot.
- **Ez az e-mail címzettjeinek** – alapértelmezés szerint a címzettek minden globális rendszergazdát tartalmazza. A globális rendszergazdák más a globális rendszergazdák, biztonsági rendszergazdák, biztonsági olvasók, a címzettek is hozzáadhat.  


A kapcsolódó párbeszédpanel megnyitásához kattintson a **riasztások** a a **beállítások** szakaszában a **Identity Protection** lapot.

![Érintett felhasználók e-mail észlelt](./media/notifications/05.png)


## <a name="weekly-digest-email"></a>Heti összefoglaló e-mail

Heti összefoglaló e-mail új kockázati események összegzését tartalmazza.  
Ezek a következők:

- Érintett felhasználók

- Gyanús tevékenységek feldolgozása

- Észlelt biztonsági rések

- A vonatkozó jelentéseket az Identity Protection mutató hivatkozások

    ![Szervizelési](./media/notifications/400.png "szervizelés")

### <a name="configuration"></a>Konfiguráció

A rendszergazdák válthat egy heti összefoglaló e-mail küldése.

![Felhasználói kockázat](./media/notifications/62.png "felhasználói kockázat")

A kapcsolódó párbeszédpanel megnyitásához kattintson a **Heti összefoglaló** a a **beállítások** szakaszában a **Identity Protection** lapot.

![Érintett felhasználók e-mail észlelt](./media/notifications/04.png)


## <a name="see-also"></a>Lásd még

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
