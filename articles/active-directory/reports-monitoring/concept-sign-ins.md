---
title: Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon | Microsoft Docs
description: A bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon – bevezetés
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d61962667953b20f4b542874e902411bb579b9c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93122843"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon

A Azure Active Directory (Azure AD) jelentéskészítési architektúrája a következő összetevőkből áll:

- **Tevékenység** 
    - **Bejelentkezések** – információk a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.
    - **Naplók**  -  A [naplók](concept-audit-logs.md) rendszertevékenységi információkat biztosítanak a felhasználókról és a csoport kezeléséről, a felügyelt alkalmazásokról és a címtárbeli tevékenységekről.
    - Naplók kiépítés   -  A [kiépítési naplók](./concept-provisioning-logs.md) lehetővé teszik az ügyfelek számára, hogy a kiépítési szolgáltatás által végzett tevékenységeket figyeljenek, például egy csoport létrehozását a ServiceNow-ben, vagy egy munkanapokból importált felhasználót. 
- **Biztonság** 
    - **Kockázatos bejelentkezések** – a [kockázatos bejelentkezés](../identity-protection/overview-identity-protection.md) egy olyan bejelentkezési kísérlet, amely nem a felhasználói fiók legitim tulajdonosa.
    - **Kockázatnak** kitett felhasználók – a [kockázatos felhasználók](../identity-protection/overview-identity-protection.md) egy olyan felhasználói fiókra vonatkozó jelző, amely esetleg sérült.

Ez a cikk áttekintést nyújt a bejelentkezési jelentésről.

## <a name="prerequisites"></a>Előfeltételek

### <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?

* A biztonsági rendszergazda, a biztonsági olvasó, a globális olvasó és a jelentési olvasó szerepkör felhasználói
* Globális rendszergazdák
* Bármely (nem rendszergazda jogosultságú) felhasználó hozzáfér a saját bejelentkezéseihez 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Milyen Azure AD-licencre van szükség a bejelentkezési tevékenységhez való hozzáféréshez?

A bejelentkezési tevékenységről szóló jelentés az [Azure ad minden kiadásában](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data) elérhető, és a Microsoft Graph API-n keresztül is elérhető.

## <a name="sign-ins-report"></a>Bejelentkezések jelentés

A felhasználói bejelentkezések jelentése a következő kérdésekre ad választ:

* Milyen egy adott felhasználó bejelentkezési mintázata?
* Hány felhasználó jelentkezett be egy adott héten?
* Milyen állapotúak ezek a bejelentkezések?

A [Azure Portal](https://portal.azure.com) menüben válassza a **Azure Active Directory** lehetőséget, vagy keresse meg és válassza ki az **Azure Active Directory** elemet bármelyik oldalon.

![Azure Active Directory kiválasztása](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

A **figyelés** területen válassza a **bejelentkezések** lehetőséget a [bejelentkezések jelentés](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)megnyitásához.

![A képernyőképen a figyelés menüből kiválasztott bejelentkezések láthatók.](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Bejelentkezési tevékenység")

Néhány bejelentkezési rekord esetében akár két órát is igénybe vehet, hogy megjelenjenek a portálon.

> [!IMPORTANT]
> A bejelentkezési jelentés csak azokat az **interaktív** bejelentkezéseket jeleníti meg, amelyek a felhasználók saját felhasználónevével és jelszavával történő manuális bejelentkezéssel jelentkeznek be. A nem interaktív bejelentkezések, mint például a szolgáltatások közötti hitelesítés, nem jelennek meg a bejelentkezési jelentésben. 

A bejelentkezési napló alapértelmezett listanézete az alábbi adatokat jeleníti meg:

- A bejelentkezés dátuma
- A kapcsolódó felhasználó
- Az alkalmazás, amelybe a felhasználó bejelentkezett
- A bejelentkezési állapot
- A kockázatészlelés állapota
- A többtényezős hitelesítési (MFA-) követelmény állapota

![A képernyőképen az Office 365 SharePoint Online-bejelentkezések láthatók.](./media/concept-sign-ins/sign-in-activity.png "Bejelentkezési tevékenység")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![A képernyőképen az oszlopok lehetőség jelenik meg a bejelentkezések oldalon.](./media/concept-sign-ins/19.png "Bejelentkezési tevékenység")

Az **oszlopok** párbeszédpanel lehetővé teszi a választható attribútumok elérését. Egy bejelentkezési jelentésben nem rendelkezhet olyan mezőkkel, amelyek egynél több értékkel rendelkeznek egy adott bejelentkezési kérelemhez oszlopként. Ez például igaz a hitelesítés részleteire, a feltételes hozzáférési adatokra és a hálózati helyre.   

![A képernyőképen az oszlopok párbeszédpanel látható, ahol kiválaszthatja az attribútumokat.](./media/concept-sign-ins/columns.png "Bejelentkezési tevékenység")

Részletesebb információkhoz jelöljön ki egy elemet a listanézet nézetben.

![A képernyőképen részletes információk láthatók.](./media/concept-sign-ins/basic-sign-in.png "Bejelentkezési tevékenység")

> [!NOTE]
> Az ügyfelek mostantól a bejelentkezési jelentéseken keresztül is elhárítják a feltételes hozzáférési házirendeket. Ha a bejelentkezési rekord **feltételes hozzáférés** lapjára kattint, az ügyfelek áttekinthetik a feltételes hozzáférési állapotot, és bemutatják a bejelentkezésre alkalmazott szabályzatok részleteit, valamint az egyes házirendek eredményét.
> További információ: a [hitelesítésszolgáltatói információkkal kapcsolatos gyakori kérdések az összes bejelentkezésnél](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>A bejelentkezési tevékenységek szűrése

Először Szűkítse le a jelentett adathalmazt egy olyan szintre, amely az Ön számára működik. Másodszor, a Date mezőt használó bejelentkezési adatok szűrése alapértelmezett szűrőként. Az Azure AD számos további szűrőt is beállíthat:

![Képernyőfelvétel: a szűrők hozzáadása lehetőség.](./media/concept-sign-ins/04.png "Bejelentkezési tevékenység")

**Kérelem azonosítója** – a kért kérés azonosítója.

**Felhasználó** – a felhasználó nevét vagy egyszerű felhasználónevét (UPN) adja meg.

**Alkalmazás** – a célalkalmazás neve.
 
**Állapot** – a bejelentkezési állapot, amely érdekli:

- Siker

- Hiba

- Megszakadt


**IP-cím** – a bérlőhöz való kapcsolódáshoz használt eszköz IP-címe.

A **hely** – a hálózatról kezdeményezett hely:

- City

- Állam/megye

- Ország/régió


**Erőforrás** – a bejelentkezéshez használt szolgáltatás neve.


**Erőforrás-azonosító** – a bejelentkezéshez használt szolgáltatás azonosítója.


**Ügyfélalkalmazás** – a bérlőhöz való kapcsolódáshoz használt ügyfélalkalmazás típusa:

![Ügyfélalkalmazás-szűrő](./media/concept-sign-ins/client-app-filter.png)


|Name|Modern hitelesítés|Leírás|
|---|:-:|---|
|Hitelesített SMTP| |Az e-mail-üzenetek küldéséhez használja a POP és az IMAP-ügyfél.|
|Automatikus észlelési| |Az Outlook és az EAS-ügyfelek használják a postaládák keresésére és az Exchange Online-ban való kapcsolódására.|
|Exchange ActiveSync| |Ez a szűrő az összes olyan bejelentkezési kísérletet megjeleníti, ahol az EAS-protokollt megkísérelték.|
|Böngésző|![Kék pipa.](./media/concept-sign-ins/check.png)|Az összes bejelentkezési kísérlet megjelenítése a felhasználóktól a böngészőben|
|Exchange ActiveSync| | Az Exchange ActiveSync használatával az Exchange Online-hoz való kapcsolódáshoz szükséges összes bejelentkezési kísérletet megjeleníti a felhasználóktól|
|Exchange Online PowerShell| |Az Exchange Online-hoz távoli PowerShell-lel való kapcsolódáshoz használatos. Ha letiltja az Exchange Online PowerShell alapszintű hitelesítését, a kapcsolódáshoz az Exchange Online PowerShell-modult kell használnia. Útmutatásért lásd: [Kapcsolódás az Exchange Online powershellhez a multi-Factor Authentication használatával](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).|
|Webes Exchange-szolgáltatások| |Az Outlook, az Outlook for Mac és a harmadik féltől származó alkalmazások által használt programozási felület.|
|IMAP4| |Egy örökölt levelezési ügyfélprogram, amely az IMAP használatával kéri le az e-maileket.|
|MAPI HTTP-n keresztül| |Az Outlook 2010 és újabb verziók használják.|
|Mobile apps és asztali ügyfelek|![Kék pipa.](./media/concept-sign-ins/check.png)|Megjeleníti a felhasználók által a Mobile apps és az asztali ügyfelek használatával történő bejelentkezési kísérleteket.|
|Kapcsolat nélküli címjegyzék| |Az Outlook által letöltött és használt címlista-gyűjtemények másolata.|
|Outlook bárhol (RPC HTTP-n keresztül)| |Az Outlook 2016 és korábbi verziók használják.|
|Outlook-szolgáltatás| |A Windows 10 rendszerhez készült mail és naptár alkalmazás használja.|
|POP3| |Egy örökölt levelezési ügyfélprogram, amely a POP3 használatával kéri le az e-maileket.|
|Jelentéskészítési webszolgáltatások| |A Jelentésadatok az Exchange Online-ban való lekéréséhez használatos.|
|Más ügyfelek| |Megjeleníti az összes olyan bejelentkezési kísérletet, amely a felhasználóktól nem tartalmaz vagy ismeretlen ügyfélalkalmazás.|



**Operációs rendszer** – az eszközön futó operációs rendszer a bérlőre való bejelentkezést használta. 


**Eszköz böngészője** – ha a kapcsolat egy böngészőből indult el, ez a mező lehetővé teszi a szűrést a böngésző neve alapján.


**Korrelációs azonosító** – a tevékenység korrelációs azonosítója.




**Feltételes hozzáférés** – az alkalmazott feltételes hozzáférési szabályok állapota

- **Nincs alkalmazva**: a rendszer nem alkalmaz szabályzatot a felhasználóra és az alkalmazásra a bejelentkezés során.

- **Sikeres**: egy vagy több, a felhasználóra és alkalmazásra alkalmazott feltételes hozzáférési szabályzat (de nem feltétlenül a többi feltétel) a bejelentkezés során. 

- **Hiba**: a bejelentkezés megfelelt legalább egy feltételes hozzáférési házirend felhasználói és alkalmazási feltételének, és a vezérlők nem teljesülnek, vagy a hozzáférés letiltására van beállítva.









## <a name="download-sign-in-activities"></a>Bejelentkezési tevékenységek letöltése

Kattintson a **Letöltés** lehetőségre a legfrissebb 250 000-rekordok CSV-vagy JSON-fájljának létrehozásához. Ha a Azure Portalon kívül szeretne dolgozni vele, kezdje [a bejelentkezési adatszolgáltatások letöltésével](quickstart-download-sign-in-report.md) .  

![Letöltés](./media/concept-sign-ins/71.png "Letöltés")

> [!IMPORTANT]
> A letöltendő rekordok számát a [Azure Active Directory jelentés adatmegőrzési szabályzata](reference-reports-data-retention.md)korlátozza.  


## <a name="sign-ins-data-shortcuts"></a>Bejelentkezési adathivatkozások

Az Azure AD és a Azure Portal egyaránt biztosít további belépési pontokat a bejelentkezési adathoz:

- Az Identitáskezelés biztonsági védelme – áttekintés
- Felhasználók
- Csoportok
- Vállalati alkalmazások

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Felhasználói bejelentkezések adatai az Identity Security Protectionben

A felhasználói bejelentkezési gráf a **személyazonosság biztonsági védelme** áttekintése oldalon a bejelentkezések heti összesítéseit jeleníti meg. Az időtartam alapértelmezett értéke 30 nap.

![A képernyőképen egy hónap alatt a bejelentkezések gráfja látható.](./media/concept-sign-ins/06.png "Bejelentkezési tevékenység")

A bejelentkezési grafikon egyik napjára kattintva áttekintést kap az adott nap bejelentkezési tevékenységeiről.

A bejelentkezési tevékenységek listájának minden sora a következőkről ad információkat:

* Ki jelentkezett be?
* Melyik alkalmazás volt a bejelentkezés célja?
* Mi a bejelentkezés állapota?
* Mi a bejelentkezés MFA-állapota?

Az elemekre kattintva részletes információk érhetők el a bejelentkezési műveletről:

- Felhasználóazonosító
- User
- Felhasználónév
- Alkalmazásazonosító
- Alkalmazás
- Ügyfél
- Hely
- IP-cím
- Date
- Az MFA megadása kötelező
- Bejelentkezés állapota

> [!NOTE]
> Az IP-címek oly módon vannak kiadva, hogy az IP-cím és az azt tartalmazó számítógép fizikailag ne legyen végleges kapcsolat. Az IP-címek leképezése az a tény, hogy a mobilszolgáltatók és a VPN-ek olyan központi készletekből származó IP-címeket adnak ki, amelyek gyakran nagyon távol vannak az ügyfél eszközének tényleges használatáról. Jelenleg az Azure AD-jelentésekben az IP-címek fizikai helyre konvertálása a legjobb megoldás a Nyomkövetések, a beállításjegyzék-adatok, a fordított irányú keresés és egyéb információk alapján.

A **Felhasználók** oldalon teljes körű áttekintést kaphat az összes felhasználói bejelentkezésről a **Tevékenységek** szakaszban található **Bejelentkezések** elemre kattintva.

![Képernyőfelvétel: a tevékenység szakasz, ahol kiválaszthatja a bejelentkezéseket.](./media/concept-sign-ins/08.png "Bejelentkezési tevékenység")

## <a name="usage-of-managed-applications"></a>Felügyelt alkalmazások használati adatai

A bejelentkezési információk alkalmazás-központú nézetével az alábbi kérdésekre kaphat választ:

* Ki használja az alkalmazásaimat?
* Mi a szervezet első három alkalmazása?
* Hogyan csinálom a legújabb alkalmazást?

Ezen adat beléptetési pontja a szervezet első három alkalmazása. Az adat a **vállalati alkalmazások** **Áttekintés** szakaszában, az elmúlt 30 nap jelentésében található.

![Képernyőfelvétel: Itt választhatja ki az Áttekintés lehetőséget.](./media/concept-sign-ins/10.png "Bejelentkezési tevékenység")

Az alkalmazás-használati gráfok hetente összesítik az első három alkalmazás bejelentkezéseit egy adott időszakban. Az alapértelmezett időszak 30 nap.

![Képernyőfelvétel: az alkalmazás használatának egy hónapos időszakra vonatkozó megjelenítése.](./media/concept-sign-ins/graph-chart.png "Bejelentkezési tevékenység")

Igény esetén egy adott alkalmazást is kiemelhet.

![Jelentéskészítés](./media/concept-sign-ins/single-app-usage-graph.png "Jelentéskészítés")

Az alkalmazáshasználati grafikon egyik napjára kattintva részletes listát kap a bejelentkezési tevékenységekről.

A **Bejelentkezések** lehetőség az alkalmazások összes bejelentkezési eseményének teljes körű áttekintését biztosítja.

## <a name="microsoft-365-activity-logs"></a>Microsoft 365 tevékenység naplói

A [Microsoft 365 felügyeleti központban](/office365/admin/admin-overview/about-the-admin-center)megtekintheti Microsoft 365 tevékenység naplóit. Vegye figyelembe, hogy Microsoft 365 tevékenység és az Azure AD-tevékenység naplói jelentős számú címtár-erőforrást osztanak meg. Csak a Microsoft 365 felügyeleti központ biztosítja a Microsoft 365 tevékenység naplóinak teljes nézetét. 

Az [Office 365 felügyeleti API](/office/office-365-management-api/office-365-management-apis-overview)-k használatával programozott módon is elérheti a Microsoft 365 tevékenység naplóit.

## <a name="next-steps"></a>Következő lépések

* [Bejelentkezési tevékenység jelentésének hibakódja](reference-sign-ins-error-codes.md)
* [Az Azure AD adatmegőrzési szabályzatai](reference-reports-data-retention.md)
* [Azure AD-jelentés késései](reference-reports-latencies.md)