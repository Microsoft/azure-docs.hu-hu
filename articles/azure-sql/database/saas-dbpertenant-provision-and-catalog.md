---
title: Új bérlők kiépítése egy több-bérlős alkalmazásban
description: Ismerje meg, hogyan hozhat létre és katalogizálhat új bérlőket egy Azure SQL Database több-bérlős SaaS-alkalmazásban
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 26add03929551c912b4d7b7cf10741d53333689a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780563"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Ismerje meg, hogyan hozhat létre új bérlőket, és hogyan regisztrálhat azokat a katalógusban
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebből az oktatóanyagból megtudhatja, hogyan lehet SaaS-mintákat kiépíteni és katalogizálni. Azt is megtudhatja, hogyan valósulnak meg a Wingtip tickets SaaS-adatbázis-bérlői alkalmazásokban. Létrehozhatja és inicializálhatja az új bérlői adatbázisokat, és regisztrálhatja őket az alkalmazás bérlői katalógusában. A katalógus egy olyan adatbázis, amely a SaaS-alkalmazás számos bérlője és az adatmennyiség közötti leképezést tárolja. A katalógus fontos szerepet játszik az alkalmazások és a felügyeleti kérelmek megfelelő adatbázisba való irányításában.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Egyetlen új bérlő kiépítése.
> * További bérlők kötegének kiépítése.


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip tickets SaaS-adatbázis-bérlői alkalmazás telepítve van. Ha kevesebb mint öt perc alatt szeretné üzembe helyezni, tekintse meg [a Wingtip tickets SaaS adatbázis-bérlői alkalmazás üzembe helyezése és megismerése](./saas-dbpertenant-get-started-deploy.md)című témakört.
* Az Azure PowerShell telepítve van. További információért lásd [az Azure PowerShell használatának első lépéseit](/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>A SaaS-katalógus mintájának bemutatása

Az adatbázis-alapú több-bérlős SaaS-alkalmazásokban fontos tudni, hogy az egyes bérlők hol vannak tárolva. A SaaS-katalógus mintájában a katalógus-adatbázis tárolja az egyes bérlők és az adattároló adatbázisa közötti leképezést. Ez a minta akkor érvényes, ha a bérlői adatforgalom több adatbázis között van elosztva.

Az egyes bérlőket a katalógus egyik kulcsa azonosítja, amely az adatbázisa helyére van leképezve. A Wingtip jegyek alkalmazásban a kulcs a bérlő nevének kivonatával jön létre. Ez a séma lehetővé teszi, hogy az alkalmazás létrehozza a kulcsot az alkalmazás URL-címében szereplő bérlői név alapján. Más bérlői kulcsokra vonatkozó sémák is használhatók.

A katalógus lehetővé teszi az adatbázis nevének vagy helyének módosítását az alkalmazás minimális hatásával. A több-bérlős adatbázis-modellben ez a funkció a bérlő adatbázisok közötti áthelyezését is lehetővé teszi. A katalógus azt is felhasználhatja, hogy a bérlő vagy az adatbázis offline állapotban van-e a karbantartáshoz vagy más műveletekhez. Ez a képesség az [egyetlen bérlő visszaállítása oktatóanyagban](saas-dbpertenant-restore-single-tenant.md)található.

A katalógus további bérlői vagy adatbázis-metaadatokat is tárolhat, ilyen például a bérlők számára felkínált séma verziója, szolgáltatáscsomag vagy SLA. A katalógus más adatokat is tárolhat, amelyek lehetővé teszik az alkalmazások felügyeletét, az ügyfélszolgálatot vagy a DevOps.

Az SaaS-alkalmazáson túl a katalógus is engedélyezheti az adatbázis-eszközöket. A Wingtip tickets SaaS-adatbázis – bérlői minta alapján a katalógus a több-bérlős lekérdezés engedélyezésére szolgál, amelyet az [ad hoc jelentéskészítési oktatóanyagban](saas-tenancy-cross-tenant-reporting.md)vizsgálunk meg. Az adatbázisok közötti feladatok felügyeletét a [séma kezelése](saas-tenancy-schema-management.md) és a [bérlői elemzési](saas-tenancy-tenant-analytics.md) oktatóanyagok ismertetik.

A Wingtip tickets SaaS-mintákban a katalógus a [Elastic Database ügyféloldali kódtár (EDCL)](elastic-database-client-library.md)szegmens felügyeleti funkcióinak használatával valósul meg. A EDCL a javában és a .NET-keretrendszerben érhető el. A EDCL lehetővé teszi, hogy az alkalmazások egy adatbázis-alapú szegmens-hozzárendelést hozzanak létre, kezeljenek és használjanak.

A szegmensek térképe a szegmensek (adatbázisok) listáját, valamint a kulcsok (bérlők) és a szegmensek közötti leképezést tartalmazza. A EDCL függvények a bérlői kiépítés során használatosak a szegmenses Térkép bejegyzéseinek létrehozásához. Az alkalmazások futási időben használják a megfelelő adatbázishoz való kapcsolódáshoz. A EDCL gyorsítótárazza a kapcsolódási adatokat, hogy csökkentse a katalógus-adatbázis forgalmát, és gyorsítsák fel az alkalmazást.

> [!IMPORTANT]
> A leképezési szolgáltatás elérhető a katalógus-adatbázisban, de *nem szerkesztheti*. Csak Elastic Database ügyféloldali függvénytár-API-k használatával szerkesztheti a megfeleltetési adataikat. A leképezési adatkezelési kockázatok közvetlen módosítása a katalógust sérült, és nem támogatott.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Az SaaS-kiépítési minta bemutatása

Amikor új bérlőt ad hozzá egy olyan SaaS-alkalmazáshoz, amely egybérlős adatbázis-modellt használ, új bérlői adatbázist kell kiépítenie. Az adatbázist a megfelelő helyen és szolgáltatási szinten kell létrehozni. Emellett a megfelelő séma-és hivatkozási adataival kell inicializálni. És a megfelelő bérlői kulcs alatt regisztrálni kell a katalógusban.

Az adatbázis-kiépítés különböző megközelítési módokat használhat. SQL-szkripteket futtathat, üzembe helyezhet egy bacpac, vagy másolhat egy sablon-adatbázist.

Az adatbázis kiépítése a séma-felügyeleti stratégia részévé kell, hogy legyen. Győződjön meg arról, hogy az új adatbázisok a legújabb sémával vannak kiépítve. Ezt a követelményt a [séma kezelése oktatóanyag](saas-tenancy-schema-management.md)ismerteti.

A Wingtip tickets adatbázis-bérlői alkalmazás az új bérlőket a _basetenantdb_ nevű sablon-adatbázis másolásával, amely a katalógus-kiszolgálón van telepítve. A kiépítés az alkalmazásba a regisztrálási élmény részeként integrálható. Emellett a parancsfájlok használatával offline is használható. Ez az oktatóanyag a PowerShell használatával vizsgálja a kiépítés folyamatát.

A kiépítési parancsfájlok a _basetenantdb_ -adatbázis másolásával új bérlői adatbázist hoznak létre egy rugalmas készletben. A bérlői adatbázis a _newtenant_ DNS-aliashoz leképezett bérlői kiszolgálón jön létre. Ez az alias az új bérlők kiépítésére szolgáló kiszolgálóra mutató hivatkozást tart fenn, és frissül, hogy egy helyreállítási bérlői kiszolgálóra mutasson a vész-helyreállítási oktatóanyagokban (Dr. a[georestore](./saas-dbpertenant-dr-geo-restore.md), [Dr replikáció használatával](./saas-dbpertenant-dr-geo-replication.md)). A parancsfájlok ezt követően inicializálják az adatbázist a bérlő-specifikus adatokkal, és regisztrálják azt a katalógus szegmensének térképén. A bérlői adatbázisok a bérlő neve alapján kapják meg a neveket. Ez az elnevezési séma nem a minta kritikus része. A katalógus leképezi a bérlői kulcsot az adatbázis nevére, így bármely elnevezési konvenció használható.


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS-adatbázis-bérlői alkalmazás parancsfájljainak beolvasása

A Wingtip jegyek SaaS-szkriptek és az alkalmazás forráskódja az [WingtipTicketsSaaS-DbPerTenant GitHub-](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) tárházban érhető el. Tekintse meg az [általános útmutatót](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip tickets SaaS-parancsfájlok letöltésének és feloldásának lépéseihez.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Részletes útmutató a kiépítéshez és katalógusba vételhez

Annak megismeréséhez, hogy a Wingtip tickets alkalmazás hogyan valósítja meg az új bérlői üzembe helyezést, adjon hozzá egy töréspontot, és kövesse a munkafolyamatot a bérlő kiépítése közben.

1. A PowerShell ISE-ben nyissa meg a... \\ A Learning \\ -modulok ProvisionAndCatalog \\ _Demo-ProvisionAndCatalog.ps1_ , és a következő paramétereket kell megadni:

   * **$TenantName** = az új helyszín neve (például *Bushwillow Blues*).
   * **$VenueType** = az egyik előre definiált helyszín típusa: _blues, ClassicalMusic, Dance, jazz, judo, Motor Racing, többcélú, Opera, rockzene, Soccer_.
   * **$DemoScenario**  =  **1**. *egyetlen bérlő kiépítése*.

2. Töréspont hozzáadásához vigye a kurzort a *New-bérlőt* tartalmazó sorra. Ezután nyomja le az F9 billentyűt.

   ![A képernyőfelvétel egy olyan parancsfájlt mutat be, amelynek New-Tenant a Töréspont hozzáadására van kijelölve.](./media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. A szkript futtatásához nyomja le az F5 billentyűt.

4. Miután a szkript végrehajtása leáll a törésponton, nyomja le az F11 billentyűt a kód beírásához.

   ![Hibakeresés](./media/saas-dbpertenant-provision-and-catalog/debug.png)



A szkript végrehajtásának nyomon követése a **hibakeresési** menüpont használatával. Nyomja le az F10 és az F11 billentyűt a meghívott függvények beléptetéséhez. A PowerShell-parancsfájlok hibakeresésével kapcsolatos további információkért lásd: [Tippek a PowerShell-parancsfájlok használatához és hibakereséséhez](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).


Nem kell explicit módon követnie ezt a munkafolyamatot. Ismerteti, hogyan lehet hibakeresést végezni a parancsfájlban.

* **Importálja a CatalogAndDatabaseManagement. psm1 modult.** Katalógust és bérlői szintű absztrakciót biztosít a szegmens [felügyeleti](elastic-scale-shard-map-management.md) függvényeknél. Ez a modul nagy mennyiségű katalógus-mintát ágyaz be, és érdemes megvizsgálni.
* **Importálja a SubscriptionManagement. psm1 modult.** Az Azure-ba való bejelentkezéshez és a használni kívánt Azure-előfizetés kiválasztásához használható függvényeket tartalmazza.
* **Konfigurációs adatok beolvasása.** Lépjen be Get-Configuration az F11 használatával, és nézze meg, hogyan van megadva az alkalmazás konfigurációja. Az erőforrásnevek és az alkalmazás-specifikus értékek itt vannak meghatározva. Ne módosítsa ezeket az értékeket egészen addig, amíg nem ismeri a parancsfájlokat.
* **A katalógus objektum beolvasása.** Lépjen be a Get-Catalog szolgáltatásba, amely a magasabb szintű parancsfájlban használt Catalog objektumot állít össze és ad vissza. Ez a függvény a **AzureShardManagement. psm1**-ből importált szegmens-felügyeleti függvényeket használja. A katalógus objektum a következő elemekből áll:

   * $catalogServerFullyQualifiedName a standard szárral és a felhasználó nevével ( _Catalog- \<user\> . database. Windows .net_) jön létre.
   * $catalogDatabaseName – a *tenantcatalog* konfigurációból származik.
   * $shardMapManager – ez az objektum a katalógus-adatbázisból van inicializálva.
   * $shardMap – ez az objektum a katalógus-adatbázisban található _tenantcatalog_ szilánkleképezésből van inicializálva. Egy katalógus-objektum áll és visszaadva. Ez a magasabb szintű parancsfájlban használatos.
* **Az új bérlői kulcs kiszámítása.** A bérlőkulcsot egy kivonatoló algoritmus hozza létre a bérlő nevéből.
* **Ellenőrizze, hogy a bérlői kulcs már létezik-e.** A katalógus be van jelölve, hogy a kulcs elérhető legyen.
* **A bérlői adatbázis kiépítése a New-TenantDatabase használatával történik.** Az F11 használatával megtudhatja, hogyan kell kiépíteni az adatbázist egy [Azure Resource Manager sablonnal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

    Az adatbázis neve a bérlő nevéből jön létre, hogy egyértelmű legyen, melyik szilánk melyik bérlőhöz tartozik. Más adatbázis-elnevezési konvenciókat is használhat. Egy Resource Manager-sablon létrehoz egy bérlői adatbázist egy sablon-adatbázis (_baseTenantDB_) a katalógus-kiszolgálón való másolásával. Másik lehetőségként létrehozhat egy adatbázist, és inicializálhatja azt egy bacpac importálásával. Az inicializálási parancsfájlt jól ismert helyről is végrehajthatja.

    A Resource Manager-sablon a. ..\Learning Modules\Common\ mappában található: *tenantdatabasecopytemplate.jsbekapcsolva*

* **A bérlői adatbázis még inicializálva van.** A rendszer hozzáadja a helyszín (bérlő) nevét és a helyszín típusát. Itt más inicializálás is elvégezhető.

* **A bérlői adatbázis regisztrálva van a katalógusban.** A bérlői kulcs használatával regisztrálva van a *Add-TenantDatabaseToCatalog* . Nyomja le az F11 billentyűt a részletek megtekintéséhez:

    * A katalógus-adatbázis bekerül a szilánkleképezésbe (az ismert adatbázisok listájába).
    * Létrejön a kulcsérték és a szilánk társítása.
    * A bérlő (a helyszín neve) további metaadatai hozzáadódnak a katalógus bérlők táblájához. A bérlők tábla nem része a szegmens felügyeleti sémának, és nincs telepítve a EDCL. Ez a táblázat bemutatja, hogyan terjeszthető ki a katalógus-adatbázis további alkalmazásspecifikus információk támogatására.


A kiépítés befejezése után a végrehajtás visszatér az eredeti *bemutató-ProvisionAndCatalog* parancsfájlba. Megnyílik az **események** lap az új bérlő számára a böngészőben.

   ![Események lap](./media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Bérlők kötegének kiépítése

Ez a gyakorlat 17 bérlős köteget foglal le. Javasoljuk, hogy a bérlők számára kiépítse ezt a köteget, mielőtt más Wingtip-jegyeket SaaS-adatbázis-bérlői oktatóanyagokat indít el. Több adatbázissal is dolgozhat a szolgáltatással.

1. A PowerShell ISE-ben nyissa meg a... \\ Tanulási modulok \\ ProvisionAndCatalog \\ *Demo-ProvisionAndCatalog.ps1*. Módosítsa a *$DemoScenario* paramétert 3 értékre:

   * **$DemoScenario**  =  **3**. *bérlők kötegének kiépítése*.
2. A szkript futtatásához nyomja le az F5 billentyűt.

A szkript üzembe helyezi a további bérlők kötegét. Egy [Azure Resource Manager sablont](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) használ, amely a köteget vezérli, és az egyes adatbázisok kiépítési feltételeit egy csatolt sablonra delegálja. A sablonok ily módon való alkalmazása lehetővé teszi, hogy az Azure Resource Manager közvetítse a szkriptnek a kiépítési folyamatot. A sablonok szükség esetén párhuzamosan kiépítik az adatbázisokat és kezelik az újrapróbálkozásokat. A szkript idempotens, így ha az sikertelen vagy valamilyen okból leáll, futtassa újra.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>A sikeresen telepített bérlők kötegének ellenőrzése

* A [Azure Portal](https://portal.azure.com)keresse meg a kiszolgálók listáját, és nyissa meg a *tenants1* -kiszolgálót. Válassza az **SQL-adatbázisok** lehetőséget, és győződjön meg arról, hogy a 17 további adatbázist tartalmazó köteg már szerepel a listában.

   ![Adatbázisok listája](./media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Egyéb kiépítési minták

Az oktatóanyagban nem szereplő egyéb kiépítési minták:

**Adatbázisok előzetes kiépítése**: az előzetes kiépítési minta kihasználja azt a tényt, hogy a rugalmas készletben lévő adatbázisok nem vesznek fel extra költségeket. A számlázás a rugalmas készletre vonatkozik, nem az adatbázisokra. Az üresjáratban lévő adatbázisok nem használnak erőforrást. Ha az adatbázisokat előzetesen kiépíti egy készletbe, és szükség esetén lefoglalja őket, csökkentheti a bérlők hozzáadásának idejét. Az előre kiépített adatbázisok száma szükség szerint módosítható a várható kiépítési sebességhez megfelelő puffer megőrzése érdekében.

**Automatikus kiépítés**: az automatikus kiépítési mintában a létesítési szolgáltatás szükség szerint automatikusan kiépíti a kiszolgálókat, készleteket és adatbázisokat. Ha szeretné, a rugalmas készletekben is megadhatja az előzetesen kiépíthető adatbázisokat. Ha az adatbázisok le vannak szerelve és törlődnek, a kiépítési szolgáltatás kitöltheti a rugalmas készletekben lévő hézagokat. Ilyen szolgáltatás lehet egyszerű vagy összetett, például a több földrajzi helyről történő kiépítés és a földrajzi replikálás beállítása a vész-helyreállításhoz.

Az automatikus kiépítési mintával egy ügyfélalkalmazás vagy parancsfájl kiépítési kérelmet küld egy várólistára, amelyet a kiépítési szolgáltatás feldolgoz. Ezután lekérdezi a szolgáltatást a Befejezés megállapításához. Ha az előzetes kiépítés használatban van, a rendszer gyorsan kezeli a kérelmeket. A szolgáltatás kiépít egy helyettesítő adatbázist a háttérben.


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Egyetlen új bérlő kiépítése.
> * További bérlők kötegének kiépítése.
> * Lépjen be a bérlők kiépítés részleteibe, és regisztrálja őket a katalógusba.

Próbálja ki a [Teljesítményfigyelés oktatóanyagot](./saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>További források

* További [oktatóanyagok, amelyek a Wingtip tickets SaaS-adatbázis/bérlői alkalmazásra épülnek](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastic Database-ügyfélkódtár](elastic-database-client-library.md)
* [Parancsfájlok hibakeresése a Windows PowerShell integrált parancsprogram-kezelési környezetban](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)