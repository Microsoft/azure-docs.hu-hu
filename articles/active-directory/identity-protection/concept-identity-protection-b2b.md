---
title: Identity Protection és B2B-felhasználók – Azure Active Directory
description: Az Identity Protection használata B2B-felhasználókkal, hogyan működik és ismert korlátozások
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9ec9e110c3a476c9096ae3e216c9780da0e0f49
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88949196"
---
# <a name="identity-protection-and-b2b-users"></a>Identity Protection- és B2B-felhasználók

Az Azure AD B2B együttműködés segítségével a szervezetek kockázati alapú házirendeket alkalmazhatnak a B2B-felhasználók számára az Identity Protection használatával. Ezeket a házirendeket kétféleképpen lehet konfigurálni:

- A rendszergazdák konfigurálhatják a beépített Identity Protection kockázatkezelési szabályzatokat, amelyek az összes alkalmazásra érvényesek, beleértve a vendég felhasználókat is.
- A rendszergazdák a feltételes hozzáférési szabályzatokat feltételként, a vendég felhasználókat is megadhatják a bejelentkezési kockázat alapján.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>A VÁLLALATKÖZI csoportmunka-felhasználók kockázati értékelése

A VÁLLALATKÖZI együttműködés felhasználóinak felhasználói kockázata a saját címtárában lesz kiértékelve. Ezeknek a felhasználóknak a valós idejű bejelentkezési kockázata az erőforrás-címtárban lesz kiértékelve, amikor megpróbálnak hozzáférni az erőforráshoz.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Az Identity Protection korlátai a B2B csoportmunka-felhasználók számára

Az Identity Protection az erőforrás-címtár VÁLLALATKÖZI együttműködési felhasználói számára való megvalósításának korlátai a saját címtárában meglévő identitásuk miatt korlátozottak. A fő korlátozások a következők:

- Ha a vendég felhasználó elindítja az Identity Protection felhasználói kockázati házirendjét, hogy kényszerítse a jelszó-visszaállítást, azok le lesznek **tiltva**. Ezt a blokkot az erőforrás-címtárban található jelszavak alaphelyzetbe állításának lehetősége okozza.
- **A vendég felhasználók nem jelennek meg a kockázatos felhasználók jelentésében**. Ez a láthatósági adatvesztés oka a VÁLLALATKÖZI felhasználó kezdőkönyvtárának bekövetkezett kockázatának kiértékelése.
- A rendszergazdák **nem tudják eloszlatni vagy szervizelni a kockázatos B2B csoportmunka-felhasználókat** az erőforrás-címtárban. A funkcionalitás elvesztése oka az, hogy az erőforrás-címtárban lévő rendszergazdák nem férnek hozzá a B2B felhasználó kezdőkönyvtárának.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Miért nem tudom elhárítani a kockázatos B2B csoportmunka-felhasználókat a címtárban?

A VÁLLALATKÖZI felhasználók kockázati kiértékelése és szervizelése a saját címtárában történik. Ennek a ténynek köszönhetően a vendég felhasználói nem jelennek meg a kockázatos felhasználók jelentésben az erőforrás-címtárban, és az erőforrás könyvtárában lévő rendszergazdák nem kényszerítik a biztonságos jelszó-visszaállítást a felhasználók számára.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>Mi a teendő, ha egy VÁLLALATKÖZI együttműködési felhasználót Blokkoltak a szervezeten belüli kockázatkezelési szabályzat miatt?

Ha a saját címtárában lévő kockázatos B2B-felhasználót a kockázatkezelési házirend blokkolja, a felhasználónak a saját címtárában kell kijavítania ezt a kockázatot. A felhasználók a saját címtárának biztonságos jelszó-visszaállítási szolgáltatásával javíthatják a kockázatokat. Ha a saját címtárában nincs engedélyezve az önkiszolgáló jelszó-visszaállítás, fel kell vennie a kapcsolatot a saját szervezet informatikai munkatársaival, hogy a rendszergazda manuálisan utasítsa el a kockázatot, vagy állítsa alaphelyzetbe a jelszavát.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>Hogyan meggátolja a VÁLLALATKÖZI együttműködés felhasználói számára a kockázatkezelési szabályzatok hatását?

A szervezet kockázatalapú feltételes hozzáférési házirendjeinek B2B-felhasználóinak kizárásával megakadályozható, hogy a VÁLLALATKÖZI felhasználók kiértékeljék vagy letiltsák a kockázatértékelést. A B2B-felhasználók kizárásához hozzon létre egy csoportot az Azure AD-ben, amely az összes szervezet vendég felhasználóját tartalmazza. Ezt követően adja hozzá ezt a csoportot a beépített Identity Protection-felhasználói kockázat és a bejelentkezési kockázati szabályzatok kizárásához, valamint minden olyan feltételes hozzáférési szabályzatot, amely feltételként a bejelentkezési kockázatokat használja.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő cikkeket az Azure AD B2B együttműködésről:

- [Mi az az Azure AD B2B együttműködés?](../external-identities/what-is-b2b.md)