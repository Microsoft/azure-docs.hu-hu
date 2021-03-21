---
title: Mit jelent a kockázat? Azure AD Identity Protection
description: A Azure AD Identity Protection kockázatának ismertetése
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18e504579c750caf452ef74844c4a388ec96448a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97954485"
---
# <a name="what-is-risk"></a>Mit jelent a kockázat?

A Azure AD Identity Protection kockázati észlelése magában foglalja a címtárban lévő felhasználói fiókokkal kapcsolatos azonosított gyanús műveleteket.

Az Identity Protection lehetővé teszi a szervezetek számára a hatékony erőforrásokhoz való hozzáférést, így gyorsan megtekintheti és reagálhat ezekre a gyanús műveletekre. 

![A kockázatos felhasználókat és bejelentkezéseket bemutató biztonsági áttekintés](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

> [!NOTE]
> Az Identity Protection csak a megfelelő hitelesítő adatok használata esetén hoz létre kockázati észleléseket. Ha a bejelentkezés során helytelen hitelesítő adatok szerepelnek, akkor nem a hitelesítő adatok biztonságának kockázatát jelzi.

## <a name="risk-types-and-detection"></a>Kockázati típusok és észlelés

Kétféle kockázati **felhasználó** és **Bejelentkezés** , valamint két típusú észlelési vagy számítási módszer áll rendelkezésre a **valós időben** és a **kapcsolat nélküli üzemmódban**.

A valós idejű észlelések nem jelennek meg a jelentéskészítésben öt – tíz percen belül. Előfordulhat, hogy az offline észlelések nem jelennek meg a jelentéskészítésben két-huszonnégy órán keresztül.

### <a name="user-risk"></a>Felhasználói kockázat

A felhasználói kockázat azt jelzi, hogy egy adott identitás vagy fiók biztonsága sérül. 

Ezeket a kockázatokat a Microsoft belső és külső veszélyforrások elleni intelligenciával, például a biztonsági kutatókkal, bűnüldözési szakemberekkel, a Microsoft biztonsági csoportjaival és más megbízható forrásokkal együtt offline számítjuk ki.

| Kockázati észlelés | Description |
| --- | --- |
| Kiszivárgott hitelesítő adatok | Ez a kockázati észlelési típus azt jelzi, hogy a felhasználó érvényes hitelesítő adatait kiszivárgott. Ha a kiberbűnözők veszélyezteti a legitim felhasználók érvényes jelszavait, gyakran megosztják ezeket a hitelesítő adatokat. Ezt a megosztást általában nyilvánosan közzétesszük a Dark web, a Sites vagy a Trading szolgáltatásban, és a hitelesítő adatokat a fekete piacon forgalmazzák. Ha a Microsoft által kiszivárgott hitelesítő adatokkal rendelkező szolgáltatás felhasználói hitelesítő adatokat kér a sötét webhelyről, illetve más forrásokból, akkor azokat a rendszer az Azure AD-felhasználók aktuális érvényes hitelesítő adataival ellenőrzi, hogy érvényes egyezéseket keres. A kiszivárgott hitelesítő adatokkal kapcsolatos további információkért tekintse meg a [gyakori kérdéseket](#common-questions). |
| Azure AD-fenyegetések felderítése | Ez a kockázati észlelési típus azt jelzi, hogy az adott felhasználó számára szokatlan felhasználói tevékenység van, vagy konzisztens a Microsoft belső és külső veszélyforrások elleni hírszerzési forrásaira épülő ismert támadási mintákkal. |

### <a name="sign-in-risk"></a>Bejelentkezési kockázat

A bejelentkezési kockázat azt jelzi, hogy egy adott hitelesítési kérelem nem jogosult az identitás tulajdonosa számára. 

Ezek a kockázatok valós időben vagy a Microsoft belső és külső veszélyforrások elleni intelligenciával, például biztonsági kutatókkal, bűnüldöző szakemberekkel, a Microsoft biztonsági csoportjaival és más megbízható forrásokkal is kiszámíthatók.

| Kockázati észlelés | Észlelés típusa | Description |
| --- | --- | --- |
| Névtelen IP-cím | Valós idejű | Ez a kockázati észlelési típus a névtelen IP-címről (például Tor-böngésző vagy névtelen VPN) érkező bejelentkezéseket jelzi. Ezeket az IP-címeket jellemzően olyan szereplők használják, akik el szeretnék rejteni a bejelentkezési telemetria (IP-cím, hely, eszköz stb.) a potenciálisan rosszindulatú szándék érdekében. |
| Szokatlan utazás | Offline | Ez a kockázati észlelési típus két, földrajzilag távoli helyről származó bejelentkezést azonosít, ahol a felhasználók legalább egyike atipikus lehet a felhasználó számára, a múltbeli viselkedés miatt. Ez a gépi tanulási algoritmus számos más tényezőt is figyelembe vesz, és azt, hogy a felhasználó az első helyről a másodikra utazzon, valamint azt, hogy egy másik felhasználó ugyanazokat a hitelesítő adatokat használja. <br><br> Az algoritmus figyelmen kívül hagyja a "hamis pozitív" állapotot, amely hozzájárul a lehetetlen utazási feltételekhez, például a VPN-hez és a szervezet más felhasználói által rendszeresen használt helyekhez. A rendszer kezdeti tanulási időszaka a legkorábbi 14 nap vagy 10 bejelentkezés, amely során egy új felhasználó bejelentkezési viselkedését tanulja meg. |
| Kártevők társított IP-címe | Offline | Ez a kockázat-észlelési típus olyan IP-címekről származó bejelentkezéseket jelez, amelyekről ismert, hogy aktívan kommunikálnak egy robot-kiszolgálóval. Ezt az észlelést a felhasználó eszközének IP-címeinek az IP-címekkel való korrelációja határozza meg, amelyek a bot-kiszolgálóval kapcsolatban voltak aktívak. |
| Szokatlan bejelentkezési tulajdonságok | Valós idejű | Ez a kockázati észlelési típus a rendellenes bejelentkezések kereséséhez megtekinti a korábbi bejelentkezési előzményeket (IP-cím, szélesség/hosszúság és ASN). A rendszer a felhasználó által használt korábbi helyszínekről tárol adatokat, és ezeket a "ismerős" helyet tekinti át. A kockázat észlelése akkor aktiválódik, ha a bejelentkezés olyan helyről történik, amely még nem szerepel az ismerős helyek listáján. Az újonnan létrehozott felhasználók "tanulási módban" lesznek egy olyan időszakra, amelyben a nem ismert bejelentkezési tulajdonságok kockázati észlelése ki lesz kapcsolva, miközben az algoritmusok megtudhatják a felhasználó viselkedését. A tanulási mód időtartama dinamikus, és attól függ, hogy mennyi időt vesz igénybe az algoritmus, hogy elegendő információt gyűjtsön a felhasználó bejelentkezési mintáit illetően. A minimális időtartam öt nap. A felhasználók hosszú inaktivitás után visszatérhetnek a tanulási módba. A rendszer emellett figyelmen kívül hagyja a jól ismert eszközökről érkező bejelentkezéseket és a jól ismert helyhez földrajzilag közel lévő helyeket. <br><br> Ezt az észlelést az alapszintű hitelesítéshez (vagy örökölt protokollokhoz) is futtatjuk. Mivel ezek a protokollok nem rendelkeznek modern tulajdonságokkal, például az ügyfél-AZONOSÍTÓval, a hamis pozitív telemetria csökkentése korlátozott. Javasoljuk ügyfeleinknek, hogy a modern hitelesítésre térjenek át. |
| A rendszergazda által megerősített felhasználó sérült | Offline | Ez az észlelés azt jelzi, hogy egy rendszergazda a kockázatos felhasználók felhasználói felületén vagy a riskyUsers API-val jelölte meg a "felhasználói biztonság megerősítése" beállítást. Annak megtekintéséhez, hogy mely rendszergazda erősítette meg ezt a felhasználót, ellenőrizze a felhasználó kockázati előzményeit (UI vagy API használatával). |
| Kártékony IP-cím | Offline | Ez az észlelés azt jelzi, hogy egy rosszindulatú IP-címről jelentkezik be a bejelentkezés. Az IP-címek a nagy meghibásodási arányok alapján kártékonynak minősülnek, mert az IP-címről vagy más IP-címekről származó, érvénytelen hitelesítő adatok érkeznek. |
| Gyanús beérkezett fájlok kezelésére vonatkozó szabályok | Offline | Ezt az észlelést [Microsoft Cloud app Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules)észlelte. Ez az észlelési profil a környezetet, és riasztást küld, ha gyanús szabályok vannak beállítva, amelyek üzeneteket vagy mappákat törölnek vagy helyeznek át egy felhasználó postaládájába. Ez az észlelés azt jelezheti, hogy a felhasználó fiókja biztonságban van, hogy az üzenetek szándékosan rejtettek, és a postaláda a levélszemét vagy a kártevők terjesztésére szolgál a szervezetben. |
| Jelszó spray | Offline | A jelszó-szórásos támadás olyankor történik, ahol több felhasználónevet is megtámadnak a gyakori jelszavakkal egy egységes találgatásos kényszerített módon a jogosulatlan hozzáférés megszerzéséhez. Ez a kockázati észlelés akkor aktiválódik, ha jelszó-szórásos támadást hajtottak végre. |
| Lehetetlen utazás | Offline | Ezt az észlelést [Microsoft Cloud app Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel)észlelte. Ez az észlelés két felhasználói tevékenységet azonosít (egy vagy több munkamenet), amely a földrajzilag távoli helyekről származik, és a felhasználó az első helyről a másikra való utazásnál rövidebb időt vett igénybe, ami azt jelzi, hogy egy másik felhasználó ugyanazokat a hitelesítő adatokat használja. |
| Új ország | Offline | Ezt az észlelést [Microsoft Cloud app Security (MCAS)](/cloud-app-security/anomaly-detection-policy#activity-from-infrequent-country)észlelte. Ez az észlelés a múltbeli tevékenységek helyét vizsgálja meg az új és a ritkán használt helyszínek meghatározásához. Az anomáliák észlelési motorja a szervezet felhasználói által használt korábbi helyekre vonatkozó adatokat tárolja. |
| Névtelen IP-címről származó tevékenység | Offline | Ezt az észlelést [Microsoft Cloud app Security (MCAS)](/cloud-app-security/anomaly-detection-policy#activity-from-anonymous-ip-addresses)észlelte. Ez az észlelés azt azonosítja, hogy a felhasználók olyan IP-címről voltak aktívak, amelyet névtelen proxy IP-címként azonosítottak. |
| Gyanús beérkezett fájlok továbbítása | Offline | Ezt az észlelést [Microsoft Cloud app Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-forwarding)észlelte. Ez az észlelés gyanús e-mail továbbítási szabályokat keres, például ha egy felhasználó létrehozott egy beérkezett fájlok nevű szabályt, amely az összes e-mail-másolatát egy külső címre továbbítja. |

### <a name="other-risk-detections"></a>Egyéb kockázati észlelések

| Kockázati észlelés | Észlelés típusa | Description |
| --- | --- | --- |
| További kockázat észlelhető | Valós idejű vagy offline | Ez az észlelés azt jelzi, hogy a rendszer a fenti prémium észlelések egyikét észlelte. Mivel a prémium észlelések csak prémium szintű Azure AD P2-ügyfelek számára láthatók, a "további kockázat észlelhető", prémium szintű Azure AD P2 licencek nélküli ügyfelek számára. |

## <a name="common-questions"></a>Gyakori kérdések

### <a name="risk-levels"></a>Kockázati szintek

Az Identity Protection három szinten kategorizálja a kockázatot: alacsony, közepes és magas. [Egyéni Identity Protection-házirendek](./concept-identity-protection-policies.md#custom-conditional-access-policy)konfigurálásakor azt is beállíthatja, hogy ne legyen **kockázati** szint. Nincs kockázat azt jelenti, hogy a felhasználó személyazonossága nem sérült.

Habár a Microsoft nem ad meg konkrét részleteket a kockázatok kiszámításáról, azt fogjuk mondani, hogy minden szint nagyobb megbízhatóságot biztosít a felhasználó vagy a bejelentkezés biztonsága szempontjából. Előfordulhat például, hogy egy felhasználó ismeretlen bejelentkezési tulajdonságainak egy példánya nem fenyegeti a kiszivárgott hitelesítő adatokat egy másik felhasználó számára.

### <a name="password-hash-synchronization"></a>Jelszókivonat szinkronizálása

A kockázati észlelések, például a kiszivárgott hitelesítő adatok és a jelszó-szórás megköveteli a jelszó-kivonatok jelenlétét az észleléshez. A jelszó-kivonat szinkronizálásával kapcsolatos további információkért tekintse meg a [jelszó-kivonat szinkronizálásának megvalósítása Azure ad Connect szinkronizálással](../hybrid/how-to-connect-password-hash-synchronization.md)című cikket.

### <a name="leaked-credentials"></a>Kiszivárgott hitelesítő adatok

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Hol találja a Microsoft a kiszivárgott hitelesítő adatokat?

A Microsoft számos helyen talál kiszivárgott hitelesítő adatokat, többek között:

- Nyilvános beillesztési helyek, például pastebin.com és paste.ca, ahol a rossz szereplők általában ilyen anyagokat küldenek. Ez a hely a legtöbb rossz színész, amely az ellopott hitelesítő adatok megkeresésére a vadászat első leállítása.
- Bűnüldöző szervek.
- A Microsoft egyéb csoportjai sötét webes kutatást végeznek.

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>Miért nem jelenik meg a kiszivárgott hitelesítő adatok?

A kiszivárgott hitelesítő adatok feldolgozása bármikor megtörténik, amikor a Microsoft új, nyilvánosan elérhető köteget talál. Az érzékeny természet miatt a kiszivárgott hitelesítő adatokat a rendszer röviddel a feldolgozás után törli. Csak az új kiszivárgott hitelesítő adatok lesznek feldolgozva a jelszó-kivonatoló szinkronizálás (PHS) engedélyezése után a bérlőn. A rendszer nem végez ellenőrzést a korábban megtalált hitelesítő adatokkal szemben. 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>Nem láttam elég sokáig a kiszivárgott hitelesítő adatokat érintő kockázati eseményeket?

Ha még nem látott kiszivárgott hitelesítőadat-kockázati eseményt, az a következő okok miatt van:

- Nincs engedélyezve a PHS a bérlő számára.
- A Microsoft nem talált olyan kiszivárgott hitelesítő adatokat, amelyek megfelelnek a felhasználóinak.

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Milyen gyakran dolgozza fel a Microsoft az új hitelesítő adatokat?

A hitelesítő adatok feldolgozása közvetlenül a megtalálása után történik, általában naponta több kötegben.

## <a name="next-steps"></a>Következő lépések

- [A kockázatok enyhítésére rendelkezésre álló szabályzatok](concept-identity-protection-policies.md)
- [Biztonsági áttekintés](concept-identity-protection-security-overview.md)