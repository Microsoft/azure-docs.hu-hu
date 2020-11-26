---
title: Naplók kiépítés a Azure Active Directory portálon (előzetes verzió) | Microsoft Docs
description: A naplók kiépítési jelentéseinek bemutatása a Azure Active Directory portálon
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
ms.date: 10/07/2020
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2408db2d91740350405f11e2a1250ab9b3a4fe31
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181203"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Jelentések kiépítési jelentései a Azure Active Directory portálon (előzetes verzió)

A Azure Active Directory (Azure AD) jelentéskészítési architektúrája a következő összetevőkből áll:

- **Tevékenység** 
    - **Bejelentkezések** – információk a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.
    - **Naplók**  -  A [naplók](concept-audit-logs.md) rendszertevékenységi információkat biztosítanak a felhasználókról és a csoport kezeléséről, a felügyelt alkalmazásokról és a címtárbeli tevékenységekről.
    - **Naplók** kiosztása – rendszertevékenység nyújtása az Azure ad-kiépítési szolgáltatás által kiépített felhasználókkal, csoportokkal és szerepkörökkel kapcsolatban. 

- **Biztonság** 
    - **Kockázatos bejelentkezések** – a [kockázatos bejelentkezés](../identity-protection/overview-identity-protection.md) egy olyan bejelentkezési kísérletre utal, amelyet a felhasználói fiók jogos tulajdonosaként elvégeztek.
    - **Kockázatnak** kitett felhasználók – a [kockázatos felhasználók](../identity-protection/overview-identity-protection.md) egy olyan felhasználói fiókra vonatkozó jelző, amely esetleg sérült.

Ez a témakör áttekintést nyújt a kiépítési jelentésről.

## <a name="prerequisites"></a>Előfeltételek

### <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?
* Az alkalmazás tulajdonosai megtekinthetik a saját alkalmazások naplóit
* Felhasználók a biztonsági rendszergazda, a biztonsági olvasó, a jelentéskészítő, az alkalmazás rendszergazdája és a Felhőbeli alkalmazás rendszergazdai szerepkörei
* Globális rendszergazdák


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Milyen Azure AD-licencre van szükség a kiépítési tevékenységekhez való hozzáféréshez?

A bérlőnek prémium szintű Azure AD licenccel kell rendelkeznie ahhoz, hogy láthassa a teljes kiépítési tevékenység jelentését. A Azure Active Directory kiadásának frissítéséhez tekintse meg a [prémium szintű Azure Active Directory első lépéseivel foglalkozó](../fundamentals/active-directory-get-started-premium.md) témakört. 

## <a name="provisioning-logs"></a>Üzembehelyezési naplók

A kiépítési naplók a következő kérdésekre adnak választ:

* Milyen csoportokat sikerült létrehozni a ServiceNow-ben?
* Milyen szerepkörök lettek importálva a Amazon Web Services?
* Mely felhasználók lettek sikeresen létrehozva a DropBoxban?

A kiépítési naplókat úgy érheti el, ha kijelöli a **kiépítési** naplókat a [Azure Portal](https://portal.azure.com) **Azure Active Directory** paneljének **figyelés** szakaszában. Akár két órát is igénybe vehet, hogy egyes kiépítési rekordok megjelenjenek a portálon.

![Naplók kiépítés](./media/concept-provisioning-logs/access-provisioning-logs.png "Üzembehelyezési naplók")


A kiépítési napló egy alapértelmezett listanézet, amely a következőket jeleníti meg:

- Az identitás
- A művelet
- A forrásoldali System
- A célként megadott System
- Az állapot
- A dátum


![Alapértelmezett oszlopok](./media/concept-provisioning-logs/default-columns.png "Alapértelmezett oszlopok")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Oszlop kiválasztása](./media/concept-provisioning-logs/column-chooser.png "Oszlop kiválasztása")

További mezőket jeleníthet meg, vagy eltávolíthatja a már megjelenített mezőket.

![Elérhető oszlopok](./media/concept-provisioning-logs/available-columns.png "Elérhető oszlopok")

Részletesebb információkhoz jelöljön ki egy elemet a listanézet nézetben.

![Részletes információk](./media/concept-provisioning-logs/steps.png "Szűrő")


## <a name="filter-provisioning-activities"></a>Kiépítési tevékenységek szűrése

A kiépítési adatait szűrheti. Egyes szűrési értékek dinamikusan vannak feltöltve a bérlő alapján. Ha például nem rendelkezik létrehozási eseményekkel a bérlőben, a létrehozáshoz nem lesz szűrő lehetőség.
Az alapértelmezett nézetben a következő szűrőket választhatja ki:

- Identitás
- Dátum
- Állapot
- Művelet


![Szűrők hozzáadása](./media/concept-provisioning-logs/default-filter.png "Szűrő")

Az **Identity** szűrő segítségével megadhatja a nevet vagy az Ön számára fontos identitást. Ez az identitás lehet felhasználó, csoport, szerepkör vagy más objektum. Az objektum neve vagy azonosítója alapján kereshet. Az azonosító forgatókönyv szerint változik. Ha például egy objektumot kiépít az Azure AD-ből a SalesForce-be, a forrás azonosítója az Azure AD-beli felhasználó objektumazonosítóa, míg a TargetID a Salesforce felhasználójának azonosítója. Ha a munkahelyről a Active Directoryre való kiépítés után a forrás azonosítója a munkanap munkavégző alkalmazottjának azonosítója. Vegye figyelembe, hogy a felhasználó neve nem mindig szerepel az Identity oszlopban. Mindig egy azonosító lesz. 


A **Dátum** szűrővel időkeretet lehet meghatározni a visszaadott adatokhoz.  
Lehetséges értékek:

- 1 hónap
- 7 nap
- 30 nap
- 24 óra
- Egyéni időintervallum

Amikor kiválaszt egy egyéni időkeretet, beállíthatja a kezdési és a befejezési dátumot.


Az **állapot** szűrő segítségével a következőket választhatja ki:

- Mind
- Success
- Hiba
- Kimarad



A **művelet** szűrője lehetővé teszi a következő szűrését:

- Létrehozás 
- Frissítés
- Törlés
- Letiltás
- Egyéb

Emellett az alapértelmezett nézet szűrői esetében a következő szűrőket is beállíthatja:

- AZONOSÍTÓJÚ feladatok
- Ciklus azonosítója
- Változás azonosítója
- Forrás azonosítója
- Cél azonosítója
- Alkalmazás


![Válasszon ki egy mezőt](./media/concept-provisioning-logs/add-filter.png "Válasszon ki egy mezőt")


- **Job ID** – a rendszer minden olyan alkalmazáshoz társít egy egyedi azonosítójú feladatot, amelyhez engedélyezte az üzembe helyezést.   

- **Ciklus azonosítója** – egyedi módon azonosítja a létesítési ciklust. Ezt az azonosítót megoszthatja a támogatással, hogy megkeresse azt a ciklust, amelyben ez az esemény történt.

- A kiépítési esemény egyedi azonosítójának **módosítása** . Megoszthatja ezt az azonosítót, hogy támogassa a kiépítési esemény megkeresését.   


- **Forrásoldali rendszer** – lehetővé teszi annak megadását, hogy az identitás honnan legyen kiépítve. Ha például egy objektumot kiépít az Azure AD-ből a ServiceNow-be, akkor a forrásoldali rendszer az Azure AD. 

- Célrendszer – lehetővé teszi annak megadását, hogy az identitás hol legyen kiépítve a **szolgáltatásba** . Ha például egy objektumot kiépít az Azure AD-ből a ServiceNow-be, a rendszer ServiceNow. 

- **Alkalmazás** – lehetővé teszi, hogy csak az adott karakterláncot tartalmazó megjelenítendő névvel rendelkező alkalmazások rekordjait jelenítse meg.

 

## <a name="provisioning-details"></a>Kiépítés részletei 

Amikor kiválaszt egy elemet a kiépítési lista nézetben, az elemről további részleteket talál.
A részletek a következő kategóriák alapján vannak csoportosítva:

- Lépések

- Hibák és javaslatok

- Módosított tulajdonságok

- Összefoglalás


![Kiépítés részletei](./media/concept-provisioning-logs/provisioning-tabs.png "Tabulátorok")



### <a name="steps"></a>Lépések

A **lépések** lapon az objektum kiépítéséhez szükséges lépések szerepelnek. Az objektumok kiépítés négy lépésből állhat: 

- Objektum importálása
- Annak megállapítása, hogy az objektum hatókörben van-e
- Objektum egyeztetése a forrás és a cél között
- Objektum kiépítése (művelet elvégzése – ez lehet létrehozás, frissítés, törlés vagy Letiltás)



![Képernyőfelvétel: a lépések lap, amely a kiépítési lépéseket mutatja be.](./media/concept-provisioning-logs/steps.png "Szűrő")


### <a name="troubleshoot-and-recommendations"></a>Hibák és javaslatok


A **hibakeresés és javaslatok** lap a hibakódot és az okot adja meg. A hiba adatai csak meghibásodás esetén érhetők el. 


### <a name="modified-properties"></a>Módosított tulajdonságok

A **módosított tulajdonságok** a régi értéket és az új értéket jelenítik meg. Olyan esetekben, amikor nincs régi érték, a régi érték oszlop üres. 


### <a name="summary"></a>Összefoglalás

Az **Összefoglalás** lapon áttekintheti, hogy mi történt, és milyen azonosítókat tartalmaz a forrás és a cél rendszer objektumához. 

## <a name="what-you-should-know"></a>Alapismeretek

- Ha ingyenes kiadással rendelkezik, a Azure Portal 30 napig tárolja a kiépítési adatgyűjtési jelentést. A kiépítési naplók 30 napon túli megőrzés céljából közzétehetők a [log Analyticsben](../app-provisioning/application-provisioning-log-analytics.md) . 

- A Change ID attribútum egyedi azonosítóként használható. Ez például a terméktámogatással való interakció esetén hasznos.

- Jelenleg nincs lehetőség a kiépítési adatainak CSV-fájlként való letöltésére, de az adatexportálást [Microsoft Graph](/graph/api/provisioningobjectsummary-list?tabs=http&view=graph-rest-beta)használatával végezheti el.

- A kihagyott eseményeket a hatókörön kívüli felhasználók számára is megtekintheti. Ez várható, különösen akkor, ha a szinkronizálási hatókör az összes felhasználóra és csoportra van beállítva. A szolgáltatás a bérlő összes objektumát kiértékeli, még a hatókörön kívül is. 

- A kiépítési naplók jelenleg nem érhetők el a kormányzati felhőben. Ha nem tudja elérni a kiépítési naplókat, használja a naplókat ideiglenes megkerülő megoldásként.  

## <a name="error-codes"></a>Hibakódok

Az alábbi táblázat segítségével jobban megismerheti, Hogyan oldhatók meg a kiépítési naplókban esetlegesen felmerülő hibák. A hiányzó hibakódok esetében küldjön visszajelzést az oldal alján található hivatkozás használatával. 

|Hibakód|Leírás|
|---|---|
|Ütközés, EntryConflict|Javítsa ki az ütköző attribútum értékeit az Azure AD-ben vagy az alkalmazásban, vagy tekintse át a megfelelő attribútum-konfigurációt, ha az ütköző felhasználói fióknak meg kell egyeznie és át kellene vennie. Az egyeztetési attribútumok konfigurálásával kapcsolatos további információkért tekintse át az alábbi [dokumentációt](../app-provisioning/customize-application-attributes.md) .|
|TooManyRequests|A célalkalmazás elutasította ezt a kísérletet a felhasználó frissítésére, mert túlterhelt, és túl sok kérést fogad. Semmi teendő. A rendszer automatikusan kivonja ezt a kísérletet. A Microsoft értesítette a problémát is.|
|InternalServerError |A célalkalmazás váratlan hibát adott vissza. Előfordulhat, hogy egy szolgáltatási probléma van a célalkalmazás számára, amely megakadályozza ennek működését. Ez a kísérlet 40 percen belül automatikusan kimarad.|
|InsufficientRights, MethodNotAllowed, NotPermitted, jogosulatlan| Az Azure AD képes volt hitelesíteni a megcélzott alkalmazást, de nem jogosult a frissítés végrehajtására. Tekintse át a célalkalmazás által biztosított utasításokat, valamint az alkalmazásra vonatkozó [oktatóanyagot](../saas-apps/tutorial-list.md).|
|UnprocessableEntity|A célalkalmazás váratlan választ adott vissza. Lehetséges, hogy a célalkalmazás konfigurációja nem megfelelő, vagy a célalkalmazás olyan szolgáltatási problémával jár, amely megakadályozza ennek működését.|
|WebExceptionProtocolError |HTTP protokoll hiba történt a célalkalmazáshoz való csatlakozás során. Semmi teendő. Ez a kísérlet 40 percen belül automatikusan kimarad.|
|InvalidAnchor|Már nem létezik olyan felhasználó, aki korábban létrehozta vagy egyeztette a kiépítési szolgáltatás. Győződjön meg arról, hogy a felhasználó létezik. Az összes felhasználó újraegyezésének kényszerítéséhez az MS Graph API használatával [indítsa újra a feladatot](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). Vegye figyelembe, hogy a kiépítés újraindítása elindítja a kezdeti ciklust, amely időt vehet igénybe. Emellett törli a kiépítési szolgáltatás által a működéshez használt gyorsítótárat is, ami azt jelenti, hogy a bérlő minden felhasználóját és csoportját újra ki kell értékelni, és bizonyos kiépítési eseményeket el lehet dobni.|
|Nincs implementálva | A célalkalmazás váratlan választ adott vissza. Lehetséges, hogy az alkalmazás konfigurációja nem megfelelő, vagy előfordulhat, hogy probléma van a célalkalmazás szolgáltatással, amely megakadályozza ennek működését. Tekintse át a célalkalmazás által biztosított utasításokat, valamint az alkalmazásra vonatkozó [oktatóanyagot](../saas-apps/tutorial-list.md). |
|MandatoryFieldsMissing, MissingValues |A felhasználó nem hozható létre, mert hiányoznak a szükséges értékek. Javítsa ki a hiányzó attribútum-értékeket a forrás rekordban, vagy tekintse át a megfelelő attribútumok konfigurációját, hogy a kötelező mezők ne legyenek kihagyva. [További](../app-provisioning/customize-application-attributes.md) információ a megfeleltetési attribútumok konfigurálásáról.|
|SchemaAttributeNotFound |A művelet nem hajtható végre, mert egy olyan attribútum lett megadva, amely nem létezik a célalkalmazás alkalmazásban. Tekintse meg az attribútumok testreszabásával kapcsolatos [dokumentációt](../app-provisioning/customize-application-attributes.md) , és győződjön meg arról, hogy a konfiguráció helyes.|
|InternalError |Belső szolgáltatási hiba történt az Azure AD-létesítési szolgáltatásban. Semmi teendő. Ez a kísérlet 40 perc múlva automatikusan újra próbálkozik.|
|InvalidDomain |A műveletet nem lehetett végrehajtani, mert egy attribútumérték érvénytelen tartománynevet tartalmaz. Frissítse a tartománynevet a felhasználón, vagy adja hozzá azt a célalkalmazás engedélyezett listájához. |
|Időtúllépés |A műveletet nem lehetett befejezni, mert a célalkalmazás túl sokáig tartott a válaszadáshoz. Semmi teendő. Ez a kísérlet 40 perc múlva automatikusan újra próbálkozik.|
|LicenseLimitExceeded|A felhasználó nem hozható létre a célalkalmazás alkalmazásban, mert nincsenek elérhető licencek ehhez a felhasználóhoz. További licenceket is megadhat a célalkalmazás számára, vagy áttekintheti a felhasználói hozzárendelések és attribútumok leképezésének konfigurációját, hogy a megfelelő felhasználók hozzá legyenek rendelve a megfelelő attribútumokhoz.|
|DuplicateTargetEntries  |A műveletet nem lehetett befejezni, mert a célalkalmazás több felhasználója található a konfigurált egyező attribútumokkal. Távolítsa el az ismétlődő felhasználót a célalkalmazás alkalmazásból, vagy konfigurálja újra az attribútum-hozzárendeléseket az [itt](../app-provisioning/customize-application-attributes.md)leírtak szerint.|
|DuplicateSourceEntries | A műveletet nem lehetett befejezni, mert egynél több felhasználó található a konfigurált egyező attribútumokkal. Távolítsa el az ismétlődő felhasználót, vagy konfigurálja újra az attribútum-hozzárendeléseket az [itt](../app-provisioning/customize-application-attributes.md)leírtak szerint.|
|ImportSkipped | Az egyes felhasználók kiértékelése után a rendszer megkísérli importálni a felhasználót a forrásrendszer használatával. Ez a hiba általában akkor fordul elő, ha az importálandó felhasználó hiányzik az attribútum-hozzárendelésekben definiált megfelelő tulajdonság. A megfelelő attribútumhoz tartozó felhasználói objektumon nincs megadva érték, nem értékelhető ki a hatókör, a megfeleltetés vagy az Exportálás módosítása. Vegye figyelembe, hogy a hiba jelenléte nem jelzi, hogy a felhasználó hatókörben van, mivel még nem értékelte ki a hatókört a felhasználó számára.|
|EntrySynchronizationSkipped | A kiépítési szolgáltatás sikeresen lekérdezte a forrás rendszerét, és azonosította a felhasználót. A felhasználóra vonatkozóan nem történt további művelet, és a rendszer kihagyta őket. A kihagyás oka az lehet, hogy a felhasználó hatókörén kívül esik, vagy a felhasználó már meglévő a célszámítógépen, és nincs szükség további módosításokra.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Amikor lekéréses kérelmet küld egy felhasználó vagy csoport beolvasására, a válaszban több felhasználót vagy csoportot kaptunk. A rendszer csak egy felhasználót vagy csoportot várt a válaszban. Ha [például](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)lekéri egy csoport lekérését, és egy szűrőt biztosít a tagok kizárásához, és a scim-végpont visszaadja a tagokat, ezt a hibát fogjuk kidobni.|

## <a name="next-steps"></a>Következő lépések

* [A felhasználó kiépítési állapotának megtekintése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Hiba történt a felhasználók Azure AD Gallery-alkalmazásba való konfigurálásának beállításakor](../app-provisioning/application-provisioning-config-problem.md)
* [Naplók kiépítés gráf API-val](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)