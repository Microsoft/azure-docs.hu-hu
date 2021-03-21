---
title: Azure SQL Database táblák migrálása az Azure CosmosDB-be Azure Data Factory
description: Készítsen egy meglévő normalizált adatbázis-sémát a Azure SQL Databaseból, és telepítse át az Azure-CosmosDB denormalizált tárolóba Azure Data Factory használatával.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: 3d67ac9474704fac39dbe7eb91aead5c4babc4ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383942"
---
# <a name="migrate-normalized-database-schema-from-azure-sql-database-to-azure-cosmosdb-denormalized-container"></a>Normalizált adatbázisséma migrálása Azure SQL Database-ből denormalizált Azure Cosmos DB-tárolóba

Ez az útmutató bemutatja, hogyan végezheti el a Azure SQL Database meglévő normalizált adatbázis-sémáját, és hogyan alakíthatja át egy Azure CosmosDB-re normalizált sémába az Azure CosmosDB való betöltéshez.

Az SQL-sémákat általában harmadik normál formában modellezik, így a normalizált sémák nagy mennyiségű adatintegritást és kevesebb ismétlődő adatértéket biztosítanak. A lekérdezések összekapcsolhatók az entitásokkal a táblák között az olvasáshoz. A CosmosDB egy-egy gyűjteményen vagy tárolón belüli, a dokumentumon belül található adatokkal rendelkező, elvégezhető, gyors tranzakciós és lekérdezésre optimalizált.

A Azure Data Factory használatával egy olyan folyamatot hozunk létre, amely egyetlen leképezési adatfolyamot használ két Azure SQL Database normalizált táblázatból való olvasásra, amely elsődleges és külső kulcsokat tartalmaz az entitási kapcsolatként. Az ADF egyetlen streambe fogja csatlakoztatni ezeket a táblákat az adatáramlási Spark motorral, összegyűjti a csatlakoztatott sorokat tömbökbe, és egyéni, tisztított dokumentumokat hoz létre az új Azure CosmosDB-tárolóba való beszúráshoz.

Ez az útmutató egy új, "Orders" nevű tárolót hoz létre, amely a ```SalesOrderHeader``` ```SalesOrderDetail``` standard SQL Server AdventureWorks-mintaadatbázis és a táblákat fogja használni. Ezek a táblák a által összekapcsolt értékesítési tranzakciókat jelölik ```SalesOrderID``` . Mindegyik egyedi részlet rekord saját elsődleges kulccsal rendelkezik ```SalesOrderDetailID``` . A fejléc és a részletek közötti kapcsolat ```1:M``` . Csatoljuk ```SalesOrderID``` Az ADF-ben, majd minden kapcsolódó részletes rekordot egy "detail" nevű tömbbe kell bemutatnia.

Az útmutatóhoz tartozó reprezentatív SQL-lekérdezés a következő:

```
  SELECT
  o.SalesOrderID,
  o.OrderDate,
  o.Status,
  o.ShipDate,
  o.SalesOrderNumber,
  o.ShipMethod,
  o.SubTotal,
  (select SalesOrderDetailID, UnitPrice, OrderQty from SalesLT.SalesOrderDetail od where od.SalesOrderID = o.SalesOrderID for json auto) as OrderDetails
FROM SalesLT.SalesOrderHeader o;
```

Az eredményül kapott CosmosDB-tároló egyetlen dokumentumba ágyazza be a belső lekérdezést, és így néz ki:

![Gyűjtemény](media/data-flow/cosmosb3.png)

## <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Új folyamat létrehozásához válassza az **+ új folyamat** elemet.

2. Adatfolyam-tevékenység hozzáadása

3. Az adatfolyam tevékenységben válassza az **új leképezési** adatfolyam lehetőséget.

4. Ezt az adatáramlási gráfot az alábbi grafikonon fogjuk létrehozni

![Adatfolyam-gráf](media/data-flow/cosmosb1.png)

5. Adja meg a "SourceOrderDetails" forrását. Adatkészlet esetén hozzon létre egy új Azure SQL Database adatkészletet, amely a ```SalesOrderDetail``` táblára mutat.

6. Adja meg a "SourceOrderHeader" forrását. Adatkészlet esetén hozzon létre egy új Azure SQL Database adatkészletet, amely a ```SalesOrderHeader``` táblára mutat.

7. A felső forrásnál adjon hozzá egy származtatott oszlopot a "SourceOrderDetails" után. Hívja meg az új "TypeCast" átalakítást. Az oszlopot fel kell kerekíteni ```UnitPrice``` , és egy dupla adattípusba kell CosmosDB. Állítsa be a képletet a következőre: ```toDouble(round(UnitPrice,2))``` .

8. Adjon hozzá egy másik származtatott oszlopot, és hívja meg a "MakeStruct" kifejezést. Itt hozunk létre egy hierarchikus struktúrát, amely az értékeket a részletek táblából fogja tárolni. Ne feledje, hogy a részletek a ```M:1``` fejléchez való kapcsolat. Nevezze el az új struktúrát, ```orderdetailsstruct``` és hozza létre a hierarchiát úgy, hogy az egyes aloszlopokat a bejövő oszlop nevére állítsa be:

![Struktúra létrehozása](media/data-flow/cosmosb9.png)

9. Most nyissa meg a Sales header forrását. Vegyen fel egy JOIN transzformációt. A jobb oldali válassza a "MakeStruct" lehetőséget. Ne állítsa belső illesztésre, és válassza az ```SalesOrderID``` illesztési feltétel mindkét oldalát.

10. Kattintson a hozzáadott új illesztés adatelőnézet fülére, hogy az eredmények megjelenjenek ebben a pontban. Ekkor meg kell jelennie az összes, a részletsorok összes sorához csatolt fejlécnek. Ennek az az oka, hogy az illesztés a alkalmazásból lett létrehozva ```SalesOrderID``` . Ezután egyesítjük a részleteket a közös sorokból a részletek struktúrába, és összesítjük a közös sorokat.

![Csatlakozás](media/data-flow/cosmosb4.png)

11. Mielőtt létrehoztuk a tömböket a sorok denormalizálása érdekében, először el kell távolítania a nemkívánatos oszlopokat, és győződjön meg arról, hogy az adatértékek megfelelnek a CosmosDB adattípusoknak.

12. Vegyen fel egy kiválasztott átalakítást, és a mező leképezése beállításnál adja meg a következőt:

![Oszlop súroló](media/data-flow/cosmosb5.png)

13. Most nézzük meg újra a pénznem oszlopot, ezúttal ```TotalDue``` . Mint a 7. lépésben, a képletet állítsa a következőre: ```toDouble(round(TotalDue,2))``` .

14. A sorokat a közös kulcs szerinti csoportosítással fogjuk denormalizálni ```SalesOrderID``` . Adjon hozzá egy összesített átalakítást, és állítsa be a csoportot a következőre: ```SalesOrderID``` .

15. Az aggregált képletben adjon hozzá egy új, "Részletek" nevű oszlopot, és használja ezt a képletet a korábban létrehozott struktúra értékeinek összegyűjtéséhez ```orderdetailsstruct``` : ```collect(orderdetailsstruct)``` .

16. Az összesített átalakítás csak az aggregált vagy a Group By képletek részét képező oszlopokat fogja kiadni. Ezért a Sales (értékesítés) fejlécből is meg kell adnia az oszlopokat. Ehhez adjon hozzá egy, az azonos összesített átalakításban szereplő oszlop mintát. Ez a minta a kimenet összes többi oszlopát is tartalmazza:

```instr(name,'OrderQty')==0&&instr(name,'UnitPrice')==0&&instr(name,'SalesOrderID')==0```

17. Használja a többi tulajdonság "this" szintaxisát, hogy ugyanazokat az oszlopokat használják, és a ```first()``` függvényt összesítve használja:

![Összesítés](media/data-flow/cosmosb6.png)

18. Készen áll az áttelepítési folyamat befejezésére egy fogadó átalakítás hozzáadásával. Az adatkészlet mellett kattintson az új elemre, és adjon hozzá egy CosmosDB adatkészletet, amely a CosmosDB-adatbázisra mutat. A gyűjteményhez "Orders" (megrendelések) kifejezést fogunk hívni, és nem fog sémát és dokumentumokat létrehozni, mert menet közben jön létre.

19. A fogadó beállításainál a partíció kulcs ```\SalesOrderID``` és gyűjtemény művelet "újbóli létrehozás" elemre. Győződjön meg arról, hogy a leképezés lap így néz ki:

![Képernyőfelvétel: a leképezés lapot jeleníti meg.](media/data-flow/cosmosb7.png)

20. Kattintson az adatelőnézet elemre, és győződjön meg arról, hogy ezek a 32-sorok úgy vannak beállítva, hogy új dokumentumként beszúrják az új tárolóba:

![A képernyőképen az adatelőnézet lap látható.](media/data-flow/cosmosb8.png)

Ha minden jól látható, most már készen áll egy új folyamat létrehozására, ezt az adatfolyam-tevékenységet hozzáadja a folyamathoz, és végrehajtja. Végrehajtható hibakeresésből vagy aktivált futtatásból. Néhány perc elteltével a CosmosDB-adatbázis "Orders" nevű új, denormalizált tárolóját kell használnia.

## <a name="next-steps"></a>Következő lépések

* Hozza létre a többi adatáramlási logikát az adatforgalom- [átalakítások](concepts-data-flow-overview.md)leképezése használatával.
* [Töltse le az oktatóanyaghoz tartozó befejezett folyamat sablont](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/SQL%20Orders%20to%20CosmosDB.zip) , és importálja a sablont a gyárba.
