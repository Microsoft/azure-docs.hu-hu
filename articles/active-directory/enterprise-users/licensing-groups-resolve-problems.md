---
title: Csoportos licenc-hozzárendelési problémák megoldása – Azure Active Directory | Microsoft Docs
description: Licenc-hozzárendelési problémák azonosítása és megoldása Azure Active Directory csoport alapú licencelés használatakor
services: active-directory
keywords: Az Azure AD licencelése
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ceea2dbcf408b6b35ef7c26580278d3979244119
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551538"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Egy csoport licenc-hozzárendelési problémáinak azonosítása és megoldása Azure Active Directory

A Azure Active Directory (Azure AD) csoport alapú licencelése a licencelési hiba állapotában lévő felhasználók fogalmát mutatja be. Ebből a cikkből megtudhatja, hogy a felhasználók milyen okokból kerülhetnek be ebben az állapotban.

Ha a licenceket közvetlenül az egyes felhasználókhoz rendeli, a csoport alapú licencelés használata nélkül, a hozzárendelési művelet sikertelen lehet. Ha például egy felhasználói rendszeren futtatja a PowerShell `Set-MsolUserLicense` -parancsmagot, a parancsmag számos, az üzleti logikával kapcsolatos okból sikertelen lehet. Előfordulhat például, hogy nem áll rendelkezésre elegendő számú licenc, vagy a két szolgáltatási csomag közötti ütközés, amely nem rendelhető hozzá egyszerre. A probléma azonnal vissza lesz jelentve.

Ha csoportos licencelést használ, ugyanazok a hibák fordulhatnak elő, de a háttérben történnek, miközben az Azure AD szolgáltatás licenceket rendel hozzá. Emiatt a hibákat nem lehet azonnal tájékoztatni. Ehelyett a rendszer rögzíti a felhasználói objektumot, majd a felügyeleti portálon keresztül jelentést készít. A felhasználó számára a licenc eredeti szándéka soha nem vész el, de a jövőbeli vizsgálat és megoldás érdekében hiba állapotban van rögzítve.

## <a name="find-license-assignment-errors"></a>Licenc-hozzárendelési hibák keresése

### <a name="to-find-users-in-an-error-state-in-a-group"></a>Felhasználók megkeresése egy csoportban lévő hibás állapottal

1. Nyissa meg a csoportot az Áttekintés lapra, és válassza a **licencek** lehetőséget. Egy értesítés jelenik meg, ha bármilyen felhasználó hibás állapotban van.

   ![Csoport és hiba értesítéseinek üzenete](./media/licensing-groups-resolve-problems/group-error-notification.png)

1. Válassza ki az értesítést az összes érintett felhasználó listájának megnyitásához. Az egyes felhasználókat egyenként is kiválaszthatja a további részletek megtekintéséhez.

   ![a csoport licencelési hibája állapotú felhasználók listája](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

1. Az összes olyan csoport megkereséséhez, amely legalább egy hibát tartalmaz, a **Azure Active Directory** panelen válassza a **licencek** lehetőséget, majd válassza az **Áttekintés** lehetőséget. Ha a csoportok beavatkozást igényelnek, egy információs mező jelenik meg.

   ![A hibás állapotú csoportok áttekintése és információi](./media/licensing-groups-resolve-problems/group-errors-widget.png)

1. Jelölje be a jelölőnégyzetet az összes hibát tartalmazó csoport listájának megtekintéséhez. További részleteket az egyes csoportok közül választhat ki.

   ![Hibákkal rendelkező csoportok áttekintése és listája](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)

A következő részekben ismertetjük az egyes lehetséges problémák leírását és a megoldás módját.

## <a name="not-enough-licenses"></a>Nincs elég licenc

**Probléma:** Nincs elég elérhető licenc a csoportban megadott egyik termékhez sem. További licenceket kell vásárolnia a termékhez, vagy fel kell vennie a nem használt licenceket más felhasználóktól vagy csoportokból.

Ha szeretné megtekinteni, hogy hány licenc áll rendelkezésre, lépjen **Azure Active Directory**  >  **licencek**  >  **minden termék** lehetőségre.

Ha szeretné megtekinteni, hogy mely felhasználók és csoportok használják a licenceket, válasszon ki egy terméket. A **licenccel rendelkező felhasználók** területen megtekintheti az összes olyan felhasználó listáját, akik közvetlenül vagy egy vagy több csoporton keresztül rendeltek hozzá licenceket. A **licencelt csoportok** területen láthatja az összes olyan csoportot, amelyhez a termékek hozzá lettek rendelve.

**PowerShell:** A PowerShell-parancsmagok ezt a hibát jelentik a _CountViolation_.

## <a name="conflicting-service-plans"></a>Ütköző szolgáltatási csomagok

**Probléma:** A csoportban megadott termékek egyike olyan szolgáltatási csomagot tartalmaz, amely ütközik egy másik terméken keresztül már hozzárendelt felhasználóval. Egyes szolgáltatási csomagok úgy vannak konfigurálva, hogy nem rendelhetők hozzá ugyanahhoz a felhasználóhoz, mint egy másik, kapcsolódó szolgáltatáscsomag.

Gondolja át a következő példát. A felhasználó rendelkezik az Office 365 Enterprise *E1* -licenccel közvetlenül hozzárendelt licenctel, és minden csomag engedélyezve van. A felhasználó hozzá lett adva egy olyan csoporthoz, amelyhez az Office 365 Enterprise *E3* termék hozzá van rendelve. Az E3 termék olyan szolgáltatási csomagokat tartalmaz, amelyek nem fedik át az E1-ben foglalt csomagokat, így a csoportos licenc-hozzárendelés meghiúsul az "ütköző szolgáltatási csomagok" hibával. Ebben a példában az ütköző szolgáltatási csomagok a következők:

- Az Exchange Online (2. csomag) ütközik az Exchange Online-val (1. csomag).

Az ütközés megoldásához le kell tiltania a csomagok két részét. Letilthatja a felhasználóhoz közvetlenül hozzárendelt E1-licencet. Vagy módosítania kell a teljes csoport licenc-hozzárendelését, és le kell tiltania a terveket az E3-licencben. Azt is megteheti, hogy eltávolítja az E1-licencet a felhasználótól, ha az az E3 licenc kontextusában redundáns.

Az ütköző licencek feloldásával kapcsolatos döntés mindig a rendszergazdához tartozik. Az Azure AD nem oldja meg automatikusan a licencek ütközéseit.

**PowerShell:** A PowerShell-parancsmagok ezt a hibát jelentik a _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Más termékek ettől a licenctől függenek

**Probléma:** A csoportban megadott termékek egyike egy olyan szervizcsomagot tartalmaz, amelyet egy másik Service-csomaghoz kell engedélyezni egy másik termékben a működéshez. Ez a hiba akkor fordul elő, amikor az Azure AD megkísérli eltávolítani az alapul szolgáló szolgáltatási csomagot. Ez például akkor fordulhat elő, ha eltávolítja a felhasználót a csoportból.

A probléma megoldásához meg kell győződnie arról, hogy a szükséges csomag továbbra is hozzá van rendelve a felhasználókhoz valamilyen más módszerrel, vagy hogy a függő szolgáltatások le vannak tiltva az adott felhasználók számára. Ezt követően megfelelően eltávolíthatja a csoport licencét ezekből a felhasználókból.

**PowerShell:** A PowerShell-parancsmagok ezt a hibát jelentik a _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>A használat helye nem engedélyezett

**Probléma:** Bizonyos Microsoft-szolgáltatások nem érhetők el minden helyen a helyi törvények és rendeletek miatt. Ahhoz, hogy licencet rendeljen a felhasználóhoz, meg kell adnia a felhasználó **használati helye** tulajdonságát. A Azure Portalban megadhatja a **felhasználói**  >  **profil**  >  **szerkesztése** szakaszban található helyet.

Ha az Azure AD olyan felhasználóhoz próbál csoport-licencet hozzárendelni, amelynek a használati helye nem támogatott, a művelet sikertelen lesz, és hibát jelez a felhasználónál.

A probléma megoldásához távolítsa el a felhasználókat a nem támogatott helyekről a licencelt csoportból. Ha a jelenlegi használati hely értéke nem a tényleges felhasználói helynek felel meg, akkor módosíthatja őket úgy, hogy a licencek megfelelően legyenek hozzárendelve a következő alkalommal (ha az új hely támogatott).

**PowerShell:** A PowerShell-parancsmagok ezt a hibát jelentik a _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Amikor az Azure AD hozzárendeli a csoportos licenceket, a megadott használati hely nélküli felhasználók öröklik a címtár helyét. Javasoljuk, hogy a rendszergazdák a helyi jogszabályoknak és előírásoknak való megfelelés érdekében a csoport alapú licencelés használata előtt állítsa be a használati hely helyes értékeit a felhasználók számára.

## <a name="duplicate-proxy-addresses"></a>Duplikált proxy címek

Ha az Exchange Online-t használja, előfordulhat, hogy a szervezet egyes felhasználói helytelenül vannak konfigurálva ugyanazzal a proxycímek értékkel. Ha a csoport alapú licencelés megpróbál hozzárendelni egy licencet egy ilyen felhasználóhoz, az sikertelen lesz, és azt mutatja, hogy a "proxy címe már használatban van".

> [!TIP]
> Ha szeretné megtekinteni, hogy van-e duplikált proxy címe, hajtsa végre a következő PowerShell-parancsmagot az Exchange Online-ban:
> ```
> Get-Recipient -ResultSize unlimited | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> A problémával kapcsolatos további információkért tekintse [meg a "proxy címe már használatban van" hibaüzenetet az Exchange Online-ban](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). A cikk azt is ismerteti, [hogyan csatlakozhat az Exchange Online-hoz a távoli PowerShell használatával](/powershell/exchange/connect-to-exchange-online-powershell).

Miután feloldotta az érintett felhasználók proxy-címeivel kapcsolatos problémákat, ügyeljen arra, hogy a licencek feldolgozását kényszerítse a csoporton, hogy a licencek érvénybe lépjenek.

## <a name="azure-ad-mail-and-proxyaddresses-attribute-change"></a>Az Azure AD levelezési és ProxyAddresses attribútumának változása

**Probléma:** Egy felhasználó vagy csoport licenc-hozzárendelésének frissítésekor láthatja, hogy egyes felhasználók Azure AD mail-és ProxyAddresses-attribútuma módosult.

A licenc-hozzárendelés felhasználó általi frissítése a proxy-címek kiszámításának aktiválását eredményezi, ami módosíthatja a felhasználói attribútumokat. A változás pontos okának megismeréséhez és a probléma megoldásához tekintse meg ezt a cikket a [proxyAddresses attribútum az Azure ad-ben való feltöltésének módjáról](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

## <a name="licenseassignmentattributeconcurrencyexception-in-audit-logs"></a>LicenseAssignmentAttributeConcurrencyException a naplókban

**Probléma:** A felhasználó rendelkezik a LicenseAssignmentAttributeConcurrencyException a naplózási naplókban.
Ha a csoportos licencelés megkísérli feldolgozni ugyanazon licenc egy felhasználóhoz való egyidejű licenc-hozzárendelését, a rendszer ezt a kivételt rögzíti a felhasználónál. Ez általában akkor fordul elő, ha egy felhasználó több, azonos hozzárendelt licenccel rendelkező csoport tagja. Az Azure AD megkísérli a felhasználói licenc feldolgozását, és elhárítja a problémát. A probléma megoldásához nem szükséges művelet az ügyféltől.

## <a name="more-than-one-product-license-assigned-to-a-group"></a>Egynél több, csoporthoz rendelt licenc

Egy csoporthoz több licencet is hozzárendelhet. Hozzárendelheti például az Office 365 Enterprise E3-t és Enterprise Mobility + Securityt egy csoporthoz, hogy egyszerűen engedélyezze az összes befoglalt szolgáltatást a felhasználók számára.

Az Azure AD megkísérli a csoportban megadott összes licenc hozzárendelését az egyes felhasználók számára. Ha az Azure AD nem tudja hozzárendelni az egyik terméket az üzleti logikával kapcsolatos problémák miatt, akkor a csoportba nem rendeli hozzá a többi licencet sem. Ilyen például, ha nincs elég licenc az összeshez, vagy ha ütközik a felhasználó által engedélyezett más szolgáltatásokkal.

Megtekintheti azokat a felhasználókat, akik nem tudtak beolvasni a hozzárendelést, és a probléma által érintett termékeket is megnézheti.

## <a name="when-a-licensed-group-is-deleted"></a>Licencelt csoport törlésekor

A csoport törlése előtt el kell távolítania a csoporthoz rendelt összes licencet. A licencek eltávolítása azonban a csoport összes felhasználója számára hosszabb időt is igénybe vehet. A licenc-hozzárendelések egy csoportból való eltávolításakor hibák léphetnek fel, ha a felhasználó függő licenccel rendelkezik, vagy ha van proxy-ütközési probléma, amely tiltja a licencek eltávolítását. Ha egy felhasználónak van olyan licence, amely a csoport törlése miatt eltávolított licenccel rendelkezik, a rendszer a felhasználóhoz tartozó licenc-hozzárendelést örökölt, közvetlen értékre konvertálja.

Vegyünk például egy olyan csoportot, amely az Office 365 E3/E5 csomaggal van társítva a Skype vállalati verzióhoz készült Service-csomaghoz. Azt is Képzelje el, hogy a csoport néhány tagja rendelkezik közvetlenül hozzárendelt hangkonferencia-licencekkel. A csoport törlése után a csoportos licencelés megpróbálja eltávolítani az Office 365 E3/E5-et az összes felhasználótól. Mivel a hangkonferencia a Skype vállalati verziótól függ, a hozzárendelt hangkonferencia-szolgáltatást használó felhasználók számára a csoport alapú licencelés az Office 365 E3/E5 licenceket a licenc-hozzárendelésre konvertálja.

## <a name="manage-licenses-for-products-with-prerequisites"></a>Az előfeltételekkel rendelkező termékek licencének kezelése

Egyes Microsoft Online termékekhez, amelyekhez Ön is tartozik, *bővítmények*. A bővítmények használatához az előfeltételként szükséges szervizcsomagot engedélyezni kell egy felhasználónak vagy csoportnak ahhoz, hogy licencet lehessen rendelni. A csoport alapú licenceléssel a rendszer megköveteli, hogy az előfeltétel-és a kiegészítő szolgáltatási csomagok is ugyanabban a csoportban legyenek. Ezzel biztosítható, hogy a csoportba felvett felhasználók megkapják a teljesen működőképes terméket. Vizsgáljuk meg a következő példát:

A Microsoft munkahelyi elemzési szolgáltatás egy kiegészítő termék. Egyetlen szervizcsomagot tartalmaz ugyanazzal a névvel. Ezt a szolgáltatáscsomag csak akkor rendelheti hozzá egy felhasználóhoz vagy csoporthoz, ha az alábbi előfeltételek egyike is hozzá van rendelve:

- Exchange Online (1. csomag)
- Exchange Online (2. csomag)

Ha ezt a terméket a saját csoportra próbálja hozzárendelni, a portál egy értesítési üzenetet küld vissza. Ha kiválasztjuk az elem részleteit, a következő hibaüzenet jelenik meg:

  "A licencelési művelet nem sikerült. A függő szolgáltatások hozzáadása vagy eltávolítása előtt győződjön meg arról, hogy a csoport rendelkezik a szükséges szolgáltatásokkal. **A szolgáltatáshoz a Microsoft munkahelyi elemzéséhez szükség van az Exchange Online (2. csomag) használatára is.**

Ahhoz, hogy ezt a kiegészítő licencet egy csoporthoz rendelje, biztosítania kell, hogy a csoport tartalmazza az előfeltételként szükséges szolgáltatási csomagot is. Előfordulhat például, hogy frissítünk egy meglévő csoportot, amely már tartalmazza a teljes Office 365 E3 terméket, majd hozzáadja a kiegészítő terméket.

Létrehozhat egy önálló csoportot is, amely csak azokat a minimálisan szükséges termékeket tartalmazza, amelyekkel a bővítmény működik. Ez a szolgáltatás csak a kiválasztott felhasználók licencelésére használható a kiegészítő termékhez. Az előző példa alapján a következő termékeket rendeli hozzá ugyanahhoz a csoporthoz:

- Office 365 Enterprise E3 és csak az Exchange Online (2. csomag) szolgáltatási csomag engedélyezve
- Microsoft Workplace Analytics

Mostantól a csoportba felvett felhasználók az E3 termék egy licencét és a munkahelyi elemzési termék egy licencét használják fel. Ugyanakkor ezek a felhasználók egy másik csoport tagjai lehetnek, amelyek teljes körű E3 terméket biztosítanak, és a termékhez csak egy licencet használnak fel.

> [!TIP]
> Az előfeltételként szükséges szolgáltatási csomagokhoz több csoportot is létrehozhat. Ha például az Office 365 Enterprise E1-et és az Office 365 Enterprise E3-ot használja a felhasználók számára, két csoportot hozhat létre a Microsoft munkahelyi elemzési licenchez: az egyik az E1 előfeltételként, a másik pedig az E3-ot használja. Ez lehetővé teszi, hogy további licencek használata nélkül terjessze a bővítményt az E1 és az E3 felhasználók számára.

## <a name="force-group-license-processing-to-resolve-errors"></a>Csoportházirend-licenc feldolgozásának kényszerítése a hibák elhárításához

Attól függően, hogy milyen lépéseket hajtott végre a hibák elhárításához, szükség lehet a csoportok feldolgozásának manuális elindítására a felhasználói állapot frissítéséhez.

Ha például egy licencet szabadít fel a felhasználóktól a közvetlen licenc-hozzárendelések eltávolításával, akkor olyan csoportok feldolgozását kell elindítania, amelyek korábban nem tudtak teljes mértékben felvenni az összes felhasználói tagot. Egy csoport újrafeldolgozásához lépjen a csoport ablaktáblára, nyissa meg a **licenceket**, majd kattintson az eszköztáron az **újrafeldolgozás** gombra.

## <a name="force-user-license-processing-to-resolve-errors"></a>A felhasználói licencek feldolgozásának kényszerítése a hibák elhárítása érdekében

Attól függően, hogy milyen lépéseket hajtott végre a hibák elhárításához, szükség lehet a felhasználók állapotának frissítéséhez szükséges manuális aktiválásra.

Ha például egy érintett felhasználó duplikált proxy-problémáját oldja fel, aktiválnia kell a felhasználó feldolgozását. Egy felhasználó újrafeldolgozásához lépjen a felhasználó ablaktáblára, nyissa meg a **licenceket**, majd kattintson az eszköztáron az **újrafeldolgozás** gombra.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a licencek csoportokon keresztüli kezelésével kapcsolatos egyéb forgatókönyvekről, tekintse meg a következőket:

* [Mi a Azure Active Directory csoportos licencelése?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban](./licensing-groups-assign.md)
* [Egyéni, licenccel rendelkező felhasználók migrálása csoportalapú licencelésre az Azure Active Directoryban](licensing-groups-migrate-users.md)
* [Felhasználók áttelepítése licencek között a csoport alapú licencelés használatával Azure Active Directory](licensing-groups-change-licenses.md)
* [Az Azure Active Directory csoportalapú licencelésének további forgatókönyvei](./licensing-group-advanced.md)
* [PowerShell-példák csoportházirend-alapú licenceléshez Azure Active Directory](licensing-ps-examples.md)