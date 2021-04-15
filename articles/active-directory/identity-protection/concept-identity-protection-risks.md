---
title: Mit jelent a kockázat? Azure AD Identity Protection
description: A Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87516ddcce32ab205b13139c057a2ab999146b74
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376358"
---
# <a name="what-is-risk"></a>Mit jelent a kockázat?

A szolgáltatás kockázatészlelése Azure AD Identity Protection a címtárban található felhasználói fiókokkal kapcsolatos azonosított gyanús műveleteket is.

Az Identity Protection hatékony erőforrásokhoz biztosít hozzáférést a szervezetek számára, hogy gyorsan látják és reagáljanak ezekre a gyanús műveletekre. 

![A kockázatos felhasználókat és bejelentkezéseket bemutató biztonsági áttekintés](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

> [!NOTE]
> Az Identity Protection csak akkor hoz létre kockázatészlelést, ha a megfelelő hitelesítő adatokat használja. Ha helytelen hitelesítő adatokat használ a bejelentkezéshez, az nem jelenti a hitelesítő adatok biztonságának sérülésének kockázatát.

## <a name="risk-types-and-detection"></a>Kockázattípusok és észlelés

Kétféle kockázattípus létezik:  **Felhasználó** és Bejelentkezés, valamint kétféle észlelési vagy számítási **valós** idejű és **offline**.

Előfordulhat, hogy a valós idejű észlelések 5–10 percig nem hatnak a jelentéskészítésre. Előfordulhat, hogy az offline észlelések nem 2–24 órán át hatnak a jelentéskészítésre.

### <a name="user-risk"></a>Felhasználói kockázat

A felhasználói kockázat annak a valószínűségét jelzi, hogy egy adott identitás vagy fiók biztonsága sérült. 

Ezeket a kockázatokat kapcsolat nélküli üzemmódban számítjuk ki a Microsoft belső és külső fenyegetés-felderítési forrásainak használatával, beleértve a biztonsági kutatókat, a rendészeti szakembereket, a Microsoft biztonsági csapatait és más megbízható forrásokat.

| Kockázatészlelés | Leírás |
| --- | --- |
| Kiszivárgott hitelesítő adatok | Ez a kockázatészlelési típus azt jelzi, hogy a felhasználó érvényes hitelesítő adatai kiszivárogtak. Amikor a kiberbűnözés feltöri a szabályos felhasználók érvényes jelszavait, gyakran megosztják ezeket a hitelesítő adatokat. Ez a megosztás általában úgy történik, hogy nyilvánosan közzétenünk a sötét weben, beillesztünk webhelyeket, vagy a hitelesítő adatokkal a fekete piacon értékesítünk. Amikor a Microsoft kiszivárogt hitelesítő adatokat szolgáltatás felhasználói hitelesítő adatokat szerez be a dark webről, webhelyeket vagy más forrásokat illeszt be, a rendszer ellenőrzi, hogy az Azure AD-felhasználók aktuális érvényes hitelesítő adataival keresnek-e érvényes egyezéseket. További információ a kiszivárgott hitelesítő adatokról: [Gyakori kérdések.](#common-questions) |
| Azure AD Intelligens veszélyforrás-felderítés | Ez a kockázatészlelési típus olyan felhasználói tevékenységet jelez, amely szokatlan az adott felhasználónál, vagy a Microsoft belső és külső fenyegetésészlelési forrásai alapján konzisztens az ismert támadási mintákkal. |

### <a name="sign-in-risk"></a>Bejelentkezési kockázat

A bejelentkezési kockázat annak a valószínűségét jelenti, hogy az identitás tulajdonosa nem engedélyezi egy adott hitelesítési kérést. 

Ezek a kockázatok valós időben vagy offline kiszámíthatóak a Microsoft belső és külső fenyegetés-felderítési forrásaival, beleértve a biztonsági kutatókat, a rendészeti szakembereket, a Microsoft biztonsági csapatait és más megbízható forrásokat.

| Kockázatészlelés | Észlelés típusa | Leírás |
| --- | --- | --- |
| Névtelen IP-cím | Valós idejű | Ez a kockázatészlelési típus névtelen IP-címről (például Tor böngészőből vagy névtelen VPN-ről) származó bejelentkezéseket jelez. Ezeket az IP-címeket általában olyan aktak használják, akik el szeretnék rejteni a bejelentkezési telemetriai adataikat (IP-cím, hely, eszköz stb.) potenciálisan rosszindulatú szándék miatt. |
| Szokatlan utazás | Offline | Ez a kockázatészlelési típus két, földrajzilag távoli helyről származó bejelentkezést azonosít, amelyek közül legalább az egyik a korábbi viselkedés alapján a felhasználó számára is atipikus lehet. Ez a gépi tanulási algoritmus számos más tényező mellett figyelembe veszi a két bejelentkezés és a felhasználónak az első helyről a másodikra való utazása közötti időt, ami azt jelzi, hogy egy másik felhasználó ugyanazt a hitelesítő adatokat használja. <br><br> Az algoritmus figyelmen kívül hagyja a nyilvánvaló "téves riasztásokat", amelyek hozzájárulnak a lehetetlen utazási körülményekhez, például a VPN-hez és a szervezet más felhasználói által rendszeresen használt helyekhez. A rendszer kezdeti tanulási időszaka a legkorábbi 14 nap vagy 10 bejelentkezés, amely során megtanulja az új felhasználó bejelentkezési viselkedését. |
| kártevő szoftverrel összekapcsolt IP-cím | Offline | Ez a kockázatészlelési típus olyan, kártevővel fertőzött IP-címekről indított bejelentkezéseket jelez, amelyekről ismert, hogy aktívan kommunikálnak a robotkiszolgálóval. Ezt az észlelést a felhasználó eszközének IP-címei és a robotkiszolgáló aktív használata közben egy robotkiszolgálóval kapcsolatban lévő IP-címek határozzák meg. |
| Szokatlan bejelentkezési tulajdonságok | Valós idejű | Ez a kockázatészlelési típus a korábbi bejelentkezési előzményeket (IP, Szélesség/ hosszúság és ASN) veszi figyelembe a rendellenes bejelentkezések azonosításához. A rendszer a felhasználó által használt korábbi helyek adatait tárolja, és ezeket a "ismerős" helyeket veszi figyelembe. A kockázatészlelés akkor aktiválódik, ha a bejelentkezés olyan helyről történik, amely még nem szerepel a jól ismert helyek listájában. Az újonnan létrehozott felhasználók egy ideig "tanulási módban" lesznek, amikor az ismeretlen bejelentkezési tulajdonságok kockázatészlelései ki lesznek kapcsolva, miközben az algoritmusok megismerik a felhasználó viselkedését. A tanulási mód időtartama dinamikus, és attól függ, hogy mennyi időt vesz igénybe az algoritmus, hogy elegendő információt gyűjtsön a felhasználó bejelentkezési mintáiról. A minimális időtartam öt nap. A felhasználó hosszú tétlenség után visszamehet a tanulási módba. A rendszer figyelmen kívül hagyja a jól ismert eszközökről és az ismerős helyekhez földrajzilag közeli helyekről való bejelentkezéseket is. <br><br> Ezt az észlelést alapszintű hitelesítéshez (vagy örökölt protokollokhoz) is futtatjuk. Mivel ezek a protokollok nem rendelkeznek olyan modern tulajdonságokkal, mint az ügyfél-azonosító, a téves riasztások számának csökkentése érdekében korlátozott telemetria áll rendelkezésre. Javasoljuk ügyfeleinknek, hogy lépjenek át a modern hitelesítésre. |
| rendszergazda által megerősített fiókfeltörés | Offline | Ez az észlelés azt jelzi, hogy egy rendszergazda a Kockázatos felhasználók felhasználói felületen vagy a RiskyUsers API használatával a "Felhasználó sérült biztonságának megerősítése" beállítást választotta. Annak ellenőrzéséhez, hogy melyik rendszergazda erősítette meg a felhasználó biztonságának sérülését, tekintse meg a felhasználó kockázati előzményeit (a felhasználói felületen vagy az API-n keresztül). |
| rosszindulatú IP-cím | Offline | Ez az észlelés kártékony IP-címről való bejelentkezést jelez. Az IP-címek az IP-címről vagy más IP-hírnévforrásokból kapott érvénytelen hitelesítő adatok miatt nagy hibai arányok miatt rosszindulatúnak minősülnek. |
| gyanús üzenetkezelési szabályok | Offline | Ezt az észlelést a Microsoft Cloud App Security [(MCAS) felderíti.](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules) Ez az észlelési profil profilt indít a környezetben, és riasztásokat aktivál, ha gyanús szabályok vannak beállítva, amelyek üzeneteket vagy mappákat törölnek vagy mozgatnak egy felhasználó postaládájában. Ez az észlelés arra utalhat, hogy a felhasználó fiókja sérült, az üzenetek szándékosan rejtettek, és hogy a postaládát levélszemét vagy kártevő szoftver terjesztésére használják a szervezetben. |
| szórásos jelszófeltörés | Offline | A szórásos jelszavas támadás során több felhasználónevet is megtámadnak, és a közös jelszavakat egységes találgatásos módon támadják meg, hogy jogosulatlan hozzáférést szerezzenek. Ez a kockázatészlelés jelszó szórásos támadás esetén aktiválódik. |
| Lehetetlen utazás | Offline | Ezt az észlelést a [Microsoft Cloud App Security (MCAS) felderíti.](/cloud-app-security/anomaly-detection-policy#impossible-travel) Ez az észlelés két olyan felhasználói tevékenységet azonosít (egy vagy több munkamenetet), amelyek földrajzilag távoli helyekről származnak egy, az első helyről a másodikra való utazáshoz szükséges időnél rövidebb időszakon belül, ami azt jelzi, hogy egy másik felhasználó ugyanazt a hitelesítő adatokat használja. |
| új ország | Offline | Ezt az észlelést a [Microsoft Cloud App Security (MCAS) felderíti.](/cloud-app-security/anomaly-detection-policy#activity-from-infrequent-country) Ez az észlelés a korábbi tevékenység helyszíneit veszi figyelembe az új és a ritka helyek meghatározásához. Az anomáliadetektálási motor a szervezet felhasználói által használt korábbi helyek adatait tárolja. |
| névtelen IP-címről indított tevékenység | Offline | Ezt az észlelést a [Microsoft Cloud App Security (MCAS) felderíti.](/cloud-app-security/anomaly-detection-policy#activity-from-anonymous-ip-addresses) Ez az észlelés azonosítja, hogy a felhasználók egy névtelen proxy IP-címként azonosított IP-címről voltak aktívak. |
| gyanús üzenettovábbítás | Offline | Ezt az észlelést a [Microsoft Cloud App Security (MCAS) felderíti.](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-forwarding) Ez az észlelés gyanús e-mail-továbbítási szabályokat keres, például ha egy felhasználó létrehozott egy beérkezett üzenetekre vonatkozó szabályt, amely az összes e-mail másolatát egy külső címre továbbítja. |

### <a name="other-risk-detections"></a>Egyéb kockázatészlelések

| Kockázatészlelés | Észlelés típusa | Leírás |
| --- | --- | --- |
| további észlelt kockázat | Valós idejű vagy offline | Ez az észlelés azt jelzi, hogy a rendszer a fenti prémium szintű észlelések valamelyikét észlelte. Mivel a prémium szintű észlelések csak prémium szintű Azure AD P2-ügyfelek számára láthatók, "további kockázat észlelhető" a P2 licenccel nem rendelkező prémium szintű Azure AD esetén. |

## <a name="common-questions"></a>Gyakori kérdések

### <a name="risk-levels"></a>Kockázati szintek

Az Identity Protection három szintre kategorizálja a kockázatokat: alacsony, közepes és magas. Az egyéni [identitásvédelmi szabályzatok konfigurálásakor](./concept-identity-protection-policies.md#custom-conditional-access-policy)konfigurálhatja úgy is, hogy a Nincs kockázati szint **beállítással aktiválódnak.** A Nincs kockázat azt jelenti, hogy nincs aktív jele annak, hogy a felhasználó identitása sérült volna.

Bár a Microsoft nem részletezi a kockázatok kiszámításának módját, azt fogjuk mondani, hogy minden szint nagyobb megbízhatóságot biztosít a felhasználó vagy a bejelentkezés biztonságának sérülése szempontjából. Előfordulhat például, hogy egy felhasználó ismeretlen bejelentkezési tulajdonságainak egy példánya nem lesz olyan veszélyeztetve, mint egy másik felhasználó kiszivárgott hitelesítő adatai.

### <a name="password-hash-synchronization"></a>Jelszókivonat szinkronizálása

Az olyan kockázatészlelések, mint a kiszivárgott hitelesítő adatok, jelszó-azonosítókra van szükség az észleléshez. A jelszó-kivonat szinkronizálásával kapcsolatos további információkért lásd: Jelszó-kivonatszinkronizálás megvalósítása Azure AD Connect [szinkronizálásával.](../hybrid/how-to-connect-password-hash-synchronization.md)

### <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Hol találja a Microsoft a kiszivárgott hitelesítő adatokat?

A Microsoft számos helyen megtalálja a kiszivárgott hitelesítő adatokat, például:

- A nyilvános beillesztési webhelyek, például a pastebin.com és paste.ca, ahol a rossz szereplő általában ilyen anyagokat ad vissza. Ez a hely a legtöbb rossz színész első állomása, amikor ellopott hitelesítő adatokat keresnek.
- Rendészeti szervek.
- A Microsoft más csoportjai sötét webes kutatásokat végeznek.

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>Miért nem látom a kiszivárgott hitelesítő adatokat?

kiszivárgott hitelesítő adatok a rendszer minden olyan esetben feldolgoz, amikor a Microsoft új, nyilvánosan elérhető köteget talál. Bizalmas jellegéből adódóan a kiszivárgott hitelesítő adatok röviddel a feldolgozás után törlődnek. A rendszer csak a jelszó-kivonatszinkronizálás (PHS) engedélyezése után talált új, kiszivárgott hitelesítő adatokat fogja feldolgozni a bérlőn. A rendszer nem ellenőrzi a korábban talált hitelesítőadat-párokat. 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>Már jó ideje nem láthatók kiszivárgott hitelesítőadat-kockázati események?

Ha nem látott kiszivárgott hitelesítőadat-kockázati eseményeket, annak az alábbi okai vannak:

- A PHS nincs engedélyezve a bérlő számára.
- A Microsoft nem talált olyan kiszivárgott hitelesítőadat-párokat, amelyek megfelelnek a felhasználóinak.

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Milyen gyakran használja a Microsoft az új hitelesítő adatokat?

A hitelesítő adatok feldolgozása közvetlenül a megtalálás után történik, általában naponta több kötegben.

## <a name="next-steps"></a>Következő lépések

- [A kockázatok csökkentésére rendelkezésre álló szabályzatok](concept-identity-protection-policies.md)
- [Biztonsági áttekintés](concept-identity-protection-security-overview.md)