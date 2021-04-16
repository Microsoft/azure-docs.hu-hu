---
title: Ajánlott adatbetöltési eljárások dedikált SQL-készletekhez
description: Javaslatok és teljesítményoptimalizálás az adatok dedikált SQL-készletekkel való betöltéséhez a Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 294b2a5188f0dfd8cb29f21bdbb29236b1740231
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565865"
---
# <a name="best-practices-for-loading-data-using-dedicated-sql-pools-in-azure-synapse-analytics"></a>Ajánlott eljárások az adatok dedikált SQL-készletek használatával való betöltéséhez a Azure Synapse Analytics

Ebben a cikkben az adatok dedikált SQL-készlet használatával való betöltésére vonatkozó javaslatokat és teljesítményoptimalizálást tanulhatja meg.

## <a name="preparing-data-in-azure-storage"></a>Adatok előkészítése az Azure Storage-ban

A késés minimalizálása érdekében közös hellyel kell elosztani a tárolási réteget és a dedikált SQL-készletet.

Az adatok ORC fájlformátumba való exportálásakor Java memóriahiány-hibák jelentkezhetnek, ha a szövegoszlopok túl nagyok. Ezt a korlátozást úgy küszöbölheti ki, ha az oszlopok csak egy részhalmazát exportálja.

Minden fájlformátum eltérő teljesítményjellemzővel rendelkezik. A leggyorsabb betöltés érdekében használjon tömörített, tagolt szövegfájlokat. Az UTF-8 és UTF-16 formátum teljesítménye között minimális a különbség.

A nagy tömörített fájlokat ossza fel kisebb tömörített fájlokra.

## <a name="running-loads-with-enough-compute"></a>Betöltések futtatása elegendő számítási teljesítménnyel

A leggyorsabb betöltési sebesség érdekében egyszerre egy betöltési feladatot futtasson. Ha ez nem megvalósítható, futtason egyszerre minimális számú terhelést. Ha nagy betöltési feladatra számít, fontolja meg a dedikált SQL-készlet felméretezését a betöltés előtt.

A betöltések megfelelő számítási erőforrásokkal való futtatásához hozzon létre betöltések futtatására kijelölt felhasználókat. Az egyes betöltést töltő felhasználók besorolása egy adott számítási feladatcsoportba. Betöltés futtatásához jelentkezzen be az egyik betöltést töltő felhasználóként, majd futtassa a betöltést. A terhelés a felhasználó számítási feladatcsoportja mellett fut.  

### <a name="example-of-creating-a-loading-user"></a>Példa egy betöltést végző felhasználó létrehozására

Ez a példa egy adott számítási feladatcsoporthoz besorolt betöltést használó felhasználót hoz létre. Ennek első lépése a **főkiszolgálóhoz való csatlakozás** és egy bejelentkezés létrehozása.

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

Csatlakozzon a dedikált SQL-készlethez, és hozzon létre egy felhasználót. A következő kód feltételezi, hogy csatlakozott a mySampleDataWarehouse nevű adatbázishoz. Betöltőnek nevezett felhasználó létrehozásáról, valamint táblák létrehozásáról és betöltéséről ad engedélyt a felhasználónak a [COPY utasítással.](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) Ezután a felhasználót a DataLoads számítási feladatcsoportba osztályba csoportosítja a maximális erőforrásokkal. 

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
>Ez egy szélsőséges példa az SQL-készlet 100%-os erőforrásainak egyetlen terhelésre való elosztásra. Ez legfeljebb 1 egyidejűséget ad meg. Vegye figyelembe, hogy ezt csak a kezdeti terheléshez szabad használni, ahol további számítási feladatcsoportokat kell létrehoznia a saját konfigurációival, hogy erőforrásokat hozzon létre a számítási feladatok között. 

Ha a terhelést a betöltési számítási feladatcsoport erőforrásaival együtt futtatja, jelentkezzen be betöltőként, és futtassa a terhelést.

## <a name="allowing-multiple-users-to-load-polybase"></a>Több felhasználó betöltésének lehetővé teszi (PolyBase)

Gyakran van szükség arra, hogy több felhasználó töltse be az adatokat egy dedikált SQL-készletbe. A CREATE TABLE [AS SELECT (Transact-SQL) (PolyBase)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) használatával való betöltéshez az adatbázis CONTROL engedélyére van szükség.  A CONTROL engedély az összes séma vezérlését biztosítja.

Előfordulhat, hogy nem szeretné, hogy minden betöltést végző felhasználó vezérelési jogot kapjon az összes sémához. Az engedélyek korlátozására használja a DENY CONTROL utasítást.

Vegyünk például két adatbázissémát: schema_A az A részleghez, és schema_B a B részleghez. Legyen user_A és user_B két PolyBase-betöltést végző adatbázis-felhasználó az A, illetve a B részlegen. Mindkét felhasználó kapott adatbázisszintű CONTROL jogosultságokat. Az A és B séma létrehozói zárolják a sémáikat a DENY utasítás segítségével:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A és user_B ki vannak zárva a másik osztály sémája elől.

## <a name="loading-to-a-staging-table"></a>Betöltés előkészítési táblába

Az adatok dedikált SQL-készlettáblába való áthelyezésének leggyorsabb betöltési sebességéhez töltse be az adatokat egy előkészítési táblába.  Határozza meg az előkészítési táblát halomként, és használjon ciklikus időszeletelést a terjesztési beállításhoz.

Vegye figyelembe, hogy a betöltés általában egy kétlépéses folyamat, amelyben először be kell töltenie egy előkészítési táblába, majd be kell illesztenie az adatokat egy éles dedikált SQL-készlettáblába. Ha az éles tábla kivonatoló terjesztést használ, a betöltés és a beszúrás teljes ideje gyorsabb lehet, ha meghatároz egy előkészítési táblát a kivonatoló terjesztéssel.

Az előkészítési táblába való betöltés több időt vesz igénybe, de a sorok az éles táblába való beszúrásának második lépése nem jár a disztribúciók közötti adatmozgatással.

## <a name="loading-to-a-columnstore-index"></a>Betöltés oszlopcentrikus indexbe

Az oszlopcentrikus indexek sok memóriát igényelnek az adatok jó minőségű sorcsoportokba való tömörítéséhez. A legjobb tömörítési és indexelési hatékonyság érdekében az oszlopcentrikus indexnek a maximális 1 048 576 sort kell tömörítenie az egyes sorcsoportokba.

Ha korlátozott a rendelkezésre álló memória mennyisége, előfordulhat, hogy az oszlopcentrikus index nem éri el a maximális tömörítési sebességet. Ez a forgatókönyv viszont hatással van a lekérdezési teljesítményre. A témakör részletes bemutatása: [Oszloptár memóriájának optimalizálása](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Annak érdekben, hogy elég memória álljon a betöltést végző felhasználók rendelkezésére a maximális tömörítési sebesség eléréséhez, használjon olyan betöltést végző felhasználókat, akik közepes vagy nagy erőforrásosztály tagjai.
- Töltsön be elég sort az új sorcsoportok teljes feltöltéséhez. Kötegelt betöltés során minden 1 048 576. sor teljes sorcsoportként közvetlenül az oszloptárba van tömörítve. A 102 400 sornál kisebb betöltések a deltatárba küldik a sorokat, ahol a sorok B-fában vannak tárolva.

> [!NOTE]
> Ha túl kevés sort tölt be, előfordulhat, hogy ezek mind a deltatárhoz vannak irányítva, és nem tömörítik azonnal oszlopcentrikus formátumba.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>A köteg méretének növelése az SqLBulkCopy API vagy a bcp használata esetén

A COPY utasítással való betöltés biztosítja a legnagyobb átviteli sebességet dedikált SQL-készletekkel. Ha a COPY nem használható a betöltéshez, és az [SqLBulkCopy API-t](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) vagy [a bcp-t](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)kell használnia, érdemes megfontolni a kötegméret növelését a jobb átviteli sebesség érdekében.

> [!TIP]
> Az optimális kötegméret meghatározásának ajánlott alapkonfigurációja a 100 ezer és 1 millió közötti kötegméret.

## <a name="handling-loading-failures"></a>Betöltési hibák kezelése

Egy külső táblát használó betöltés meghiúsulhat a következő hibával: *„A lekérdezés megszakadt – a rendszer elérte a felső visszautasítási küszöbértéket külső forrásból való beolvasás során”*. Ez az üzenet azt jelzi, hogy a külső adatok szabálytalan rekordokat tartalmaznak.

Az adatrekord akkor számít trágárnak, ha megfelel az alábbi feltételek egyikének:

- Az adattípusok és az oszlopok száma nem egyezik meg a külső tábla oszlopdefinícióival.
- Az adatok nem felelnek meg a megadott külső fájlformátumnak.

A szabálytalan rekordok kijavításához győződjön meg arról, hogy a külső tábla- és fájlformátum-definíciók helyesek, és hogy a külső adatok megfelelnek ezeknek a definícióknak.

Ha a külső adatrekordok egy része nem megfelelő, dönthet úgy, hogy elutasítja ezeket a rekordokat a lekérdezésekhez a [CREATE EXTERNAL TABLE (Transact-SQL) (KÜLSŐ TÁBLA LÉTREHOZÁSA (Transact-SQL) )](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)elutasítási lehetőségének használatával.

## <a name="inserting-data-into-a-production-table"></a>Adatok beszúrása az éles táblába

A kis táblák [INSERT utasítással](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) végzett egyszeri feltöltése vagy akár egy keresés rendszeres újratöltése is megfelelő lehet, ha egy, a következőhöz hasonló utasítást használ: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Az egyszeres beszúrásoknál azonban hatékonyabb egy kötegelt betöltés végrehajtása.

Ha több ezer egyszeres beszúrást hajt végre egy nap, kötegelje a beszúrásokat, hogy kötegelve tölthesse be őket.  Fejlesszen folyamatokat, amelyek az egyszeres beszúrásokat egy fájlhoz fűzik, majd hozzon létre egy másik folyamatot, amely időszakosan betölti a fájlt.

## <a name="creating-statistics-after-the-load"></a>Statisztika létrehozása a betöltés után

A lekérdezési teljesítmény javításához fontos létrehozni statisztikákat a táblák összes oszlopához az első betöltés után, illetve az adatok minden lényeges módosítását követően. A statisztikákat manuálisan is létre lehet tenni, vagy engedélyezheti a [AUTO_CREATE_STATISTICS.](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic)

A statisztika részletes ismertetése: [Statisztika](sql-data-warehouse-tables-statistics.md). Az alábbi példa bemutatja, hogyan hozhat létre statisztikákat manuálisan a Customer_Speed oszlopához.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys-polybase"></a>Tárkulcsok elforgatása (PolyBase)

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

- Ha többet szeretne megtudni a COPY utasításról vagy a PolyBase-ről a kinyerési, betöltési és átalakítási (ELT) folyamat tervezésekor, olvassa el a Következőt: [ElT](design-elt-data-loading.md)tervezése Azure Synapse Analytics.
- Betöltési oktatóanyag esetén a COPY utasítással töltse be az adatokat az Azure Blob Storage-ból a [Synapse SQL.](./load-data-from-azure-blob-storage-using-copy.md)
- Az adatbetöltések monitorozása: [A számítási feladat monitorozása DMV-kkel](sql-data-warehouse-manage-monitor.md).