---
title: Kiszolgáló nélküli SQL-készlet költségkezelése
description: Ez a dokumentum ismerteti, hogyan kezelheti a kiszolgáló nélküli SQL-készlet költségeit és a feldolgozott adatmennyiséget az Azure Storage-ban tárolt adatlekérdezés során.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8a26f8ced5e91810f8cadff0a27796dc817e6517
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491569"
---
# <a name="cost-management-for-serverless-sql-pool-in-azure-synapse-analytics"></a>A kiszolgáló nélküli SQL-készlet Cost Management az Azure szinapszis Analyticsben

Ez a cikk azt ismerteti, hogyan lehet megbecsülni és kezelni a kiszolgáló nélküli SQL-készlet költségeit az Azure szinapszis Analyticsben:
- A lekérdezés kiadása előtt feldolgozott adat becsült mennyisége
- A Cost Control szolgáltatás használata a költségvetés beállításához

Ismerje meg, hogy az Azure szinapszis Analytics kiszolgáló nélküli SQL-készletének költségei csak a havi költségek egy részét jelentik az Azure-számlán. Ha más Azure-szolgáltatásokat használ, akkor az Azure-előfizetésében használt összes Azure-szolgáltatás és erőforrás után, beleértve a harmadik féltől származó szolgáltatásokat is. Ez a cikk bemutatja, hogyan tervezheti meg és kezelheti a kiszolgáló nélküli SQL-készlet költségeit az Azure szinapszis Analytics szolgáltatásban.

## <a name="data-processed"></a>Feldolgozott adatok

A *feldolgozott* adatmennyiség a rendszer által ideiglenesen a lekérdezés futtatása közben tárolt adatmennyiség. A feldolgozott adatmennyiség a következő mennyiségekből áll:

- A tárterületről beolvasott adatok mennyisége. Ez az összeg a következőket tartalmazza:
  - Az adatgyűjtés során beolvasott adatolvasások.
  - Adatok olvasása a metaadatok olvasása közben (metaadatokat, például a parketta-t tartalmazó fájlformátumok esetében).
- A közbenső eredményekben lévő adatmennyiség. Ezeket az adatátvitelt a rendszer a csomópontok között a lekérdezés futtatása közben továbbítja. Ez magában foglalja a végpontra irányuló adatátvitelt tömörítetlen formátumban. 
- A tárterületre írt adatmennyiség. Ha a CETAS-t használja az eredményhalmaz tárolóba való exportálásához, akkor a rendszer a CETAS kiválasztása résznél a feldolgozott adatmennyiséget adja hozzá a feldolgozott adatmennyiséghez.

A fájlok a tárterületről való olvasása igen optimalizált. A folyamat a következőket használja:

- A beolvasás, amely némi terhelést eredményezhet az olvasott adatmennyiséghez képest. Ha egy lekérdezés teljes fájlt olvas, akkor nincs terhelés. Ha a fájlok részben olvashatók, például az első N lekérdezésekben, akkor egy kicsit több adat is olvasható a beolvasás használatával.
- Egy optimalizált vesszővel tagolt (CSV) elemző. Ha PARSER_VERSION = "2.0"-t használ a CSV-fájlok olvasásához, akkor a tárterületről beolvasott adatok mennyisége némileg megnő. Az optimalizált CSV-elemző párhuzamosan olvassa be a fájlokat, az azonos méretű adattömbökben. Az adattömbök nem feltétlenül tartalmaznak egész sort. Az összes sor elemzésének biztosítása érdekében az optimalizált CSV-elemző a szomszédos adattömbök kis töredékeit is beolvassa. Ez a folyamat kis mennyiségű terhelést eredményez.

## <a name="statistics"></a>Statisztika

A kiszolgáló nélküli SQL Pool lekérdezés-optimalizáló a statisztikára támaszkodik az optimális lekérdezés-végrehajtási tervek létrehozásához. A statisztikákat manuálisan is létrehozhatja. Ellenkező esetben a kiszolgáló nélküli SQL-készlet automatikusan létrehozza azokat. Mindkét esetben a statisztikák egy külön lekérdezés futtatásával jönnek létre, amely egy megadott mintavételi arányban ad vissza egy adott oszlopot. Ez a lekérdezés a feldolgozott adatmennyiséggel rendelkezik.

Ha ugyanazt vagy bármely olyan lekérdezést futtat, amely kihasználhatja a létrehozott statisztikai adatokat, akkor a statisztikákat a rendszer újból felhasználja, ha lehetséges. Nincs további feldolgozott adat a statisztikák létrehozásához.

Amikor statisztikákat hoznak létre egy Parquet oszlophoz, csak a megfelelő oszlop olvasható a fájlokból. Egy CSV-oszlop statisztikáinak létrehozásakor a teljes fájlok beolvasása és elemzése történik.

## <a name="rounding"></a>Kerekítés

A feldolgozott adatmennyiséget a rendszer a legközelebbi MB/s értékre kerekíti. Minden lekérdezés legalább 10 MB feldolgozott adatmennyiséget tartalmaz.

## <a name="what-data-processed-doesnt-include"></a>A feldolgozott adatfeldolgozási szolgáltatás nem tartalmazza

- Kiszolgálói szintű metaadatok (például bejelentkezések, szerepkörök és kiszolgálói szintű hitelesítő adatok).
- A végpontban létrehozott adatbázisok. Ezek az adatbázisok csak metaadatokat tartalmaznak (például felhasználók, szerepkörök, sémák, nézetek, beágyazott tábla értékű függvények [TVFs], tárolt eljárások, adatbázis-hatókörű hitelesítő adatok, külső adatforrások, külső fájlformátumok és külső táblák).
  - Ha séma-következtetést használ, akkor a rendszer beolvassa a fájlok töredékeit az oszlopnevek és az adattípusok kikövetkeztetve, és a feldolgozott adatmennyiséghez hozzáadja az olvasott adatmennyiséget.
- Adatdefiníciós nyelvi (DDL) utasítások, kivéve a CREATE STATISTICS utasítást, mert a megadott minta százalék alapján dolgozza fel az adatokat a tárolóból.
- Csak metaadat-lekérdezések.

## <a name="reducing-the-amount-of-data-processed"></a>A feldolgozott adatmennyiség csökkentése

Optimalizálhatja a feldolgozott adatmennyiséget, és javíthatja a teljesítményt, ha particionálja és átalakítja az adatait egy tömörített, oszlop alapú formátumba, például a parketta használatára.

## <a name="examples"></a>Példák

Képzelje el a három táblázatot.

- A population_csv táblát 5 TB-os CSV-fájl támogatja. A fájlok öt egyenlő méretű oszlopba vannak rendezve.
- A population_parquet tábla ugyanazokkal az értékekkel rendelkezik, mint a population_csv táblázat. A biztonsági mentés 1 TB-os parketta-fájlokból áll. Ez a tábla kisebb, mint az előző, mert az adattömörítési formátuma egy parketta.
- A very_small_csv táblát 100 KB-os CSV-fájl támogatja.

**1. lekérdezés**: a Sum (populáció) kiválasztása population_csv

Ez a lekérdezés teljes fájlokat olvas és elemez a sokaság oszlop értékeinek lekéréséhez. A csomópontok feldolgozzák ennek a táblának a töredékeit, és az egyes töredékek populációs összegét a rendszer a csomópontok között továbbítja. A végső összeg a végpontra kerül. 

Ez a lekérdezés 5 TB-nyi adat feldolgozását és kis mennyiségű terhelést dolgoz fel a töredékek összegének átadásához.

**2. lekérdezés**: válassza a Sum (populáció) elemet population_parquet

Ha tömörített és oszlopos formátumokat (például a Parquet) kérdez le, a rendszer kevesebb adatmennyiséget olvas be, mint az 1. lekérdezésben. Ez az eredmény jelenik meg, mert a kiszolgáló nélküli SQL-készlet egyetlen tömörített oszlopot olvas a teljes fájl helyett. Ebben az esetben a 0,2 TB olvasható. (Öt egyenlő méretű oszlop 0,2 TB.) A csomópontok feldolgozzák ennek a táblának a töredékeit, és az egyes töredékek populációs összegét a rendszer a csomópontok között továbbítja. A végső összeg a végpontra kerül. 

Ez a lekérdezés 0,2 TB-ot és kis mennyiségű terhelést dolgoz fel a töredékek összegének átadásához.

**3. lekérdezés**: select * from population_parquet

Ez a lekérdezés beolvassa az összes oszlopot, és az összes adatokat tömörítetlen formátumban továbbítja. Ha a tömörítési formátum 5:1, akkor a lekérdezés 6 TB-ot dolgoz fel, mert 1 TB-ot olvas, és 5 TB tömörítetlen adatokat továbbít.

**4. lekérdezés**: válassza a Count (*) elemet very_small_csv

Ez a lekérdezés teljes fájlokat olvas be. A tábla tárolási fájljainak teljes mérete 100 KB. A csomópontok feldolgozzák ennek a táblának a töredékeit, és az egyes töredékek összegét a csomópontok között továbbítjuk. A végső összeg a végpontra kerül. 

Ez a lekérdezés valamivel több mint 100 KB adatmennyiséget dolgoz fel. A lekérdezésben feldolgozott adatmennyiség 10 MB-ra van kerekítve, a jelen cikk [kerekítési](#rounding) szakaszában megadott módon.

## <a name="cost-control"></a>Cost Control

A kiszolgáló nélküli SQL-készlet Cost Control funkciója lehetővé teszi a költségvetés beállítását a feldolgozott adatmennyiség tekintetében. Megadhatja, hogy egy adott nap, hét és hónap után milyen mennyiségű adat legyen feldolgozva a TB-ban. Egy vagy több költségvetést is beállíthat. A kiszolgáló nélküli SQL-készlet költséghatékonyságának konfigurálásához használhatja a szinapszis studiót vagy a T-SQL-T.

## <a name="configure-cost-control-for-serverless-sql-pool-in-synapse-studio"></a>A kiszolgáló nélküli SQL-készlet költséghatékonyságának konfigurálása a szinapszis Studióban
 
A kiszolgáló nélküli SQL-készletre vonatkozó Cost Control konfigurálása a szinapszis Studióban navigáljon a bal oldali menüben található elem kezelésére, mint az SQL Pool elem kiválasztása az elemzési készletek területen. Amikor a kiszolgáló nélküli SQL-készletre mutat, megjelenik a Cost Control ikonja – kattintson erre az ikonra.

![Cost Control – navigáció](./media/data-processed/cost-control-menu.png)

A Cost Control ikonra kattintva megjelenik egy oldalsó sáv:

![Cost Control-konfiguráció](./media/data-processed/cost-control-sidebar.png)

Egy vagy több költségvetés beállításához először kattintson a beállítani kívánt költségvetéshez tartozó választógomb engedélyezése gombra, a szövegmezőbe írja be az egész értéket. Az érték egysége a TBs. Miután konfigurálta a kívánt költségvetést, kattintson az Apply (alkalmaz) gombra az oldalsó sáv alján. Ekkor már be van állítva a költségkeret.

## <a name="configure-cost-control-for-serverless-sql-pool-in-t-sql"></a>A kiszolgáló nélküli SQL-készlethez tartozó Cost Control konfigurálása a T-SQL-ben

Ha a T-SQL-ben a kiszolgáló nélküli SQL-készlethez tartozó költséghatékonyságot szeretné beállítani, a következő tárolt eljárások közül egyet vagy többet kell végrehajtania.

```sql
sp_set_data_processed_limit
    @type = N'daily',
    @limit_tb = 1

sp_set_data_processed_limit
    @type= N'weekly',
    @limit_tb = 2

sp_set_data_processed_limit
    @type= N'monthly',
    @limit_tb = 3334
```

Az aktuális konfiguráció megtekintéséhez hajtsa végre a következő T-SQL-utasítást:

```sql
SELECT * FROM sys.configurations
WHERE name like 'Data processed %';
```

Ha szeretné megtekinteni, hogy mennyi adat lett feldolgozva az aktuális nap, hét vagy hónap során, hajtsa végre a következő T-SQL-utasítást:

```sql
SELECT * FROM sys.dm_external_data_processed
```

## <a name="next-steps"></a>Következő lépések

A teljesítményre vonatkozó lekérdezések optimalizálásával kapcsolatos további információkért lásd: [ajánlott eljárások kiszolgáló nélküli SQL-készlethez](best-practices-sql-on-demand.md).
