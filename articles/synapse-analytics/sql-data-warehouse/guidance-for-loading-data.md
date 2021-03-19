---
title: Ajánlott eljárások a dedikált SQL-készletekhez
description: Javaslatok és teljesítmény-optimalizálás az betöltéshez dedikált SQL-készletek használatával az Azure szinapszis Analyticsben.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 004084097e2f24a4ceb36f7a016049a574b80b42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594835"
---
# <a name="best-practices-for-loading-data-using-dedicated-sql-pools-in-azure-synapse-analytics"></a>Ajánlott eljárások az betöltéshez dedikált SQL-készletek használatával az Azure szinapszis Analyticsben

Ebből a cikkből megtudhatja, milyen ajánlásokat és teljesítmény-optimalizálást kell használnia az betöltéshez a dedikált SQL-készlet használatával.

## <a name="preparing-data-in-azure-storage"></a>Adatok előkészítése az Azure Storage-ban

A késés minimalizálásához helyezze a tárolási réteget és a dedikált SQL-készletet.

Az adatok ORC fájlformátumba való exportálásakor Java memóriahiány-hibák jelentkezhetnek, ha a szövegoszlopok túl nagyok. Ezt a korlátozást úgy küszöbölheti ki, ha az oszlopok csak egy részhalmazát exportálja.

Minden fájlformátum eltérő teljesítményjellemzővel rendelkezik. A leggyorsabb betöltés érdekében használjon tömörített, tagolt szövegfájlokat. Az UTF-8 és UTF-16 formátum teljesítménye között minimális a különbség.

A nagy tömörített fájlokat ossza fel kisebb tömörített fájlokra.

## <a name="running-loads-with-enough-compute"></a>Betöltések futtatása elegendő számítási teljesítménnyel

A leggyorsabb betöltési sebesség érdekében egyszerre egy betöltési feladatot futtasson. Ha ez nem valósítható meg, futtasson minimális számú terhelést egyszerre. Ha nagy betöltési feladatra számíthat, érdemes lehet a dedikált SQL-készletet a terhelés előtt méretezni.

A betöltések megfelelő számítási erőforrásokkal való futtatásához hozzon létre betöltések futtatására kijelölt felhasználókat. Az egyes betöltési felhasználókat egy adott munkaterhelés-csoportba sorolja be. Betöltés futtatásához jelentkezzen be az egyik betöltési felhasználóként, majd futtassa a betöltést. A betöltés a felhasználó munkaterhelési csoportján fut.  

### <a name="example-of-creating-a-loading-user"></a>Példa egy betöltést végző felhasználó létrehozására

Ez a példa egy adott munkaterhelés-csoportba sorolt betöltési felhasználót hoz létre. Ennek első lépése a **főkiszolgálóhoz való csatlakozás** és egy bejelentkezés létrehozása.

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

Kapcsolódjon a dedikált SQL-készlethez, és hozzon létre egy felhasználót. A következő kód azt feltételezi, hogy csatlakozik a mySampleDataWarehouse nevű adatbázishoz. Bemutatja, hogyan hozhat létre egy betöltő felhasználót, és a [másolási utasítás](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)használatával lehetővé teszi a felhasználók számára táblák és betöltés létrehozását. Ezután osztályozza a felhasználót a DataLoads munkaterhelés csoportba a maximális erőforrásokkal. 

```sql
   -- Connect to the dedicated SQL pool
   CREATE USER loader FOR LOGIN loader;
   GRANT ADMINISTER DATABASE BULK OPERATIONS TO loader;
   GRANT INSERT ON <yourtablename> TO loader;
   GRANT SELECT ON <yourtablename> TO loader;
   GRANT CREATE TABLE TO loader;
   GRANT ALTER ON SCHEMA::dbo TO loader;
   
   CREATE WORKLOAD GROUP DataLoads
   WITH ( 
       MIN_PERCENTAGE_RESOURCE = 100
       ,CAP_PERCENTAGE_RESOURCE = 100
       ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 100
    );

   CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
   WITH (
         WORKLOAD_GROUP = 'DataLoads'
       ,MEMBERNAME = 'loader'
   );
```
<br><br>
>[!IMPORTANT] 
>Ez egy rendkívüli példa arra, hogy az SQL-készlet 100%-os erőforrásait egyetlen terhelésre foglalja le. Ez maximális párhuzamosságot biztosít 1. Vegye figyelembe, hogy ezt csak a kezdeti terheléshez kell használni, ahol további munkaterhelés-csoportokat kell létrehoznia a saját konfigurációkkal, hogy balanace az erőforrásokat a számítási feladatokhoz. 

Ha terhelést szeretne futtatni a terhelés betöltéséhez szükséges erőforrásokkal, jelentkezzen be betöltőként, és futtassa a terhelést.

## <a name="allowing-multiple-users-to-load-polybase"></a>Több felhasználó betöltésének engedélyezése (alapszintű)

Gyakran van szükség több felhasználó betöltésére egy dedikált SQL-készletbe. A ( [Transact-SQL) CREATE TABLE](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) betöltéséhez az ADATBÁZISnak vezérlési engedélyekkel kell rendelkeznie.  A CONTROL engedély az összes séma vezérlését biztosítja.

Előfordulhat, hogy nem szeretné, hogy minden betöltést végző felhasználó vezérelési jogot kapjon az összes sémához. Az engedélyek korlátozására használja a DENY CONTROL utasítást.

Vegyünk például két adatbázissémát: schema_A az A részleghez, és schema_B a B részleghez. Legyen user_A és user_B két PolyBase-betöltést végző adatbázis-felhasználó az A, illetve a B részlegen. Mindkét felhasználó kapott adatbázisszintű CONTROL jogosultságokat. Az A és B séma létrehozói zárolják a sémáikat a DENY utasítás segítségével:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

A User_A és user_B mostantól ki vannak zárva a másik részleg sémájában.

## <a name="loading-to-a-staging-table"></a>Betöltés előkészítési táblába

Az adat egy dedikált SQL Pool-táblába való áthelyezéséhez szükséges leggyorsabb betöltési sebesség eléréséhez töltse be az adathalmazt egy átmeneti táblába.  Határozza meg az előkészítési táblát halomként, és használjon ciklikus időszeletelést a terjesztési beállításhoz.

Vegye figyelembe, hogy a betöltés általában kétlépéses folyamat, amelyet először betölt egy előkészítési táblába, majd beszúrja az adathalmazt egy éles, dedikált SQL Pool-táblába. Ha az éles tábla kivonatoló terjesztést használ, a betöltés és a beszúrás teljes ideje gyorsabb lehet, ha meghatároz egy előkészítési táblát a kivonatoló terjesztéssel.

Az előkészítési táblába való betöltés több időt vesz igénybe, de a sorok az éles táblába való beszúrásának második lépése nem jár a disztribúciók közötti adatmozgatással.

## <a name="loading-to-a-columnstore-index"></a>Betöltés oszlopcentrikus indexbe

Az oszlopcentrikus indexek sok memóriát igényelnek az adatok jó minőségű sorcsoportokba való tömörítéséhez. A legjobb tömörítési és indexelési hatékonyság érdekében az oszlopcentrikus indexnek a maximális 1 048 576 sort kell tömörítenie az egyes sorcsoportokba.

Ha korlátozott a rendelkezésre álló memória mennyisége, előfordulhat, hogy az oszlopcentrikus index nem éri el a maximális tömörítési sebességet. Ez a forgatókönyv az effekt-lekérdezési teljesítményt eredményezi. A témakör részletes bemutatása: [Oszloptár memóriájának optimalizálása](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Annak érdekben, hogy elég memória álljon a betöltést végző felhasználók rendelkezésére a maximális tömörítési sebesség eléréséhez, használjon olyan betöltést végző felhasználókat, akik közepes vagy nagy erőforrásosztály tagjai.
- Töltsön be elég sort az új sorcsoportok teljes feltöltéséhez. Kötegelt betöltés során minden 1 048 576. sor teljes sorcsoportként közvetlenül az oszloptárba van tömörítve. A 102 400 sornál kisebb betöltések a deltatárba küldik a sorokat, ahol a sorok B-fában vannak tárolva.

> [!NOTE]
> Ha túl kevés sort tölt be, előfordulhat, hogy az összes útvonala a deltatárba küldik, és nem tömöríti azonnal a oszlopcentrikus formátumba.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>A Batch méretének növeléséhez a SqLBulkCopy API vagy a BCP használata esetén

A COPY utasítással való betöltés a legmagasabb átviteli sebességet biztosítja dedikált SQL-készletekkel. Ha nem tudja használni a MÁSOLÁSt a betöltéshez, és a [SQLBULKCOPY API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) -t vagy [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)-t kell használnia, érdemes növelni a Batch méretét a jobb átviteli sebesség érdekében.

> [!TIP]
> Az optimális batch-kapacitás meghatározásához az ajánlott alapkonfiguráció a 100 K és 1 millió közötti szám közötti méretű köteg.

## <a name="handling-loading-failures"></a>Betöltési hibák kezelése

Egy külső táblát használó betöltés meghiúsulhat a következő hibával: *„A lekérdezés megszakadt – a rendszer elérte a felső visszautasítási küszöbértéket külső forrásból való beolvasás során”*. Ez az üzenet azt jelzi, hogy a külső adatok szabálytalan rekordokat tartalmaznak.

Az adatrekordok akkor tekinthetők inkonzisztensnek, ha megfelelnek az alábbi feltételek egyikének:

- Az oszlopok adattípusai és száma nem egyezik a külső tábla oszlopainak definíciójában szereplő értékekkel.
- Az érték nem felel meg a megadott külső fájlformátumnak.

A szabálytalan rekordok kijavításához győződjön meg arról, hogy a külső tábla- és fájlformátum-definíciók helyesek, és hogy a külső adatok megfelelnek ezeknek a definícióknak.

Ha a külső adatrekordok egy részhalmaza inkonzisztens, dönthet úgy, hogy elutasítja ezeket a rekordokat a lekérdezésekhez a [külső tábla létrehozása (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)elutasítási beállításainak használatával.

## <a name="inserting-data-into-a-production-table"></a>Adatok beszúrása az éles táblába

A kis táblák [INSERT utasítással](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) végzett egyszeri feltöltése vagy akár egy keresés rendszeres újratöltése is megfelelő lehet, ha egy, a következőhöz hasonló utasítást használ: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Az egyszeres beszúrásoknál azonban hatékonyabb egy kötegelt betöltés végrehajtása.

Ha több ezer egyszeres beszúrást hajt végre egy nap, kötegelje a beszúrásokat, hogy kötegelve tölthesse be őket.  Fejlesszen folyamatokat, amelyek az egyszeres beszúrásokat egy fájlhoz fűzik, majd hozzon létre egy másik folyamatot, amely időszakosan betölti a fájlt.

## <a name="creating-statistics-after-the-load"></a>Statisztika létrehozása a betöltés után

A lekérdezési teljesítmény javításához fontos létrehozni statisztikákat a táblák összes oszlopához az első betöltés után, illetve az adatok minden lényeges módosítását követően. A statisztikák létrehozása manuálisan is elvégezhető, vagy engedélyezheti a [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic).

A statisztika részletes ismertetése: [Statisztika](sql-data-warehouse-tables-statistics.md). Az alábbi példa bemutatja, hogyan hozhat létre manuálisan statisztikákat a Customer_Speed tábla öt oszlopán.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys-polybase"></a>Tárolási kulcsok elforgatása (alapszintű)

Biztonsági szempontból érdemes rendszeresen módosítani a Blob Storage hozzáférési kulcsát. A Blob Storage-fiókhoz két tárkulcs tartozik, amely lehetővé teszi a kulcsok közötti váltást.

Az Azure Storage-fiók kulcsainak rotálása:

Adja ki az [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) parancsot minden olyan tárfiókhoz, amelynek módosult a kulcsa.

Példa:

Létrejön az eredeti kulcs

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

A kulcs rotálása az 1. kulcsból a 2. kulcsba

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

A mögöttes külső adatforrásokban nem kell más módosítást elvégezni.

## <a name="next-steps"></a>Következő lépések

- A kinyerési, betöltési és átalakítási (ELT) folyamat megtervezése során a MÁSOLÁSi utasítással vagy a kiindulással kapcsolatos további információkért lásd: [elt az Azure szinapszis analyticshez](design-elt-data-loading.md).
- A betöltési oktatóanyaghoz [használja a copy utasítást az adatok Azure Blob Storage-ból a SZINAPSZIS SQL-be való betöltéséhez](./load-data-from-azure-blob-storage-using-copy.md).
- Az adatbetöltések monitorozása: [A számítási feladat monitorozása DMV-kkel](sql-data-warehouse-manage-monitor.md).