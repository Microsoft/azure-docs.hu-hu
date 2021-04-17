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
ms.date: 04/16/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99f1f27cb087dc83295dddade4c0fca551a0d9c9
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589686"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon

A Azure Active Directory portál három tevékenységnaplóhoz biztosít hozzáférést:

- **Bejelentkezések** – Információk a bejelentkezésekkel és az erőforrások felhasználók által való felhasználását.
- **[Naplózás](concept-audit-logs.md)** – A bérlőre alkalmazott változásokkal kapcsolatos információk, például felhasználók és csoportkezelés, vagy a bérlői erőforrásokra alkalmazott frissítések.
- **[Kiépítés](concept-provisioning-logs.md)** – A kiépítési szolgáltatás által végrehajtott tevékenységek, például egy csoport létrehozása a ServiceNow-ban vagy egy Workdayből importált felhasználó.

Ez a cikk áttekintést nyújt a bejelentkezési jelentésről.

## <a name="prerequisites"></a>Előfeltételek

### <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?

* A Biztonsági rendszergazda, a Biztonsági olvasó, a Globális olvasó és a Jelentésolvasó szerepkörben szereplő felhasználók
* Globális rendszergazdák
* Bármely (nem rendszergazda jogosultságú) felhasználó hozzáfér a saját bejelentkezéseihez 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Milyen Azure AD-licencre van szükség a bejelentkezési tevékenységhez való hozzáféréshez?

A bejelentkezési tevékenységre vonatkozó jelentés az [Azure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data) összes kiadásában elérhető, és a Microsoft Graph API-n keresztül is elérhető.

## <a name="sign-ins-report"></a>Bejelentkezési jelentés

A felhasználói bejelentkezési jelentés a következő kérdésekre ad választ:

* Milyen egy adott felhasználó bejelentkezési mintázata?
* Hány felhasználó jelentkezett be egy adott héten?
* Milyen állapotúak ezek a bejelentkezések?

A [Azure Portal](https://portal.azure.com) menüben válassza a **Azure Active Directory** lehetőséget, vagy keresse meg és Azure Active Directory **bármelyik** oldalról.

![Válassza a Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

A **Figyelés** alatt **válassza a Bejelentkezések** lehetőséget a [Bejelentkezések jelentés megnyitásához.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)

![Képernyőkép a Figyelés menüből kiválasztott bejelentkezések elemről.](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Bejelentkezési tevékenység")

Néhány bejelentkezési rekord a portálon való megjelenítése akár két órát is igénybe vehet.

> [!IMPORTANT]
> A bejelentkezési jelentés csak az  interaktív bejelentkezéseket jeleníti meg, azaz azokat a bejelentkezéseket, amelyekben a felhasználó manuálisan jelentkezik be a felhasználónevével és jelszavával. A nem interaktív bejelentkezések, például a szolgáltatások között való hitelesítés, nem jelennek meg a bejelentkezési jelentésben. 

A bejelentkezési napló alapértelmezett listanézete az alábbi adatokat jeleníti meg:

- A bejelentkezés dátuma
- A kapcsolódó felhasználó
- Az alkalmazás, amelybe a felhasználó bejelentkezett
- A bejelentkezési állapot
- A kockázatészlelés állapota
- A többtényezős hitelesítési (MFA-) követelmény állapota

![Képernyőkép az Office 365 SharePoint Online-bejelentkezésről.](./media/concept-sign-ins/sign-in-activity.png "Bejelentkezési tevékenység")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Képernyőkép az Oszlopok lehetőségről a Bejelentkezések lapon.](./media/concept-sign-ins/19.png "Bejelentkezési tevékenység")

Az **Oszlopok** párbeszédpanelen hozzáférhet a választható attribútumokhoz. A bejelentkezési jelentésekben nem lehet olyan mező, amely egynél több értékkel bír egy adott bejelentkezési kérelemhez oszlopként. Ez például igaz a hitelesítési adatokra, a feltételes hozzáférési adatokra és a hálózati helyre.   

![Képernyőkép az Oszlopok párbeszédpanelről, ahol attribútumokat választhat ki.](./media/concept-sign-ins/columns.png "Bejelentkezési tevékenység")

Részletesebb információkért válasszon ki egy elemet a listanézetben.

![Részletes információs nézet képernyőképe.](./media/concept-sign-ins/basic-sign-in.png "Bejelentkezési tevékenység")

> [!NOTE]
> Az ügyfelek mostantól az összes bejelentkezési jelentésen keresztül hibaelhárítást kaphatnak a feltételes hozzáférési szabályzatokkal. A bejelentkezési  rekord Feltételes hozzáférés lapfülére kattintva az ügyfelek áttekinthetik a feltételes hozzáférés állapotát, és részletesen áttekintheti a bejelentkezésre alkalmazott szabályzatokat és az egyes szabályzatok eredményét.
> További információkért tekintse meg a hitelesítésszolgáltatói információkkal kapcsolatos gyakori kérdéseket [az összes bejelentkezésben.](reports-faq.md#conditional-access)


## <a name="sign-in-error-code"></a>Bejelentkezési hibakód

Ha egy bejelentkezés sikertelen volt, a kapcsolódó naplóelem  Alapvető információk szakaszában további információt kaphat az okról. 

![bejelentkezési hibakód](./media/concept-all-sign-ins/error-code.png)
 
Bár a naplóelem a hiba okát is tartalmazza, előfordulhatnak olyan esetek, amikor további információkat kap a bejelentkezési [hibakeresési eszközével.](https://login.microsoftonline.com/error) Ha például elérhető, ez az eszköz biztosítja a szervizelés lépéseit.  

![Hibakód-keresési eszköz](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities&quot;></a>A bejelentkezési tevékenységek szűrése

Először is szűkítse le a jelentett adatokat az Ön számára megfelelő szintre. Másodszor szűrje a bejelentkezési adatokat alapértelmezett szűrőként a dátummezővel. Az Azure AD további szűrők széles körét kínálja, amelyek beállíthatók:

![A Szűrők hozzáadása lehetőséget bemutató képernyőkép.](./media/concept-sign-ins/04.png &quot;Bejelentkezési tevékenység")

**Kérelemazonosító** – Az Ön számára fontos kérés azonosítója.

**Felhasználó** – Az Ön számára fontos felhasználó neve vagy egyszerű felhasználóneve (UPN).

**Alkalmazás** – A célalkalmazás neve.
 
**Állapot** – Az Ön számára fontos bejelentkezési állapot:

- Siker

- Hiba

- Megszakított


**IP-cím** – A bérlőhöz való csatlakozáshoz használt eszköz IP-címe.

Hely **–** A kapcsolat kezdeményezésének helye:

- City

- Állam/megye

- Ország/régió


**Erőforrás** – A bejelentkezéshez használt szolgáltatás neve.


**Erőforrás-azonosító** – A bejelentkezéshez használt szolgáltatás azonosítója.


**Ügyfélalkalmazás** – A bérlőhöz való csatlakozáshoz használt ügyfélalkalmazás típusa:

![Ügyfélalkalmazás szűrője](./media/concept-sign-ins/client-app-filter.png)


|Name|Modern hitelesítés|Description|
|---|:-:|---|
|Hitelesített SMTP| |A POP- és IMAP-ügyfelek e-mail-üzenetek küldésére használják.|
|Automatikus észlelés| |Az Outlook és az EAS-ügyfelek használják postaládák keresésére és csatlakoztatásra az Exchange Online-ban.|
|Exchange ActiveSync| |Ez a szűrő az összes olyan bejelentkezési kísérletet megjeleníti, ahol az EAS protokollt megkísérelték.|
|Böngésző|![Kék pipa.](./media/concept-sign-ins/check.png)|Megjeleníti a felhasználók összes bejelentkezési kísérletét webböngészők használatával|
|Exchange ActiveSync| | Megjeleníti az exchange ActiveSyncet az Exchange Online-hoz való csatlakozásra használó ügyfélalkalmazásokat használó felhasználók bejelentkezési kísérletét|
|Exchange Online PowerShell| |Az Exchange Online-hoz való távoli PowerShell-kapcsolathoz használható. Ha blokkolja az Exchange Online PowerShell alapszintű hitelesítését, a csatlakozáshoz az Exchange Online PowerShell-modult kell használnia. Útmutatásért lásd: [Csatlakozás az Exchange Online PowerShellhez többtényezős hitelesítéssel.](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)|
|Webes Exchange-szolgáltatások| |Az Outlook, a Mac Outlook és a külső alkalmazások által használt programozási felület.|
|IMAP4| |Egy örökölt levelezési ügyfélprogram, amely az IMAP használatával olvassa be az e-maileket.|
|MAPI HTTP-protokollon keresztül| |Az Outlook 2010 és újabb verziójában használható.|
|Mobilalkalmazások és asztali ügyfelek|![Kék pipa.](./media/concept-sign-ins/check.png)|Megjeleníti a mobilalkalmazásokat és asztali ügyfeleket használó felhasználók bejelentkezési kísérleteit.|
|Offline címjegyzék| |Az Outlook által letöltött és használt címlista-gyűjtemények másolata.|
|Outlook Anywhere (HTTP-n keresztüli RPC)| |Az Outlook 2016 és korábbi verziói használják.|
|Outlook szolgáltatás| |A Mail és a Calendar alkalmazás használja a Windows 10.|
|POP3| |Egy pop3-as e-mail-címet használó örökölt levelezőprogram az e-mailek lekérésére.|
|Jelentéskészítési webszolgáltatások| |Jelentésadatok lekérésére használható az Exchange Online-ban.|
|Más ügyfelek| |Megjeleníti azon felhasználók bejelentkezési kísérletét, akiknél az ügyfélalkalmazás nincs benne vagy ismeretlen.|



**Operációs rendszer** – Az eszközön futó operációs rendszer, amely a bérlőbe való bejelentkezést használta. 


**Eszközböngésző** – Ha a kapcsolatot egy böngészőből kezdeményezték, ez a mező lehetővé teszi a böngészőnév alapján való szűrést.


**Korrelációs** azonosító – A tevékenység korrelációs azonosítója.




**Feltételes hozzáférés** – Az alkalmazott feltételes hozzáférési szabályok állapota

- **Nincs alkalmazva:** Nincs házirend alkalmazva a felhasználóra és az alkalmazásra a bejelentkezés során.

- **Sikeres:** Egy vagy több feltételes hozzáférési szabályzat érvényes a felhasználóra és az alkalmazásra (de nem feltétlenül a többi feltételre) a bejelentkezés során. 

- **Hiba:** A bejelentkezés legalább egy feltételes hozzáférési szabályzat felhasználóra és alkalmazásra vonatkozó feltételének teljesült, és a vezérlők nem teljesülnek, vagy a hozzáférés letiltása van beállítva.









## <a name="download-sign-in-activities"></a>Bejelentkezési tevékenységek letöltése

Kattintson **a Letöltés lehetőségre** a legutóbbi 250 000 rekord CSV- vagy JSON-fájljainak létrehozásához. Kezdje a [bejelentkezési adatok](quickstart-download-sign-in-report.md) letöltésével, ha az alkalmazáson kívül szeretné Azure Portal.  

![Letöltés](./media/concept-sign-ins/71.png "Letöltés")

> [!IMPORTANT]
> A letölthető rekordok számát a jelentésmegőrzési [szabályzatok Azure Active Directory korlátozza.](reference-reports-data-retention.md)  


## <a name="sign-ins-data-shortcuts"></a>Bejelentkezési adatok parancsikonjai

Az Azure AD és a Azure Portal is további belépési pontokat biztosítanak a bejelentkezési adatokhoz:

- Az Identity Security Protection áttekintése
- Felhasználók
- Csoportok
- Vállalati alkalmazások

### <a name="users-sign-ins-data-in-identity-security-protection"></a>A felhasználók az Identity Security Protectionben jelentkeznek be az adatokba

Az Identity **Security Protection** áttekintő oldalán található felhasználói bejelentkezési grafikon a bejelentkezések heti összesítését jeleníti meg. Az időszak alapértelmezett beállítása 30 nap.

![Képernyőkép az egy hónapra lekért bejelentkezések grafikonján.](./media/concept-sign-ins/06.png "Bejelentkezési tevékenység")

A bejelentkezési grafikon egyik napjára kattintva áttekintést kap az adott nap bejelentkezési tevékenységeiről.

A bejelentkezési tevékenységek listájának minden sora a következőkről ad információkat:

* Ki jelentkezett be?
* Melyik alkalmazás volt a bejelentkezés célja?
* Mi a bejelentkezés állapota?
* Mi a bejelentkezés MFA-állapota?

Az elemekre kattintva részletes információk érhetők el a bejelentkezési műveletről:

- Felhasználóazonosító
- Felhasználó
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
> Az IP-címek úgy vannak kibocsátva, hogy nincs határozott kapcsolat az IP-cím és az adott címmel fizikailag elhelyezkedő számítógép között. Az IP-címek leképezése bonyolult feladat, mert a mobilszolgáltatók és a VPN-ek olyan központi készletekből származó IP-címeket adnak ki, amelyek gyakran nagyon távol vannak az ügyféleszköz használatának helyéhez. Jelenleg az Azure AD-jelentésekben az IP-címek fizikai helyre konvertálása a legjobb megoldás nyomkövetések, beállításjegyzék-adatok, fordított névkeresések és egyéb információk alapján.

A **Felhasználók** oldalon teljes körű áttekintést kaphat az összes felhasználói bejelentkezésről a **Tevékenységek** szakaszban található **Bejelentkezések** elemre kattintva.

![Képernyőkép a Tevékenység szakaszról, ahol kiválaszthatja a Bejelentkezések lehetőséget.](./media/concept-sign-ins/08.png "Bejelentkezési tevékenység")

## <a name="usage-of-managed-applications"></a>Felügyelt alkalmazások használati adatai

A bejelentkezési információk alkalmazás-központú nézetével az alábbi kérdésekre kaphat választ:

* Ki használja az alkalmazásaimat?
* Melyik az első három alkalmazás a szervezetben?
* Hogyan működik a legújabb alkalmazásom?

Az adatok belépési pontja a szervezeten belül az első három alkalmazás. Az adatokat az elmúlt 30 nap jelentése tartalmazza a Vállalati **alkalmazások** Áttekintés **szakaszában.**

![Az Áttekintés kiválasztásának képernyőképe.](./media/concept-sign-ins/10.png "Bejelentkezési tevékenység")

Az alkalmazáshasználati grafikonok a bejelentkezések heti összesítését ábrázolják az első három alkalmazáshoz egy adott időszakban. Az alapértelmezett időszak 30 nap.

![Képernyőkép az egy hónapos alkalmazáshasználatról.](./media/concept-sign-ins/graph-chart.png "Bejelentkezési tevékenység")

Igény esetén egy adott alkalmazást is kiemelhet.

![Jelentéskészítés](./media/concept-sign-ins/single-app-usage-graph.png "Jelentéskészítés")

Az alkalmazáshasználati grafikon egyik napjára kattintva részletes listát kap a bejelentkezési tevékenységekről.

A **Bejelentkezések** lehetőség az alkalmazások összes bejelentkezési eseményének teljes körű áttekintését biztosítja.

## <a name="microsoft-365-activity-logs"></a>Microsoft 365 tevékenységnaplók

A tevékenységnaplókat Microsoft 365 a következő [Microsoft 365 Felügyeleti központ:](/office365/admin/admin-overview/about-the-admin-center). Vegye figyelembe, hogy Microsoft 365 tevékenység és az Azure AD-tevékenységnaplók jelentős számú címtárerőforrást osztnak meg. Csak a Microsoft 365 Felügyeleti központ biztosít teljes körű áttekintést a Microsoft 365 tevékenységnaplókról. 

Az Office [365](/office/office-365-management-api/office-365-management-apis-overview)felügyeleti API Microsoft 365 programozott módon is elérheti a tevékenységnaplókat.

## <a name="next-steps"></a>Következő lépések

* [Bejelentkezési tevékenység jelentésének hibakódja](reference-sign-ins-error-codes.md)
* [Azure AD adatmegőrzési szabályzatok](reference-reports-data-retention.md)
* [Az Azure AD-jelentések késései](reference-reports-latencies.md)