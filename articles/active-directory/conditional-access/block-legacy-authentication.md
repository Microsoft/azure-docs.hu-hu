---
title: Régi hitelesítés blokkolása – Azure Active Directory
description: Megtudhatja, hogyan javíthatja a biztonsági helyzeteket az Azure AD feltételes hozzáféréssel való örökölt hitelesítés letiltásával.
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
ms.openlocfilehash: 84c8b82219f2b2aea39bbcd23f030243d9ea8635
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861804"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Útmutató: Az Azure AD-hez szükséges régi hitelesítés blokkolása feltételes hozzáféréssel   

A felhőalkalmazásokhoz való egyszerű hozzáférés érdekében a Azure Active Directory (Azure AD) számos hitelesítési protokollt támogat, beleértve a régi hitelesítést is. Az örökölt protokollok azonban nem támogatják a többtényezős hitelesítést (MFA). Az MFA számos környezetben gyakori követelmény a személyi adatok ellopása esetén. 

Alex Weinert, a Microsoft identitásbiztonsági igazgatója, 2020. március [](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) 12-én közzétett blogbejegyzésében az Új eszközök a régi hitelesítés blokkolására a szervezetben kiemeli, hogy a szervezeteknek miért kell blokkolni az örökölt hitelesítést, és hogy a Microsoft milyen egyéb eszközöket biztosít a feladat végrehajtásához:

> Ahhoz, hogy az MFA hatékony legyen, le kell tiltani az örökölt hitelesítést is. Ennek az az oka, hogy az olyan régi hitelesítési protokollok, mint a POP, SMTP, IMAP és MAPI nem kényszerítik az MFA-t, így előnyben részesített belépési pontok a szervezetet megtámadó támadók számára...
> 
>... Az azure-Azure Active Directory (Azure AD) forgalmának elemzésével Azure Active Directory régi hitelesítések számai a következőek:
> 
> - A szórásos jelszavas támadások több mint 99%-a régi hitelesítési protokollokat használ
> - A hitelesítő adatok beöltéséből való támadások több mint 97%-a régi hitelesítést használ
> - Olyan szervezetek Azure AD-fiókjai, amelyek letiltották az örökölt hitelesítési élményt, 67 százalékkal kevesebb biztonsági hiba történt, mint azok, amelyeknél engedélyezve van a régi hitelesítés
>

Ha a környezet készen áll az örökölt hitelesítés blokkolásra a bérlő védelmének javítása érdekében, ezt a célt feltételes hozzáféréssel valósíthatja meg. Ez a cikk bemutatja, hogyan konfigurálhat olyan feltételes hozzáférési szabályzatokat, amelyek letiltják a bérlő örökölt hitelesítését. A feltételes hozzáférést is magukban foglaló licenccel nem rendelkező ügyfelek a biztonsági alapértelmezéseket [()](../fundamentals/concept-fundamentals-security-defaults.md)használva blokkolhatják az örökölt hitelesítést.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy [](overview.md) ismeri az Azure AD feltételes hozzáférés alapvető fogalmait.

## <a name="scenario-description"></a>Forgatókönyv leírása

Az Azure AD számos legelterjedtebb hitelesítési és engedélyezési protokollt támogat, beleértve az örökölt hitelesítést is. Az örökölt hitelesítés olyan protokollokra utal, amelyek alapszintű hitelesítést használnak. Ezek a protokollok általában nem kényszerítik a kéttényezős hitelesítést. Példák az örökölt hitelesítésen alapuló alkalmazásokra:

- Régebbi Microsoft Office alkalmazások
- Levelezési protokollokat (például POP, IMAP és SMTP) használó alkalmazások

Az egytényezős hitelesítés (például felhasználónév és jelszó) manapság nem elég. A jelszavak rosszak, mivel könnyen kitalálhatóak, és mi (emberek) rossz választásnak vagyunk a jó jelszavak között. A jelszavak különböző támadásokkal, például adathalászattal és szórásos jelszójelszó-szórással szemben is sebezhetők. A jelszóval szembeni fenyegetések elleni védelem egyik legegyszerűbb módja a többtényezős hitelesítés (MFA) megvalósítása. Az MFA használata esetén még ha egy támadó birtokában is van egy felhasználó jelszavának, a jelszó önmagában nem elegendő az adatok sikeres hitelesítéséhez és eléréséhez.

Hogyan akadályozhatja meg, hogy az örökölt hitelesítést használó alkalmazások hozzáférjenek a bérlői erőforrásokhoz? Azt javasoljuk, hogy csak tiltsa le őket egy feltételes hozzáférési szabályzatban. Ha szükséges, csak bizonyos felhasználók és meghatározott hálózati helyek számára engedélyezi az örökölt hitelesítésen alapuló alkalmazások használatát.

A feltételes hozzáférési szabályzatok az első hitelesítési tényező befejezése után kényszerítve vannak. Ezért a feltételes hozzáférés nem tekinthető első vonalvédelmi megoldásnak olyan forgatókönyvekhez, mint a szolgáltatásmegtagadási (DoS-) támadások, de a hozzáférés meghatározásához képes az eseményekből származó jelek (például a bejelentkezési kockázati szint, a kérés helye stb.) alapján meghatározni a hozzáférést.

## <a name="implementation"></a>Implementálás

Ez a szakasz azt ismerteti, hogyan konfigurálhat feltételes hozzáférési szabályzatot az örökölt hitelesítés letiltása érdekében. 

### <a name="legacy-authentication-protocols"></a>Régi hitelesítési protokollok

Az alábbi beállítások régi hitelesítési protokolloknak minősülnek

- Hitelesített SMTP – A POP- és IMAP-ügyfelek e-mail-üzenetek küldésére használják.
- Automatikus észlelés – Az Outlook és az EAS-ügyfelek az Exchange Online postaládáihoz keresnek és csatlakoznak.
- Exchange ActiveSync (EAS) – Postaládákhoz való csatlakozásra használható az Exchange Online-ban.
- Exchange Online PowerShell – Távoli PowerShell-kapcsolaton keresztül csatlakozik az Exchange Online-hoz. Ha letiltja az Exchange Online PowerShell alapszintű hitelesítését, a csatlakozáshoz az Exchange Online PowerShell-modult kell használnia. Útmutatásért lásd: [Csatlakozás az Exchange Online PowerShellhez többtényezős hitelesítéssel.](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)
- Exchange Web Services (EWS) – Az Outlook, a Mac Outlook és a külső alkalmazások által használt programozási felület.
- IMAP4 – Az IMAP e-mail-ügyfelek használják.
- MAPI HTTP-protokollon (MAPI/HTTP) – Az Outlook 2010 és újabb verziójában használatos.
- Offline címjegyzék (OAB) – Az Outlook által letöltött és használt címlista-gyűjtemények másolata.
- Outlook Anywhere (HTTP-n keresztüli RPC) – Az Outlook 2016 és korábbi verziói használják.
- Outlook Szolgáltatás – A Mail és a Calendar alkalmazás használja a Windows 10.
- POP3 – A POP e-mail-ügyfelek használják.
- Jelentéskészítési webszolgáltatások – Jelentésadatok lekérésére használható az Exchange Online-ban.
- Egyéb ügyfelek – Az örökölt hitelesítést használóként azonosított egyéb protokollok.

A hitelesítési protokollokkal és szolgáltatásokkal kapcsolatos további információkért lásd a bejelentkezési tevékenységre vonatkozó jelentéseket a [Azure Active Directory portálon.](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)

### <a name="identify-legacy-authentication-use"></a>Régi hitelesítési használat azonosítása

Mielőtt blokkolná az örökölt hitelesítést a címtárban, először meg kell értenie, hogy a felhasználók régebbi hitelesítést használó alkalmazásokkal és a teljes címtárra hatással vannak-e. Az Azure AD bejelentkezési naplói segítségével megértheti, hogy örökölt hitelesítést használ-e.

1. Lépjen a **Azure Portal**  >  **Azure Active Directory**  >  **bejelentkezések lapra.**
1. Ha nem jelenik meg, adja hozzá az Ügyfélalkalmazás oszlopot az Oszlopok  >  **ügyfélalkalmazás elemre kattintva.**
1. **Szűrők hozzáadása**  >  **Ügyfélalkalmazás >** az összes régi hitelesítési protokollt. A kiválasztások alkalmazáshoz és a párbeszédpanel bezárásához válassza a kijelölést a szűrési párbeszédpanelen kívül.
1. Ha aktiválta az [](../reports-monitoring/concept-all-sign-ins.md)új bejelentkezési tevékenységre vonatkozó jelentések előzetes kiadását, ismételje meg a fenti lépéseket a Felhasználói bejelentkezések **(nem interaktív)** lapon is.

A szűrés csak az örökölt hitelesítési protokollok bejelentkezési kísérletét mutatja meg. Az egyes bejelentkezési próbálkozások mindegyikére kattintva további részleteket talál. Az **Alapszintű információ** lap Ügyfélalkalmazás mezője **jelzi,** hogy melyik örökölt hitelesítési protokollt használta.

Ezek a naplók jelzik, hogy mely felhasználók függnek még az örökölt hitelesítéstől, és mely alkalmazások használnak örökölt protokollokat a hitelesítési kérések igényléséhez. Azok a felhasználók, akik nem jelennek meg ezekben a naplókban, és megerősítik, hogy nem használják az örökölt hitelesítést, csak ezekhez a felhasználókhoz implementálja a feltételes hozzáférési szabályzatot.

## <a name="block-legacy-authentication"></a>Régi hitelesítési folyamat letiltása 

A feltételes hozzáférési szabályzatok két módon tiltják le az örökölt hitelesítést.

- [Az örökölt hitelesítés közvetlen blokkolása](#directly-blocking-legacy-authentication)
- [Az örökölt hitelesítés közvetett blokkolása](#indirectly-blocking-legacy-authentication)
 
### <a name="directly-blocking-legacy-authentication"></a>Az örökölt hitelesítés közvetlen blokkolása

Az örökölt hitelesítés letiltását a legegyszerűbben úgy lehet letiltani a teljes szervezetben, ha konfigurál egy feltételes hozzáférési szabályzatot, amely kifejezetten az örökölt hitelesítési ügyfelekre vonatkozik, és letiltja a hozzáférést. Amikor felhasználókat és alkalmazásokat rendel a szabályzathoz, mindenképpen zárja ki azokat a felhasználókat és szolgáltatásfiókokat, amelyek továbbra is régi hitelesítéssel kell bejelentkezniük. Konfigurálja az ügyfélalkalmazások feltételét az **Exchange ActiveSync-ügyfelek és az** Egyéb ügyfelek lehetőség **kiválasztásával.** Ezeknek az ügyfélalkalmazásoknak a hozzáférését úgy tilthatja le, hogy a hozzáférés-vezérlést a Hozzáférés blokkolása beállításra konfigurálja.

![Az ügyfélalkalmazások feltétele úgy van konfigurálva, hogy letiltsa az örökölt hitelesítést](./media/block-legacy-authentication/client-apps-condition-configured-yes.png)

### <a name="indirectly-blocking-legacy-authentication"></a>Az örökölt hitelesítés közvetett blokkolása

Még ha a szervezet nem is áll készen az örökölt hitelesítés blokkolásra a teljes szervezetben, gondoskodnia kell arról, hogy az örökölt hitelesítést használó bejelentkezések ne kerüljék meg azokat a szabályzatokat, amelyek engedély-vezérlést igényelnek, például megkövetelik a többtényezős hitelesítést vagy a megfelelő/hibrid Azure AD-hez csatlakozott eszközöket. A hitelesítés során az örökölt hitelesítési ügyfelek nem támogatják az MFA küldését, az eszköz megfelelőségét vagy az állapotinformációk Azure AD-be való beléptetését. Ezért alkalmazza a szabályzatokat megadó vezérlőkkel az összes ügyfélalkalmazásra, hogy a rendszer letiltsa az örökölt hitelesítésen alapuló bejelentkezéseket, amelyek nem teljesítik a hozzáférés-vezérlést. Ha 2020 augusztusában általánosan elérhető az ügyfélalkalmazások feltétele, az újonnan létrehozott feltételes hozzáférési szabályzatok alapértelmezés szerint minden ügyfélalkalmazásra érvényesek.

![Ügyfélalkalmazások feltételének alapértelmezett konfigurációja](./media/block-legacy-authentication/client-apps-condition-configured-no.png)

## <a name="what-you-should-know"></a>Alapismeretek

A hozzáférés más ügyfelekkel **való** letiltása az Exchange Online PowerShellt és a Dynamics 365-öt is letiltja alapszintű hitelesítéssel.

Az Egyéb ügyfelek házirend **konfigurálása** letiltja a teljes szervezetet bizonyos ügyfelektől, például az SPConnecttől. Ez a blokk azért történik, mert a régebbi ügyfelek nem várt módon hitelesítik magukat. A probléma nem vonatkozik a nagyobb Office-alkalmazásokra, például a régebbi Office-ügyfelekre.

Akár 24 órát is igénybe vehet, hogy a szabályzat életbe lép.

Az Egyéb ügyfelek feltételhez az összes elérhető **engedélyvezérlőt kiválaszthatja;** A végfelhasználói élmény azonban mindig ugyanaz – a hozzáférés le van tiltva.

### <a name="sharepoint-online-and-b2b-guest-users"></a>SharePoint Online- és B2B-vendégfelhasználók

A B2B-felhasználók SharePoint Online-hitelesítéssel történő, örökölt hitelesítéssel való letiltásához a szervezeteknek le kell tiltaják az örökölt hitelesítést a SharePointban a PowerShell-paranccsal, és a paramétert a következőre kell `Set-SPOTenant` `-LegacyAuthProtocolsEnabled` be kapcsolniuk: `$false` . A paraméter beállításával kapcsolatos további információt a SharePoint PowerShell [Set-SPOTenant](/powershell/module/sharepoint-online/set-spotenant) parancsával kapcsolatos referenciadokumentumban talál.

## <a name="next-steps"></a>Következő lépések

- [Hatás meghatározása a feltételes hozzáférés csak jelentési módjával](howto-conditional-access-insights-reporting.md)
- Ha még nem ismeri a feltételes hozzáférési szabályzatok konfigurálását, tekintse meg az [MFA](../authentication/tutorial-enable-azure-mfa.md) megkövetelása adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel.
- A modern hitelesítési támogatással kapcsolatos további információkért lásd: [How modern authentication works for Office 2013 and Office 2016 client apps](/office365/enterprise/modern-auth-for-office-2013-and-2016) (Hogyan működik a modern hitelesítés az Office 2013-ban és az Office 2016 ügyfélalkalmazásokban?) 
- [Többfunkciós eszköz vagy alkalmazás beállítása e-mailek e-mailek küld Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)