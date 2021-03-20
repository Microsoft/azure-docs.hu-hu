---
title: Szószedet Azure Active Directory Identity Protection
description: Szószedet Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a17f52e519a84c58c9ed5b75259cc0b96d0a175
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88949174"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Szószedet Azure Active Directory Identity Protection

### <a name="at-risk-user"></a>Veszélyeztetett (felhasználó)
Egy vagy több aktív kockázati észleléssel rendelkező felhasználó. 

### <a name="atypical-sign-in-location"></a>Szokatlan bejelentkezési hely
Egy földrajzi helyről való bejelentkezés, amely nem jellemző az adott felhasználóra, a hasonló felhasználókra vagy a bérlőre.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Azure Active Directory biztonsági modulja, amely összevont nézetet biztosít a szervezet identitásait érintő kockázati észlelések és lehetséges sebezhetőségek tekintetében.

### <a name="conditional-access"></a>Feltételes hozzáférés
Az erőforrásokhoz való hozzáférés biztosítására szolgáló szabályzat. A feltételes hozzáférési szabályokat a Azure Active Directory tárolja, és az Azure AD kiértékeli az erőforráshoz való hozzáférés megadását megelőzően.  Ilyen szabályok például a hozzáférés korlátozása a felhasználói hely, az Eszközállapot vagy a felhasználói hitelesítési módszer alapján.

### <a name="credentials"></a>Hitelesítő adatok
Információ, amely tartalmazza a helyi és hálózati erőforrásokhoz való hozzáféréshez használt azonosítás azonosítását és igazolását. A hitelesítő adatok például felhasználónevek és jelszavak, intelligens kártyák és tanúsítványok.

### <a name="event"></a>Esemény
Egy tevékenység rekordja Azure Active Directoryban.

### <a name="false-positive-risk-detection"></a>Hamis pozitív (kockázati észlelés)
Egy Identity Protection-felhasználó által manuálisan beállított kockázatkezelési állapot, amely azt jelzi, hogy a rendszer megvizsgálta a kockázat észlelését, és a kockázat észlelése helytelenül van megjelölve.

### <a name="identity"></a>Identitás
Olyan személy vagy entitás, amelyet hitelesítéssel kell ellenőrizni, például jelszó vagy tanúsítvány alapján.

### <a name="identity-risk-detection"></a>Identitás kockázatának észlelése
Az Identity Protection által rendellenesként megjelölt Azure AD-esemény, amely arra utalhat, hogy az identitás biztonsága sérült.

### <a name="ignored-risk-detection"></a>Figyelmen kívül hagyva (kockázati észlelés)
Egy Identity Protection-felhasználó által manuálisan beállított kockázatkezelési állapot, amely azt jelzi, hogy a kockázatok észlelése lezárult a Szervizelési művelet végrehajtása nélkül.

### <a name="impossible-travel-from-atypical-locations"></a>Nem lehet utazni szokatlan helyekről
A kockázatkezelés akkor aktiválódik, ha ugyanahhoz a felhasználóhoz két bejelentkezést észlelt a rendszer, ahol legalább az egyik szokatlan bejelentkezési helyről van szó, és a bejelentkezések közötti idő rövidebb, mint a helyek közötti fizikai utazás minimális ideje.  

### <a name="investigation"></a>Vizsgálat
A kockázatértékeléssel kapcsolatos tevékenységek, naplók és egyéb kapcsolódó információk áttekintésének folyamata annak eldöntéséhez, hogy szükség van-e a szervizelésre vagy a kockázatcsökkentő lépésekre, meg kell ismernie, hogy az identitást és az identitást hogyan használták fel, és megértette a feltört identitás használatának módját.

### <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok
Az aktuális felhasználói hitelesítő adatok (Felhasználónév és jelszó) kiváltása akkor aktiválódik, amikor a kutatók a Dark web-ben nyilvánosan közzétették a felhasználókat.

### <a name="mitigation"></a>Kockázatcsökkentés
Olyan művelet, amely korlátozza vagy megszünteti a támadók számára a sérült identitás vagy eszköz kihasználása az identitás vagy az eszköz biztonságos állapotba való visszaállítása nélkül. A mérséklés nem oldja meg az identitáshoz vagy eszközhöz társított korábbi kockázati észleléseket.

### <a name="multi-factor-authentication"></a>Többtényezős hitelesítés
Olyan hitelesítési módszer, amelynek két vagy több hitelesítési módszerre van szüksége, ami magában foglalhatja a felhasználó által birtokolt, ilyen tanúsítványokat is. amit a felhasználó ismer, például felhasználóneveket, jelszavakat vagy pass-kifejezéseket; fizikai attribútumok, például ujjlenyomatok; és személyes attribútumok, például személyes aláírások.

### <a name="offline-detection"></a>Offline észlelés
A rendellenességek észlelése és a kockázat kiértékelése, például bejelentkezési kísérlet a tény után, egy már megtörtént esemény után.

### <a name="policy-condition"></a>Házirend-feltétel
Egy biztonsági szabályzat része, amely meghatározza az entitásokat (csoportokat, felhasználókat, alkalmazásokat, eszköz platformokat, eszköz-állapotokat, IP-tartományokat, ügyfél-típusokat) a szabályzatban, vagy ki van zárva belőle.

### <a name="policy-rule"></a>Házirend-szabály
A biztonsági szabályzat azon része, amely leírja a szabályzatot kiváltó körülményeket, valamint a szabályzat indításakor végrehajtott műveleteket.

### <a name="prevention"></a>Megelőzés
Egy olyan művelet, amely megakadályozza a szervezet sérülését egy olyan személyazonosság vagy eszköz általi visszaélés miatt, amely gyaníthatóan vagy ismeri a veszélyt. A megelőzési műveletek nem védik az eszközt vagy az identitást, és nem oldják fel a korábbi kockázati észleléseket.

### <a name="privileged-user"></a>Kiemelt jogosultságú (felhasználó)
A kockázatok észlelésének időpontjában a felhasználó állandó vagy ideiglenes rendszergazdai engedéllyel rendelkezett a Azure Active Directory egy vagy több erőforrásához, például a globális rendszergazda, a számlázási rendszergazda, a szolgáltatás-rendszergazda, a felhasználói rendszergazda és a jelszó-rendszergazda számára. 

### <a name="real-time"></a>Valós idejű
Lásd: valós idejű észlelés.

### <a name="real-time-detection"></a>Valós idejű észlelés
A rendellenességek észlelése és a kockázat kiértékelése olyan eseményeknél, mint például a bejelentkezési kísérlet, mielőtt az esemény továbblép.

### <a name="remediated-risk-detection"></a>Szervizelés (kockázat észlelése)
Az Identity Protection automatikusan beállítja a kockázatkezelési állapotot, ami azt jelzi, hogy a kockázat észlelését a rendszer az ilyen típusú kockázati észleléshez használt standard szervizelési művelettel szervizelte. Ha például a rendszer alaphelyzetbe állítja a felhasználói jelszót, számos olyan kockázati észlelést észlel, amely azt jelzi, hogy az előző jelszó biztonsága megsérül.

### <a name="remediation"></a>Szervizelés
Egy olyan művelet, amely olyan identitás vagy eszköz védelmét teszi biztonságossá, amely korábban gyanúba esett vagy ismert volt. Egy szervizelési művelet visszaállítja az identitást vagy az eszközt egy biztonságos állapotba, és feloldja az identitáshoz vagy eszközhöz társított korábbi veszélyforrások észlelését.

### <a name="resolved-risk-detection"></a>Megoldott (kockázati észlelés)
Egy Identity Protection-felhasználó által manuálisan beállított kockázatkezelési állapot, amely azt jelzi, hogy a felhasználó megfelelő szervizelési műveletet végzett az Identity Protectionn kívül, és a kockázat észlelését zártnak kell tekinteni.

### <a name="risk-detection-status"></a>Kockázatészlelés állapota
Egy kockázatkezelési tulajdonság, amely azt jelzi, hogy az esemény aktív-e, és ha be van zárva, a Bezárás oka.

### <a name="risk-detection-type"></a>Kockázat észlelésének típusa
A kockázat észlelésének kategóriája, amely azt jelzi, hogy milyen típusú anomália okozta az eseményt kockázatos.

### <a name="risk-level-risk-detection"></a>Kockázati szint (kockázati észlelés)
A kockázat észlelésének súlyossága (magas, közepes vagy alacsony), amely segíti az Identity Protection-felhasználók számára, hogy a szervezetük kockázatának csökkentése érdekében az általuk végrehajtott műveleteket rangsorolják. 

### <a name="risk-level-sign-in"></a>Kockázati szint (bejelentkezés)
Egy adott bejelentkezés valószínűségének (magas, közepes vagy alacsony) jele, hogy valaki más megpróbálja használni a felhasználó identitását.

### <a name="risk-level-user-compromise"></a>Kockázati szint (felhasználói kompromisszum)
Az identitás veszélyének valószínűsége (magas, közepes vagy alacsony).

### <a name="risk-level-vulnerability"></a>Kockázati szint (sebezhetőség)
A biztonsági rés súlyossági szintjének (magas, közepes vagy alacsony) jelzése, hogy az Identity Protection-felhasználók rangsorolják a szervezete kockázatának csökkentése érdekében végrehajtott műveleteket.

### <a name="secure-identity"></a>Biztonságos (identitás)
A feltört identitást nem sérült állapotra állíthatja vissza, például a jelszó módosítása vagy a számítógép-áttelepítési művelet.

### <a name="security-policy"></a>Biztonsági szabályzat
Házirend-szabályok és feltételek gyűjteménye. A szabályzatok alkalmazhatók olyan entitásokra, mint például a felhasználók, a csoportok, az alkalmazások, az eszközök, az eszközök platformok, az eszközök állapotai, az IP-tartományok és az Auth 2.0-ügyfél típusa. Ha a szabályzat engedélyezve van, akkor a rendszer akkor értékeli ki, ha a házirendben szereplő entitás jogkivonatot ad ki egy erőforráshoz.

### <a name="sign-in-v"></a>Bejelentkezés (v)
Azure Active Directory identitásának hitelesítése.

### <a name="sign-in-n"></a>Bejelentkezés (n)
A Azure Active Directory identitás hitelesítésének folyamata vagy művelete, valamint a műveletet rögzítő esemény.

### <a name="sign-in-from-anonymous-ip-address"></a>Bejelentkezés névtelen IP-címről
Egy, a névtelen proxy IP-címeként azonosított IP-címről érkező sikeres bejelentkezés után kiváltott kockázat észlelése.

### <a name="sign-in-from-infected-device"></a>Bejelentkezés fertőzött eszközről
A kockázatkezelés akkor aktiválódik, ha a bejelentkezés egy IP-címről származik, amelyet egy vagy több olyan sérült eszköz használ, amely aktívan próbálkozik a bot-kiszolgálóval való kommunikációra.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Bejelentkezés gyanús tevékenységű IP-címről
A kockázatok észlelése egy olyan IP-címről sikeres bejelentkezés után váltott ki, amely a sikertelen bejelentkezési kísérleteket több felhasználói fiókra vonatkozóan rövid idő alatt elvégezte.

### <a name="sign-in-from-unfamiliar-location"></a>Bejelentkezés ismeretlen helyről
A kockázatkezelés akkor aktiválódik, amikor egy felhasználó sikeresen bejelentkezik egy új helyről (IP-cím, szélesség/hosszúság és ASN).

### <a name="sign-in-risk"></a>Bejelentkezési kockázat
Lásd: kockázati szint (bejelentkezés)

### <a name="sign-in-risk-policy"></a>Bejelentkezési kockázati szabályzat
Egy feltételes hozzáférési szabályzat, amely kiértékeli a kockázatot egy adott bejelentkezésre, és az előre meghatározott feltételek és szabályok alapján alkalmazza a kockázatcsökkentő tényezőket.

### <a name="user-compromise-risk"></a>Felhasználói kompromisszumos kockázat
Lásd: kockázati szint (felhasználói kompromisszum)

### <a name="user-risk"></a>Felhasználói kockázat
Lásd: kockázati szint (felhasználói kompromisszum).

### <a name="user-risk-policy"></a>Felhasználói kockázati szabályzat
Feltételes hozzáférési szabályzat, amely figyelembe veszi a bejelentkezést, és az előre meghatározott feltételek és szabályok alapján alkalmazza a korlátozásokat.

### <a name="users-flagged-for-risk"></a>Kockázatosként megjelölt felhasználók
Azok a felhasználók, akiknek aktív vagy szervizelt kockázati észlelésük van

### <a name="vulnerability"></a>Biztonsági rés
A Azure Active Directory konfigurációját vagy feltételét, amely a könyvtárat kihasználja a biztonsági rések vagy fenyegetések ellen.

## <a name="see-also"></a>Lásd még

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)