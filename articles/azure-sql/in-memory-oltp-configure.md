---
title: In-Memory OLTP javítja az SQL tranzakció Teljesítményfigyelőt
description: In-Memory OLTP a Azure SQL Database és az Azure SQL felügyelt példányának meglévő adatbázisaiban lévő tranzakciós teljesítmény javítása érdekében.
services: sql-database
ms.service: sql-database
ms.custom: sqldbrb=2
ms.subservice: development
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: e17e98e784b7453c87814c5cce5c03568f66b1cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91619746"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>In-Memory OLTP használatával javíthatja az alkalmazás teljesítményét a Azure SQL Database és az Azure SQL felügyelt példányain
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

A [memóriában tárolt OLTP](in-memory-oltp-overview.md) felhasználható a tranzakciók feldolgozásának, az adatok betöltésének és az átmeneti adatkörnyezetek teljesítményének javítására, [prémium és üzletileg kritikus rétegbeli](database/service-tiers-vcore.md) adatbázisokban, az árképzési szint növelése nélkül.

> [!NOTE]
> Ismerje meg [, hogy a kvórum megduplázza a kulcsfontosságú adatbázis számítási feladatait, miközben 70%-kal csökkenti a DTU a Azure SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

Kövesse az alábbi lépéseket In-Memory OLTP a meglévő adatbázisban való elfogadásához.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>1. lépés: Győződjön meg arról, hogy prémium és üzletileg kritikus szintű adatbázist használ

In-Memory OLTP csak prémium és üzletileg kritikus szintű adatbázisokban támogatott. A In-Memory akkor támogatott, ha a visszaadott eredmény 1 (nem 0):

```sql
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*Xtp* a *szélsőséges tranzakció-feldolgozáshoz*

## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>2. lépés: az áttelepíteni kívánt objektumok azonosítása In-Memory OLTP

A SSMS tartalmaz egy **tranzakciós teljesítmény-elemzési áttekintő** jelentést, amelyet egy aktív számítási feladattal rendelkező adatbázison lehet futtatni. A jelentés azokat a táblákat és tárolt eljárásokat azonosítja, amelyek In-Memory OLTP való áttelepítésre jelöltek.

A SSMS-ben a jelentés létrehozásához:

* A **Object Explorer** kattintson a jobb gombbal az adatbázis-csomópontra.
* Kattintson a **jelentések**  >  **szabványos jelentések**  >  **tranzakciók teljesítményének elemzése áttekintés** elemre.

További információ: [annak meghatározása, hogy egy tábla vagy tárolt eljárás In-Memory OLTP legyen-e portolva](/sql/relational-databases/in-memory-oltp/determining-if-a-table-or-stored-procedure-should-be-ported-to-in-memory-oltp).

## <a name="step-3-create-a-comparable-test-database"></a>3. lépés: összehasonlítható tesztelési adatbázis létrehozása

Tegyük fel, hogy a jelentés azt jelzi, hogy az adatbázis egy olyan táblát tartalmaz, amely kihasználhatja a memóriára optimalizált táblára való átalakítás előnyeit. Javasoljuk, hogy az első teszteléssel erősítse meg a jelzést teszteléssel.

Szüksége lesz az éles adatbázis tesztelési példányára. A tesztelési adatbázisnak ugyanazon a szolgáltatási szint szintjén kell lennie, mint az éles adatbázisnak.

A teszteléshez a következő módon csípést tehet a teszt-adatbázison:

1. Kapcsolódjon a tesztelési adatbázishoz a SSMS használatával.
2. Ha nem szeretné, hogy a WITH (SNAPSHOT) lehetőség megjelenjen a lekérdezésekben, állítsa az adatbázis lehetőséget az alábbi T-SQL-utasításban látható módon:

   ```sql
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>4. lépés: táblák átmigrálása

Létre kell hoznia és fel kell töltenie a tesztelni kívánt tábla memóriára optimalizált másolatát. A következőkkel hozható létre:

* A SSMS-ben a hasznos memória optimalizálása varázsló.
* Kézi T-SQL.

### <a name="memory-optimization-wizard-in-ssms"></a>Memória-optimalizálási varázsló a SSMS-ben

Az áttelepítési lehetőség használata:

1. Kapcsolódjon a test adatbázishoz a SSMS használatával.
2. A **Object Explorer** kattintson a jobb gombbal a táblára, majd kattintson a **memória-optimalizálási tanácsadó** elemre.

   Megjelenik a **tábla memória-optimalizáló tanácsadó** varázslója.
3. A varázslóban kattintson az áttelepítés ellenőrzése elemre (vagy a **tovább** gombra) annak **ellenőrzéséhez** , hogy a tábla rendelkezik-e olyan nem támogatott funkciókkal, amelyek nem támogatottak a memóriára optimalizált táblákban. További információkért lásd:

   * Memória- *optimalizálási ellenőrzőlista* a [memória-optimalizálási tanácsadóban](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor).
   * [A Transact-SQL-szerkezetek In-Memory OLTP nem támogatottak](/sql/relational-databases/in-memory-oltp/transact-sql-constructs-not-supported-by-in-memory-oltp).
   * [Áttelepítés In-Memory OLTP](/sql/relational-databases/in-memory-oltp/plan-your-adoption-of-in-memory-oltp-features-in-sql-server).
4. Ha a tábla nem rendelkezik nem támogatott funkciókkal, az Advisor a tényleges sémát és az adatáttelepítést is elvégezheti Önnek.

### <a name="manual-t-sql"></a>Kézi T-SQL

Az áttelepítési lehetőség használata:

1. Kapcsolódjon a test-adatbázishoz a SSMS (vagy egy hasonló segédprogram) használatával.
2. Szerezze be a teljes T-SQL-szkriptet a táblához és az indexekhez.

   * A SSMS kattintson a jobb gombbal a tábla csomópontjára.
   * Kattintson a **parancsfájl**  >  **-táblázat létrehozása**  >  **új lekérdezési ablakként** lehetőségre.
3. A parancsfájl ablakban adja hozzá a (MEMORY_OPTIMIZED = ON) parancsot a CREATE TABLE utasításhoz.
4. FÜRTÖZÖTT index esetén módosítsa azt nem FÜRTÖZÖTT értékre.
5. Nevezze át a meglévő táblát SP_RENAME használatával.
6. Hozza létre a tábla új, memóriára optimalizált példányát a szerkesztett CREATE TABLE szkript futtatásával.
7. Másolja az adatait a memóriára optimalizált táblába INSERT... VÁLASSZA KI A * ELEMET:

```sql
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```

## <a name="step-5-optional-migrate-stored-procedures"></a>5. lépés (nem kötelező): tárolt eljárások migrálása

A In-Memory funkció egy tárolt eljárást is módosíthat a jobb teljesítmény érdekében.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>A natív módon lefordított tárolt eljárásokkal kapcsolatos megfontolások

A natív módon lefordított tárolt eljárásoknak az alábbi beállításokkal kell rendelkezniük a T-SQL WITH záradékban:

* NATIVE_COMPILATION
* SCHEMABINDING: azokat a táblákat, amelyekben a tárolt eljárás nem módosítható az oszlop definíciói szerint, ami hatással lehet a tárolt eljárásra, hacsak nem dobja el a tárolt eljárást.

A natív modulnak egy Big [Atomic blokkot](/sql/relational-databases/in-memory-oltp/atomic-blocks-in-native-procedures) kell használnia a tranzakciók kezeléséhez. Explicit BEGIN TRANSACTION vagy VISSZAÁLLÍTÁSi tranzakció esetében nincs szerepkör. Ha a kód egy üzleti szabály megsértését észleli, az atomi blokkot egy [THROW](/sql/t-sql/language-elements/throw-transact-sql) utasítással vonhatja le.

### <a name="typical-create-procedure-for-natively-compiled"></a>Tipikus LÉTREHOZÁSi eljárás natív módon lefordítva

A natív módon lefordított tárolt eljárások létrehozása általában a T-SQL használatával történik a következő sablonhoz hasonló módon:

```sql
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* A TRANSACTION_ISOLATION_LEVEL a PILLANATKÉP a natív módon lefordított tárolt eljárás leggyakoribb értéke. Azonban a többi érték egy részhalmaza is támogatott:
  
  * ISMÉTELHETŐ OLVASÁS
  * SZERIALIZÁLHATÓ
* A nyelvi értéknek jelen kell lennie a sys. languages nézetben.

### <a name="how-to-migrate-a-stored-procedure"></a>Tárolt eljárás áttelepítésének módja

Az áttelepítési lépések a következők:

1. Szerezze be az eljárás létrehozása parancsfájlt a szabályos értelmezésű tárolt eljáráshoz.
2. Írja át a fejlécét az előző sablonnak megfelelően.
3. Győződjön meg arról, hogy a T-SQL-kód tárolt eljárása olyan funkciókat használ, amelyek nem támogatottak a natív módon lefordított tárolt eljárásokhoz. Szükség esetén hajtsa végre a megkerülő megoldásokat.

   Részletekért lásd: [a natív módon lefordított tárolt eljárások áttelepítési problémái](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables).
4. Nevezze át a régi tárolt eljárást SP_RENAME használatával. Vagy egyszerűen dobja el.
5. Futtassa a szerkesztett CREATE PROCEDURE T-SQL-szkriptet.

## <a name="step-6-run-your-workload-in-test"></a>6. lépés: a számítási feladatok futtatása a tesztben

Futtasson munkaterhelést a tesztelési adatbázisban, amely hasonló az éles adatbázisban futó munkaterheléshez. Ennek során meg kell jelennie a In-Memory funkció által a táblák és tárolt eljárások esetében elért teljesítménybeli nyereségnek.

A számítási feladatok fő attribútumai a következők:

* Az egyidejű kapcsolatok száma.
* Olvasási/írási arány.

A teszt munkaterhelések testreszabásához és futtatásához érdemes lehet használni a hasznos `ostress.exe` eszközt, amely ebben a [memóriában olvasható](in-memory-oltp-overview.md) .

A hálózati késés csökkentése érdekében futtassa a tesztet ugyanabban az Azure földrajzi régióban, ahol az adatbázis létezik.

## <a name="step-7-post-implementation-monitoring"></a>7. lépés: a megvalósítás utáni figyelés

Tekintse át a In-Memory megvalósításának teljesítménybeli hatásait az éles környezetben:

* [In-Memory tároló figyelése](in-memory-oltp-monitor-space.md).
* [Figyelés a dinamikus felügyeleti nézetek használatával](database/monitoring-with-dmvs.md)

## <a name="related-links"></a>Kapcsolódó hivatkozások

* [Memóriában tárolt OLTP (memórián belüli optimalizálás)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
* [A natív módon lefordított tárolt eljárások bemutatása](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables)
* [Memória-optimalizálási tanácsadó](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor)
