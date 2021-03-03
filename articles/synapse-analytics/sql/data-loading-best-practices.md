---
title: Ajánlott adatbetöltési eljárások
description: Javaslatok és teljesítmény-optimalizálás az adatgyűjtés dedikált SQL-készletbe való betöltéséhez az Azure szinapszis Analytics szolgáltatásban.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 3c8c34cc3e23306f1d024cfa36b40c7975caa8c6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674278"
---
# <a name="best-practices-for-loading-data-into-a-dedicated-sql-pool-azure-synapse-analytics"></a>Ajánlott eljárások az Azure szinapszis Analytics dedikált SQL-készletbe való betöltéséhez

Ebből a cikkből megtudhatja, milyen javaslatokat és teljesítmény-optimalizálást talál az adat betöltéséhez.

## <a name="prepare-data-in-azure-storage"></a>Az Azure Storage-ban való adatelőkészítés

A késés minimalizálásához helyezze a tárolási réteget és a dedikált SQL-készletet.

Az adatok ORC fájlformátumba való exportálásakor Java memóriahiány-hibák jelentkezhetnek, ha a szövegoszlopok túl nagyok. Ezt a korlátozást úgy küszöbölheti ki, ha az oszlopok csak egy részhalmazát exportálja.

A nem tudja betölteni a 1 000 000 bájtnál több adattal rendelkező sorokat. Az Azure Blob Storage-ba vagy az Azure Data Lake Store-ba helyezett szöveges fájlok nem tartalmazhatnak 1 000 000 bájtnál több adatot. Ez a bájtkorlátozás a táblasémától függetlenül érvényes.

Minden fájlformátum eltérő teljesítményjellemzővel rendelkezik. A leggyorsabb betöltés érdekében használjon tömörített, tagolt szövegfájlokat. Az UTF-8 és UTF-16 formátum teljesítménye között minimális a különbség.

A nagy tömörített fájlokat ossza fel kisebb tömörített fájlokra.

## <a name="run-loads-with-enough-compute"></a>A terhelések futtatása elegendő számítási feladatokkal

A leggyorsabb betöltési sebesség érdekében egyszerre egy betöltési feladatot futtasson. Ha ez nem lehetséges, egyszerre a lehető legkevesebb betöltést futtassa. Ha nagy betöltési feladatra számíthat, érdemes lehet a dedikált SQL-készletet a terhelés előtt méretezni.

A betöltések megfelelő számítási erőforrásokkal való futtatásához hozzon létre betöltések futtatására kijelölt felhasználókat. Rendelje hozzá az egyes betöltési felhasználókat egy adott erőforrás-osztályhoz vagy munkaterhelési csoporthoz. Betöltés futtatásához jelentkezzen be az egyik betöltési felhasználóként, majd futtassa a betöltést. A betöltés a felhasználó erőforrásosztályával fut.  Ez a módszer egyszerűbb, mint a felhasználó erőforrásosztályának módosításával próbálkozni, hogy az megfeleljen az aktuális erőforrásosztály-igénynek.

### <a name="create-a-loading-user"></a>Betöltési felhasználó létrehozása

Ez a példa létrehoz egy betöltést végző felhasználót a staticrc20 erőforrásosztályhoz. Ennek első lépése a **főkiszolgálóhoz való csatlakozás** és egy bejelentkezés létrehozása.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

Kapcsolódjon az adattárházhoz, majd hozzon létre egy felhasználót. A következő kód azt feltételezi, hogy a mySampleDataWarehouse nevű adatbázishoz kapcsolódik. A kód azt mutatja, hogyan lehet létrehozni egy LoaderRC20 nevű felhasználót, illetve hogyan lehet vezérlői jogosultságot adni számára egy adatbázishoz. Ezután a kód felveszi a felhasználót a staticrc20 adatbázis-szerepkör tagjaként.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

Ha a Staticrc20 erőforrásosztályhoz erőforrás-osztályaihoz erőforrásokkal rendelkező terhelést szeretne futtatni, jelentkezzen be LoaderRC20 néven, és futtassa a terhelést.

A betöltéseket inkább statikus, mint dinamikus erőforrásosztályokkal futtassa. A statikus erőforrás-osztályok használata ugyanazokat az erőforrásokat garantálja, függetlenül az [adattárház-egységektől](resource-consumption-models.md). Ha dinamikus erőforrásosztályt használ, az erőforrások a szolgáltatásszinttől függően változhatnak. Dinamikus osztályok esetében egy alacsonyabb szolgáltatási szint azt jelenti, hogy feltehetően nagyobb erőforrásosztályt kell használnia a betöltést végző felhasználóhoz.

## <a name="allow-multiple-users-to-load"></a>Több felhasználó betöltésének engedélyezése

Gyakran van szükség több olyan felhasználóra, akik adatokat töltenek egy adattárházba. A [(Transact-SQL) CREATE TABLE](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) betöltéséhez az ADATBÁZISnak vezérlési engedélyekkel kell rendelkeznie.  A CONTROL engedély az összes séma vezérlését biztosítja. Előfordulhat, hogy nem szeretné, hogy minden betöltést végző felhasználó vezérelési jogot kapjon az összes sémához. Az engedélyek korlátozására használja a DENY CONTROL utasítást.

Vegyünk például két adatbázissémát: schema_A az A részleghez, és schema_B a B részleghez. Legyen user_A és user_B két PolyBase-betöltést végző adatbázis-felhasználó az A, illetve a B részlegen. Mindkét felhasználó kapott adatbázisszintű CONTROL jogosultságokat. Az A és B séma létrehozói zárolják a sémáikat a DENY utasítás segítségével:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

A User_A és user_B mostantól ki vannak zárva a másik részleg sémájában.

## <a name="load-to-a-staging-table"></a>Betöltés előkészítési táblába

Az adattárház táblájába való adatáthelyezés leggyorsabb betöltési sebességének eléréséhez töltse be az adatokat egy előkészítési táblába.  Határozza meg az előkészítési táblát halomként, és használjon ciklikus időszeletelést a terjesztési beállításhoz.

Vegye figyelembe, hogy a betöltés általában két lépésből álló folyamat, amely során először az előkészítési táblába tölti be, majd beszúrja az adatokat egy éles adattárháztáblába. Ha az éles tábla kivonatoló terjesztést használ, a betöltés és a beszúrás teljes ideje gyorsabb lehet, ha meghatároz egy előkészítési táblát a kivonatoló terjesztéssel. Az előkészítési táblába való betöltés több időt vesz igénybe, de a sorok az éles táblába való beszúrásának második lépése nem jár a disztribúciók közötti adatmozgatással.

## <a name="load-to-a-columnstore-index"></a>Betöltés egy oszlopcentrikus indexbe

Az oszlopcentrikus indexek sok memóriát igényelnek az adatok jó minőségű sorcsoportokba való tömörítéséhez. A legjobb tömörítési és indexelési hatékonyság érdekében az oszlopcentrikus indexnek a maximális 1 048 576 sort kell tömörítenie az egyes sorcsoportokba. Ha korlátozott a rendelkezésre álló memória mennyisége, előfordulhat, hogy az oszlopcentrikus index nem éri el a maximális tömörítési sebességet. Ez a hatás lekérdezési teljesítményt nyújt. A témakör részletes bemutatása: [Oszloptár memóriájának optimalizálása](data-load-columnstore-compression.md).

- Annak érdekben, hogy elég memória álljon a betöltést végző felhasználók rendelkezésére a maximális tömörítési sebesség eléréséhez, használjon olyan betöltést végző felhasználókat, akik közepes vagy nagy erőforrásosztály tagjai.
- Töltsön be elég sort az új sorcsoportok teljes feltöltéséhez. Tömeges betöltés során minden 1 048 576-sor a teljes sorcsoport közvetlenül a oszlopcentrikus tömöríti. A 102 400 sornál kisebb betöltések a deltatárba küldik a sorokat, ahol a sorok B-fában vannak tárolva. Ha kevesebb sort tölt be, előfordulhat, hogy mind a deltatárba kerül, és a rendszer nem tömöríti azokat azonnal oszloptár formátumba.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>A Batch méretének növeléséhez a SQLBulkCopy API vagy a BCP használata esetén

Ahogy korábban említettük, a "Base" betöltéssel a legmagasabb átviteli sebesség lesz elérhető a szinapszis SQL-készlettel. Ha a SQLBulkCopy API-t (vagy a BCP-t) nem lehet betöltésre használni, érdemes megfontolnia a köteg méretének növelését a jobb teljesítmény érdekében – a jó ökölszabály a 100 000 – 1 000-es sorok közötti batch-méret.

## <a name="manage-loading-failures"></a>Betöltési hibák kezelése

Egy külső táblát használó betöltés meghiúsulhat a következő hibával: *„A lekérdezés megszakadt – a rendszer elérte a felső visszautasítási küszöbértéket külső forrásból való beolvasás során”*. Ez az üzenet azt jelzi, hogy a külső adatok szabálytalan rekordokat tartalmaznak. Az adatrekord akkor számít „szabálytalannak”, ha az oszlopok adattípusai és száma nem felel meg a külső tábla definícióinak, vagy ha az adatok nem felelnek meg a megadott külső fájlformátumnak.

A szabálytalan rekordok kijavításához győződjön meg arról, hogy a külső tábla- és fájlformátum-definíciók helyesek, és hogy a külső adatok megfelelnek ezeknek a definícióknak. Amennyiben a külső adatrekordok egy részhalmaza szabálytalan, dönthet úgy, hogy nem tart igényt ezekre a rekordokra a lekérdezéseihez. Ehhez használja a CREATE EXTERNAL TABLE visszautasítási lehetőségeit.

## <a name="insert-data-into-a-production-table"></a>Adatbeszúrás éles táblába

A kis táblák [INSERT utasítással](/sql/t-sql/statements/insert-transact-sql?view=azure-sqldw-latest&preserve-view=true) végzett egyszeri feltöltése vagy akár egy keresés rendszeres újratöltése is megfelelő lehet, ha egy, a következőhöz hasonló utasítást használ: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Az egyszeres lapkák azonban nem annyira hatékonyak, mint a tömeges betöltés.

Ha több ezer egyszeres beszúrást hajt végre egy nap, kötegelje a beszúrásokat, hogy kötegelve tölthesse be őket.  Fejlesszen folyamatokat, amelyek az egyszeres beszúrásokat egy fájlhoz fűzik, majd hozzon létre egy másik folyamatot, amely időszakosan betölti a fájlt.

## <a name="create-statistics-after-the-load"></a>Statisztika létrehozása a betöltés után

A lekérdezési teljesítmény javítása érdekében fontos, hogy az első betöltést követően az összes tábla összes oszlopának statisztikát hozzon létre, vagy az adatokban jelentős változások történnek. A statisztikák létrehozása manuálisan is elvégezhető, vagy engedélyezheti az [automatikus létrehozási statisztikát](../sql-data-warehouse/sql-data-warehouse-tables-statistics.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

A statisztika részletes ismertetése: [Statisztika](develop-tables-statistics.md). Az alábbi példa bemutatja, hogyan hozhat létre manuálisan statisztikákat a Customer_Speed tábla öt oszlopán.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Tárkulcsok rotálása

Biztonsági szempontból érdemes rendszeresen módosítani a Blob Storage hozzáférési kulcsát. A Blob Storage-fiókhoz két tárkulcs tartozik, amely lehetővé teszi a kulcsok közötti váltást.

Az Azure Storage-fiók kulcsainak rotálása:

Adja ki az [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) parancsot minden olyan tárfiókhoz, amelynek módosult a kulcsa.

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

- További információ a kinyerési, betöltési és átalakítási (ELT) folyamatról: [design elt for Azure szinapszis Analytics](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- A betöltési oktatóanyaghoz [használja a Base elemet az adatok Azure Blob Storage-ból az Azure szinapszis analyticsbe való betöltéséhez](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).
- Az adatbetöltések monitorozása: [A számítási feladat monitorozása DMV-kkel](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).