---
title: Kiépítés a SaaS több-bérlőben
description: Ismerje meg, hogyan lehet új bérlőket kiépíteni és katalogizálni egy Azure SQL Database több-bérlős SaaS-alkalmazásban
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: eddb0c8339069025f0742e9bcbc371efbef094ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793330"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Új bérlők kiépítése és katalógusa egy SaaS-alkalmazásban többvállalatos több-bérlős Azure SQL Database használatával
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk az új bérlők kiépítése és katalogizálása, egy *több-bérlős szegmensben lévő adatbázis* -modell vagy-minta alapján történik.

Ez a cikk két fő részből áll:

- Az új bérlők kiépítési és katalogizálása [fogalmi vitát](#goto_2_conceptual) .

- [Oktatóanyag](#goto_1_tutorial) , amely kiemeli a kiépítés és a katalogizálás által megvalósított PowerShell-szkript kódját.
  - Az oktatóanyag a Wingtip tickets SaaS-alkalmazást használja, amelyet a több-bérlős szegmensben lévő adatbázis mintához alakítottak ki.

<a name="goto_2_conceptual"></a>

## <a name="database-pattern"></a>Adatbázis mintája

Ez a szakasz néhány továbbiat is követ, és megvitatja a több-bérlős többplatformos adatbázis-minta fogalmait.

Ebben a több-bérlős tagolt modellben az egyes adatbázisokban lévő tábla sémái tartalmazzák a bérlői adattárakat tároló táblák elsődleges kulcsában található bérlői kulcsot. A bérlői kulcs lehetővé teszi, hogy az egyes adatbázisok 0, 1 vagy több bérlőt tároljanak. A többszintű adatbázisok használata megkönnyíti az alkalmazásrendszer számára a nagy számú bérlő támogatását. Az egyik bérlő összes adathalmazát egyetlen adatbázisban tárolja a rendszer. A bérlők nagy száma a több szegmensben lévő adatbázis között oszlik meg. A katalógus-adatbázis tárolja az egyes bérlők hozzárendelését az adatbázisához.

#### <a name="isolation-versus-lower-cost"></a>Elkülönítés és alacsonyabb díj

Egy olyan bérlő, amely az adatbázissal rendelkezik, az elkülönítés előnyeit élvezi. A bérlő az adatbázist egy korábbi dátumra állíthatja vissza anélkül, hogy a többi bérlőre hatással lenne. Az adatbázis teljesítménye úgy állítható be, hogy optimalizálja az egyik bérlőt, és ne veszélyeztesse más bérlőket. A probléma az, hogy az elkülönítés több költséggel jár, mint az adatbázis más Bérlővel való megosztása.

Egy új bérlő kiosztásakor megoszthat egy adatbázist más Bérlővel, vagy elhelyezheti a saját új adatbázisában is. Később megváltoztathatja az elméjét, és áthelyezheti az adatbázist a másik helyzetbe.

A több Bérlővel és önálló Bérlővel rendelkező adatbázisok ugyanabban az SaaS-alkalmazásban vannak összekeverve, hogy optimalizálják az egyes bérlők költségeit vagy elkülönítését.

   ![Több-bérlős adatbázis-alkalmazás a bérlői katalógussal](./media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Bérlői katalógus mintája

Ha két vagy több olyan adatbázisa van, amelyek mindegyike legalább egy bérlőt tartalmaz, az alkalmazásnak meg kell tudnia találni, hogy melyik adatbázis tárolja az aktuális érdeklődésre számot tartó bérlőt. A katalógus-adatbázis tárolja ezt a leképezést.

#### <a name="tenant-key"></a>Bérlői kulcs

Az egyes bérlők esetében a Wingtip alkalmazás egy egyedi kulcsot származtathat, amely a bérlői kulcs. Az alkalmazás kibontja a bérlő nevét a weblap URL-címéből. Az alkalmazás kivonata a nevet a kulcs beszerzéséhez. Az alkalmazás a kulcsot használja a katalógus eléréséhez. A katalógus kereszthivatkozások információi a bérlőt tároló adatbázisról. Az alkalmazás az adatbázis adatait használja a kapcsolódáshoz. Más bérlői kulcsokra vonatkozó sémák is használhatók.

A katalógus használata lehetővé teszi, hogy a bérlői adatbázis neve vagy helye a kiépítés után megváltozzon az alkalmazás megszakítása nélkül. Egy több-bérlős adatbázis-modellben a katalógus a bérlő adatbázisok közötti áthelyezését is tartalmazza.

#### <a name="tenant-metadata-beyond-location"></a>Bérlői metaadatok a helyszínen kívül

A katalógus azt is jelezheti, hogy a bérlő offline állapotban van-e a karbantartáshoz vagy más műveletekhez. A katalógus pedig kiterjeszthető további bérlői vagy adatbázis-metaadatok tárolására, például a következő elemekre:
- Egy adatbázis szolgáltatási szintje vagy kiadása.
- Az adatbázis-séma verziója.
- A bérlő neve és az SLA-ja (szolgáltatói szerződés).
- Az alkalmazások felügyeletét, az ügyfélszolgálatot vagy a devops folyamatokat lehetővé tevő információk.

A katalógus használható a több-bérlős jelentéskészítés, a séma-kezelés és az Adatkivonatok elemzés céljából történő engedélyezésére is.

### <a name="elastic-database-client-library"></a>Elastic Database ügyféloldali kódtár

A Wingtip-ben a katalógus a *tenantcatalog* -adatbázisban lett implementálva. A *tenantcatalog* az [Elastic Database ÜGYFÉLOLDALI kódtár (EDCL)](elastic-database-client-library.md)szegmens felügyeleti funkciói segítségével hozható létre. A függvénytár lehetővé teszi, hogy az alkalmazás egy adatbázisban tárolt szegmensi *térképet* hozzon létre, kezelje és használja. A szegmensek közötti Térkép keresztezi a bérlői kulcsot a szegmensével, ami azt jelenti, hogy a szilánkokra osztott adatbázis van.

A bérlői kiépítés során a EDCL függvények alkalmazásokból vagy PowerShell-parancsfájlokból is felhasználhatók a szegmensek közötti Térkép bejegyzéseinek létrehozásához. A EDCL függvények később is használhatók a megfelelő adatbázishoz való kapcsolódáshoz. A EDCL gyorsítótárazza a kapcsolati adatokat, hogy csökkentse a katalógus-adatbázis forgalmát, és gyorsítsa fel a csatlakozás folyamatát.

> [!IMPORTANT]
> Ne *szerkessze* a katalógus-adatbázisban lévő adatszerkesztést a közvetlen hozzáférés használatával! A közvetlen frissítések nem támogatottak az adatsérülés magas kockázata miatt. Ehelyett csak a EDCL API-k használatával szerkessze a megfeleltetési adataikat.

## <a name="tenant-provisioning-pattern"></a>Bérlői kiépítési minta

#### <a name="checklist"></a>Ellenőrzőlista

Ha új bérlőt szeretne kiépíteni egy meglévő megosztott adatbázisba, a megosztott adatbázisnak a következő kérdéseket kell megtennie:
- Van elég hely az új bérlő számára?
- Rendelkezik olyan táblákkal, amelyek az új bérlőhöz szükséges referenciákkal rendelkeznek, vagy hozzáadhatók az adatsorok?
- Rendelkezik az új bérlő alapsémájának megfelelő változatával?
- Az új bérlőhöz közel található a megfelelő földrajzi helyen?
- Az új bérlő a megfelelő szolgáltatási szinten van?

Ha azt szeretné, hogy az új bérlő a saját adatbázisában is el legyen különítve, akkor a bérlő előírásai alapján hozhatja létre.

A kiépítés befejezése után regisztrálnia kell a bérlőt a katalógusban. Végezetül a bérlő leképezése is felvehető a megfelelő szegmensre való hivatkozáshoz.

#### <a name="template-database"></a>Sablon adatbázisa

Az adatbázis kiépítése SQL-parancsfájlok végrehajtásával, bacpac üzembe helyezésével vagy sablon-adatbázis másolásával. A Wingtip-alkalmazások új bérlői adatbázisok létrehozásához másolnak egy sablon-adatbázist.

Bármely alkalmazáshoz hasonlóan a Wingtip is fejlődik az idő múlásával. Időnként a Wingtip módosítania kell az adatbázist. A módosítások a következő elemeket tartalmazhatják:
- Új vagy módosított séma.
- Új vagy módosított hivatkozási érték.
- A rutin adatbázis-karbantartási feladatai biztosítják az alkalmazások optimális teljesítményét.

A SaaS-alkalmazásokkal ezeket a módosításokat koordinált módon kell üzembe helyezni valószínűleg nagyszámú bérlői adatbázison. Ahhoz, hogy ezek a változások a jövőbeli bérlői adatbázisokban legyenek, be kell építeni őket a létesítési folyamatba. Ezt a kihívást tovább vizsgálja a [séma kezelése oktatóanyagban](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Parancsfájlok

Ebben az oktatóanyagban a bérlői kiépítési szkriptek az alábbi forgatókönyveket támogatják:
- Bérlő kiépítés egy meglévő, más bérlők által megosztott adatbázisba.
- Bérlő kiépítés a saját adatbázisába.

A bérlői adatai ezután inicializálva és regisztrálva vannak a katalógus szegmensének térképén. A minta alkalmazásban a több bérlőt tartalmazó adatbázisok általános nevet kapnak, például *tenants1* vagy *tenants2*. Az egyetlen bérlőt tartalmazó adatbázisok a bérlő nevét kapják meg. A mintában használt egyedi elnevezési konvenciók nem kritikus részét képezik a mintának, mivel a katalógus használata lehetővé teszi a nevek hozzárendelését az adatbázishoz.

<a name="goto_1_tutorial"></a>

## <a name="tutorial-begins"></a>Az oktatóanyag kezdete

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Bérlő kiépítése több-bérlős adatbázisba
> * Bérlő kiépítése egyetlen bérlős adatbázisba
> * Bérlők kötegének kiépítése több-bérlős és egybérlős adatbázisokba
> * Adatbázis és bérlői leképezés regisztrálása egy katalógusban

#### <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

- Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](/powershell/azure/get-started-azureps)

- A Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás telepítve van. Ha kevesebb mint öt perc alatt kíván üzembe helyezni, tekintse meg [a Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás üzembe helyezése és megismerése](./saas-multitenantdb-get-started-deploy.md) című részt

- A Wingtip parancsfájljainak és forráskódjának beolvasása:
    - A Wingtip jegyek SaaS több-bérlős adatbázis-parancsfájljai és az alkalmazás forráskódja a [WingtipTicketsSaaS-MultitenantDB GitHub-](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) tárházban érhető el.
    - A Wingtip-parancsfájlok letöltéséhez és feloldásához szükséges lépéseket lásd: [Általános útmutatás](saas-tenancy-wingtip-app-guidance-tips.md) .

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Bérlő kiépítése más bérlők által *megosztott* adatbázisba

Ebben a szakaszban a PowerShell-parancsfájlok által végzett kiépítés főbb műveleteinek listáját láthatja. Ezután használja a PowerShell ISE hibakeresőt a parancsfájlok lépéseinek megjelenítéséhez, hogy megtekintse a műveleteket a kódban.

#### <a name="major-actions-of-provisioning"></a>A kiépítés főbb műveletei

A következő a kiépítési munkafolyamat legfontosabb elemei:

- **Az új bérlői kulcs kiszámítása**: a rendszer egy kivonatoló függvényt használ a bérlői kulcs létrehozásához a bérlő nevéből.
- **Ellenőrizze, hogy a bérlői kulcs már létezik**-e: a katalógus be van jelölve, hogy a kulcs még ne legyen regisztrálva.
- **Bérlő inicializálása az alapértelmezett bérlői adatbázisban**: a bérlői adatbázis frissítve lett az új bérlői adatok hozzáadására.
- **Bérlő regisztrálása a katalógusban**: az új bérlői kulcs és a meglévő tenants1-adatbázis közötti leképezés hozzá lesz adva a katalógushoz.
- **Adja hozzá a bérlő nevét egy katalógus-kiterjesztési táblához**: a rendszer hozzáadja a helyszín nevét a katalógus bérlők táblájához.  Ez a Hozzáadás azt mutatja be, hogyan terjeszthető ki a katalógus-adatbázis további alkalmazásspecifikus adatok támogatásához.
- **Nyissa meg az Events (események) lapot az új bérlő számára**: megnyílik a *Bushwillow blues* Events oldal a böngészőben.

   ![Képernyőkép, amely az új bérlő eseményeinek oldalát jeleníti meg.](./media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Hibakereső lépések

Annak megismeréséhez, hogy a Wingtip alkalmazás hogyan valósítja meg az új bérlők üzembe helyezését egy megosztott adatbázisban, adjon hozzá egy töréspontot és egy lépést a munkafolyamaton keresztül:

1. A *POWERSHELL ISE*-ben nyissa meg a... \\ A Learning \\ -modulok ProvisionTenants \\ *Demo-ProvisionTenants.ps1* , és a következő paramétereket kell megadni:
   - **$TenantName**  =  **Bushwillow blues**, egy új helyszín neve.
   - **$VenueType**  =  **blues**, az egyik előre definiált helyszín típusa: blues, ClassicalMusic, Dance, jazz, judo, Motorsport, többcélú, Opera, rockzene, Soccer (kisbetűs, nincs szóköz).
   - **$DemoScenario**  =  **1**. a bérlők kiépítése egy megosztott adatbázisban más bérlők használatával.

2. Adjon hozzá egy töréspontot úgy, hogy a kurzort bárhová helyezi a 38. sorban, a *New-bérlőt* tartalmazó sorban, majd nyomja le az **F9** billentyűt.

   ![Képernyőkép, amely kiemeli az új bérlőt tartalmazó sort.](./media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Futtassa a szkriptet az **F5** billentyű lenyomásával.

4. Miután a szkript végrehajtása leáll a törésponton, nyomja le az **F11** billentyűt a kód beléptetéséhez.

   ![A képernyőképen a hibakeresési menüt tartalmazó Windows PowerShell integrált parancsprogram-kezelési környezet látható, majd válassza ki a kiválasztott elemet.](./media/saas-multitenantdb-provision-and-catalog/debug.png)

5. A szkript végrehajtásának nyomon követéséhez használja a **Debug** menüpontot, az **F10** és az **F11** billentyűt a függvények meghívásához.

A PowerShell-parancsfájlok hibakeresésével kapcsolatos további információkért lásd: [Tippek a PowerShell-parancsfájlok használatához és hibakereséséhez](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Bérlő kiépítése a *saját* adatbázisában

#### <a name="major-actions-of-provisioning"></a>A kiépítés főbb műveletei

Az alábbiakban a parancsfájl nyomkövetésének lépésein áthaladó munkafolyamat legfontosabb elemei láthatók:

- **Az új bérlői kulcs kiszámítása**: a rendszer egy kivonatoló függvényt használ a bérlői kulcs létrehozásához a bérlő nevéből.
- **Ellenőrizze, hogy a bérlői kulcs már létezik**-e: a katalógus be van jelölve, hogy a kulcs még ne legyen regisztrálva.
- **Új bérlői adatbázis létrehozása**: az adatbázist a *Basetenantdb* -adatbázis Resource Manager-sablonnal történő másolásával hozza létre a rendszer.  Az új adatbázis neve a bérlő neve alapján történik.
- **Adatbázis hozzáadása a katalógushoz**: az új bérlői adatbázis a katalógusban szegmensként van regisztrálva.
- **Bérlő inicializálása az alapértelmezett bérlői adatbázisban**: a bérlői adatbázis frissítve lett az új bérlői adatok hozzáadására.
- **Bérlő regisztrálása a katalógusban**: az új bérlői kulcs és a *sequoiasoccer* -adatbázis közötti leképezés hozzá lesz adva a katalógushoz.
- A **bérlő neve hozzá van adva a katalógushoz**: a rendszer hozzáadja a helyszín nevét a katalógus bérlői bővítmény táblájához.
- **Nyissa meg az Events (események) lapot az új bérlő számára**: a *Sequoia Soccer* Events oldal megnyílik a böngészőben.

   ![események](./media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Hibakereső lépések

Most járjon végig a parancsfájlon, amikor a bérlőt a saját adatbázisában hozza létre:

1. Továbbra is.. \\ . A tanulási modulok \\ ProvisionTenants \\ *Demo-ProvisionTenants.ps1* a következő paramétereket adja meg:
   - **$TenantName**  =  **Sequoia Soccer**, egy új helyszín neve.
   - **$VenueType**  =  **futball**, az egyik előre definiált helyszín típusa: blues, ClassicalMusic, Dance, jazz, judo, Motorsport, többcélú, Opera, rockzene, Soccer (kisbetű, nem szóköz).
   - **$DemoScenario**  =  **2**. a bérlő kiépítése a saját adatbázisba.

2. Vegyen fel egy új töréspontot úgy, hogy a kurzort bárhová helyezi a 57. sorban, a következő sorral: *& &nbsp; $PSScriptRoot \new-tenantanddatabase '*, és nyomja le az **F9** billentyűt.

   ![töréspont](./media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Futtassa a szkriptet az **F5** billentyű lenyomásával.

4. Miután a szkript végrehajtása leáll a törésponton, nyomja le az **F11** billentyűt a kód beírásához.  A végrehajtás nyomon követéséhez használja az **F10** és az **F11** billentyűt, és lépjen be a függvényekbe.

## <a name="provision-a-batch-of-tenants"></a>Bérlők kötegének kiépítése

Ez a gyakorlat 17 bérlős köteget foglal le. Javasoljuk, hogy a bérlők számára kiépítse ezt a köteget, mielőtt más Wingtip-jegyeket is megkezdenén, hogy több adatbázis működjön együtt a szolgáltatással.

1. A *POWERSHELL ISE*-ben nyissa meg a... \\ A Learning \\ -modulok ProvisionTenants \\ *Demo-ProvisionTenants.ps1* , és a *$DemoScenario* paramétert a 4 értékre kell módosítani:
   - **$DemoScenario**  =  **4**. a bérlők kötegének kiépítése egy megosztott adatbázisba.

2. Nyomja le az **F5** billentyűt, és futtassa a szkriptet.

### <a name="verify-the-deployed-set-of-tenants"></a>Bérlők telepített készletének ellenőrzése

Ebben a szakaszban egy megosztott adatbázisban üzembe helyezett és a saját adatbázisaiba helyezett bérlők együttese szerepel. A Azure Portal a létrehozott adatbázisok vizsgálatára is használható. A [Azure Portal](https://portal.azure.com)nyissa meg a **tenants1-MT- \<USER\>** Servert az SQL-kiszolgálók listájának tallózásával.  Az **SQL-adatbázisok** listájának tartalmaznia kell a megosztott **tenants1** -adatbázist és a saját adatbázisában lévő bérlők adatbázisait:

   ![adatbázislista](./media/saas-multitenantdb-provision-and-catalog/Databases.png)

Míg a Azure Portal megjeleníti a bérlői adatbázisokat, nem teszi lehetővé a bérlők *megtekintését a megosztott* adatbázisban. A bérlők teljes listáját megtekintheti a Wingtip **Events hub** weboldalán, valamint a katalógus tallózásával is.

#### <a name="using-wingtip-tickets-events-hub-page"></a>A Wingtip tickets Events hub lapja

Nyissa meg az Events hub lapot a böngészőben (http: Events \<USER\> . Wingtip-Mt.. trafficmanager.net)

#### <a name="using-catalog-database"></a>A katalógus-adatbázis használata

A bérlők és az azokhoz tartozó adatbázisok teljes listája elérhető a katalógusban. A rendszer egy SQL-nézetet biztosít, amely összekapcsolja a bérlő nevét az adatbázis nevével. A nézet szépen mutatja be a katalógusban tárolt metaadatok kiterjesztésének értékét.
- Az SQL-nézet a tenantcatalog-adatbázisban érhető el.
- A bérlő nevét a bérlők táblában tárolja a rendszer.
- Az adatbázis nevét a rendszer a szegmens felügyeleti táblázatokban tárolja.

1. A SQL Server Management Studio (SSMS) alkalmazásban kapcsolódjon a bérlők kiszolgálójához a **Catalog-Mt. \<USER\> címen. database.windows.net**, bejelentkezés = **Developer** és Password = **P \@ ssword1**

    ![SSMS-kapcsolatok párbeszédpanel](./media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. A SSMS Object Explorer tallózással keresse meg a nézeteket a *tenantcatalog* adatbázisban.

3. Kattintson a jobb gombbal a nézet *TenantsExtended* , és válassza a **legfelső 1000-sorok kiválasztása** lehetőséget. Jegyezze fel a bérlő neve és az adatbázis közötti leképezést a különböző bérlők számára.

    ![ExtendedTenants nézet a SSMS](./media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)

## <a name="other-provisioning-patterns"></a>Egyéb kiépítési minták

Ez a szakasz további érdekes kiépítési mintákat tárgyal.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Adatbázisok előzetes kiépítés rugalmas készletekben

Az előzetes kiépítési minta azt a tényt használja ki, hogy rugalmas készletek használata esetén a számlázás nem az adatbázisok készlete. Így az adatbázisok további költségek nélkül is hozzáadhatók egy rugalmas készlethez. Ez az előzetes üzembe helyezés jelentősen csökkenti a bérlők adatbázisba való kiépítéséhez szükséges időt. Az előre kiépített adatbázisok száma szükség szerint módosítható a várható kiépítési sebességhez megfelelő puffer megőrzése érdekében.

#### <a name="auto-provisioning"></a>Automatikus üzembe helyezés

Az automatikus kiépítési mintában a dedikált kiépítési szolgáltatás a kiszolgálók, készletek és adatbázisok igény szerinti automatikus kiosztására szolgál. Ez az automatizálás magában foglalja a rugalmas készletekben lévő adatbázisok előzetes kiépítési folyamatát. Ha az adatbázisok leszerelése és törlése is megtörtént, a rugalmas készletekben létrehozott hézagokat a kiépítési szolgáltatás a kívánt módon kitöltheti.

Az ilyen típusú automatizált szolgáltatás egyszerű vagy összetett lehet. Előfordulhat például, hogy az automatizálás több földrajzi régión belül képes kiépíteni az üzembe helyezést, és képes a földrajzi replikálást beállítani a vész-helyreállításhoz. Az automatikus kiépítési mintával egy ügyfélalkalmazás vagy parancsfájl kiépítési kérelmet küld egy várólistába, amelyet egy kiépítési szolgáltatás feldolgoz. A szkript ezután lekérdezi a befejezést. Ha az előzetes kiépítés használatos, a rendszer gyorsan kezeli a kérelmeket, míg a háttérben futó szolgáltatás felügyeli a helyettesítő adatbázis kiépítési folyamatát.

## <a name="additional-resources"></a>További források

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Elastic Database-ügyfélkódtár](elastic-database-client-library.md)
- [Parancsfájlok hibakeresése Windows PowerShell integrált parancsprogram-kezelési környezet](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Egyetlen új bérlő kiépítése egy megosztott, több-bérlős adatbázisba és saját adatbázisba
> * További bérlők kötegelt kiépítése
> * A bérlők kiépítési és a katalógusba való regisztrálásának részletei

Próbálja ki a [Teljesítményfigyelés oktatóanyagot](./saas-multitenantdb-performance-monitoring.md).