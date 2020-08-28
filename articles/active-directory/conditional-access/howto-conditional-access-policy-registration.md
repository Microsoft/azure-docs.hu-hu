---
title: Feltételes hozzáférés – kombinált biztonsági információk – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása a biztonsági adatok regisztrálásához
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: da68e21aa279ea2503a21ce35eee52f8e49d1434
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049094"
---
# <a name="conditional-access-securing-security-info-registration"></a>Feltételes hozzáférés: biztonsági adatok regisztrációjának biztonságossá tétele

Az Azure Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás felhasználó általi regisztrálásának engedélyezése a felhasználói műveletekhez a feltételes hozzáférési házirendben. Ez az előzetes verziójú funkció olyan szervezetek számára érhető el, akik engedélyezték a [közös regisztráció előzetes](../authentication/concept-registration-mfa-sspr-combined.md)verzióját. Ez a funkció olyan szervezeteknél engedélyezhető, ahol olyan feltételekkel kívánják használni az Azure Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítást (SSPR), mint a megbízható hálózati helyekkel való hozzáférés korlátozása. A felhasználható feltételekkel kapcsolatos további információkért tekintse meg a [feltételes hozzáférés: feltételek](concept-conditional-access-conditions.md)című cikket.

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Szabályzat létrehozása megbízható helyről való regisztráció megköveteléséhez

A következő házirend az összes kijelölt felhasználóra vonatkozik, akik a közös regisztrációs élmény használatával próbálnak regisztrálni, és blokkolja a hozzáférést, kivéve, ha egy megbízható hálózatként megjelölt helyről csatlakoznak.

1. A **Azure Portal**keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférés**lehetőséget.
1. Válassza az **új szabályzat**lehetőséget.
1. A név mezőben adja meg a szabályzat nevét. Például **a megbízható hálózatokon található kombinált biztonsági adatok regisztrálása**.
1. A **hozzárendelések**területen válassza a **felhasználók és csoportok**lehetőséget, majd válassza ki azokat a felhasználókat és csoportokat, amelyekre alkalmazni szeretné a szabályzatot.

   > [!WARNING]
   > A felhasználók számára engedélyezni kell a [kombinált regisztrációt](../authentication/howto-registration-mfa-sspr-combined.md).

1. A **Cloud apps vagy műveletek**területen válassza a **felhasználói műveletek**lehetőséget, és jelölje be a **biztonsági információk regisztrálása**jelölőnégyzetet.
1. A **feltételek**  >  **helye**területen.
   1. Konfigurálja az **Igen értéket**.
   1. Adjon meg **bármilyen helyet**.
   1. **Az összes megbízható helyszín**kizárása.
   1. Válassza a **kész** lehetőséget a helyszínek panelen.
   1. Válassza a **kész** lehetőséget a feltételek panelen.
1. A **feltételek**  >  **ügyfélalkalmazások (előzetes verzió)** területen állítsa **Configure** az **Igen**értékre, majd válassza a **kész**lehetőséget.
1. A **hozzáférés-vezérlés**  >  **megadása**területen.
   1. Válassza a **hozzáférés letiltása**lehetőséget.
   1. Ezután kattintson a **Kiválasztás** elemre.
1. Állítsa a **Házirend engedélyezése** kapcsolót **Be** állásba.
1. Kattintson a **Mentés** gombra.

A szabályzat 6. lépésében a szervezeteknek döntéseket hozhatnak. A fenti szabályzatnak megbízható hálózati helyről kell regisztrálnia. A szervezetek dönthetnek úgy, hogy az összes rendelkezésre álló feltételt kihasználják a **helyszínek**helyett. Ne feledje, hogy ez a házirend egy blokkolási házirend, ezért a benne foglalt összes adat le van tiltva, és minden, ami nem felel meg a belefoglalásnak. 

Néhány esetben előfordulhat, hogy a fenti 6. lépésben a hely helyett az eszköz állapotát szeretné használni:

6. A **feltételek**  >  **eszköz állapota (előzetes verzió)**.
   1. Konfigurálja az **Igen értéket**.
   1. **Minden eszköz állapotának**belefoglalása.
   1. **Az eszköz megfelelőként megjelölt** **hibrid Azure ad** -beli és/vagy eszközének kizárása
   1. Válassza a **kész** lehetőséget a helyszínek panelen.
   1. Válassza a **kész** lehetőséget a feltételek panelen.

> [!WARNING]
> Ha az eszköz állapotát feltételként használja a házirendben, ez hatással lehet a vendég felhasználókra a címtárban. A [csak jelentési mód](concept-conditional-access-report-only.md) segíthet meghatározni a szabályzatok döntéseinek hatását.
> Vegye figyelembe, hogy a csak jelentési mód nem alkalmazható a "felhasználói műveletek" hatókörű HITELESÍTÉSSZOLGÁLTATÓI házirendek esetében.

## <a name="next-steps"></a>Következő lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[A hatás meghatározása a feltételes hozzáférésről szóló jelentés módban](howto-conditional-access-insights-reporting.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
