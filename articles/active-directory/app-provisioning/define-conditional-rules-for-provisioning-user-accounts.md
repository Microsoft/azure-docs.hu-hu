---
title: Alkalmazások kiépítése a hatóköri szűrőkkel | Microsoft Docs
description: Ismerje meg, hogyan használhatók a hatóköri szűrők, hogy megakadályozza az olyan alkalmazásokban lévő objektumok használatát, amelyek támogatják az automatikus felhasználó-kiépítés kiépítési módját, ha egy objektum nem felel meg az üzleti követelményeinek.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: kenwith
ms.openlocfilehash: 7fff456b7ad6e980fc3c9bda36cfcab02e2ed863
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255831"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Attribútum-alapú alkalmazás-kiépítés hatóköri szűrőkkel
Ennek a cikknek a célja annak ismertetése, hogyan használhatók a hatóköri szűrők olyan attribútum-alapú szabályok definiálásához, amelyek meghatározzák, hogy mely felhasználók legyenek kiépítve egy alkalmazáshoz.

## <a name="scoping-filter-use-cases"></a>Hatókör-szűrő használati esetei

A hatóköri szűrők lehetővé teszik, hogy az Azure Active Directory (Azure AD) kiépítési szolgáltatás belefoglaljon vagy kizárjon egy adott értékkel egyező attribútummal rendelkező felhasználókat. Ha például a felhasználók az Azure AD-ből egy értékesítési csapat által használt SaaS-alkalmazásba vannak kiépítve, akkor megadhatja, hogy csak a "Sales" osztály "részleg" attribútummal rendelkező felhasználók legyenek kiépítve.

A hatóköri szűrők a kiépítési összekötő típusától függően eltérő módon is használhatók:

* **Kimenő kiépítés az Azure ad-ből az SaaS-alkalmazásokba**. Ha az Azure AD a forrásoldali rendszer, a felhasználók [és csoportok hozzárendelései](../manage-apps/assign-user-or-group-access-portal.md) a leggyakoribb módszer annak meghatározására, hogy mely felhasználók tartoznak a kiépítés hatóköréhez. Ezek a hozzárendelések az egyszeri bejelentkezés engedélyezésére is használhatók, és egyetlen módszert biztosítanak a hozzáférés és a kiépítés kezeléséhez. A hatókörhöz tartozó szűrők opcionálisan, a hozzárendelések mellett vagy ahelyett is használhatók, hogy az attribútumok alapján szűrhetik a felhasználókat.

    >[!TIP]
    > Egy vállalati alkalmazás hozzárendelései alapján letilthatja az üzembe helyezést, ha a [hatókör](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) menü Beállítások elemére módosítja a kiépítési beállítások lehetőséget az **összes felhasználó és csoport szinkronizálásához**. 

* **Bejövő kiépítés a HCM-alkalmazásokból az Azure ad-be és a Active Directoryba**. Ha egy [HCM-alkalmazás, például a munkanap](../saas-apps/workday-tutorial.md) a forrásrendszer, a hatóköri szűrők az elsődleges módszer annak meghatározására, hogy mely felhasználókat kell kiépíteni a HCM-alkalmazásból Active Directory vagy az Azure ad-be.

Alapértelmezés szerint az Azure AD kiépítési összekötői nem rendelkeznek konfigurált attribútum-alapú hatóköri szűrőkkel. 

## <a name="scoping-filter-construction"></a>Hatókör-szűrő kialakítása

Egy hatóköri szűrő egy vagy több *záradékból* áll. A záradékok határozzák meg, hogy mely felhasználók számára engedélyezett a hatókör-szűrő átadása az egyes felhasználók attribútumainak kiértékelésével. Előfordulhat például, hogy egy olyan záradékkal rendelkezik, amely megköveteli, hogy a felhasználó "State" attribútuma "New York" legyen, így csak a New York-i felhasználók lettek kiépítve az alkalmazásba. 

Egyetlen záradék egyetlen feltételt határoz meg egyetlen attribútum értékének megadásához. Ha egy hatókör-szűrőben több záradék jön létre, a rendszer a "és a" logikával együtt értékeli ki őket. Ez azt jelenti, hogy az összes záradéknak az "igaz" értéket kell kiértékelnie ahhoz, hogy egy felhasználó kiépíthető legyen.

Végül több hatókör-szűrő is létrehozható egyetlen alkalmazáshoz. Ha több hatóköri szűrő van jelen, a rendszer a "vagy" logikával együtt értékeli ki őket. Ez azt jelenti, hogy ha a beállított hatóköri szűrők bármelyikének összes záradéka "true" értékre van kiértékelve, a felhasználó kiépítve.

Az Azure AD-létesítési szolgáltatás által feldolgozott minden egyes felhasználót vagy csoportot a rendszer minden egyes hatóköri szűrőnél egyedileg értékel ki.

Példaként vegye figyelembe a következő hatókör-szűrőt:

![Hatókör-szűrő](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

A hatókör-szűrőnek megfelelően a felhasználóknak meg kell felelniük a következő feltételeknek:

* A szolgáltatásnak New Yorkban kell lennie.
* A mérnöki részlegnek kell dolgozniuk.
* A vállalat alkalmazotti AZONOSÍTÓjának 1 000 000 és 2 000 000 között kell lennie.
* A beosztás értéke nem lehet null vagy üres.

## <a name="create-scoping-filters"></a>Hatóköri szűrők létrehozása
A hatóköri szűrők az egyes Azure AD-beli felhasználói létesítési összekötők attribútum-hozzárendeléseinek részeként vannak konfigurálva. Az alábbi eljárás azt feltételezi, hogy már beállította az automatikus kiépítési műveletet az [egyik támogatott alkalmazáshoz](../saas-apps/tutorial-list.md) , és egy hatókör-szűrőt ad hozzá.

### <a name="create-a-scoping-filter"></a>Hatókör-szűrő létrehozása
1. A [Azure Portal](https://portal.azure.com)nyissa meg a **Azure Active Directory**  >  **vállalati alkalmazások**  >  **minden alkalmazás** szakaszt.

2. Válassza ki azt az alkalmazást, amelyhez az automatikus kiépítés konfigurálva van: például "ServiceNow".

3. Válassza a **Kiépítés** lapot.

4. A **leképezések** szakaszban válassza ki azt a leképezést, amelyhez hatókör-szűrőt szeretne konfigurálni: például "Azure Active Directory felhasználók szinkronizálása a ServiceNow".

5. Válassza ki a **forrás objektum hatóköre** menüt.

6. Válassza a **hatókör hozzáadása szűrő** elemet.

7. Definiáljon egy záradékot úgy, hogy kiválasztja a forrás **attribútum nevét**, egy **operátort** és egy **attribútum-értéket** , amely megfelel a következőnek:. A következő operátorok támogatottak:

   a. **Egyenlő**. A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum pontosan egyezik a bemeneti karakterlánc értékével (kis-és nagybetűk megkülönböztetése).

   b. **nem egyenlő**. A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum nem egyezik a bemeneti karakterlánc értékével (kis-és nagybetűk megkülönböztetése).

   c. **igaz**. A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum értéke TRUE (igaz) logikai érték.

   d. **hamis**. A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum a hamis logikai értéket tartalmazza.

   e. **NULL értékű**. A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum üres.

   f. **nem null**. A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum nem üres.

   : **REGEX egyezés**. A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum megfelel egy reguláris kifejezési mintának. Például: ([1-9] [0-9]) a 10 és 99 közötti számra illeszkedik.

   h. **nem a REGEX egyezése**. A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum nem felel meg a reguláris kifejezési mintának.
   
   i. **Greater_Than.** A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum nagyobb az értéknél. A hatóköri szűrőben megadott értéknek egész számnak kell lennie, és a felhasználó attribútumának egész számnak ([0, 1, 2,...]) kell lennie. 
   
   j. **Greater_Than_OR_EQUALS.** A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum értéke nagyobb vagy egyenlő, mint az érték. A hatóköri szűrőben megadott értéknek egész számnak kell lennie, és a felhasználó attribútumának egész számnak ([0, 1, 2,...]) kell lennie. 
   
   k. **Tartalmazza.** A záradék "igaz" értéket ad vissza, ha a kiértékelt attribútum tartalmazza a sztringet (kis-és nagybetűk megkülönböztetése) az [itt](/dotnet/api/system.string.contains)leírt módon 


>[!IMPORTANT] 
> - A IsMemberOf szűrő jelenleg nem támogatott.
> - A többértékű attribútumok esetében nem támogatott egyenlő és nem egyenlő.

9. Szükség esetén ismételje meg a 7-8. lépést további hatókör-záradékok hozzáadásához.

10. A **hatóköri szűrő címe** területen adja meg a hatókör-szűrő nevét.

11. Válassza az **OK** lehetőséget.

12. Kattintson ismét az **OK gombra** a **hatókör szűrők** képernyőjén. Szükség esetén ismételje meg a 6-11-es lépést egy másik hatókör-szűrő hozzáadásához.

13. Válassza a **Mentés** lehetőséget az **attribútum-leképezési** képernyőn. 

>[!IMPORTANT] 
> Egy új hatókör-szűrő mentése új teljes szinkronizálást indít az alkalmazáshoz, ahol a rendszer az új hatókör-szűrővel újra kiértékeli a forrásrendszer összes felhasználóját. Ha az alkalmazás egyik felhasználója korábban a kiépítés hatókörében volt, de a hatókörön kívül esik, a fiókja le van tiltva, vagy az alkalmazás nem lesz kiépítve. Az alapértelmezett viselkedés felülbírálásához tekintse meg a [Hatókörön kívüli felhasználói fiókok törlésének kihagyását](../app-provisioning/skip-out-of-scope-deletions.md)ismertető témakört.


## <a name="common-scoping-filters"></a>Gyakori hatókörű szűrők
| Cél attribútum| Operátor | Érték | Leírás|
|----|----|----|----|
|userPrincipalName|REGEX EGYEZÉS|.\*@domain.com |Minden olyan felhasználó, aki rendelkezik a tartománnyal rendelkező userPrincipal, a @domain.com kiépítés hatóköre lesz|
|userPrincipalName|NEM REGEX EGYEZÉS|.\*@domain.com|Minden olyan felhasználó, aki rendelkezik a tartománnyal rendelkező userPrincipal, ki lesz a @domain.com kiépítés hatóköre|
|Részleg|EGYENLŐ|értékesítési|Az értékesítési részleg összes felhasználója a kiépítés hatókörében van|
|workerID|REGEX EGYEZÉS|(1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])| A 1000000 és 2000000 közötti workerIDs rendelkező alkalmazottak a kiépítés hatókörébe tartoznak.|

## <a name="related-articles"></a>Kapcsolódó cikkek
* [A felhasználók üzembe helyezésének automatizálása és az SaaS-alkalmazások megszüntetése](../app-provisioning/user-provisioning.md)
* [Attribútum-hozzárendelések testreszabása a felhasználók üzembe helyezéséhez](../app-provisioning/customize-application-attributes.md)
* [Kifejezések írása attribútumleképezésekhez](functions-for-customizing-application-data.md)
* [Fiók kiépítési értesítései](../app-provisioning/user-provisioning.md)
* [A SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez Azure Active Directoryról alkalmazások számára](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Az SaaS-alkalmazások integrálásával kapcsolatos oktatóanyagok listája](../saas-apps/tutorial-list.md)
