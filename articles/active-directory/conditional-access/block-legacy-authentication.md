---
title: Örökölt hitelesítés tiltása – Azure Active Directory
description: Ismerje meg, hogyan javíthatja biztonsági állapotát azáltal, hogy blokkolja az örökölt hitelesítést az Azure AD feltételes hozzáférés használatával.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09f98e3d6c7997d9cae2737b25f4323021e29bfb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98892439"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Útmutató: Az Azure AD-hez szükséges régi hitelesítés blokkolása feltételes hozzáféréssel   

Ahhoz, hogy a felhasználók könnyen hozzáférhessenek a felhőalapú alkalmazásokhoz, Azure Active Directory (Azure AD) a hitelesítési protokollok széles választékát támogatja, beleértve az örökölt hitelesítést is. A régi protokollok azonban nem támogatják a többtényezős hitelesítést (MFA). Az MFA számos környezetben gyakori követelmény a személyazonossági lopás kezelése. 

Alex Weinert, a Microsoft Identity Security igazgatója, az 2020-as március 12-én, a [szervezet örökölt hitelesítésének blokkolására szolgáló új eszközöket](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) ismertető blogjában hangsúlyozza, hogy miért blokkolja a szervezetek az örökölt hitelesítést, és hogy a Microsoft milyen további eszközöket biztosít a feladat végrehajtásához:

> Ahhoz, hogy az MFA hatékony legyen, le kell tiltania az örökölt hitelesítést is. Ennek az az oka, hogy az olyan örökölt hitelesítési protokollok, mint a POP, az SMTP, az IMAP és a MAPI, nem tudják érvényesíteni az MFA-t, így előnyben részesített belépési pontokat biztosítanak a szervezete
> 
>... Az Azure Active Directory (Azure AD) forgalmának elemzésével kapcsolatos örökölt hitelesítés számai:
> 
> - A jelszó-szórásos támadások több mint 99 százaléka használja az örökölt hitelesítési protokollokat
> - A hitelesítő adatok több mint 97 százaléka örökölt hitelesítést használ
> - Azure AD-fiókok olyan szervezeteknél, amelyek letiltották az örökölt hitelesítési élményt 67 százalékkal kevesebb kompromisszumot, mint az örökölt hitelesítés engedélyezése esetén
>

Ha a környezet készen áll az örökölt hitelesítés letiltására a bérlői védelem javítása érdekében, ezt a célt feltételes hozzáféréssel is elvégezheti. Ez a cikk azt ismerteti, hogyan konfigurálhatja a bérlő örökölt hitelesítését tiltó feltételes hozzáférési szabályzatokat.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy ismeri az Azure AD feltételes hozzáférés [alapvető fogalmait](overview.md) .

## <a name="scenario-description"></a>Forgatókönyv leírása

Az Azure AD számos, a legszélesebb körben használt hitelesítési és engedélyezési protokollt támogat, beleértve az örökölt hitelesítést is. Az örökölt hitelesítés az alapszintű hitelesítést használó protokollokra vonatkozik. Ezek a protokollok általában nem kényszerítik ki a második faktoros hitelesítés típusát. Példák az örökölt hitelesítésen alapuló alkalmazásokra:

- Régebbi Microsoft Office alkalmazások
- Levelezési protokollokat (például POP, IMAP és SMTP) használó alkalmazások

Az egytényezős hitelesítés (például Felhasználónév és jelszó) nem elég ezekben a napokban. A jelszavak helytelenek, mert könnyen kitalálhatóak, és mi (emberek) rosszak a jó jelszavak kiválasztásakor. A jelszavakat számos különböző támadás, például az adathalászat és a jelszó-spray is sebezhetővé teszi. A jelszó-fenyegetések elleni védelem egyik legegyszerűbb módja a többtényezős hitelesítés (MFA) megvalósítása. Az MFA-val még akkor is, ha egy támadó beolvas egy felhasználói jelszót, a jelszó önmagában nem elegendő ahhoz, hogy sikeresen hitelesítse és hozzáférjen az adataihoz.

Hogyan akadályozhatja meg, hogy az örökölt hitelesítést használó alkalmazások hozzáférjenek a bérlő erőforrásaihoz? A javaslat célja, hogy csak feltételes hozzáférési házirenddel blokkolja őket. Ha szükséges, csak bizonyos felhasználóknak és adott hálózati helyszíneknek engedélyezi az örökölt hitelesítésen alapuló alkalmazások használatát.

A feltételes hozzáférési házirendeket az első tényezős hitelesítés befejeződése után kényszeríti ki a rendszer. Ezért a feltételes hozzáférés nem az első vonali védelem, mint például a szolgáltatásmegtagadási (DoS) támadások, de az ilyen eseményekből származó jeleket is használhat (például a bejelentkezési kockázati szint, a kérelem helye stb.) a hozzáférés meghatározásához.

## <a name="implementation"></a>Implementálás

Ez a szakasz azt ismerteti, hogyan lehet feltételes hozzáférési szabályzatot konfigurálni a régi hitelesítés blokkolására. 

### <a name="legacy-authentication-protocols"></a>Örökölt hitelesítési protokollok

A következő beállítások tekinthetők örökölt hitelesítési protokolloknak

- Hitelesített SMTP – a POP-és IMAP-ügyfelek által használt e-mail-üzenetek küldésére használható.
- Automatikus észlelés – az Outlook és az EAS-ügyfelek által használt postaládák keresése és kapcsolódás az Exchange Online-ban.
- Exchange ActiveSync (EAS) – a postaládákhoz való kapcsolódásra szolgál az Exchange Online-ban.
- Exchange Online PowerShell – az Exchange Online-hoz való kapcsolódáshoz használt távoli PowerShell-lel. Ha letiltja az Exchange Online PowerShell alapszintű hitelesítését, a kapcsolódáshoz az Exchange Online PowerShell-modult kell használnia. Útmutatásért lásd: [Kapcsolódás az Exchange Online powershellhez a multi-Factor Authentication használatával](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
- Exchange Web Services (EWS) – az Outlook, az Outlook for Mac és a harmadik féltől származó alkalmazások által használt programozási felület.
- IMAP4 – az IMAP e-mail ügyfélprogramok által használt.
- MAPI HTTP-n keresztül (MAPI/HTTP) – az Outlook 2010-es és újabb verzióiban használt.
- Offline címjegyzék (OAB) – az Outlook által letöltött és használt címlista-gyűjtemények másolata.
- Outlook Anywhere (HTTP-n keresztüli RPC) – az Outlook 2016-es és korábbi verzióiban használatos.
- Outlook-szolgáltatás – a Windows 10 rendszerhez készült mail és naptár alkalmazás használja.
- POP3 – a POP e-mail ügyfélprogramok által használt.
- Jelentéskészítési webszolgáltatások – a Jelentésadatok beolvasására szolgálnak az Exchange Online-ban.
- Egyéb ügyfelek – az örökölt hitelesítés használatával azonosított egyéb protokollok.

További információ ezekről a hitelesítési protokollokról és szolgáltatásokról: [bejelentkezési tevékenységek jelentései a Azure Active Directory portálon](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

### <a name="identify-legacy-authentication-use"></a>Örökölt hitelesítési használat azonosítása

Mielőtt blokkolni tudja az örökölt hitelesítést a címtárban, először meg kell értenie, hogy a felhasználók rendelkeznek-e örökölt hitelesítést használó alkalmazásokkal, és hogy ez milyen hatással van a teljes címtárra. Az Azure AD bejelentkezési naplói segítségével megtudhatja, hogy örökölt hitelesítést használ-e.

1. Navigáljon a **Azure Portal**  >  **Azure Active Directory** a  >  **bejelentkezések** elemre.
1. Ha nem látható, akkor adja hozzá az ügyfélalkalmazás oszlopot, ha az **oszlopok**  >  **ügyfélalkalmazás** elemre kattint.
1. **Szűrők hozzáadása**  >  **Ügyfélalkalmazás** > válassza ki az összes örökölt hitelesítési protokollt. Válassza a Szűrés párbeszédpanelen kívül a kívánt beállításokat, majd a párbeszédpanel bezárásához.
1. Ha aktiválta az [új bejelentkezési tevékenység jelentésének előzetes](../reports-monitoring/concept-all-sign-ins.md)verzióját, ismételje meg a fenti lépéseket a **felhasználói bejelentkezések (nem interaktív)** lapon is.

A szűrés csak az örökölt hitelesítési protokollok által végrehajtott bejelentkezési kísérleteket fogja megjeleníteni. Az egyes bejelentkezési kísérletekre kattintva további részleteket is megtudhat. Az **alapinformációk** lapon található **ügyfélalkalmazás** mező jelzi, hogy melyik örökölt hitelesítési protokollt használta a rendszer.

Ezek a naplók azt jelzik, hogy mely felhasználók maradnak az örökölt hitelesítéstől függően, és mely alkalmazások örökölt protokollokat használnak a hitelesítési kérések elvégzéséhez. Azoknál a felhasználóknál, akik nem jelennek meg a naplókban, és a rendszer megerősíti, hogy nem használ örökölt hitelesítést, csak ezekre a felhasználókra alkalmazza a feltételes hozzáférési szabályzatot.

## <a name="block-legacy-authentication"></a>Régi hitelesítési folyamat letiltása 

A feltételes hozzáférési szabályzatok használatának két módja van a régi hitelesítés blokkolására.

- [Az örökölt hitelesítés közvetlen blokkolása](#directly-blocking-legacy-authentication)
- [Az örökölt hitelesítés indirekt blokkolása](#indirectly-blocking-legacy-authentication)
 
### <a name="directly-blocking-legacy-authentication"></a>Az örökölt hitelesítés közvetlen blokkolása

A teljes szervezeten belüli örökölt hitelesítés blokkolásának legegyszerűbb módja egy olyan feltételes hozzáférési szabályzat konfigurálása, amely kifejezetten az örökölt hitelesítési ügyfelekre vonatkozik, és blokkolja a hozzáférést. Amikor felhasználókat és alkalmazásokat rendel a Szabályzathoz, ügyeljen arra, hogy kizárjon olyan felhasználókat és szolgáltatásfiókot, amelyeknek továbbra is be kell jelentkezniük a régi hitelesítés használatával. Konfigurálja az ügyfélalkalmazások feltételeit az **Exchange ActiveSync-ügyfelek** és **más ügyfelek** lehetőség kiválasztásával. Ezen ügyfélalkalmazások hozzáférésének letiltásához konfigurálja a hozzáférés-vezérlést a hozzáférés blokkolásához.

![Az örökölt hitelesítés blokkolására konfigurált ügyfélalkalmazás-feltétel](./media/block-legacy-authentication/client-apps-condition-configured-yes.png)

### <a name="indirectly-blocking-legacy-authentication"></a>Az örökölt hitelesítés indirekt blokkolása

Még ha a szervezet nem áll készen a teljes szervezeten belüli örökölt hitelesítés blokkolására, ügyeljen arra, hogy az örökölt hitelesítéssel rendelkező bejelentkezések ne kerüljenek olyan házirendek megkerülésére, amelyek olyan vezérlőket igényelnek, mint például a többtényezős hitelesítés vagy a megfelelő/hibrid Azure AD-hez csatlakoztatott eszközök. A hitelesítés során a régi hitelesítési ügyfelek nem támogatják az MFA, az eszköz megfelelőségének küldését vagy az állapotadatok az Azure AD-be való csatlakoztatását. Ezért a szabályzatokat az összes ügyfélalkalmazás rendelkezésére kell állítani, hogy az örökölt hitelesítési alapú bejelentkezések ne feleljenek meg a engedélyezési vezérlőknek. Az ügyfélalkalmazások általános elérhetősége az 2020 augusztusában, az újonnan létrehozott feltételes hozzáférési szabályzatok alapértelmezés szerint minden ügyfélalkalmazás esetében érvényesek.

![Ügyféloldali alkalmazások alapértelmezett konfigurációja](./media/block-legacy-authentication/client-apps-condition-configured-no.png)

## <a name="what-you-should-know"></a>Alapismeretek

A **más ügyfelekkel** való hozzáférés letiltása az alapszintű hitelesítéssel blokkolja az Exchange Online PowerShell-t és a Dynamics 365-et is.

A házirend konfigurálása **más ügyfelek** számára a teljes szervezetet blokkolja bizonyos ügyfelektől, például a SPConnect. Ez a blokk azért fordul elő, mert a régebbi ügyfelek nem várt módon végzik a hitelesítést. A probléma nem vonatkozik a nagy Office-alkalmazásokra, például a régebbi Office-ügyfelekre.

Akár 24 óráig is eltarthat, amíg a szabályzat érvénybe lép.

A **többi ügyfél** feltételéhez kiválaszthatja az összes elérhető engedélyezési vezérlőt; a végfelhasználói élmény azonban mindig ugyanaz a letiltott hozzáférés.

### <a name="sharepoint-online-and-b2b-guest-users"></a>A SharePoint Online és a B2B vendég felhasználói

Ha az örökölt hitelesítéssel szeretné letiltani a B2B felhasználói hozzáférést a SharePoint Online-hoz, a szervezeteknek le kell tiltaniuk az örökölt hitelesítést a SharePointban a `Set-SPOTenant` PowerShell parancs használatával, és a `-LegacyAuthProtocolsEnabled` paraméter `$false` A paraméter beállításával kapcsolatos további információkért tekintse meg a következőt: [set-SPOTenant](/powershell/module/sharepoint-online/set-spotenant)

## <a name="next-steps"></a>Következő lépések

- [A hatás meghatározása a feltételes hozzáférésről szóló jelentés módban](howto-conditional-access-insights-reporting.md)
- Ha még nem ismeri a feltételes hozzáférési szabályzatok konfigurálását, tekintse meg a többtényezős hitelesítés [megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáférés](../authentication/tutorial-enable-azure-mfa.md) egy példához című témakört.
- A modern hitelesítés támogatásával kapcsolatos további információkért lásd: [Hogyan működik a modern hitelesítés az office 2013 és az office 2016 ügyfélalkalmazások számára](/office365/enterprise/modern-auth-for-office-2013-and-2016) . 
- [Többfunkciós eszköz vagy alkalmazás beállítása az e-mailek küldéséhez Microsoft 365 használatával](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)