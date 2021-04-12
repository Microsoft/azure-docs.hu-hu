---
title: Mi az az Azure Active Directory Identity Protection?
description: Kockázatok észlelése, szervizelése, vizsgálata és elemzése Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 01/05/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.custom: contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e274d35fde6a3d55c05bcb5a9f22e75a37aa3c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97955399"
---
# <a name="what-is-identity-protection"></a>Mi az az Identity Protection?

Az Identity Protection egy olyan eszköz, amely lehetővé teszi, hogy a szervezetek három fő feladatot hajtanak végre:

- Automatizálhatja az identitás-alapú kockázatok észlelését és szervizelését.
- Vizsgálja meg a kockázatokat a portálon tárolt adatszolgáltatások használatával.
- A kockázati észlelési adataikat külső gyártótól származó eszközökre exportálhatja további elemzés céljából.

Az Identity Protection a Microsoft által az Azure AD-vel, a Microsoft-fiókokkal, a Microsoft-fiókokkal és az Xbox-szal a felhasználók védelme érdekében a Microsoft által beszerzett tapasztalatokat használja. A Microsoft naponta 6 500 000 000 000-as jeleket elemez, hogy azonosítsa és megvédje az ügyfeleket a fenyegetésektől.

A által generált és az Identity Protection által létrehozott jelek továbbra is bevonhatók olyan eszközökbe, mint a feltételes hozzáférés a hozzáférési döntések meghozatalához, illetve a biztonsági információkkal és az Event Management (SIEM) eszközre való visszatéréssel a szervezet által kényszerített szabályzatok alapján történő további vizsgálathoz.

## <a name="why-is-automation-important"></a>Miért fontos az Automation?

Az 2018 Alex Weinert [októberi blogbejegyzést](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843) , amely a Microsoft Identity Security and Protection csapatát vezeti, magyarázza el, miért fontos az automatizálás az események mennyiségének kezelésekor:

> A gépi tanulás és a heurisztikus rendszerek minden nap több mint 800 000 000 különböző fiókra vonatkozó kockázati pontszámokat biztosítanak a 18 000 000 000 300 000 000-es bejelentkezési kísérletekhez
>
> Az Ignite tavaly megbeszélte az első 3 támadást az Identity Systems-ben. Itt láthatók a támadások legutóbbi mennyisége
>   
>   - **Visszajátszási ismétlés**: 4,6 milliárd támadás észlelhető 2018 májusában
>   - **Jelszó spray**: 350k áprilisban 2018
>   - **Adathalászat**: ez nehezen számszerűsíthető, de a 23M kockázati eseményeinek száma a 2018-as számú.

## <a name="risk-detection-and-remediation"></a>Kockázatok észlelése és szervizelése

Az Identity Protection a következő besorolások kockázatait azonosítja:

| Kockázat észlelésének típusa | Leírás |
| --- | --- |
| Névtelen IP-cím | Jelentkezzen be egy névtelen IP-címről (például: Tor-böngésző, névtelenül VPN-ek). |
| Szokatlan utazás | Jelentkezzen be egy atipikus helyről a felhasználó legutóbbi bejelentkezései alapján. |
| Kártevők társított IP-címe | Jelentkezzen be egy kártevővel összekapcsolt IP-címről. |
| Szokatlan bejelentkezési tulajdonságok | Jelentkezzen be a nemrégiben nem látott tulajdonságokkal az adott felhasználó számára. |
| Kiszivárgott hitelesítő adatok | Azt jelzi, hogy a felhasználó érvényes hitelesítő adatait kiszivárgott. |
| Jelszó spray | Azt jelzi, hogy a rendszer több felhasználónevet is megtámad a közös jelszavakkal egyesített, találgatásos módon. |
| Azure AD-fenyegetések felderítése | A Microsoft belső és külső veszélyforrások hírszerzési forrásai ismert támadási mintát azonosítottak. |
| Új ország | Ezt az észlelést [Microsoft Cloud app Security (MCAS)](/cloud-app-security/anomaly-detection-policy#activity-from-infrequent-country)észlelte. |
| Névtelen IP-címről származó tevékenység | Ezt az észlelést [Microsoft Cloud app Security (MCAS)](/cloud-app-security/anomaly-detection-policy#activity-from-anonymous-ip-addresses)észlelte. |
| Gyanús beérkezett fájlok továbbítása | Ezt az észlelést [Microsoft Cloud app Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-forwarding)észlelte. |

A kockázatokról és azok kiszámításáról további részleteket a cikkben [találhat.](concept-identity-protection-risks.md)

A kockázati jelek olyan szervizelési erőfeszítéseket válthatnak ki, mint például a felhasználók megkövetelése: az Azure AD-Multi-Factor Authentication elvégzése, jelszó visszaállítása önkiszolgáló jelszó-visszaállítással vagy a rendszergazda beavatkozása nélkül.

## <a name="risk-investigation"></a>Kockázati vizsgálat

A rendszergazdák megtekinthetik az észleléseket, és szükség esetén manuálisan is végezhetnek műveleteket. Három kulcsfontosságú jelentés van, amelyet a rendszergazdák a személyazonosság védelme során használnak a nyomozáshoz:

- Kockázatos felhasználók
- Kockázatos bejelentkezések
- Kockázatészlelések

További információt a következő cikkben talál [: a kockázat vizsgálata](howto-identity-protection-investigate-risk.md).

### <a name="risk-levels"></a>Kockázati szintek

Az Identity Protection három szinten kategorizálja a kockázatot: alacsony, közepes és magas. 

Habár a Microsoft nem ad meg konkrét részleteket a kockázatok kiszámításáról, azt fogjuk mondani, hogy minden szint nagyobb megbízhatóságot biztosít a felhasználó vagy a bejelentkezés biztonsága szempontjából. Előfordulhat például, hogy egy felhasználó ismeretlen bejelentkezési tulajdonságainak egy példánya nem fenyegeti a kiszivárgott hitelesítő adatokat egy másik felhasználó számára.

## <a name="exporting-risk-data"></a>Kockázati adatexportálás

Az Identity Protectionből származó adatok exportálhatók más eszközökre az archiváláshoz és a további vizsgálathoz és korrelációhoz. A Microsoft Graph-alapú API-k lehetővé teszik, hogy a szervezetek további feldolgozás céljából gyűjtsék ezeket az adatokat egy olyan eszközön, mint például a SIEM. Az Identity Protection API elérésével kapcsolatos információkat a cikk [Azure Active Directory Identity Protection és Microsoft Graph első lépéseivel foglalkozó](howto-identity-protection-graph-api.md) cikkben találja.

Az Identity Protection-információk Azure Sentineltel való integrálásával kapcsolatos információk a cikkben találhatók, [amelyek az adatok Azure ad Identity Protectionból való összekapcsolását](../../sentinel/connect-azure-ad-identity-protection.md)ismertetik.

## <a name="permissions"></a>Engedélyek

Az Identity Protection használatához a felhasználóknak biztonsági olvasónak, biztonsági kezelőnek, biztonsági rendszergazdának, globális olvasónak vagy globális rendszergazdának kell lenniük ahhoz, hogy hozzáférjenek.

| Szerepkör | Elvégezhető | Nem hajtható végre |
| --- | --- | --- |
| Globális rendszergazda | Teljes hozzáférés az Identity Protection szolgáltatáshoz |   |
| Biztonsági rendszergazda | Teljes hozzáférés az Identity Protection szolgáltatáshoz | Felhasználó jelszavának alaphelyzetbe állítása |
| Biztonsági operátor | Az összes Identity Protection-jelentés és áttekintés panel megtekintése <br><br> Felhasználói kockázat elvetése, biztonságos bejelentkezés megerősítése, biztonság megerősítése | Szabályzatok konfigurálása vagy módosítása <br><br> Felhasználó jelszavának alaphelyzetbe állítása <br><br> Riasztások konfigurálása |
| Biztonsági olvasó | Az összes Identity Protection-jelentés és áttekintés panel megtekintése | Szabályzatok konfigurálása vagy módosítása <br><br> Felhasználó jelszavának alaphelyzetbe állítása <br><br> Riasztások konfigurálása <br><br> Visszajelzés küldése az észlelésekről |

Jelenleg a biztonsági operátori szerepkör nem fér hozzá a kockázatos bejelentkezések jelentéséhez.

A feltételes hozzáférésű rendszergazdák olyan házirendeket is létrehozhatnak, amelyek feltétele a bejelentkezési kockázat. További információt a [feltételes hozzáférés: feltételek](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)című cikkben talál.

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Képesség | Részletek  | Ingyenes Azure AD/Microsoft 365 alkalmazások | Prémium szintű Azure AD P1|Prémium szintű Azure AD P2 |
| --- | --- | --- | --- | --- |
| Kockázati szabályzatok | Felhasználói kockázati házirend (Identity Protection használatával)  | Nem | Nem |Igen | 
| Kockázati szabályzatok | Bejelentkezési kockázati szabályzat (Identity Protection vagy feltételes hozzáférés használatával)  | Nem |  Nem |Igen |
| Biztonsági jelentések | Áttekintés |  Nem | Nem |Igen |
| Biztonsági jelentések | Kockázatos felhasználók  | Korlátozott információ. Csak közepes és magas kockázatú felhasználók jelennek meg. Nincsenek részletek vagy kockázati előzmények. | Korlátozott információ. Csak közepes és magas kockázatú felhasználók jelennek meg. Nincsenek részletek vagy kockázati előzmények. | Teljes hozzáférés|
| Biztonsági jelentések | Kockázatos bejelentkezések  | Korlátozott információ. A rendszer nem jelenít meg kockázati adatokat vagy kockázati szintet. | Korlátozott információ. A rendszer nem jelenít meg kockázati adatokat vagy kockázati szintet. | Teljes hozzáférés|
| Biztonsági jelentések | Kockázatészlelések   | No | Korlátozott információ. Nem találhatók részletek a fiókban.| Teljes hozzáférés|
| Értesítések | Veszélyeztetett felhasználók észlelt riasztásai  | Nem | Nem |Igen |
| Értesítések | Heti kivonat| Nem | Nem | Igen | 
| | MFA-regisztrációs szabályzat | Nem | Nem | Igen |

A részletes jelentésekkel kapcsolatos további információkért tekintse meg a következő témakört [: a kockázatkezelés kivizsgálása](howto-identity-protection-investigate-risk.md#navigating-the-reports).

## <a name="next-steps"></a>Következő lépések

- [Biztonsági áttekintés](concept-identity-protection-security-overview.md)

- [Mi a kockázat](concept-identity-protection-risks.md)

- [A kockázatok enyhítésére rendelkezésre álló szabályzatok](concept-identity-protection-policies.md)
