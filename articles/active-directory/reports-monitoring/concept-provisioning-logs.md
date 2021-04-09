---
title: A naplók üzembe helyezésének áttekintése a Azure Portalban (előzetes verzió) | Microsoft Docs
description: Bemutatjuk, hogyan lehet bevezetni a naplózási jelentéseket Azure Active Directory a Azure Portal keresztül.
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
ms.date: 1/29/2021
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d8c4876faf9ebc2619309aa0095a8ffe1e9e93d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500546"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>A naplók kiépítési naplóinak áttekintése a Azure Portalban (előzetes verzió)

A Azure Active Directory (Azure AD) jelentéskészítési architektúrája a következő összetevőkből áll:

- Tevékenység: 
    - **Bejelentkezések**: a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról szóló információk.
    - [Naplók](concept-audit-logs.md): rendszertevékenység-információk a felhasználók és csoportok kezelésével, a felügyelt alkalmazásokkal és a címtárral kapcsolatos tevékenységekkel kapcsolatban.
    - **Naplók** kiosztása: rendszertevékenység az Azure ad-kiépítési szolgáltatás által kiépített felhasználókkal, csoportokkal és szerepkörökkel kapcsolatban. 

- Biztonság: 
    - **Kockázatos bejelentkezések**: a [kockázatos bejelentkezés](../identity-protection/overview-identity-protection.md) egy olyan bejelentkezési kísérletre utal, amelyet a felhasználói fiók tulajdonosának nem jogos tulajdonosa hajt végre.
    - A **kockázatnak** kitett felhasználók: egy [kockázatos felhasználó](../identity-protection/overview-identity-protection.md) egy olyan felhasználói fiókra vonatkozó jelző, amely esetleg sérült.

Ez a témakör áttekintést nyújt a kiépítési naplókról. A naplók választ adnak a következő kérdésekre: 

* Milyen csoportokat sikerült létrehozni a ServiceNow-ben?
* Milyen felhasználókat sikerült eltávolítani az Adobe-ból?
* A munkanapon belül sikeresen létrejöttek a felhasználók a Active Directory? 

## <a name="prerequisites"></a>Előfeltételek

Ezek a felhasználók a kiépítési naplókból férhetnek hozzá az adatkészletekhez:

* Alkalmazás tulajdonosai (saját alkalmazások naplói)
* Felhasználók a biztonsági rendszergazda, a biztonsági olvasó, a jelentéskészítő, a biztonsági operátor, az alkalmazás rendszergazdája és a Felhőbeli alkalmazás rendszergazdai szerepkörei
* Egyéni szerepkörbe tartozó felhasználók a [provisioningLogs engedéllyel](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)
* Globális rendszergazda


A kiépítési tevékenység jelentésének megtekintéséhez a bérlőnek hozzá kell rendelnie egy prémium szintű Azure AD-licencet. Az Azure AD-kiadás frissítéséhez tekintse meg a [prémium szintű Azure Active Directory első lépéseivel foglalkozó](../fundamentals/active-directory-get-started-premium.md)témakört. 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>A kiépítési naplókkal való interakció módjai 
Az ügyfelek négyféle módon kezelhetik a kiépítési naplókat:

- A naplók elérése a Azure Portalról a következő szakaszban leírtak szerint.
- A kiépítési naplók továbbítása a [Azure monitorba](../app-provisioning/application-provisioning-log-analytics.md). Ez a módszer lehetővé teszi a kiterjesztett adatmegőrzést, valamint az egyéni irányítópultok, riasztások és lekérdezések létrehozását.
- A kiépítési naplók [Microsoft Graph API](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta) -ját kérdezi le.
- A kiépítési naplók letöltése CSV-vagy JSON-fájlként.

## <a name="access-the-logs-from-the-azure-portal"></a>A naplók elérése a Azure Portal
A kiépítési naplókat úgy érheti el, ha kijelöli a **kiépítési** naplók lehetőséget a [Azure Portal](https://portal.azure.com) **Azure Active Directory** paneljének **figyelés** szakaszában. Akár két órát is igénybe vehet, hogy egyes kiépítési rekordok megjelenjenek a portálon.

![Képernyőkép, amely a kiépítési naplókhoz való hozzáférésre vonatkozó beállításokat jeleníti meg.](./media/concept-provisioning-logs/access-provisioning-logs.png "Üzembehelyezési naplók")


A kiépítési napló egy alapértelmezett listanézet, amely a következőket jeleníti meg:

- Az identitás
- A művelet
- A forrásoldali System
- A célként megadott System
- Az állapot
- A dátum


![Képernyőfelvétel: az alapértelmezett oszlopok megjelenítése egy kiépítési naplóban.](./media/concept-provisioning-logs/default-columns.png "Alapértelmezett oszlopok")

A listanézet kiválasztásával testreszabhatja az eszköztár **oszlopok** elemét.

![Képernyőkép, amely az oszlopok testreszabására szolgáló gombot jeleníti meg.](./media/concept-provisioning-logs/column-chooser.png "Oszlop kiválasztása")

Ez a terület lehetővé teszi további mezők megjelenítését vagy a már megjelenített mezők eltávolítását.

![Képernyőkép, amely néhány kiválasztott oszlopot jelenít meg.](./media/concept-provisioning-logs/available-columns.png "Elérhető oszlopok")

Részletesebb információkhoz jelöljön ki egy elemet a listanézet nézetben.

![Képernyőkép, amely részletes információkat jelenít meg.](./media/concept-provisioning-logs/steps.png "Szűrő")


## <a name="filter-provisioning-activities"></a>Kiépítési tevékenységek szűrése

A kiépítési adatait szűrheti. Egyes szűrési értékek dinamikusan vannak feltöltve a bérlő alapján. Ha például nem rendelkezik "Create" eseményekkel a bérlőben, nem lesz egy szűrő **létrehozása** lehetőség.

Az alapértelmezett nézetben a következő szűrőket választhatja ki:

- **Identitás**
- **Date**
- **Állapot**
- **Művelet**


![A szűrési értékeket bemutató képernyőkép.](./media/concept-provisioning-logs/default-filter.png "Szűrő")

Az **Identity** szűrő segítségével megadhatja a nevet vagy az Ön számára fontos identitást. Ez az identitás lehet felhasználó, csoport, szerepkör vagy más objektum. 

Az objektum neve vagy azonosítója alapján kereshet. Az azonosító forgatókönyv szerint változik. Ha például egy objektumot kiépít az Azure AD-ből a Salesforce-be, a forrás azonosítója az Azure AD-ben a felhasználó objektumazonosító. A cél azonosító a felhasználó azonosítója a Salesforce-ben. Ha munkahelyről Active Directoryra épít, a forrás azonosítója a munkanap munkavégző alkalmazottjának azonosítója. 

> [!NOTE]
> Lehet, hogy a felhasználó neve nem mindig szerepel az **Identity** oszlopban. Mindig egy azonosító lesz. 


A **Dátum** szűrővel időkeretet lehet meghatározni a visszaadott adatokhoz. Lehetséges értékek:

- 1 hónap
- 7 nap
- 30 nap
- 24 óra
- Egyéni időintervallum

Amikor kiválaszt egy egyéni időkeretet, beállíthatja a kezdési és a befejezési dátumot.

Az **állapot** szűrő segítségével a következőket választhatja ki:

- **Mind**
- **Siker**
- **Hiba**
- **Kimarad**

A **művelet** szűrője lehetővé teszi a következő műveletek szűrését:

- **Létrehozás** 
- **Frissítés**
- **Törlés**
- **Letiltás**
- **Egyéb**

Az alapértelmezett nézet szűrői mellett a következő szűrőket is beállíthatja.

![A szűrőként felvehető mezőket bemutató képernyőkép.](./media/concept-provisioning-logs/add-filter.png "Válasszon ki egy mezőt")

- **Job ID**: a rendszer minden olyan alkalmazáshoz társít egy egyedi azonosítójú feladatot, amelyhez engedélyezte az üzembe helyezést.   

- **Ciklus azonosítója**: a ciklus azonosítója egyedileg azonosítja a létesítési ciklust. Ezt az azonosítót a terméktámogatással is megoszthatja, hogy megkeresse a ciklust, amelyben ez az esemény történt.

- **Változás azonosítója**: a MÓDOSÍTÁSi azonosító a kiépítési esemény egyedi azonosítója. Ezt az azonosítót a terméktámogatással is megoszthatja a kiépítési esemény megkereséséhez.   

- **Forrásoldali rendszer**: megadhatja, hogy hol kell kiépíteni az identitást. Ha például egy objektumot kiépít az Azure AD-ből a ServiceNow-be, akkor a forrásoldali rendszer az Azure AD. 

- Célrendszer: megadhatja, hogy az identitás hol legyen kiépítve a **szolgáltatásba**. Ha például egy objektumot kiépít az Azure AD-ből a ServiceNow-be, akkor a rendszer ServiceNow. 

- **Alkalmazás**: csak azok a rekordok jelennek meg, amelyek megjelenítendő neve egy adott sztringet tartalmaz.

## <a name="provisioning-details"></a>Kiépítés részletei 

Amikor kiválaszt egy elemet a kiépítési lista nézetben, az elemről további részleteket talál. A részletek a következő lapokra vannak csoportosítva.

![Képernyőkép, amely a kiépítési részleteket tartalmazó négy lapot mutatja.](./media/concept-provisioning-logs/provisioning-tabs.png "Tabulátorok")

- **Lépések**: egy objektum kiépítéséhez szükséges lépések felvázolása. Az objektumok kiépítés négy lépésből állhat:
  
  1. Importálja az objektumot.
  1. Annak megállapítása, hogy az objektum hatókörben van-e.
  1. Egyezik a forrás és a cél közötti objektummal.
  1. Az objektum kiépítése (létrehozás, frissítés, törlés vagy Letiltás).

  ![Képernyőkép: a kiépítési lépések a lépések lapon láthatók.](./media/concept-provisioning-logs/steps.png "Szűrő")

- **Hibaelhárítási & javaslatok**: megadja a hibakódot és az okot. A hiba adatai csak akkor érhetők el, ha hiba történik.

- **Módosított tulajdonságok**: a régi értéket és az új értéket jeleníti meg. Ha nincs régi érték, az oszlop üres.

- **Összefoglalás**: áttekintést nyújt az objektumról a forrás-és a célszámítógépeken.

## <a name="download-logs-as-csv-or-json"></a>Naplók letöltése CSV-ként vagy JSON-ként

A kiépítési naplók későbbi használatra való letöltéséhez nyissa meg a Azure Portal naplóit, és válassza a **Letöltés** lehetőséget. A rendszer a kiválasztott szűrési feltételek alapján szűri a fájlt. A szűrőket a lehető legpontosabb módon végezze el a letöltés méretének és időpontjának csökkentése érdekében. 

A CSV letöltése három fájlt tartalmaz:

* **ProvisioningLogs**: az összes naplót letölti, a kiépítési lépések és a módosított tulajdonságok kivételével.
* **ProvisioningLogs_ProvisioningSteps**: a kiépítési lépéseket és a módosítási azonosítót tartalmazza. A Change ID használatával csatlakozhat az eseményhez a másik két fájllal.
* **ProvisioningLogs_ModifiedProperties**: a módosított attribútumokat és a módosítási azonosítót tartalmazza. A Change ID használatával csatlakozhat az eseményhez a másik két fájllal.

#### <a name="open-the-json-file"></a>A JSON-fájl megnyitása
A JSON-fájl megnyitásához használjon egy szövegszerkesztőt, például a [Microsoft Visual Studio Code](https://aka.ms/vscode)-ot. A Visual Studio Code megkönnyíti a fájl olvasását a szintaxis kiemelésének megadásával. A JSON-fájlt nem szerkeszthető formátumban, például a [Microsoft Edge](https://aka.ms/msedge)böngészőben is megnyithatja. 

#### <a name="prettify-the-json-file"></a>A JSON-fájl szépít
A letöltött JSON-fájl a letöltés méretének csökkentése érdekében minified formátumban van letöltve. Ez a formátum nehézvé teszi a hasznos adatok olvasását. Tekintse meg a következő két lehetőséget a fájl szépít:

- [A JSON formázásához használja a Visual Studio Code-](https://code.visualstudio.com/docs/languages/json#_formatting)ot.

- A JSON formázásához használja a PowerShellt. Ez a szkript a JSON-t olyan formátumban jeleníti meg, amely lapokat és szóközöket tartalmaz: 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>A JSON-fájl elemzése

Íme néhány példa arra, hogy hogyan dolgozhat a JSON-fájllal a PowerShell használatával. Bármilyen programozási nyelvet használhat, amellyel Ön is kényelmesen használható.  

Először [olvassa el a JSON-fájlt a](/powershell/module/microsoft.powershell.utility/convertfrom-json) következő parancs futtatásával:

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

Most már az adott forgatókönyvnek megfelelően elemezheti az adatelemzést. Bemutatunk néhány példát: 

- A JSON-fájlban lévő összes azonosítójú feladattípus kimenete:

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- Az összes olyan változási azonosító kimenete, amelynél a művelet "létrehozás":

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>Alapismeretek

Az alábbi tippek és szempontok a kiépítési jelentésekhez:

- Ha ingyenes kiadással rendelkezik, a Azure Portal 30 napig tárolja a kiépítési adatgyűjtési jelentést. A kiépítési naplókat úgy teheti közzé, hogy a 30 napon túli megőrzésre [log Analytics](../app-provisioning/application-provisioning-log-analytics.md) . 

- A Change ID attribútum egyedi azonosítóként használható. Ez akkor hasznos, ha például a terméktámogatással kommunikál.

- Előfordulhat, hogy a nem hatókörben lévő felhasználók számára a kihagyott események láthatók. Ez várható, különösen akkor, ha a szinkronizálási hatókör az összes felhasználóra és csoportra van beállítva. A szolgáltatás a bérlő összes objektumát kiértékeli, még a hatókörön kívül is. 

- A kiépítési naplók jelenleg nem érhetők el a kormányzati felhőben. Ha nem fér hozzá a kiépítési naplókhoz, használja a naplókat ideiglenes megkerülő megoldásként. 

- A kiépítési naplók nem jelenítik meg a szerepkör-importálásokat (az AWS, a Salesforce és a zendesk esetében érvényesek). A szerepkör-importálások naplóit a naplókban találja. 

## <a name="error-codes"></a>Hibakódok

A következő táblázat segítségével jobban megismerheti, Hogyan oldhatók meg a kiépítési naplókban talált hibák. A hiányzó hibakódok esetén az oldal alján található hivatkozás használatával küldjön visszajelzést. 

|Hibakód|Leírás|
|---|---|
|Ütközés, EntryConflict|Javítsa ki az ütköző attribútum értékeit az Azure AD-ben vagy az alkalmazásban. Vagy tekintse át az egyező attribútumok konfigurációját, ha az ütköző felhasználói fióknak meg kell egyeznie és át kellene vennie. A megfeleltetési attribútumok konfigurálásával kapcsolatos további információkért tekintse át a [dokumentációt](../app-provisioning/customize-application-attributes.md) .|
|TooManyRequests|A célalkalmazás elutasította ezt a kísérletet a felhasználó frissítésére, mert túlterhelt, és túl sok kérést kapott. Semmit nem kell tennie. A rendszer automatikusan kivonja ezt a kísérletet. A Microsoft értesítette a problémát is.|
|InternalServerError |A célalkalmazás váratlan hibát adott vissza. Előfordulhat, hogy a célalkalmazás szolgáltatással kapcsolatos probléma miatt ez nem működik. Ez a kísérlet 40 percen belül automatikusan kimarad.|
|InsufficientRights, MethodNotAllowed, NotPermitted, jogosulatlan| Az Azure AD hitelesített a célalkalmazás használatával, de nem jogosult a frissítés végrehajtására. Tekintse át a célalkalmazás által biztosított utasításokat, valamint az alkalmazásra vonatkozó [oktatóanyagot](../saas-apps/tutorial-list.md).|
|UnprocessableEntity|A célalkalmazás váratlan választ adott vissza. Lehet, hogy a célalkalmazás konfigurációja nem megfelelő, vagy a célalkalmazás szolgáltatással kapcsolatos problémája miatt ez nem működik.|
|WebExceptionProtocolError |HTTP-protokollhiba történt a célalkalmazás csatlakozásakor. Semmi teendő. Ez a kísérlet 40 percen belül automatikusan kimarad.|
|InvalidAnchor|Már nem létezik olyan felhasználó, aki korábban létrehozta vagy egyeztette a kiépítési szolgáltatás. Győződjön meg arról, hogy a felhasználó létezik. Az összes felhasználó új megfeleltetésének kényszerítéséhez a Microsoft Graph API használatával [indítsa újra a feladatot](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). <br><br>A kiépítés újraindítása elindítja a kezdeti ciklust, amely időt vehet igénybe. A kiépítés újraindítása törli azt a gyorsítótárat is, amelyet a kiépítési szolgáltatás a működéséhez használ. Ez azt jelenti, hogy a bérlő minden felhasználóját és csoportját újra ki kell értékelni, és előfordulhat, hogy bizonyos kiépítési események el lesznek dobva.|
|Nincs implementálva | A célalkalmazás váratlan választ adott vissza. Lehetséges, hogy az alkalmazás konfigurációja nem megfelelő, vagy a célalkalmazás szolgáltatással kapcsolatos problémája miatt ez nem működik. Tekintse át a célalkalmazás által biztosított utasításokat, valamint az alkalmazásra vonatkozó [oktatóanyagot](../saas-apps/tutorial-list.md). |
|MandatoryFieldsMissing, MissingValues |A felhasználó nem hozható létre, mert hiányoznak a szükséges értékek. Javítsa ki a hiányzó attribútum-értékeket a forrás rekordban, vagy tekintse át a megfelelő attribútum-konfigurációt, hogy a kötelező mezők ne legyenek kihagyva. [További](../app-provisioning/customize-application-attributes.md) információ a megfeleltetési attribútumok konfigurálásáról.|
|SchemaAttributeNotFound |Nem sikerült végrehajtani a műveletet, mert olyan attribútum lett megadva, amely nem létezik a célalkalmazás alkalmazásban. Tekintse meg az attribútumok testreszabásával kapcsolatos [dokumentációt](../app-provisioning/customize-application-attributes.md) , és győződjön meg arról, hogy a konfiguráció helyes.|
|InternalError |Belső szolgáltatási hiba történt az Azure AD-létesítési szolgáltatásban. Semmi teendő. Ez a kísérlet 40 perc múlva automatikusan újra próbálkozik.|
|InvalidDomain |Nem sikerült végrehajtani a műveletet, mert egy attribútum értéke érvénytelen tartománynevet tartalmaz. Frissítse a tartománynevet a felhasználón, vagy adja hozzá azt a célalkalmazás engedélyezett listájához. |
|Időtúllépés |Nem sikerült befejezni a műveletet, mert a célalkalmazás túl sokáig tartott a válaszadáshoz. Semmi teendő. Ez a kísérlet 40 perc múlva automatikusan újra próbálkozik.|
|LicenseLimitExceeded|Nem sikerült létrehozni a felhasználót a célalkalmazás alkalmazásban, mert nincsenek elérhető licencek ehhez a felhasználóhoz. Több licenc beszerzése a célalkalmazás számára. Vagy tekintse át a felhasználói hozzárendelések és attribútumok megfeleltetésének konfigurációját, és győződjön meg arról, hogy a megfelelő felhasználók hozzá vannak rendelve a megfelelő attribútumokhoz.|
|DuplicateTargetEntries  |Nem sikerült befejezni a műveletet, mert a célalkalmazás egynél több felhasználó található a konfigurált egyező attribútumokkal. Távolítsa el az ismétlődő felhasználót a célalkalmazás alkalmazásból, vagy [konfigurálja újra az attribútumok leképezéseit](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | Nem sikerült befejezni a műveletet, mert egynél több felhasználó található a konfigurált egyező attribútumokkal. Távolítsa el az ismétlődő felhasználót, vagy [konfigurálja újra az attribútumok leképezéseit](../app-provisioning/customize-application-attributes.md).|
|ImportSkipped | Amikor minden felhasználó kiértékelése megtörténik, a rendszer megpróbálja importálni a felhasználót a forrás rendszerből. Ez a hiba általában akkor fordul elő, ha az importálandó felhasználó hiányzik az attribútum-hozzárendelésekben definiált megfelelő tulajdonsággal. A megfelelő attribútum felhasználói objektumában nem szerepel érték, a rendszer nem tudja kiértékelni a hatókört, a megfeleltetést vagy az exportálási módosításokat. Vegye figyelembe, hogy a hiba jelenléte nem jelzi, hogy a felhasználó hatókörben van, mert még nem értékelte ki a hatókört a felhasználó számára.|
|EntrySynchronizationSkipped | A kiépítési szolgáltatás sikeresen lekérdezte a forrás rendszerét, és azonosította a felhasználót. A felhasználóra vonatkozóan nem történt további művelet, és a rendszer kihagyta őket. Lehetséges, hogy a felhasználó hatókörén kívül esik, vagy a felhasználó már létezett a megcélzott rendszeren, és nincs szükség további módosításokra.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Egy felhasználó vagy csoport lekérésére vonatkozó GET kérelem több felhasználót vagy csoportot kapott a válaszban. A rendszer arra vár, hogy csak egy felhasználót vagy csoportot kapjon a válaszban. Ha [például](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)egy Get kérést kap egy csoport lekérésére, és egy szűrőt biztosít a tagok kizárásához, és a tartományok közötti IDENTITÁSKEZELÉS (scim) végpont visszaadja a tagokat, ezt a hibaüzenetet fogja kapni.|

## <a name="next-steps"></a>Következő lépések

* [A felhasználó kiépítési állapotának megtekintése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Hiba történt a felhasználók Azure AD Gallery-alkalmazásba való konfigurálásának beállításakor](../app-provisioning/application-provisioning-config-problem.md)
* [Graph API az üzembe helyezési naplókhoz](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)