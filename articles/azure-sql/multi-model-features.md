---
title: Többmodelles képességek
description: A Microsoft Azure SQL lehetővé teszi több adatmodell használatát ugyanabban az adatbázisban.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: b16a2fc9f107a8420fb7d05667807a869fa3e00a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172757"
---
# <a name="multi-model-capabilities-of-azure-sql-database--sql-managed-instance"></a>Azure SQL Database & SQL felügyelt példányának többmodelles képességei
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

A többmodelles adatbázisok lehetővé teszik több adatformátumban (például a kapcsolatok, a diagramok, a JSON/XML-dokumentumok, a kulcs-érték párok stb.) történő tárolását és feldolgozását.

## <a name="when-to-use-multi-model-capabilities"></a>Mikor kell használni a többmodelles funkciókat?

Az [Azure SQL termékcsaládba tartozó termékek](azure-sql-iaas-vs-paas-what-is-overview.md) úgy vannak kialakítva, hogy működjenek együtt a legjobb teljesítményt nyújtó, a legtöbb esetben számos általános célú alkalmazás esetében. Az Azure SQL termékcsaládba tartozó termékek azonban nem korlátozódnak kizárólag a kapcsolódó adatmennyiségre. Az Azure SQL termékcsalád termékei lehetővé teszik a különböző, a rokon modellbe szorosan integrált, nem rokon formátumok használatát.
A következő esetekben érdemes megfontolni az Azure SQL termékcsalád többmodelles funkcióinak használatát:

- Olyan információkkal vagy struktúrákkal rendelkezik, amelyek jobban illeszkednek a NoSQL-modellekhez, és nem szeretne külön NoSQL-adatbázist használni.
- Az adatai többsége megfelelő a rokon modellekhez, és az adatai egyes részeit NoSQL stílusban kell modellezni.
- Széles körű Transact-SQL nyelvet szeretne használni a NoSQL-és adatelemzési lekérdezések lekérdezéséhez és elemzéséhez, valamint az SQL nyelvet használó különféle eszközök és alkalmazások integrálásához.
- Olyan adatbázis-funkciókat kíván alkalmazni, mint [a memórián belüli technológiák](in-memory-oltp-overview.md) a NoSQL-adatstruktúrák elemzésének vagy feldolgozásának javítása érdekében, [tranzakciós replikálással](managed-instance/replication-transactional-overview.md) vagy [olvasható replikákkal](database/read-scale-out.md) hozzon létre másolatot az adatokról a másik helyen, és állítson be néhány analitikus számítási feladatot az elsődleges adatbázisból.

## <a name="overview"></a>Áttekintés

Az Azure SQL termékcsalád termékei a következő többmodelles funkciókat biztosítják:

- A [Graph-funkciók](#graph-features) lehetővé teszik, hogy az adatait csomópontok és élek szerint képviseljék, és a Graph-kezelővel bővített szabványos Transact-SQL-lekérdezéseket használja `MATCH` a Graph-adatlekérdezéshez.
- A [JSON-funkciók](#json-features) lehetővé teszik a JSON-dokumentumok táblázatokba való behelyezését, a kapcsolódó adattípusok a JSON-dokumentumokra való átalakítását és fordítva. A normál Transact-SQL nyelvet a JSON-függvények használatával a dokumentumok elemzéséhez használhatja, a nem fürtözött indexeket, a oszlopcentrikus indexeket és a memóriára optimalizált táblákat pedig a lekérdezések optimalizálására használhatja.
- A [térbeli funkciók](#spatial-features) lehetővé teszik a földrajzi és a geometriai adattárolást, a térbeli indexekkel való indexelést, valamint a térbeli lekérdezések használatával történő lekérését.
- Az [XML-funkciók](#xml-features) lehetővé teszik az XML-adatok tárolását és indexelését az adatbázisban, és natív XQuery/XPath műveleteket használhatnak az XML-adatokkal való munkavégzéshez. Az Azure SQL termékcsaládba tartozó termékek speciális beépített XML-lekérdezési motorral rendelkeznek, amely XML-adatfeldolgozást végez.
- A [kulcs-érték párok](#key-value-pairs) nincsenek kifejezetten támogatva speciális funkciókként, mivel a kulcs-érték párok natív módon, kétoszlopos táblákként vannak modellezve.

  > [!Note]
  > Ugyanazzal a Transact-SQL-lekérdezéssel a JSON Path kifejezés, a XQuery/XPath kifejezések, a térbeli függvények és a Graph-lekérdezés kifejezéseket használhatja az adatbázisban tárolt adatok eléréséhez. Emellett a Transact-SQL-lekérdezéseket végrehajtó bármely eszköz vagy programozási nyelv is használhatja a lekérdezési felületet a többmodelles adattípusokhoz való hozzáféréshez. Ez a különbség a többmodelles adatbázisok, például a különböző adatmodellek speciális API-ját biztosító [Azure Cosmos db](../cosmos-db/index.yml) összehasonlítva.

A következő szakaszokban megismerheti az Azure SQL termékcsalád termékeinek legfontosabb többmodelles funkcióit.

## <a name="graph-features"></a>Gráffunkciók

Az Azure SQL termékcsalád termékei több-a-többhöz relációk modellezéséhez biztosítanak gráf-adatbázis-funkciókat. A gráfok csomópontok (vagy csúcspontok) és élek (vagy kapcsolatok) gyűjteményei. A csomópontok egy entitást (például egy személyt vagy szervezetet) jelölnek, és az Edge az általa összekapcsolt két csomópont (például a likes vagy a barátok) közötti kapcsolatot jelöli. Íme néhány olyan funkció, amely egyedi gráf-adatbázist tesz elérhetővé:

- Az élek vagy a kapcsolatok az első osztályú entitások egy gráf-adatbázisban, és rendelkezhetnek hozzájuk társított attribútumokkal vagy tulajdonságokkal.
- Egyetlen Edge rugalmasan tud csatlakoztatni több csomópontot egy gráf-adatbázisban.
- Egyszerűen kipróbálhatja a minta egyeztetését és a több ugrásos navigációs lekérdezéseket.
- A tranzitív lezáró és a polimorf lekérdezéseket egyszerűen kipróbálhatja.

A [Graph-kapcsolatok és a Graph lekérdezési képességek](/sql/relational-databases/graphs/sql-graph-overview) integrálva vannak a Transact-SQL szolgáltatásba, és a SQL Server adatbázismotor használatának előnyeit az alapszintű adatbázis-kezelő rendszerként használják.

### <a name="when-to-use-a-graph-capability"></a>Mikor használjon gráf-képességet

A Graph-adatbázis nem érhető el, mert nem érhető el a viszonyítási adatbázis használatával. A Graph-adatbázisok azonban egyszerűbbé tehetik bizonyos lekérdezések kifejtését. A másik lehetőség közül választhat a következő tényezők alapján:

- Olyan hierarchikus adat modellezése, amelyben egy csomópont több szülővel rendelkezhet, így a HierarchyId nem használható
- A modellben az alkalmazás komplex több-a-többhöz kapcsolatot tartalmaz; az alkalmazás fejlődése során új kapcsolatok lesznek hozzáadva.
- Össze kell elemezni a kapcsolattal összekapcsolt adatkapcsolatokat és kapcsolatokat.

## <a name="json-features"></a>JSON-funkciók

Az Azure SQL termékcsalád tagjai lehetővé teszik JavaScript Object Notation [(JSON)](https://www.json.org/) formátumban ábrázolt adatelemzést és lekérdezéseket, valamint a rokoni ADATAIk JSON-szövegként való exportálását.

A JSON a modern webes és mobil alkalmazások adatcseréjéhez használt népszerű adatformátum. A JSON-t a részben strukturált, a naplófájlokban vagy NoSQL-adatbázisokban (például [Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/)) tárolt fájlok tárolására is használják. Számos REST-webszolgáltatásnak az eredményeket JSON-szövegként kell megadnia, vagy JSON-ként formázott adatokhoz kell fogadnia. A legtöbb Azure-szolgáltatás, például az [azure Cognitive Search](https://azure.microsoft.com/services/search/), az [Azure Storage](https://azure.microsoft.com/services/storage/)és a [Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) olyan Rest-végpontokkal rendelkezik, amelyek JSON-t adnak vissza vagy használnak fel.


Az Azure SQL termékcsalád tagjai lehetővé teszik a JSON-adatok egyszerű használatát, és integrálni az adatbázist a modern szolgáltatásokkal, és a következő funkciókat biztosítja a JSON-adatok használatához:

![JSON-függvények](./media/multi-model-features/image_1.png)

Ha JSON-szöveggel rendelkezik, kinyerheti az adatait a JSON-ból, vagy ellenőrizheti, hogy a JSON formátuma megfelelően formázott-e a beépített függvények [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql), [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql)és [ISJSON](/sql/t-sql/functions/isjson-transact-sql)használatával. A [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql) függvény lehetővé teszi az érték JSON-szövegen belüli frissítését. A speciális lekérdezés és elemzés érdekében a [openjson utasítással](/sql/t-sql/functions/openjson-transact-sql) függvény JSON-objektumok tömbjét is át tudja alakítani sorok halmazára. Bármely SQL-lekérdezés végrehajtható a visszaadott eredményhalmaz alapján. Végezetül van egy [for JSON](/sql/relational-databases/json/format-query-results-as-json-with-for-json-sql-server) záradék, amely lehetővé teszi, hogy a rokon táblákban tárolt adatait JSON-szövegként formázza.

További információ: a [JSON-adatok használata](database/json-features.md).
A [JSON](/sql/relational-databases/json/json-data-sql-server) egy alapszintű SQL Server adatbázismotor-szolgáltatás.

### <a name="when-to-use-a-json-capability"></a>Mikor használjon JSON-képességet

A kapcsolódó modellek helyett a dokumentumok modelljei is használhatók a különböző helyzetekben:

- A séma nagy mértékű normalizálása nem jelent jelentős előnyöket, mert az objektumok összes mezőjét egyszerre éri el, vagy nem frissíti az objektumok normalizált részeit. A normalizált modell azonban növeli a lekérdezések összetettségét, mert a nagy számú tábla, amelyhez az adatgyűjtéshez csatlakoznia kell.
- A JSON-dokumentumokat natív módon használó alkalmazásokkal dolgozik a kommunikációs vagy adatmodellekben, és nem szeretne olyan további réteget bevezetni, amely átalakítja a kapcsolati adatforgalmat a JSON-ba, és fordítva.
- Egyszerűsíteni kell az adatmodellt a gyermektábla vagy az entitás-érték mintázatok normalizálása révén.
- JSON formátumban tárolt adatterhelést kell betöltenia vagy exportálnia anélkül, hogy további eszközt kellene elemezni.

## <a name="spatial-features"></a>Térbeli funkciók

A térbeli adatok a geometriai objektumok fizikai helyével és alakával kapcsolatos információkat jelölik. Ezek az objektumok lehetnek pontok vagy összetettebb objektumok, például országok/régiók, utak vagy tavak.

 A két támogatott térbeli adattípus: 

- A geometriai típus egy euklideszi (Flat) koordináta-rendszerbeli adathalmazt jelöl.
- A földrajzi típus a kör alakú koordináta-rendszerbeli adathalmazokat jelöli.

Az Azure SQL termékcsaládban több térbeli objektum is használható, így JavaScript Object Notation [(JSON)](https://www.json.org/) formátumú adatelemzést és lekérdezéseket végezhet, és a rokoni adatait JSON-szövegként exportálhatja.
például: [pont](/sql/relational-databases/spatial/point), [LineString](/sql/relational-databases/spatial/linestring), [sokszög](/sql/relational-databases/spatial/polygon)stb.

Az Azure SQL termékcsalád olyan speciális [térbeli indexeket](/sql/relational-databases/spatial/spatial-indexes-overview) is biztosít, amelyek a térbeli lekérdezések teljesítményének javításához használhatók.

A [térbeli támogatás](/sql/relational-databases/spatial/spatial-data-sql-server) egy alapszintű SQL Server adatbázismotor-szolgáltatás.

## <a name="xml-features"></a>XML-funkciók

A SQL Server adatbázismotor hatékony platformot biztosít a félig strukturált adatkezeléshez használható sokoldalú alkalmazások fejlesztéséhez. Az XML-támogatás integrálva van az adatbázismotor összes összetevőjébe, és a következőket tartalmazza:

- Az XML-adattípus. Az XML-értékek natív módon tárolhatók egy XML-adattípusú oszlopban, amely XML-sémák gyűjteménye szerint írható, vagy a típus nem hagyható el. Az XML-oszlop indexelése megtehető.
- Az XML-típus oszlopaiban és változójában tárolt XML-adatmennyiség XQuery-lekérdezésének megadására van lehetőség. A XQuery funkciói olyan Transact-SQL-lekérdezésekben használhatók, amelyek az adatbázisban használt adatmodellekhez férnek hozzá.
- Az XML-dokumentumok összes elemét automatikusan indexeli az [elsődleges XML-index](/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) használatával, vagy megadhatja a [másodlagos XML-index](/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes)használatával indexelni kívánt elérési utakat.
- OPENROWSET, amely lehetővé teszi az XML-adatmennyiségek tömeges betöltését.
- A viszonyítási adattartalom átalakítása XML-formátumba.

Az XML egy alapszintű SQL Server adatbázismotor [-](/sql/relational-databases/xml/xml-data-sql-server) szolgáltatás.

### <a name="when-to-use-an-xml-capability"></a>Mikor kell XML-képességet használni

A kapcsolódó modellek helyett a dokumentumok modelljei is használhatók a különböző helyzetekben:

- A séma nagy mértékű normalizálása nem jelent jelentős előnyöket, mert az objektumok összes mezőjét egyszerre éri el, vagy nem frissíti az objektumok normalizált részeit. A normalizált modell azonban növeli a lekérdezések összetettségét, mert a nagy számú tábla, amelyhez az adatgyűjtéshez csatlakoznia kell.
- Az XML-dokumentumokat natív módon használó alkalmazásokkal folytatott kommunikációt és adatmodelleket használ, és nem szeretne olyan további réteget bevezetni, amely átalakítja a kapcsolati adattípusokat az XML-be, és fordítva.
- Egyszerűsíteni kell az adatmodellt a gyermektábla vagy az entitás-érték mintázatok normalizálása révén.
- XML formátumban tárolt adatgyűjtést vagy-exportálást kell végeznie az adatelemzést elvégező további eszköz nélkül.

## <a name="key-value-pairs"></a>Kulcs-érték párok

Az Azure SQL termékcsalád termékei nem rendelkeznek olyan specializált típusokkal vagy szerkezetekkel, amelyek támogatják a kulcs-érték párokat, mivel a kulcs-érték struktúrák natív módon, szabványos táblázatként jeleníthetők meg:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Ezt a kulcs-érték struktúrát úgy szabhatja testre, hogy korlátozás nélkül illeszkedjen az igényeihez. Az érték például lehet XML-dokumentum a `nvarchar(max)` típus helyett, ha az érték JSON-dokumentum, a `CHECK` JSON-tartalom érvényességét ellenőrző korlátozást adhat meg. A további oszlopok egy kulcsával kapcsolatos tetszőleges számú értéket adhat hozzá, a számított oszlopok és indexek hozzáadásával egyszerűsítheti és optimalizálhatja az adatelérést, valamint a tábla memóriához/optimalizált séma-táblaként való definiálásával jobb teljesítményt érhet el.

Megtudhatja, [hogyan használja a BWin a In-Memory OLTP, hogy példátlan teljesítményt és skálázást](/archive/blogs/sqlcat/how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale) biztosítson a ASP.net gyorsítótárazási megoldáshoz, amely másodpercenként 1.200.000-kötegeket ért el, például hogyan lehet hatékonyan használni a viszonyítási modellt a gyakorlatban.

## <a name="next-steps"></a>Következő lépések

Az Azure SQL termékcsaládban található többmodelles képességek a legfontosabb SQL Server adatbázismotor-funkciók, amelyek az Azure SQL termékcsalád tagjai között vannak megosztva. Ha többet szeretne megtudni ezekről a funkciókról, látogasson el az SQL-kapcsolati adatbázis dokumentációs oldalaira:

- [Gráf feldolgozása](/sql/relational-databases/graphs/sql-graph-overview)
- [JSON-adatok](/sql/relational-databases/json/json-data-sql-server)
- [Térbeli támogatás](/sql/relational-databases/spatial/spatial-data-sql-server)
- [XML-adatok](/sql/relational-databases/xml/xml-data-sql-server)
