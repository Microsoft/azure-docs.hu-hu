---
title: Kockázati észlelések szimulálása a Azure AD Identity Protectionban
description: Ismerje meg, hogyan szimulálhatja a kockázati észleléseket az Identity Protectionben
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
ms.openlocfilehash: 7eafeaf59757fcda978fa89b4bc2f9882b769e48
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835900"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>A kockázatok észlelésének szimulálása az Identity Protectionben

Előfordulhat, hogy a rendszergazdák szimulálni szeretnék a környezetet a környezetében a következő elemek megvalósítása érdekében:

- A kockázati észlelések és biztonsági rések szimulálásával feltöltheti az adatokat az Identity Protection-környezetben.
- Hozzon létre kockázatalapú feltételes hozzáférési szabályzatokat, és tesztelje a házirendek hatását.

Ez a cikk a következő kockázati észlelési típusok szimulálása lépéseit ismerteti:

- Névtelen IP-cím (egyszerű)
- Ismeretlen bejelentkezési tulajdonságok (mérsékelt)
- Atipikus utazás (nehéz)

Az egyéb kockázati észleléseket nem lehet biztonságos módon szimulálni.

A kockázatok észlelésével kapcsolatos további információkért tekintse meg a következő cikket: [Mi a kockázat](concept-identity-protection-risks.md).

## <a name="anonymous-ip-address"></a>Névtelen IP-cím

Az alábbi eljárás végrehajtásához a következőket kell használnia:

- A [Tor böngésző](https://www.torproject.org/projects/torbrowser.html.en) névtelen IP-címeket szimulál. Előfordulhat, hogy egy virtuális gépet kell használnia, ha a szervezet a Tor böngésző használatával korlátozza a használatát.
- Olyan tesztelési fiók, amely még nincs regisztrálva az Azure AD-Multi-Factor Authentication.

**Egy névtelen IP-címről történő bejelentkezés szimulálása érdekében hajtsa végre a következő lépéseket**:

1. A [Tor-böngésző](https://www.torproject.org/projects/torbrowser.html.en)használatával navigáljon a következőhöz: [https://myapps.microsoft.com](https://myapps.microsoft.com) .   
2. Adja meg annak a fióknak a hitelesítő adatait, amelyet a **Névtelen IP-címek jelentésből származó bejelentkezésekhez** szeretne megjeleníteni.

A bejelentkezés az Identity Protection-irányítópulton 10-15 percen belül megjelenik. 

## <a name="unfamiliar-sign-in-properties"></a>Szokatlan bejelentkezési tulajdonságok

Ismeretlen helyek szimulálásához be kell jelentkeznie egy helyről, és az eszközön a teszt fiók nem jelentkezett be korábban.

Az alábbi eljárás egy újonnan létrehozott műveletet használ:

- VPN-kapcsolat, hogy szimulálja az új helyet.
- Virtuális gép, amely új eszközt szimulál.

Az alábbi eljárás végrehajtásához olyan felhasználói fiókot kell használnia, amely a következő:

- Legalább egy 30 napos bejelentkezési előzmény.
- Az Azure AD Multi-Factor Authentication engedélyezve.

**Ha a bejelentkezést ismeretlen helyről szeretné szimulálni, hajtsa végre a következő lépéseket**:

1. Ha a teszt fiókjával jelentkezik be, a többtényezős hitelesítés (MFA) kihívása nem halad át az MFA-kérdésben.
2. Az új VPN használatával lépjen a be, [https://myapps.microsoft.com](https://myapps.microsoft.com) és adja meg a teszt fiókjának hitelesítő adatait.

A bejelentkezés az Identity Protection-irányítópulton 10-15 percen belül megjelenik.

## <a name="atypical-travel"></a>Szokatlan utazás

Az atipikus utazási feltételek szimulálása nehézkes, mert az algoritmus gépi tanulást használ a hamis pozitív állapotok kiszűrésére, például az ismerős eszközökről való atipikus utazásra vagy a címtárban lévő más felhasználók által használt VPN-ről való bejelentkezésre. Emellett az algoritmushoz a felhasználók 14 napos bejelentkezési előzményeit és 10 bejelentkezést kell megadnia, mielőtt megkezdené a kockázati észlelések létrehozását. Az összetett gépi tanulási modellek és a fenti szabályok miatt előfordulhat, hogy az alábbi lépések nem vezetnek a kockázati észleléshez. Előfordulhat, hogy ezeket a lépéseket több Azure AD-fiók esetében is replikálni szeretné az észlelés szimulálása érdekében.

Az **alábbi lépéseket követve szimulálhatja az utazási kockázatok szokatlan észlelését**:

1. A normál böngészővel nyissa meg a következőt: [https://myapps.microsoft.com](https://myapps.microsoft.com) .  
2. Adja meg annak a fióknak a hitelesítő adatait, amelynél atipikus utazási kockázatos észlelést szeretne készíteni.
3. Módosítsa a felhasználói ügynököt. A Microsoft Edge-ben megváltoztathatja a felhasználói ügynököt Fejlesztői eszközök (F12) használatával.
4. Módosítsa az IP-címet. Az IP-címet VPN-lel, Tor-bővítménysel vagy egy másik adatközpontban egy új virtuális gép létrehozásával módosíthatja.
5. Jelentkezzen be, hogy [https://myapps.microsoft.com](https://myapps.microsoft.com) ugyanazokat a hitelesítő adatokat használja, mint a korábbi és az előző bejelentkezést követő néhány percen belül.

A bejelentkezés 2-4 órán belül megjelenik az Identity Protection irányítópultján.

## <a name="testing-risk-policies"></a>Kockázati házirendek tesztelése

Ez a szakasz a következő cikkben ismertetett módon ismerteti a felhasználó és a bejelentkezési kockázati szabályzatok tesztelésének lépéseit [: a kockázatkezelési házirendek konfigurálása és engedélyezése](howto-identity-protection-configure-risk-policies.md).

### <a name="user-risk-policy"></a>Felhasználói kockázati szabályzat

A felhasználói kockázatokra vonatkozó biztonsági házirend teszteléséhez hajtsa végre a következő lépéseket:

1. Navigáljon a [Azure Portal](https://portal.azure.com).
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **áttekintést**.
1. Válassza a **felhasználói kockázati házirend konfigurálása** lehetőséget.
   1. A **hozzárendelések** alatt
      1. **Felhasználók** – válassza a **minden felhasználó** lehetőséget **, vagy válassza az egyének és csoportok lehetőséget,** ha korlátozza a bevezetést.
         1. Opcionálisan dönthet úgy is, hogy kizárja a felhasználókat a szabályzatból.
      1. **Feltételek**  -  **Felhasználói kockázat** A Microsoft javaslata, hogy ezt a beállítást **magas** értékre állítsa.
   1. A **vezérlők** területen
      1. **Hozzáférés** – a Microsoft javaslata a **hozzáférés engedélyezése** és a **jelszó megkövetelése**.
   1. Szabályzat érvénybe **léptetése**  -  **Kikapcsolva**
   1. **Mentés** – ez a művelet visszaküldi az **Áttekintés** oldalra.
1. Megemelheti a tesztelési fiók felhasználói kockázatát, például szimulálva az egyik kockázati észlelést néhányszor.
1. Várjon néhány percet, majd ellenőrizze, hogy a kockázat emelt-e a felhasználó számára. Ha nem, Szimuláljon nagyobb kockázati észleléseket a felhasználó számára.
1. Térjen vissza a kockázatkezelési szabályzathoz, és állítsa be a **házirend-** módosítást **, és** **mentse** a szabályzatot.
1. Most már tesztelheti a felhasználó kockázat alapú feltételes hozzáférését úgy, hogy bejelentkezik egy emelt szintű kockázati szinttel rendelkező felhasználó használatával.

### <a name="sign-in-risk-security-policy"></a>Bejelentkezési kockázatokra vonatkozó biztonsági házirend

A bejelentkezési kockázati szabályzat teszteléséhez hajtsa végre a következő lépéseket:

1. Navigáljon a [Azure Portal](https://portal.azure.com).
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **áttekintést**.
1. Válassza a **bejelentkezési kockázati házirend konfigurálása** lehetőséget.
   1. A **hozzárendelések** alatt
      1. **Felhasználók** – válassza a **minden felhasználó** lehetőséget **, vagy válassza az egyének és csoportok lehetőséget,** ha korlátozza a bevezetést.
         1. Opcionálisan dönthet úgy is, hogy kizárja a felhasználókat a szabályzatból.
      1. **Feltételek**  -  **Bejelentkezési kockázat** A Microsoft javaslata, hogy ezt a beállítást **közepes és újabb** értékre állítsa be.
   1. A **vezérlők** területen
      1. **Hozzáférés** – a Microsoft javaslata, hogy **engedélyezze a hozzáférést** és a **többtényezős hitelesítés megkövetelését**.
   1. Szabályzat érvénybe **léptetése**  -  **Bekapcsolva**
   1. **Mentés** – ez a művelet visszaküldi az **Áttekintés** oldalra.
1. A bejelentkezési kockázat alapú feltételes hozzáférést mostantól kockázatos munkamenet használatával is tesztelheti (például a Tor böngésző használatával). 

## <a name="next-steps"></a>Következő lépések

- [Mit jelent a kockázat?](concept-identity-protection-risks.md)

- [Útmutató: kockázatkezelési szabályzatok konfigurálása és engedélyezése](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory Identity Protection](overview-identity-protection.md)
