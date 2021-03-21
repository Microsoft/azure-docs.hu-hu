---
title: A rugalmas lekérdezések áttekintése
description: A rugalmas lekérdezés lehetővé teszi a több adatbázisra kiterjedő Transact-SQL-lekérdezés futtatását.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 12/05/2019
ms.openlocfilehash: cac17bbac96d44d8d9bfce2e168de4ea6d4c5c08
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100364953"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Azure SQL Database rugalmas lekérdezés áttekintése (előzetes verzió)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A rugalmas lekérdezési funkció (előzetes verzió) lehetővé teszi, hogy több adatbázisra kiterjedő Transact-SQL-lekérdezést futtasson az Azure SQL Database-ben. Lehetővé teszi a távoli táblák elérését, valamint a Microsoft és harmadik féltől származó eszközök (Excel, Power BI, tabló stb.) összekapcsolását a több adatbázissal rendelkező adatrétegek lekérdezéséhez. Ezzel a funkcióval nagy adatrétegekre terjesztheti ki a lekérdezéseket, és üzletiintelligencia- (BI-) jelentésekben jelenítheti meg az eredményeket.

## <a name="why-use-elastic-queries"></a>Miért érdemes rugalmas lekérdezéseket használni

### <a name="azure-sql-database"></a>Azure SQL Database

Az adatbázisokban való lekérdezés Azure SQL Database teljes egészében T-SQL-ben. Ez lehetővé teszi a távoli adatbázisok írásvédett lekérdezését, és lehetőséget biztosít a jelenlegi SQL Server ügyfelek számára, hogy az alkalmazásokat három és négy részből álló névvel vagy csatolt kiszolgálóval áttelepítse SQL Databasere.

### <a name="available-on-standard-tier"></a>Elérhető a standard szinten

A rugalmas lekérdezés a standard és a prémium szintű szolgáltatási szinten is támogatott. Az alacsonyabb szintű szolgáltatási szintek teljesítményére vonatkozó korlátozásokkal kapcsolatban lásd az előzetes verzió korlátozásait ismertető szakaszt.

### <a name="push-parameters-to-remote-databases"></a>Paraméterek leküldése a távoli adatbázisokra

A rugalmas lekérdezések mostantól leküldhetik az SQL-paramétereket a távoli adatbázisoknak végrehajtás céljából.

### <a name="stored-procedure-execution"></a>Tárolt eljárás végrehajtása

Távoli tárolt eljárási hívások vagy távoli függvények végrehajtása az [SP \_ Execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database)paranccsal.

### <a name="flexibility"></a>Rugalmasság

A rugalmas lekérdezéssel rendelkező külső táblák más sémával vagy táblázattal rendelkező távoli táblákra is vonatkozhatnak.

## <a name="elastic-query-scenarios"></a>Rugalmas lekérdezési forgatókönyvek

A cél az olyan forgatókönyvek lekérdezése, amelyekben egyszerre több adatbázis is hozzájárul a sorok egyetlen átfogó eredményhez való bevezetéséhez. A lekérdezés vagy a felhasználó vagy az alkalmazás közvetlenül, vagy közvetve az adatbázishoz csatlakozó eszközökön keresztül végezhető el. Ez különösen akkor hasznos, ha jelentéseket hoz létre a kereskedelmi BI-vagy adatintegrációs eszközökkel, vagy bármely olyan alkalmazást, amely nem módosítható. A rugalmas lekérdezéssel több adatbázisra is lekérdezheti az ismerős SQL Server kapcsolódási élményt az olyan eszközökön, mint az Excel, a Power BI, a tabló vagy a Cognos.
A rugalmas lekérdezés lehetővé teszi, hogy a SQL Server Management Studio vagy a Visual Studio által kiadott lekérdezések révén egyszerű hozzáférést biztosítson az adatbázisok teljes gyűjteményéhez, és megkönnyíti a Entity Framework vagy más ORM-környezetek adatbázisok közötti lekérdezését. Az 1. ábra egy olyan forgatókönyvet mutat be, amelyben egy meglévő Felhőbeli alkalmazás (amely a [rugalmas adatbázis-ügyfél függvénytárát](elastic-database-client-library.md)használja) egy kibővített adatrétegre épül, és egy rugalmas lekérdezést használ az adatbázisok közötti jelentéskészítéshez.

**1. ábra** Kibővített adatrétegen használt rugalmas lekérdezés

![Kibővített adatrétegen használt rugalmas lekérdezés][1]

A rugalmas lekérdezés felhasználói forgatókönyveit a következő topológiák jellemzik:

* **Vertikális particionálás – adatbázis-** összevont lekérdezések (1. topológia): az adatrétegben lévő adatbázisok között vertikálisan particionálja az adatmennyiséget. A táblák különböző készletei általában különböző adatbázisokban találhatók. Ez azt jelenti, hogy a séma eltér a különböző adatbázisokon. Például a leltárhoz tartozó összes tábla egy adatbázison található, míg az összes nyilvántartással kapcsolatos tábla egy második adatbázisban található. Az ehhez a topológiához tartozó gyakori használati esetekben egy-egy lekérdezésre van szükség a különböző adatbázisok tábláiban, illetve a jelentések fordításához.
* **Vízszintes particionálás – horizontális** felskálázás (2. topológia): az adatmennyiséget vízszintesen particionálja a rendszer a sorok elosztására egy horizontálisan elosztott adatrétegben. Ezzel a módszerrel a séma megegyezik az összes részt vevő adatbázison. Ezt a megközelítést "horizontális felskálázásnak" is nevezzük. A horizontális skálázás végezhető el és kezelhető (1) a rugalmas adatbázis-eszközök kódtárai vagy (2) saját horizontálisan. Rugalmas lekérdezéssel több szegmens között lehet lekérdezni vagy lefordítani a jelentéseket. A szegmensek általában egy rugalmas készleten belüli adatbázisok. A rugalmas lekérdezéseket úgy tekintheti meg, hogy a rugalmas készlet összes adatbázisát egyszerre kérdezi le, feltéve, hogy az adatbázisok közös sémával rendelkeznek.

> [!NOTE]
> A rugalmas lekérdezés olyan jelentéskészítési forgatókönyvekhez használható, amelyekben a feldolgozás (szűrés, Összesítés) a külső forrás oldalán végezhető el. Az ETL-műveletek nem alkalmasak arra, hogy a rendszer nagy mennyiségű adatátvitelt továbbítson a távoli adatbázis (ok) ból. A nagy mennyiségű jelentési feladathoz vagy az összetettebb lekérdezésekkel rendelkező adattárház-forgatókönyvekhez is érdemes az [Azure szinapszis Analytics](https://azure.microsoft.com/services/synapse-analytics)használatát használni.
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Vertikális particionálás – adatbázisok közötti lekérdezések

A kódolás megkezdéséhez lásd: [Bevezetés az adatbázisok közötti lekérdezéssel (vertikális particionálás)](elastic-query-getting-started-vertical.md).

A rugalmas lekérdezés használatával a SQL Database adatbázisban található, SQL Database más adatbázisai számára elérhetővé teheti az adatforrásokat. Ez lehetővé teszi, hogy az egyik adatbázis lekérdezései a SQL Database-ban található bármely más távoli adatbázisban lévő táblákra hivatkozzon. Az első lépés egy külső adatforrás definiálása minden távoli adatbázishoz. A külső adatforrás a helyi adatbázisban van definiálva, amelyről hozzáférést szeretne adni a távoli adatbázisban található táblákhoz. Nincs szükség módosításra a távoli adatbázison. A tipikus vertikális particionálási forgatókönyvek esetében, ahol a különböző adatbázisok különböző sémákkal rendelkeznek, a rugalmas lekérdezések használhatók olyan gyakori használati esetek megvalósításához, mint például a hivatkozási adatokhoz való hozzáférés és az adatbázisok közötti lekérdezés.

> [!IMPORTANT]
> Rendelkeznie kell a külső adatforrásra vonatkozó engedélyekkel. Ez az engedély az ALTER DATABASE engedély részét képezi. Az alapul szolgáló adatforrásra való hivatkozáshoz minden külső ADATFORRÁS engedélyére van szükség.
>

**Hivatkozási érték**: a topológia a hivatkozási adatkezeléshez használatos. Az alábbi ábrán két táblázat (T1 és T2) található, és a rendszer egy dedikált adatbázisban tárolja a hivatkozási adatot. A rugalmas lekérdezés használatával mostantól a T1 és a T2 táblákat távolról is elérheti más adatbázisokból, ahogy az ábrán is látható. 1. topológia használata, ha a hivatkozási táblázatok kis-vagy távoli lekérdezések a hivatkozási táblázatba vannak szelektív predikátumok.

**2. ábra** Vertikális particionálás – a rugalmas lekérdezés használatával kérdezi le a hivatkozási adataikat

![Vertikális particionálás – a rugalmas lekérdezés használatával kérdezi le a hivatkozási adataikat][3]

**Adatbázisok közötti lekérdezés**: a rugalmas lekérdezések olyan használati eseteket tesznek lehetővé, amelyek a SQL Database több adatbázisában történő lekérdezését igénylik. A 3. ábra négy különböző adatbázist mutat be: CRM, leltár, HR és termékek. Az egyik adatbázisban végrehajtott lekérdezéseknek hozzá kell férniük egy vagy az összes többi adatbázishoz is. A rugalmas lekérdezés használatával az adatbázist úgy is konfigurálhatja, hogy a négy adatbázison néhány egyszerű DDL-utasítást futtat. Ezen egyszeri konfiguráció után a távoli táblához való hozzáférés olyan egyszerű, mintha a T-SQL-lekérdezésekből vagy a BI-eszközökből helyi táblára hivatkozik. Ez a módszer akkor javasolt, ha a távoli lekérdezések nem adnak vissza nagy eredményeket.

**3. ábra** Vertikális particionálás – rugalmas lekérdezés használata különböző adatbázisok lekérdezéséhez

![Vertikális particionálás – rugalmas lekérdezés használata különböző adatbázisok lekérdezéséhez][4]

A következő lépésekkel rugalmas adatbázis-lekérdezéseket konfigurálhat olyan vertikális particionálási forgatókönyvekhez, amelyek a SQL Database távoli adatbázisain található táblázathoz való hozzáférést igényelnek ugyanazzal a sémával:

* [Főkulcs Mymasterkey létrehozása](/sql/t-sql/statements/create-master-key-transact-sql)
* [Adatbázis-hatókörű hitelesítő adatok Mycredential létrehozása](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
* **RDBMS** típusú [külső adatforrás létrehozása/eldobása](/sql/t-sql/statements/create-external-data-source-transact-sql) mydatasource
* [Külső tábla Sajáttábla létrehozása/ELdobása](/sql/t-sql/statements/create-external-table-transact-sql)

A DDL-utasítások futtatása után a "sajáttábla" távoli táblát úgy érheti el, mintha egy helyi tábla lenne. Azure SQL Database automatikusan megnyit egy kapcsolódást a távoli adatbázishoz, feldolgozza a kérést a távoli adatbázison, és visszaadja az eredményeket.

## <a name="horizontal-partitioning---sharding"></a>Vízszintes particionálás – horizontális felskálázás

Ha rugalmas lekérdezést használ a jelentéskészítési feladatok elvégzésére egy lépcsőzetesen, azaz vízszintesen particionált, az adatréteghez [rugalmas adatbázis](elastic-scale-shard-map-management.md) -szegmenst kell rendelni, amely az adatréteg adatbázisainak ábrázolására szolgál. Ebben a forgatókönyvben jellemzően csak egyetlen szegmenses leképezést használunk, és a rugalmas lekérdezési képességekkel rendelkező dedikált adatbázisok (fő csomópont) a jelentéskészítési lekérdezések belépési pontként szolgálnak. Csak erre a dedikált adatbázisra van szükség a szegmenses Térkép eléréséhez. A 4. ábra ezt a topológiát és a hozzá tartozó konfigurációt mutatja be a rugalmas lekérdezési adatbázissal és a szegmenses leképezéssel. További információ a rugalmas adatbázis-ügyfél könyvtáráról és a szegmenses térképek létrehozásáról: a szegmenses [Térkép kezelése](elastic-scale-shard-map-management.md).

**4. ábra** Horizontális particionálás – a rugalmas lekérdezés használata többszintű adatrétegek jelentéskészítéséhez

![Horizontális particionálás – a rugalmas lekérdezés használata többszintű adatrétegek jelentéskészítéséhez][5]

> [!NOTE]
> A rugalmas lekérdezési adatbázis (fő csomópont) lehet különálló adatbázis, vagy lehet ugyanaz az adatbázis, amely a szegmens térképet tárolja.
> Bármilyen konfigurációt választhat, győződjön meg arról, hogy a szolgáltatási szint és az adatbázis számítási mérete elég magas ahhoz, hogy kezelni tudja a bejelentkezési/lekérdezési kérelmek várt mennyiségét.

A következő lépésekkel rugalmas adatbázis-lekérdezéseket konfigurálhat olyan horizontális particionálási forgatókönyvekhez, amelyek hozzáférést igényelnek a (jellemzően) több távoli adatbázis SQL Databaseban található táblához:

* [Főkulcs Mymasterkey létrehozása](/sql/t-sql/statements/create-master-key-transact-sql)
* [Adatbázis-hatókörű hitelesítő adatok Mycredential létrehozása](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
* Hozzon létre egy szegmenses [térképet](elastic-scale-shard-map-management.md) , amely az adatréteget jelképezi a rugalmas adatbázis ügyféloldali kódtár használatával.
* [Külső adatforrás létrehozása/ELdobása](/sql/t-sql/statements/create-external-data-source-transact-sql) **SHARD_MAP_MANAGER** típusú mydatasource
* [Külső tábla Sajáttábla létrehozása/ELdobása](/sql/t-sql/statements/create-external-table-transact-sql)

Miután végrehajtotta ezeket a lépéseket, elérheti a "sajáttábla" horizontálisan particionált táblát, mintha egy helyi tábla lenne. Azure SQL Database automatikusan több párhuzamos kapcsolatot nyit meg azokkal a távoli adatbázisokkal, ahol a táblák fizikailag vannak tárolva, feldolgozza a kéréseket a távoli adatbázisokon, és visszaadja az eredményeket.
A horizontális particionálási forgatókönyvhöz szükséges lépésekkel kapcsolatos további információkért tekintse meg a [rugalmas lekérdezés a horizontális particionáláshoz](elastic-query-horizontal-partitioning.md)című témakört.

A kódolás megkezdéséhez tekintse meg a [rugalmas lekérdezés a horizontális particionáláshoz](elastic-query-getting-started.md)való használatáról szóló témakört.

> [!IMPORTANT]
> A rugalmas lekérdezések nagy mennyiségű adatbázisban való sikeres végrehajtása nagymértékben támaszkodik az egyes adatbázisok rendelkezésre állására a lekérdezés végrehajtása során. Ha egy adatbázis nem érhető el, a teljes lekérdezés sikertelen lesz. Ha egyszerre több száz vagy több ezer adatbázis lekérdezését tervezi, győződjön meg arról, hogy az ügyfélalkalmazás újrapróbálkozott a logikai beágyazással, vagy vegye fontolóra [Elastic Database feladatok](./job-automation-overview.md) (előzetes verzió) kihasználása és az adatbázisok kisebb részhalmazának lekérdezése, az egyes lekérdezések eredményeinek összesítése egyetlen célhelyre.

## <a name="t-sql-querying"></a>T-SQL-lekérdezés

A külső adatforrások és a külső táblák meghatározása után rendszeres SQL Server kapcsolati karakterláncokat használhat a külső táblákat definiáló adatbázisokhoz való kapcsolódáshoz. Ezután futtathat T-SQL-utasításokat a külső táblákon ezen a kapcsolaton az alább ismertetett korlátozásokkal. További információkat és példákat a T-SQL-lekérdezésekről a [horizontális particionálással](elastic-query-horizontal-partitioning.md) és a [vertikális particionálással](elastic-query-vertical-partitioning.md)kapcsolatos dokumentációs témakörökben talál.

## <a name="connectivity-for-tools"></a>Eszközökhöz való kapcsolódás

Az alkalmazások és a BI-vagy adatintegrációs eszközök összekapcsolásához használhat normál SQL Server kapcsolati karakterláncokat külső táblákkal rendelkező adatbázisokhoz. Győződjön meg arról, hogy a SQL Server támogatott adatforrásként az eszköz számára. A csatlakozás után tekintse meg a rugalmas lekérdezési adatbázist és az adatbázisban található külső táblákat úgy, mint bármely más SQL Server-adatbázist, amelyhez az eszközzel csatlakozik.

> [!IMPORTANT]
> A rugalmas lekérdezésekkel Azure Active Directory használatával történő hitelesítés jelenleg nem támogatott.

## <a name="cost"></a>Költségek

A rugalmas lekérdezés a Azure SQL Database díja részét képezi. Vegye figyelembe, hogy azok a topológiák, amelyekben a távoli adatbázisok egy másik adatközpontban találhatók, mint a rugalmas lekérdezési végpont, de a távoli adatbázisokból kimenő adatokra rendszeresen az [Azure díjszabása](https://azure.microsoft.com/pricing/details/data-transfers/)vonatkozik.

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

* Az első rugalmas lekérdezés futtatása akár néhány percet is igénybe vehet a standard szintű szolgáltatási szinten. Ez az idő szükséges a rugalmas lekérdezési funkció betöltéséhez; a teljesítmény betöltése nagyobb a szolgáltatási szintek és a számítási méretek tekintetében.
* A külső adatforrások vagy a SSMS vagy SSDT külső tábláinak parancsfájlkezelése még nem támogatott.
* A SQL Database importálási/exportálási funkciói még nem támogatják a külső adatforrásokat és a külső táblákat. Ha importálás/exportálást kell használnia, dobja el ezeket az objektumokat az exportálás előtt, majd hozza létre újra az importálás után.
* A rugalmas lekérdezés jelenleg csak olvasási hozzáférést biztosít a külső táblákhoz. Használhatja azonban a teljes T-SQL-funkciót azon az adatbázison, ahol a külső tábla definiálva van. Ez hasznos lehet például az ideiglenes eredmények megtartása, például a <column_list> bejelölése <local_table>, vagy a tárolt eljárások definiálása a rugalmas lekérdezési adatbázison, amely külső táblákra hivatkozik.
* A nvarchar (max) kivételével a LOB-típusok (beleértve a térbeli típusokat is) nem támogatottak a külső táblák definíciójában. Megkerülő megoldásként létrehozhat egy nézetet a távoli adatbázison, amely a LOB-típust a nvarchar (max), az alaptábla helyett a nézetben definiálja a külső táblát, majd visszahelyezi azt az eredeti LOB-típusba a lekérdezésekben.
* Az eredményhalmaz nvarchar (max) adattípusának oszlopai letiltják a rugalmas lekérdezési implementációban használt speciális kötegelt feldolgozást, és befolyásolhatják a lekérdezés teljesítményét a nem kanonikus használati esetekben, ha nagy mennyiségű nem aggregált adat kerül át a lekérdezés eredményeképpen.
* A külső táblákon lévő oszlopok statisztikái jelenleg nem támogatottak. A tábla statisztikái támogatottak, de manuálisan kell létrehozni.
* A rugalmas lekérdezés csak Azure SQL Databaseekkel működik. SQL Server példány lekérdezéséhez nem használható.

## <a name="share-your-feedback"></a>Ossza meg velünk véleményét

Ossza meg velünk a tapasztalatait rugalmas lekérdezésekkel az alábbi MSDN-fórumokon, illetve a Stack Overflowon. A szolgáltatással kapcsolatos visszajelzések (hibák, durva élek, szolgáltatások hiányosságainak) iránt érdeklődünk.

## <a name="next-steps"></a>Következő lépések

* A vertikális particionálással kapcsolatos oktatóanyagért lásd: [Bevezetés az adatbázisok közötti lekérdezéssel (vertikális particionálás)](elastic-query-getting-started-vertical.md).
* A függőlegesen particionált információk szintaxisát és mintáit lásd: [függőlegesen particionált adatlekérdezés](elastic-query-vertical-partitioning.md)
* A horizontális particionálással (skálázással) kapcsolatos oktatóanyagért lásd: az [első lépések a rugalmas lekérdezéssel a horizontális particionáláshoz](elastic-query-getting-started.md).
* A horizontálisan particionált információk szintaxisát és mintáit lásd: [vízszintesen particionált adatlekérdezés](elastic-query-horizontal-partitioning.md)
* Lásd: az [SP \_ \_ távoli futtatása](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) olyan tárolt eljáráshoz, amely Transact-SQL-utasítást hajt végre egyetlen távoli Azure SQL Database vagy egy horizontális particionálási sémában szegmensként szolgáló adatbázis-készleten.

<!--Image references-->
[1]: ./media/elastic-query-overview/overview.png
[2]: ./media/elastic-query-overview/topology1.png
[3]: ./media/elastic-query-overview/vertpartrrefdata.png
[4]: ./media/elastic-query-overview/verticalpartitioning.png
[5]: ./media/elastic-query-overview/horizontalpartitioning.png

<!--anchors-->