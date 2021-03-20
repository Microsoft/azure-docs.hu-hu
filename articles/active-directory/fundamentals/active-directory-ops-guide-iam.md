---
title: Azure Active Directory identitás-és hozzáférés-kezelési műveletek útmutatója
description: Ez az üzemeltetési útmutató ismerteti az identitás-és hozzáférés-kezelési műveletek biztonságossá tételéhez szükséges ellenőrzéseket és műveleteket.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 66bce573be5a31641bdff809b8e9a79b617a703a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371001"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Azure Active Directory identitás-és hozzáférés-kezelési műveletek útmutatója

Az [Azure ad üzemeltetési útmutatójának](active-directory-ops-guide-intro.md) jelen szakasza azokat az ellenőrzéseket és műveleteket ismerteti, amelyeket figyelembe kell venni az identitások és a hozzájuk tartozó hozzárendelések életciklusának védelméhez és kezeléséhez.

> [!NOTE]
> Ezek a javaslatok a közzététel dátumától kezdve érvényesek, de idővel változhatnak. A szervezeteknek folyamatosan értékelniük kell az identitásuk gyakorlatát, mivel a Microsoft termékei és szolgáltatásai idővel fejlődnek.

## <a name="key-operational-processes"></a>Fő működési folyamatok

### <a name="assign-owners-to-key-tasks"></a>Tulajdonosok kiosztása a legfontosabb feladatokhoz

A Azure Active Directory kezeléséhez olyan kulcsfontosságú működési feladatok és folyamatok folyamatos végrehajtása szükséges, amelyek esetleg nem részei a bevezetési projektnek. Még mindig fontos, hogy ezeket a feladatokat a környezet fenntartásához állítsa be. A legfontosabb feladatok és az ajánlott tulajdonosok a következők:

| Feladat | Tulajdonos |
| :- | :- |
| Az Azure-előfizetések létrehozási folyamatának meghatározása | Szervezettől függően |
| Annak eldöntése, hogy ki kap Enterprise Mobility + Security licenceket | IAM Operations csapat |
| Annak eldöntése, hogy ki kap Microsoft 365 licenceket | Termelékenységi csapat |
| Döntse el, hogy ki kap más licenceket, például a Dynamics, a Visual Studio Codespaces | Alkalmazás tulajdonosa |
| Licencek hozzárendelése | IAM Operations csapat |
| Licenc-hozzárendelési hibák elhárítása és szervizelése | IAM Operations csapat |
| Identitások kiépítése az alkalmazásokban az Azure AD-ben | IAM Operations csapat |

A lista áttekintése során előfordulhat, hogy tulajdonost kell rendelnie olyan feladatokhoz, amelyek nem rendelkeznek tulajdonossal, vagy nem módosítanak tulajdonjogot olyan feladatokhoz, amelyek nem illeszkednek a fenti javaslatokhoz.

#### <a name="assigning-owners-recommended-reading"></a>A tulajdonos javasolt olvasásának kiosztása

- [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](../roles/permissions-reference.md)
- [Irányítás az Azure-ban](../../governance/index.yml)

## <a name="on-premises-identity-synchronization"></a>Helyszíni identitások szinkronizálása

### <a name="identify-and-resolve-synchronization-issues"></a>Szinkronizálási problémák azonosítása és megoldása

A Microsoft azt javasolja, hogy a helyszíni környezetben felmerülő problémák megfelelő alapismeretekkel rendelkezzenek, és a felhőben szinkronizálási problémákhoz vezethet. Mivel az automatizált eszközök, mint például a [IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) és a [Azure ad Connect Health](../hybrid/whatis-azure-ad-connect.md#why-use-azure-ad-connect-health) nagy mennyiségű téves pozitív eredményt hozhatnak létre, javasoljuk, hogy azonosítsa azokat a szinkronizálási hibákat, amelyek több mint 100 napig megmaradtak. A hosszú távú megoldatlan szinkronizálási hibák támogatási incidenseket hozhatnak. A szinkronizálás során felmerülő [hibaelhárítási hibák](../hybrid/tshoot-connect-sync-errors.md) a különböző típusú szinkronizálási hibák áttekintése, a hibákat okozó lehetséges forgatókönyvek és a hibák kijavításának lehetséges módjai.

### <a name="azure-ad-connect-sync-configuration"></a>Azure AD Connect szinkronizálási konfiguráció

Az összes hibrid élmény, az eszközön alapuló biztonsági testhelyzet és az Azure AD-integráció engedélyezéséhez szinkronizálnia kell a felhasználói fiókokat, amelyeket az alkalmazottak az asztalokra való bejelentkezéshez használnak.

Ha nem szinkronizálja az erdő felhasználói bejelentkezést, akkor módosítania kell a szinkronizálást, hogy a megfelelő erdőből álljon.

#### <a name="synchronization-scope-and-object-filtering"></a>Szinkronizálási hatókör és objektum-szűrés

A szinkronizáláshoz nem szükséges objektumok ismert gyűjtőit a következő működési előnyökkel távolíthatja el:

- A szinkronizálási hibák kevesebb forrása
- Gyorsabb szinkronizálási ciklusok
- Kevesebb "szemetet" kell végrehajtani a helyszíni, például a felhőben nem releváns helyszíni szolgáltatásfiókok esetében a globális címlista szennyezése miatt.

> [!NOTE]
> Ha úgy találja, hogy sok olyan objektumot importál, amelyet a rendszer nem exportál a felhőbe, akkor szervezeti egység vagy adott attribútumok alapján kell szűrnie.

Példák a kizárandó objektumokra:

- A felhőalapú alkalmazásokhoz nem használt szolgáltatásfiókok
- Azok a csoportok, amelyek nem a Felhőbeli forgatókönyvekben használhatók, például az erőforrásokhoz való hozzáférés biztosítására használják.
- Az Azure AD B2B együttműködésnek szánt külső identitásokkal rendelkező felhasználók vagy névjegyek
- Azok a számítógépfiókok, amelyeken az alkalmazottak nem férnek hozzá a felhőalapú alkalmazásokhoz, például kiszolgálókon

> [!NOTE]
> Ha egyetlen emberi identitásnál több fiók van kiépítve, például egy örökölt tartomány áttelepítése, az egyesítés vagy a beszerzés, csak a felhasználó által a napi rendszerességgel használt fiókot kell szinkronizálni, például hogy mire használják a számítógépére való bejelentkezést.

Ideális esetben egyensúlyt szeretne elérni a szinkronizálandó objektumok számának és a szabályok összetettségének csökkentése között. Általában a szervezeti egységek/tárolók [szűrése](../hybrid/how-to-connect-sync-configure-filtering.md) , valamint a cloudFiltered attribútumhoz hozzárendelt egyszerű attribútumok kombinációja egy hatékony szűrési kombináció.

> [!IMPORTANT]
> Ha éles környezetben használja a csoportosítást, érdemes áttérnie egy másik szűrési megközelítésre.

#### <a name="sync-failover-or-disaster-recovery"></a>Feladatátvétel vagy vész-helyreállítás szinkronizálása

Azure AD Connect kulcsfontosságú szerepet játszik a létesítési folyamatban. Ha a szinkronizálási kiszolgáló valamilyen okból offline állapotba kerül, a helyszíni módosítások nem frissíthetők a felhőben, és hozzáférési problémákhoz vezethetnek a felhasználók számára. Ezért fontos olyan feladatátvételi stratégiát definiálni, amely lehetővé teszi a rendszergazdák számára a szinkronizálás gyors folytatását, miután a szinkronizálási kiszolgáló offline állapotba kerül. Az ilyen stratégiák a következő kategóriákba tartozhatnak:

- **Azure ad Connect kiszolgáló (k) üzembe helyezése átmeneti módban** – lehetővé teszi, hogy a rendszergazda egy egyszerű konfigurációs kapcsoló használatával "népszerűsítse" az átmeneti kiszolgálót éles környezetben.
- **Virtualizálás használata** – ha az Azure ad-kapcsolódás virtuális GÉPEN (VM) van telepítve, a rendszergazdák a virtualizációs verem segítségével élő áttelepítésre vagy gyors üzembe helyezésre használhatják a virtuális gépet, és így folytatják a szinkronizálást.

Ha a szervezete nem rendelkezik a szinkronizáláshoz szükséges vész-helyreállítási és feladatátvételi stratégiával, ne habozzon üzembe helyezni Azure AD Connect átmeneti módban. Hasonlóképpen, ha az éles környezet és az előkészítési konfiguráció között eltérés tapasztalható, akkor újra kell alapterv Azure AD Connect átmeneti üzemmódot, hogy az megfeleljen az éles konfigurációnak, beleértve a szoftver-verziókat és a konfigurációkat is.

![A Azure AD Connect átmeneti üzemmód konfigurációjának képernyőképe](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>Aktualitások

A Microsoft frissítései rendszeresen Azure AD Connect. Maradjon naprakész, hogy kihasználhassa az egyes új verziók teljesítménybeli javításait, hibajavításait és új képességeit.

Ha a Azure AD Connect verziója több mint hat hónapja van hátra, frissítsen a legújabb verzióra.

#### <a name="source-anchor"></a>Forrás horgonya

Az **MS-DS-consistencyguid** használatakor a [forrás-horgony](../hybrid/plan-connect-design-concepts.md) lehetővé teszi az objektumok erdőkön és tartományokban való egyszerűbb áttelepítését, amely az ad-tartomány összevonása/tisztítása, összevonások, beszerzések és eladások esetében gyakori.

Ha jelenleg a **ObjectGuid** használja a forrás-horgonyként, javasoljuk, hogy váltson az **MS-DS-ConsistencyGuid** használatára.

#### <a name="custom-rules"></a>Egyéni szabályok

Azure AD Connect egyéni szabályok lehetővé teszik a helyszíni objektumok és a Felhőbeli objektumok közötti adatforgalom szabályozását. Az egyéni szabályok túlhasználása vagy használata azonban a következő kockázatokat jelentheti:

- Bonyolultsági hibák elhárítása
- Teljesítmény romlása az objektumok közötti összetett műveletek végrehajtásakor
- Nagyobb valószínűséggel fennáll a konfiguráció eltérése az üzemi kiszolgáló és az átmeneti kiszolgáló között.
- További terhelés a Azure AD Connect frissítésekor, ha az egyéni szabályok az 100-nál nagyobb prioritáson belül jönnek létre (amelyet a beépített szabályok használnak)

Ha túlságosan összetett szabályokat használ, érdemes megvizsgálnia az összetettség okait, és megtalálhatja az egyszerűsítési lehetőségeket. Hasonlóképpen, ha az egyéni szabályokat a 100-as prioritási értékkel hozta létre, akkor a szabályokat úgy kell kijavítania, hogy ne legyenek veszélyben vagy ütköznek az alapértelmezett készlettel.

Példák az egyéni szabályok használatára:

- **A hibás adatok kompenzálása a címtárban** – ebben az esetben ajánlott az ad-csapat tulajdonosait használni, és szervizelési feladatként törölni a címtárban lévő adatok mennyiségét, és a folyamatok módosításával elkerülheti a helytelen adatok ismételt bevezetését.
- **Az egyes felhasználók egyszeri szervizelése** – gyakran előfordul, hogy olyan szabályokat talál, amelyek különleges eseteket mutatnak be, általában egy adott felhasználó problémája miatt.
- A **"CloudFiltering" túlbonyolítása** – az objektumok számának csökkentése jó gyakorlat, ezért a szinkronizálási hatókör számos szinkronizálási szabály használatával történő létrehozása és a túlbonyolítás kockázata is fennáll. Ha összetett logika van a szervezeti egység szűrésén kívüli objektumok belefoglalására/kizárására, akkor azt javasoljuk, hogy ezt a logikát a szinkronizáláson kívül is kezeljék, és az objektumokat egyszerű "cloudFiltered" attribútummal kell ellátni, amely egy egyszerű szinkronizálási szabállyal végezhető el.

#### <a name="azure-ad-connect-configuration-documenter"></a>Azure AD Connect konfigurációs dokumentáló

A [Azure ad Connect Configuration documenter](https://github.com/Microsoft/AADConnectConfigDocumenter) egy olyan eszköz, amellyel egy Azure ad Connect-telepítés dokumentációját hozhatja létre, amely lehetővé teszi a szinkronizálási konfiguráció jobb megismerését, a megfelelő dolgok megszerzését, valamint a Azure ad Connect új összeállításának és konfigurálásának, illetve az egyéni szinkronizálási szabályok hozzáadásának vagy frissítésének módosítását. Az eszköz jelenlegi képességei a következők:

- Azure AD Connect Sync teljes konfigurációjának dokumentációja.
- A két Azure AD Connect szinkronizálási kiszolgáló konfigurációjában vagy egy adott alapkonfiguráció változásaiban bekövetkező változások dokumentációja.
- PowerShell üzembe helyezési parancsfájl létrehozása a szinkronizálási szabályok közötti különbségek vagy testreszabások áttelepítéséhez az egyik kiszolgálóról a másikra.

## <a name="assignment-to-apps-and-resources"></a>Hozzárendelés alkalmazásokhoz és erőforrásokhoz

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Csoportos licencelés a Microsoft Cloud Serviceshez

A Azure Active Directory a Microsoft Cloud Services [csoportos licencelése](./active-directory-licensing-whatis-azure-portal.md) révén egyszerűsíti a licencek kezelését. Így a IAM a csoportok infrastruktúráját és delegált felügyeletét biztosítja a szervezetek megfelelő csoportjai számára. Több módon is beállíthatja a csoportok tagságát az Azure AD-ben, beleértve a következőket:

- A helyszíni csoportokkal **szinkronizálva** a helyszíni címtárakból is származhat, ami jó illeszkedést jelenthet olyan szervezetek számára, amelyek olyan csoportos felügyeleti folyamatokkal rendelkeznek, amelyek kiterjeszthetők a licencek Microsoft 365-ben való hozzárendelésére.

- **Attribútum-alapú/dinamikus** csoportok hozhatók létre a felhőben egy felhasználói attribútumokon alapuló kifejezés alapján, például a részleg az "értékesítés". Az Azure AD megtartja a csoport tagjait, és összhangban tartja a definiált kifejezéssel. Ha ezt a csoportot használja a licenc-hozzárendeléshez, az attribútum-alapú licenc-hozzárendelést is lehetővé teszi, amely kiválóan alkalmas a címtárban magas adatminőséggel rendelkező szervezetek számára.

- A **delegált tulajdonosi** csoportok a felhőben hozhatók létre, és kijelölhető tulajdonosok is lehetnek. Így az üzleti tulajdonosok, például az együttműködési csapat vagy a BI-csapat megadható, hogy ki férhet hozzá.

Ha jelenleg manuális eljárást használ a licencek és összetevők felhasználóknak való hozzárendeléséhez, javasoljuk, hogy hozzon létre csoportos licencelést. Ha az aktuális folyamat nem figyeli a licencelési hibákat, illetve hogy mi van hozzárendelve az elérhető megoldáshoz, a licencelési hibák elhárításához és a licencelési hozzárendelés figyeléséhez meg kell határoznia a javításokat.

A licencek kezelésének egy másik aspektusa a szolgáltatási csomagok (a licenc összetevői) meghatározása, amelyet a szervezeten belüli feladatok alapján kell engedélyezni. A nem szükséges szolgáltatási csomagokhoz való hozzáférés biztosítása azt eredményezheti, hogy a felhasználók olyan eszközöket látnak el az Office Portalon, amelyeket nem tanítanak meg vagy nem használnak. Az informatikai részleg további ügyfélszolgálati kötetet, szükségtelen kiépítéseket, valamint a megfelelőség és a kockázatkezelés kockázatát is lehetővé teszi, például ha olyan személyeknek állít ki OneDrive, akik a tartalmak megosztására nem jogosultak.

A következő irányelvek segítségével határozhatja meg a felhasználóknak szóló szolgáltatási terveket:

- A rendszergazdáknak meg kell határozniuk a felhasználók számára a szerepkörük alapján felkínált szolgáltatási csomagok "csomagjait", például a Fehér galléros munkavégzők és a padló feldolgozók számára.
- Hozzon létre csoportokat fürt szerint, és rendelje hozzá a licencet a Service csomaghoz.
- Szükség esetén egy attribútum is meghatározható a felhasználók csomagjainak tárolására.

> [!IMPORTANT]
> Az Azure AD-beli csoportos licencelés bevezeti a licencelési hiba állapotában lévő felhasználók fogalmát. Ha bármilyen licencelési hibát észlel, azonnal [azonosítania kell és fel kell oldania](../enterprise-users/licensing-groups-resolve-problems.md) a licenc-hozzárendelési problémákat.

![A számítógép képernyőjének leírását tartalmazó képernyőkép automatikusan létrejön](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>Életciklus-kezelés

Ha jelenleg olyan eszközt használ, mint például a [Microsoft Identity Manager](/microsoft-identity-manager/) vagy a harmadik féltől származó rendszer, amely egy helyszíni infrastruktúrára támaszkodik, javasoljuk, hogy a hozzárendelést kiszervezje a meglévő eszközről, hozzon létre csoportos licencelést, és definiáljon csoportokon alapuló csoportos életciklus- [kezelést.](../enterprise-users/licensing-group-advanced.md#use-group-based-licensing-with-dynamic-groups) Hasonlóképpen, ha a meglévő folyamat nem veszi figyelembe a szervezeten kívüli új alkalmazottakat vagy alkalmazottakat, akkor a csoport alapú licencelést dinamikus csoportok alapján kell telepítenie, és meg kell határoznia a csoporttagság életciklusát. Végül, ha a csoportos licencelés olyan helyszíni csoportokra van telepítve, amelyek nem rendelkeznek életciklus-felügyelettel, érdemes lehet felhőalapú csoportokat használni olyan képességek engedélyezéséhez, mint például a delegált tulajdonjog vagy az attribútum-alapú dinamikus tagság.

### <a name="assignment-of-apps-with-all-users-group"></a>Alkalmazások hozzárendelése a "minden felhasználó" csoporttal

Az erőforrás-tulajdonosok úgy vélik, hogy a **minden felhasználó** csoport csak **vállalati alkalmazottakat** tartalmaz, ha azok ténylegesen **vállalati alkalmazottakat** és **vendégeket** is tartalmazhatnak. Ennek eredményeképpen különleges figyelmet igényel, ha az **összes felhasználó** csoportot használja az alkalmazás-hozzárendeléshez, és hozzáférést biztosít az erőforrásokhoz, például a SharePoint-tartalomhoz vagy-alkalmazásokhoz.

> [!IMPORTANT]
> Ha a **minden felhasználó** csoport engedélyezve van, és a feltételes hozzáférési házirendekhez, alkalmazásokhoz vagy erőforrás-hozzárendelésekhez használatos, akkor ügyeljen arra, hogy [a csoport biztonságossá](../external-identities/use-dynamic-groups.md) tétele, ha nem szeretné, hogy a vendég felhasználók is szerepeljenek. Emellett a licencelési hozzárendeléseket úgy kell kijavítania, hogy csak **vállalati alkalmazottakat** tartalmazó csoportokat hozzon létre és rendeljen hozzájuk. Ha azonban úgy találja, hogy a **minden felhasználó** csoport engedélyezve van, de nem használja az erőforrásokhoz való hozzáférést, akkor győződjön meg arról, hogy a szervezet operatív útmutatása szerint szándékosan használja ezt a csoportot (amely magában foglalja a **vállalati alkalmazottakat** és a **vendégeket** is).

### <a name="automated-user-provisioning-to-apps"></a>Automatikus felhasználó-kiépítés az alkalmazásokba

A [felhasználók automatikus](../app-provisioning/user-provisioning.md) kiosztása az alkalmazásokban a legjobb módszer a különböző rendszerekben lévő identitások egységes kiépítésének, megszüntetésének és életciklusának létrehozására.

Ha jelenleg ad-hoc módon helyez üzembe alkalmazásokat, vagy olyan dolgokkal, mint például a CSV-fájlok, az JIT vagy egy olyan helyszíni megoldás, amely nem foglalkozik az életciklus-kezeléssel, javasoljuk, hogy az Azure AD által még nem támogatott alkalmazások esetében [alkalmazza](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) az alkalmazások üzembe helyezését az Azure ad-vel.

![Azure AD-kiépítési szolgáltatás](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Azure AD Connect különbözeti szinkronizálási ciklus alapterve

Fontos megérteni a szervezet változásainak mennyiségét, és győződjön meg arról, hogy nem tart túl sokáig ahhoz, hogy kiszámítható szinkronizációs idő legyen.

Az [alapértelmezett különbözeti szinkronizálás](../hybrid/how-to-connect-sync-feature-scheduler.md) gyakorisága 30 perc. Ha a különbözeti szinkronizálás folyamatosan 30 percnél hosszabb időt vesz igénybe, vagy jelentős eltérések vannak az átmeneti és a termelési különbözeti szinkronizálási teljesítmény között, akkor vizsgálja meg és tekintse át a [Azure ad Connect teljesítményét befolyásoló tényezőket](../hybrid/plan-connect-performance-factors.md).

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Azure AD Connect ajánlott olvasmányok hibaelhárítása

- [Címtár attribútumainak előkészítése a Microsoft 365sal való szinkronizáláshoz a IdFix eszköz használatával](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect: hibák elhárítása szinkronizálás közben](../hybrid/tshoot-connect-sync-errors.md)

## <a name="summary"></a>Összefoglalás

A biztonságos identitás-infrastruktúrának öt aspektusa van. Ebben a listában gyorsan megtalálhatja és megteheti a szükséges műveleteket az identitások és jogosultságok életciklusának védelméhez és kezeléséhez a szervezetében.

- Rendeljen tulajdonosokat a legfontosabb feladatokhoz.
- A szinkronizálási problémák megkeresése és megoldása.
- A vész-helyreállítási feladatátvételi stratégia meghatározása.
- Egyszerűsítse a licencek kezelését és az alkalmazások hozzárendelését.
- A felhasználók üzembe helyezésének automatizálása az alkalmazásokban.

## <a name="next-steps"></a>Következő lépések

Ismerkedés a [hitelesítési kezelési ellenőrzésekkel és műveletekkel](active-directory-ops-guide-auth.md).