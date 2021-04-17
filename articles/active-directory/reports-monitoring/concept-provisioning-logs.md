---
title: A kiépítési naplók áttekintése a Azure Portal (előzetes verzió) | Microsoft Docs
description: Bevezetés a naplójelentések üzembe Azure Active Directory a Azure Portal.
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
ms.openlocfilehash: 468e885bab6aab4becb5aaaec7b4d52ce5ef5e07
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535999"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>A kiépítési naplók áttekintése a Azure Portal (előzetes verzió)

A jelentéskészítési architektúra Azure Active Directory (Azure AD) a következő összetevőkből áll:

- Tevékenység: 
    - **Bejelentkezések:** Információk a felügyelt alkalmazások használatáról és a felhasználói bejelentkezési tevékenységekről.
    - [Auditnaplók:](concept-audit-logs.md)Rendszertevékenység-információk a felhasználó- és csoportkezelésről, a felügyelt alkalmazásokról és a címtártevékenységről.
    - **Kiépítési naplók:** Az Azure AD kiépítési szolgáltatás által kiépített felhasználókkal, csoportokkal és szerepkörökkel kapcsolatos rendszertevékenység. 

- Biztonság: 
    - **Kockázatos bejelentkezések:** [A](../identity-protection/overview-identity-protection.md) kockázatos bejelentkezés olyan bejelentkezési kísérletet jelent, amelyet olyan felhasználó hajtott végre, aki nem a felhasználói fiók megbízható tulajdonosa.
    - **Kockázatosként megjelölt felhasználók:** A [kockázatos](../identity-protection/overview-identity-protection.md) felhasználók olyan felhasználói fiókokat jelzik, amelyek biztonsága sérült lehet.

Ez a témakör áttekintést nyújt a kiépítési naplókról. A naplók a következő kérdésekre ad választ: 

* Milyen csoportok létrehozása sikerült a ServiceNow-ban?
* Milyen felhasználókat távolított el sikeresen az Adobe?
* Melyik Workday-felhasználó lett sikeresen létrehozva a Active Directory? 

## <a name="prerequisites"></a>Előfeltételek

Ezek a felhasználók a kiépítési naplókban férhetnek hozzá az adatokhoz:

* Alkalmazástulajdonosok (a saját alkalmazásaik naplói)
* A biztonsági rendszergazda, a biztonsági olvasó, a jelentésolvasó, a biztonsági operátor, az alkalmazás-rendszergazda és a felhőalkalmazás-rendszergazda szerepkör felhasználói
* Az egyéni szerepkörben a [provisioningLogs engedéllyel rendelkező felhasználók](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)
* Globális rendszergazda


A kiépítési tevékenység jelentésének megtekintéséhez a bérlőnek rendelkeznie kell prémium szintű Azure AD társított licenccel. Az Azure AD kiadásának frissítését lásd: [Ismerkedés a prémium szintű Azure Active Directory.](../fundamentals/active-directory-get-started-premium.md) 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>A kiépítési naplók interakciói 
Az ügyfelek négy módon kommunikálhatnak a kiépítési naplóval:

- A naplók elérése a Azure Portal a következő szakaszban leírtak szerint.
- A kiépítési naplók streamelése a [Azure Monitor.](../app-provisioning/application-provisioning-log-analytics.md) Ez a módszer lehetővé teszi a kiterjesztett adatmegőrzést, valamint egyéni irányítópultok, riasztások és lekérdezések építését.
- Az üzembe [Microsoft Graph API lekérdezése.](/graph/api/resources/provisioningobjectsummary)
- A kiépítési naplók letöltése CSV- vagy JSON-fájlként.

## <a name="access-the-logs-from-the-azure-portal"></a>A naplók elérése a Azure Portal
A kiépítési naplók eléréséhez válassza a   **Kiépítési** naplók lehetőséget a Azure Active Directory panel [Azure Portal.](https://portal.azure.com) Akár két órát is igénybe vehet, hogy egyes kiépítési rekordok megjelenjenek a portálon.

![Képernyőkép a kiépítési naplók elérésére vonatkozó beállításokról.](./media/concept-provisioning-logs/access-provisioning-logs.png "Üzembehelyezési naplók")


A kiépítési napló alapértelmezett listanézete a következőt jeleníti meg:

- Az identitás
- A művelet
- A forrásrendszer
- A célrendszer
- Az állapot
- A dátum


![Képernyőkép a kiépítési napló alapértelmezett oszlopairól.](./media/concept-provisioning-logs/default-columns.png "Alapértelmezett oszlopok")

A listanézet testreszabásához válassza az **Oszlopok lehetőséget** az eszköztáron.

![Az oszlopok testreszabására vonatkozó gombot bemutató képernyőkép.](./media/concept-provisioning-logs/column-chooser.png "Oszlop kiválasztása")

Ez a terület lehetővé teszi további mezők megjelenítését vagy a már megjelenített mezők eltávolítását.

![Képernyőkép az elérhető oszlopokról, néhány kijelölt oszloppal.](./media/concept-provisioning-logs/available-columns.png "Elérhető oszlopok")

Részletesebb információkért válasszon ki egy elemet a listanézetben.

![Részletes információkat bemutató képernyőkép.](./media/concept-provisioning-logs/steps.png "Szűrő")


## <a name="filter-provisioning-activities"></a>Kiépítési tevékenységek szűrése

Szűrheti a kiépítési adatokat. Egyes szűrőértékek dinamikusan ki vannak töltve a bérlő alapján. Ha például nincsenek "létrehozási" események a bérlőben, nem lesz szűrő **létrehozása** lehetőség.

Az alapértelmezett nézetben a következő szűrőket választhatja ki:

- **Identitás**
- **Date**
- **Állapot**
- **Művelet**


![A szűrőértékeket bemutató képernyőkép.](./media/concept-provisioning-logs/default-filter.png "Szűrő")

Az **Identitás** szűrővel megadhatja az Ön számára fontos nevet vagy identitást. Ez az identitás lehet felhasználó, csoport, szerepkör vagy más objektum. 

Kereshet az objektum neve vagy azonosítója alapján. Az azonosító forgatókönyv szerint változik. Ha például egy objektumot hoz létre az Azure AD-ból a Salesforce-ba, a forrásazonosító a felhasználó objektumazonosítója az Azure AD-ban. A célazonosító a Salesforce-ban a felhasználó azonosítója. Amikor a Workdayből egy másikba Active Directory, a forrásazonosító a Workday-feldolgozó alkalmazottjának azonosítója. 

> [!NOTE]
> Előfordulhat, hogy a felhasználó neve nem mindig található meg az **Identity oszlopban.** Mindig egy azonosító lesz. 


A **Dátum** szűrővel időkeretet lehet meghatározni a visszaadott adatokhoz. Lehetséges értékek:

- 1 hónap
- 7 nap
- 30 nap
- 24 óra
- Egyéni időintervallum

Amikor kiválaszt egy egyéni időkeretet, konfigurálhat egy kezdő és egy záró dátumot.

Az **Állapot szűrővel** a következő beállításokat választhatja ki:

- **Mind**
- **Siker**
- **Hiba**
- **Kihagyva**

A **Művelet** szűrővel a következő műveleteket szűrheti:

- **Létrehozás** 
- **Frissítés**
- **Törlés**
- **Letiltás**
- **Egyéb**

Az alapértelmezett nézet szűrői mellett a következő szűrőket is beállíthatja.

![Képernyőkép a szűrőkként felvehető mezőkről.](./media/concept-provisioning-logs/add-filter.png "Mező kiválasztója")

- **Feladatazonosító:** Minden olyan alkalmazáshoz egyedi feladatazonosító van társítva, amely számára engedélyezte a kiépítést.   

- **Ciklusazonosító:** A ciklusazonosító egyedileg azonosítja a kiépítési ciklust. Ezt az azonosítót megoszthatja a terméktámogatással, hogy ki tudja keresni az esemény ciklusát.

- **Módosítási azonosító:** A változásazonosító a kiépítési esemény egyedi azonosítója. Ezt az azonosítót megoszthatja a terméktámogatással, hogy ki tudja keresni a kiépítési eseményt.   

- **Forrásrendszer:** Megadhatja, hogy honnan lesz kiépítve az identitás. Ha például az Azure AD-ból a ServiceNow-ba hoz ki egy objektumot, a forrásrendszer az Azure AD. 

- **Célrendszer:** Megadhatja, hogy hová lesz kiépítve az identitás. Ha például az Azure AD-ból a ServiceNow-ba hoz ki egy objektumot, a célrendszer a ServiceNow. 

- **Alkalmazás:** Csak olyan alkalmazásrekordokat jeleníthet meg, amelyek megjelenítendő neve egy adott sztringet tartalmaz.

## <a name="provisioning-details"></a>Kiépítés részletei 

Amikor kiválaszt egy elemet a kiépítési lista nézetben, további részleteket kap erről az elemről. A részletek a következő lapokra vannak csoportosítva.

![A kiépítési adatokat tartalmazó négy lapot bemutató képernyőkép.](./media/concept-provisioning-logs/provisioning-tabs.png "Tabulátorok")

- **Lépések:** Az objektumok üzembe építésének lépéseit ismerteti. Egy objektum kiépítése négy lépésből állhat:
  
  1. Importálja az objektumot.
  1. Állapítsa meg, hogy az objektum a hatókörben van-e.
  1. Az objektum egyezése a forrás és a cél között.
  1. Az objektum kiépítése (létrehozás, frissítés, törlés vagy letiltás).

  ![A Lépések lapon látható kiépítési lépéseket bemutató képernyőkép.](./media/concept-provisioning-logs/steps.png "Szűrő")

- **Hibaelhárítási & javaslatok:** A hibakódot és annak okát tartalmazza. A hibainformációk csak hiba esetén érhetők el.

- **Módosított tulajdonságok:** A régi és az új értéket jeleníti meg. Ha nincs régi érték, az oszlop üres.

- **Összefoglalás:** Áttekintést nyújt a forrás- és célrendszerekben történt objektumokról és azonosítókról.

## <a name="download-logs-as-csv-or-json"></a>Naplók letöltése CSV-fájlként vagy JSON-fájlként

A kiépítési naplókat letöltheti későbbi használatra, ha a naplók között a Azure Portal a **Letöltés kiválasztásával.** A fájl a kiválasztott szűrési feltételek alapján lesz szűrve. A letöltés méretének és időének csökkentése érdekében a szűrőket a lehető legspecifikusabbra kell tenni. 

A CSV-letöltés három fájlt tartalmaz:

* **ProvisioningLogs:** Letölti az összes naplót, kivéve a kiépítési lépéseket és a módosított tulajdonságokat.
* **ProvisioningLogs_ProvisioningSteps:** A kiépítési lépéseket és a módosítás azonosítóját tartalmazza. A módosításazonosítóval az eseményt a másik két fájlhoz is használhatja.
* **ProvisioningLogs_ModifiedProperties**: A módosított attribútumokat és a módosítás azonosítóját tartalmazza. A módosításazonosítóval az eseményt a másik két fájlhoz is használhatja.

#### <a name="open-the-json-file"></a>Nyissa meg a JSON-fájlt
A JSON-fájl megnyitásához használjon egy szövegszerkesztőt, például a [Microsoft Visual Studio Code-et.](https://aka.ms/vscode) Visual Studio Code szintaxiskiemelővel megkönnyíti a fájl olvasását. A JSON-fájlt a böngészőkben is megnyithatja, például a következő [formátumban: Microsoft Edge.](https://aka.ms/msedge) 

#### <a name="prettify-the-json-file"></a>A JSON-fájl lebujtálása
A JSON-fájl a letöltés méretének csökkentése érdekében lecsökkent formátumban lesz letöltve. Ezzel a formátummal a hasznos adatok nehezen olvashatók. Tekintse meg a fájl megtömítésének két beállítását:

- A [JSON Visual Studio Kód használatával formázza a JSON-t.](https://code.visualstudio.com/docs/languages/json#_formatting)

- A JSON formázása a PowerShell használatával. Ez a szkript tabulátorokat és szóközöket tartalmazó formátumban fogja kihozni a JSON-t: 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>A JSON-fájl elemezve

Íme néhány példaparancs a JSON-fájl PowerShell használatával való munkához. Bármilyen programnyelvet használhat, amit csak tud.  

Először olvassa [el a JSON-fájlt a](/powershell/module/microsoft.powershell.utility/convertfrom-json) következő parancs futtatásával:

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

Most már elemezheti az adatokat a forgatókönyvnek megfelelően. Bemutatunk néhány példát: 

- A JSON-fájlban található összes feladat-id kimenete:

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- Az összes olyan esemény módosítási adatának kimenete, ahol a művelet "létrehozás" volt:

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>Alapismeretek

Íme néhány tipp és megfontolandó szempont a kiépítési jelentésekhez:

- A Azure Portal 30 napig tárolja a jelentett kiépítési adatokat, ha prémium kiadással rendelkezik, és 7 napig, ha ingyenes kiadással rendelkezik. A kiépítési naplókat közzéteheti a [Log Analyticsben,](../app-provisioning/application-provisioning-log-analytics.md) így 30 nap után is megtarthatja azokat. 

- A change ID attribútumot egyedi azonosítóként használhatja. Ez például akkor hasznos, ha például a terméktámogatást használja.

- Előfordulhat, hogy kihagyott eseményeket lát a hatókörbe nem tartozó felhasználóknál. Ez várható, különösen akkor, ha a szinkronizálás hatóköre az összes felhasználóra és csoportra van beállítva. A szolgáltatás a bérlő összes objektumát kiértékeli, még azokat is, amelyek nem tartoznak a hatókörbe. 

- A kiépítési naplók jelenleg nem érhetők el a kormányzati felhőben. Ha nem fér hozzá a kiépítési naplókhoz, ideiglenes áthidaló megoldásként használja az auditnaplókat. 

- A kiépítési naplók nem mutatják a szerepkörimportokat (az AWS, a Salesforce és a Zendesk esetében). A szerepkörimportálások naplóit az auditnaplókban találja. 

## <a name="error-codes"></a>Hibakódok

Az alábbi táblázat segítségével jobban megértheti, hogyan háríthatja el a kiépítési naplókban található hibákat. A hiányzó hibakódok esetén a lap alján található hivatkozásra kattintva küldhet visszajelzést. 

|Hibakód|Leírás|
|---|---|
|Ütközés, EntryConflict|Javítsa ki az ütköző attribútumértékeket az Azure AD-ban vagy az alkalmazásban. Vagy tekintse át a megfelelő attribútumkonfigurációt, ha az ütköző felhasználói fióknak egyeznie kellett volna, és át kellett vennie a fiókot. Az [egyező](../app-provisioning/customize-application-attributes.md) attribútumok konfigurálásával kapcsolatos további információkért tekintse át a dokumentációt.|
|TooManyRequests|A célalkalmazás elutasította a felhasználó frissítésére tett kísérletet, mert túlterhelt, és túl sok kérést kapott. Semmit nem kell tenni. Ezt a kísérletet a rendszer automatikusan visszavonja. A Microsoft is értesítést kap erről a problémáról.|
|InternalServerError |A célalkalmazás váratlan hibát adott vissza. Előfordulhat, hogy a célalkalmazással kapcsolatban egy szolgáltatás problémája nem működik. Ezt a kísérletet a rendszer 40 percen belül automatikusan visszavonja.|
|InsufficientRights, MethodNotAllowed, NotPermitted, Unauthorized| Az Azure AD hitelesítve lett a célalkalmazással, de nem jogosult a frissítés elvégzésére. Tekintse át a célalkalmazás által megadott utasításokat, valamint a megfelelő [alkalmazás-oktatóanyagot.](../saas-apps/tutorial-list.md)|
|UnprocessableEntity (Feldolgozhatatlanság)|A célalkalmazás váratlan választ adott vissza. Előfordulhat, hogy a célalkalmazás konfigurációja helytelen, vagy a célalkalmazással kapcsolatos szolgáltatási probléma megakadályozhatja ezt a problémát.|
|WebExceptionProtocolError |HTTP-protokollhiba történt a célalkalmazáshoz való csatlakozás során. Nincs mit tenni. Ezt a kísérletet a rendszer 40 percen belül automatikusan visszavonja.|
|InvalidAnchor (Érvénytelen érték)|A korábban létrehozott vagy a kiépítési szolgáltatás által megfelelt felhasználó már nem létezik. Győződjön meg arról, hogy a felhasználó létezik. Az összes felhasználó új egyezésének kényszerítenie kell a Microsoft Graph API-val a [feladat újraindításához.](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true) <br><br>A kiépítés újraindítása elindít egy kezdeti ciklust, amely időt vehet igénybe. A kiépítés újraindítása a kiépítési szolgáltatás által a működéshez használt gyorsítótárat is törli. Ez azt jelenti, hogy a bérlőben minden felhasználót és csoportot újra ki kell értékelni, és előfordulhat, hogy bizonyos kiépítési események el lesznek dobva.|
|Nincs megvalósítva | A célalkalmazás váratlan választ adott vissza. Előfordulhat, hogy az alkalmazás konfigurációja helytelen, vagy a célalkalmazással kapcsolatos szolgáltatásbeli probléma megakadályozhatja ezt a problémát. Tekintse át a célalkalmazás által megadott utasításokat és a megfelelő [alkalmazás-oktatóanyagot.](../saas-apps/tutorial-list.md) |
|MandatoryFieldsMissing, MissingValues |A felhasználót nem lehetett létrehozni, mert hiányoznak a szükséges értékek. Javítsa ki a forrásrekord hiányzó attribútumértékeket, vagy tekintse át az egyező attribútumkonfigurációt, hogy a kötelező mezők ne legyen kihagyva. [További információ](../app-provisioning/customize-application-attributes.md) az egyező attribútumok konfigurálásával kapcsolatban.|
|SchemaAttributeNotFound |A műveletet nem sikerült végrehajtani, mert olyan attribútum lett megadva, amely nem létezik a célalkalmazásban. Tekintse meg [az attribútumok testreszabásával](../app-provisioning/customize-application-attributes.md) kapcsolatos dokumentációt, és ellenőrizze, hogy a konfiguráció helyes-e.|
|InternalError |Belső szolgáltatási hiba történt az Azure AD kiépítési szolgáltatásban. Nincs mit tenni. A rendszer 40 percen belül automatikusan újra megkísérli ezt a kísérletet.|
|InvalidDomain (Tartomány érvénytelen tartománya) |A műveletet nem sikerült végrehajtani, mert egy attribútumérték érvénytelen tartománynevet tartalmaz. Frissítse a felhasználó tartománynevét, vagy adja hozzá a célalkalmazás engedélyezett listájához. |
|Időtúllépés |A műveletet nem sikerült befejezni, mert a célalkalmazás túl sokáig tartott a válaszadáshoz. Nincs mit tenni. A rendszer 40 percen belül automatikusan újra megkísérli ezt a kísérletet.|
|LicenseLimitExceeded (Licenclimitexceeded)|A felhasználót nem sikerült létrehozni a célalkalmazásban, mert ehhez a felhasználóhoz nincsenek elérhető licencek. További licencek beszerzése a célalkalmazáshoz. Vagy tekintse át a felhasználó-hozzárendelések és attribútumleképezés konfigurációját, és győződjön meg arról, hogy a megfelelő felhasználók vannak hozzárendelve a megfelelő attribútumokkal.|
|DuplicateTargetEntries (IsmétlődőtargetEntries)  |A műveletet nem sikerült befejezni, mert a célalkalmazásban egynél több felhasználó található a konfigurált egyező attribútumokkal. Távolítsa el a duplikált felhasználót a célalkalmazásból, vagy [konfigurálja újra az attribútumleképezéseket.](../app-provisioning/customize-application-attributes.md)|
|DuplicateSourceEntries (Ismétlődő adatforrás-elemek) | A műveletet nem sikerült befejezni, mert több felhasználó is megtalálható a konfigurált egyező attribútumokkal. Távolítsa el a duplikált felhasználót, vagy [konfigurálja újra az attribútumleképezéseket.](../app-provisioning/customize-application-attributes.md)|
|ImportSkipped (Importált) | Az egyes felhasználók kiértékelése után a rendszer megpróbálja importálni a felhasználót a forrásrendszerből. Ez a hiba általában akkor fordul elő, ha az importált felhasználóból hiányzik az attribútumleképezésben meghatározott egyező tulajdonság. Ha nincs érték a felhasználói objektumon az egyező attribútumhoz, a rendszer nem tudja kiértékelni a hatókör-, egyeztetési és exportálási módosításokat. Vegye figyelembe, hogy a hiba jelenléte nem jelzi, hogy a felhasználó hatókörben van, mert még nem értékelt hatókör-hatókört a felhasználóra.|
|EntrySynchronizationSkipped | A kiépítési szolgáltatás sikeresen lekérdezte a forrásrendszert, és azonosította a felhasználót. A felhasználón nem történt további művelet, és a rendszer kihagyta őket. Előfordulhat, hogy a felhasználó nem volt a hatókörben, vagy már létezett a célrendszerben, további módosítások nélkül.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Get kérés egy felhasználó vagy csoport lekérésére, amely több felhasználót vagy csoportot kapott a válaszban. A rendszer arra számít, hogy a válaszban csak egy felhasználó vagy csoport lesz. [Ha például](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)GET kérést ad egy csoport lekérésére, és szűrőt ad meg a tagok kizárásához, és a System for Cross-Domain Identity Management (SCIM) végpont visszaadja a tagokat, ez a hibaüzenet jelenik meg.|

## <a name="next-steps"></a>Következő lépések

* [A felhasználóátépítés állapotának ellenőrzése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Probléma egy Azure AD katalógusbeli alkalmazás felhasználóátépítésének konfigurálásával](../app-provisioning/application-provisioning-config-problem.md)
* [Graph API a kiépítési naplókhoz](/graph/api/resources/provisioningobjectsummary)