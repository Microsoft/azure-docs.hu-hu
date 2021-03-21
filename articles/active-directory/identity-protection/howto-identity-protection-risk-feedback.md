---
title: Kockázati visszajelzés nyújtása Azure Active Directory Identity Protection
description: Hogyan és miért érdemes visszajelzést adni az Identity Protection kockázati észleléséről.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 939ffc9e2a8fb8ce3a25dc212e3df34dc6bb2ec1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88950357"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Útmutató: kockázati visszajelzés küldése Azure AD Identity Protection

Azure AD Identity Protection lehetővé teszi, hogy visszajelzést nyújtson a kockázatértékelésről. A következő dokumentum felsorolja azokat a forgatókönyveket, amelyekkel visszajelzést szeretne küldeni a Azure AD Identity Protection kockázatértékeléséről és azok beépítéséről.

## <a name="what-is-a-detection"></a>Mi az észlelés?

Az Identity Protection-észlelés egy gyanús tevékenységnek az identitás kockázati perspektívájában való jelzése. Ezeket a gyanús tevékenységeket kockázati észlelésnek nevezzük. Ezek az identitás-alapú észlelések heurisztikus, gépi tanulás vagy partner termékekből származhatnak. Ezek az észlelések a bejelentkezési kockázat és a felhasználói kockázat meghatározására szolgálnak.

* A felhasználói kockázat azt jelenti, hogy az identitás sérült.
* A bejelentkezési kockázat azt jelenti, hogy a bejelentkezés valószínűsége sérült (például a bejelentkezést nem az identitás tulajdonosa engedélyezi).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Miért érdemes kockázati visszajelzést adni az Azure AD kockázatértékeléséhez? 

Az Azure AD kockázati visszajelzésének több oka is van:

- Az **Azure ad felhasználói vagy bejelentkezési kockázatbecslését helytelenül találta**. Például a "kockázatos bejelentkezések" jelentésben látható Bejelentkezés jóindulatú volt, és a bejelentkezéshez tartozó összes észlelés hamis pozitív volt.
- **Ellenőrizte, hogy az Azure ad felhasználói vagy bejelentkezési kockázatfelmérése helyes volt**. Például a "kockázatos bejelentkezések" jelentésben látható Bejelentkezés valóban rosszindulatú, és azt szeretné, hogy az Azure AD tudja, hogy a bejelentkezés összes észlelése igaz pozitív volt.
- Kijavította **a kockázatot a Azure ad Identity Protectionon kívüli felhasználónál** , és szeretné frissíteni a felhasználó kockázati szintjét.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Hogyan használja az Azure AD a kockázatos visszajelzést?

Az Azure AD a Visszajelzésével frissíti az alapul szolgáló felhasználó és/vagy bejelentkezés kockázatát, valamint az események pontosságát. Ez a visszajelzés segít a végfelhasználó biztonságossá tételében. Ha például a rendszer megerősíti a bejelentkezést, az Azure AD azonnal növeli a felhasználó kockázatát és a bejelentkezés összesített kockázatát (nem valós idejű kockázat) magasra. Ha ez a felhasználó a felhasználói kockázati házirendben szerepel, hogy a magas kockázatú felhasználókat kényszerítse a jelszavak biztonságos alaphelyzetbe állítására, a felhasználó a következő bejelentkezéskor automatikusan szervizelni fogja magát.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Hogyan adhatok ki kockázati visszajelzést, és mi történik a motorháztető alatt?

Az alábbi forgatókönyvek és mechanizmusok az Azure AD-re vonatkozó kockázati visszajelzéseket biztosítanak.

| Eset | Hogyan adhat visszajelzést? | Mi történik a motorháztető alatt? | Jegyzetek |
| --- | --- | --- | --- |
| **Bejelentkezés nem sérült (hamis pozitív)** <br> A "kockázatos bejelentkezések" jelentésben a kockázatos bejelentkezés [kockázati állapot = veszélyeztetett] látható, de a bejelentkezés nem sérült meg. | Válassza ki a bejelentkezést, és kattintson a "Bejelentkezés biztonságos megerősítése" elemre. | Az Azure AD áthelyezi a bejelentkezés összesített kockázatát, hogy egyik sem [kockázati állapot = megerősített biztonság; Kockázati szint (aggregált) =-], és megfordítja a felhasználói kockázatra gyakorolt hatását. | Jelenleg a "bejelentkezési biztonság megerősítése" beállítás csak a "kockázatos bejelentkezések" jelentésben érhető el. |
| **Bejelentkezés feltört (igaz pozitív)** <br> A "kockázatos bejelentkezések" jelentés a kockázatos bejelentkezési [kockázati állapot = kockázat] értéket jeleníti meg, alacsony kockázatú [kockázati szint (aggregált) = alacsony], és a bejelentkezés valóban sérült. | Válassza ki a bejelentkezést, és kattintson a "Bejelentkezés megerősítése – feltört" elemre. | Az Azure AD áthelyezi a bejelentkezés összesített kockázatát, és a kockázat a magas [kockázat állapot = megerősítve: sérült; Kockázati szint = magas]. | Jelenleg a "megerősítő bejelentkezés megerősítése" beállítás csak a "kockázatos bejelentkezések" jelentésben érhető el. |
| **Felhasználó által megsérült (igaz pozitív)** <br> A "kockázatos felhasználók" jelentés a kockázatos [kockázati állapot = kockázat] kockázati tényezőt jeleníti meg, amely alacsony kockázatú [kockázati szint = alacsony], és a felhasználót valóban feltörték. | Válassza ki a felhasználót, és kattintson a "felhasználó megerősítve" lehetőségre. | Az Azure AD áthelyezi a felhasználói kockázatot a magas [kockázati állapot = megerősített sérült; Kockázati szint = magas] és felvesz egy új észlelési "rendszergazda által megerősített felhasználó sérült". | Jelenleg a "felhasználói biztonság megerősítése" beállítás csak a "kockázatos felhasználók" jelentésben érhető el. <br> A "rendszergazda által megerősített felhasználó által megsérült" észlelés a "kockázatos felhasználók" jelentés "bejelentkezéshez nem csatolt" lapján jelenik meg. |
| **A Azure AD Identity Protectionon kívüli szervizelt felhasználó (valódi pozitív és szervizelt)** <br> A "kockázatos felhasználók" jelentés egy veszélyeztetett felhasználót mutat be, és a későbbiekben a Azure AD Identity Protectionon kívül szervizelem a felhasználót. | 1. Válassza ki a felhasználót, és kattintson a "a felhasználó által feltörtek megerősítése" gombra. (Ez a folyamat megerősíti az Azure AD-t, hogy a felhasználó valóban sérült.) <br> 2. Várjon, amíg a felhasználó "kockázati szintje" magasra mutat. (Ezúttal az Azure AD számára szükséges időt biztosít a fenti visszajelzések elvégzéséhez a kockázati motornak.) <br> 3. Válassza ki a felhasználót, és kattintson a "felhasználói kockázat elvetése" gombra. (Ez a folyamat megerősíti az Azure AD-t, hogy a felhasználó már nem sérült.) |  Az Azure AD a következő kockázatokat helyezi át a felhasználónak: nincs [kockázati állapot = elutasítva; Kockázati szint =-], és az aktív kockázattal rendelkező összes meglévő bejelentkezés kockázatának kizárja. | Ha a "felhasználói kockázat elvetése" gombra kattint, a rendszer bezárja a felhasználó és a korábbi bejelentkezések összes kockázatát. Ez a művelet nem vonható vissza. |
| **A felhasználó nem sérült (hamis pozitív)** <br> A "kockázatos felhasználók" jelentés a veszélyeztetett felhasználónál jelenik meg, de a felhasználó nem sérül. | Válassza ki a felhasználót, és kattintson a "felhasználói kockázat elvetése" gombra. (Ez a folyamat megerősíti az Azure AD-t, hogy a felhasználó nem sérült.) | Az Azure AD a következő kockázatokat helyezi át a felhasználónak: nincs [kockázati állapot = elutasítva; Kockázati szint =-]. | Ha a "felhasználói kockázat elvetése" gombra kattint, a rendszer bezárja a felhasználó és a korábbi bejelentkezések összes kockázatát. Ez a művelet nem vonható vissza. |
| Le szeretném állítani a felhasználói kockázatot, de nem vagyok biztos benne, hogy a felhasználó biztonságban van-e. | Válassza ki a felhasználót, és kattintson a "felhasználói kockázat elvetése" gombra. (Ez a folyamat megerősíti az Azure AD-t, hogy a felhasználó már nem sérült.) | Az Azure AD a következő kockázatokat helyezi át a felhasználónak: nincs [kockázati állapot = elutasítva; Kockázati szint =-]. | Ha a "felhasználói kockázat elvetése" gombra kattint, a rendszer bezárja a felhasználó és a korábbi bejelentkezések összes kockázatát. Ez a művelet nem vonható vissza. Javasoljuk, hogy a "jelszó alaphelyzetbe állítása" gombra kattintva javítsa a felhasználót, vagy kérje meg a felhasználótól a hitelesítő adatok biztonságos visszaállítását/módosítását. |

A rendszer offline állapotba állítja a felhasználói kockázat észlelésével kapcsolatos visszajelzéseket, és eltarthat egy ideig a frissítéshez. A kockázat-feldolgozási állapot oszlop a visszajelzések feldolgozásának aktuális állapotát fogja biztosítani.

![Kockázat-feldolgozási állapot a kockázatos felhasználói jelentéshez](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Következő lépések

- [Azure Active Directory Identity Protection kockázati észlelések referenciája](./concept-identity-protection-risks.md)