---
title: Tranzakciók használata az Azure szinapszis Analytics SQL-készletben
description: Ez a cikk tippeket tartalmaz a tranzakciók megvalósításához és megoldások fejlesztéséhez a szinapszis SQL-készletben.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/22/2019
ms.author: xiaoyul
ms.custom: azure-synapse
ms.reviewer: igorstan
ms.openlocfilehash: 8144c588d4b6794cadc0577bf63dabc2cc3e0efd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98677286"
---
# <a name="use-transactions-in-a-sql-pool-in-azure-synapse"></a>SQL-készletben lévő tranzakciók használata az Azure Szinapszisban 

Ez a cikk tippeket tartalmaz a tranzakciók megvalósításához és a megoldások SQL-készletben való fejlesztéséhez.

## <a name="what-to-expect"></a>Amire számíthat

Ahogy várható, az SQL-készlet az adatraktár számítási feladatainak részeként támogatja a tranzakciókat. Az SQL-készlet méretezésének biztosítása érdekében azonban bizonyos szolgáltatások a SQL Serverhoz képest korlátozottak. Ez a cikk kiemeli a különbségeket.

## <a name="transaction-isolation-levels"></a>Tranzakciók elkülönítési szintjei

Az SQL-készlet savas tranzakciókat valósít meg. A tranzakciós támogatás elkülönítési szintje alapértelmezés szerint nem VÉGLEGESÍThető.  Ha a Master adatbázishoz csatlakozik, a felhasználói SQL-készlet READ_COMMITTED_SNAPSHOT adatbázis lehetőségének bekapcsolásával módosíthatja az előjegyzett PILLANATKÉPek ELKÜLÖNÍTÉSét.  

Ha engedélyezve van, a rendszer az ebben az adatbázisban lévő összes tranzakciót az OLVASÁSI véglegesített PILLANATKÉPek ELKÜLÖNÍTÉSe területen hajtja végre, és a munkamenet szintjén az olvasás VÉGLEGESÍTÉSe beállítást nem fogja figyelembe venni. A részletekért lásd az [Alter Database set Options (Transact-SQL) beállítást](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

## <a name="transaction-size"></a>Tranzakció mérete

Egyetlen adatmódosítási tranzakció mérete korlátozott. A korlátot a rendszer eloszlás alapján alkalmazza. Ezért a teljes foglalás kiszámításához a korlátot a terjesztési szám alapján kell megszorozni.

Annak érdekében, hogy a tranzakció sorainak maximális száma megközelítse a terjesztési korlátot az egyes sorok teljes méretével. A változó hosszúságú oszlopok esetében érdemes lehet átlagos oszlopszélességet használni a maximális méret helyett.

A következő táblázatban két feltételezés történt:

* Az adatforgalom egyenletes eloszlása történt
* Az átlagos sor hossza 250 bájt.

## <a name="gen2"></a>Gen2

| [DWU](./sql-data-warehouse-overview-what-is.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) | Korlát/elosztás (GB) | Eloszlások száma | Tranzakciók maximális mérete (GB) | Sorok száma eloszlás szerint | Sorok másodpercenkénti maximális száma |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4 000 000 |240 000 000 |
| DW200c |1.5 |60 |90 |6,000,000 |360 000 000 |
| DW300c |2.25 |60 |135 |9 000 000 |540 000 000 |
| DW400c |3 |60 |180 |12 000 000 |720 000 000 |
| DW500c lehetőséget |3,75 |60 |225 |15 000 000 |900 000 000 |
| DW1000c |7,5 |60 |450 |30 000 000 |1 800 000 000 |
| DW1500c |11,25 |60 |675 |45 000 000 |2 700 000 000 |
| DW2000c |15 |60 |900 |60 000 000 |3 600 000 000 |
| DW2500c |18,75 |60 |1125 |75 000 000 |4 500 000 000 |
| DW3000c |22,5 |60 |1 350 |90 000 000 |5 400 000 000 |
| DW5000c |37,5 |60 |2 250 |150 000 000 |9 000 000 000 |
| DW6000c |45 |60 |2 700 |180 000 000 |10 800 000 000 |
| DW7500c |56,25 |60 |3 375 |225 000 000 |13 500 000 000 |
| DW10000c |75 |60 |4.500 |300,000,000 |18 000 000 000 |
| DW15000c |112,5 |60 |6750 |450 000 000 |27 000 000 000 |
| DW30000c |225 |60 |13 500 |900 000 000 |54 000 000 000 |

## <a name="gen1"></a>Gen1

| [DWU](./sql-data-warehouse-overview-what-is.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) | Korlát/elosztás (GB) | Eloszlások száma | Tranzakciók maximális mérete (GB) | Sorok száma eloszlás szerint | Sorok másodpercenkénti maximális száma |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4 000 000 |240 000 000 |
| DW200 |1.5 |60 |90 |6,000,000 |360 000 000 |
| DW300 |2.25 |60 |135 |9 000 000 |540 000 000 |
| DW400 |3 |60 |180 |12 000 000 |720 000 000 |
| DW500 |3,75 |60 |225 |15 000 000 |900 000 000 |
| KOR DW600 |4,5 |60 |270 |18 000 000 |1 080 000 000 |
| DW1000 |7,5 |60 |450 |30 000 000 |1 800 000 000 |
| DW1200 |9 |60 |540 |36 000 000 |2 160 000 000 |
| DW1500 |11,25 |60 |675 |45 000 000 |2 700 000 000 |
| DW2000 |15 |60 |900 |60 000 000 |3 600 000 000 |
| DW3000 |22,5 |60 |1 350 |90 000 000 |5 400 000 000 |
| DW6000 |45 |60 |2 700 |180 000 000 |10 800 000 000 |

A tranzakció méretének korlátját tranzakció vagy művelet alapján kell alkalmazni. A rendszer nem alkalmazza az összes egyidejű tranzakcióra. Ezért minden tranzakció számára engedélyezett az adatmennyiség megírása a naplóba.

A naplóba írt adatmennyiség optimalizálása és minimálisra csökkentése érdekében tekintse meg a [tranzakciók ajánlott eljárásait](sql-data-warehouse-develop-best-practices-transactions.md) ismertető cikket.

> [!WARNING]
> A tranzakciók maximális mérete csak olyan KIVONATOLÓ vagy ROUND_ROBIN elosztott táblák esetében érhető el, amelyekben még az adateloszlás is látható. Ha a tranzakció elferdített módon ír a disztribúcióba, akkor a maximális tranzakció mérete előtt a korlát valószínűleg eléri a határértéket.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Tranzakció állapota

Az SQL-készlet a XACT_STATE () függvényt használja a sikertelen tranzakciók jelentésére a-2 érték használatával. Ez az érték azt jelenti, hogy a tranzakció meghiúsult, és csak visszaállításra van megjelölve.

> [!NOTE]
> A (2) XACT_STATE függvény használata a sikertelen tranzakciók jelölésére a SQL Server eltérő viselkedését jelöli. A SQL Server a-1 érték használatával nem véglegesíthető tranzakciót jelöl. A SQL Server egy tranzakción belül bizonyos hibákat el lehet viselni anélkül, hogy nem véglegesíthető jelöléssel kellene megjelölni. Például `SELECT 1/0` hibát okozhat, de nem kényszerítheti a tranzakciót nem véglegesíthető állapotba.

A SQL Server a nem véglegesíthető tranzakcióban is engedélyezi a beolvasást. Az SQL-készlet azonban nem teszi lehetővé. Ha egy SQL Pool-tranzakción belül hiba történik, a rendszer automatikusan megadja a-2 állapotot, és nem fog tudni további kiválasztási utasításokat készíteni, amíg az utasítás vissza nem áll.

Ezért fontos, hogy az alkalmazás kódjában ellenőrizze, hogy az XACT_STATE () protokollt használja-e, mivel lehetséges, hogy programkódot kell módosítania.

SQL Server például a következőhöz hasonló tranzakciót láthat:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Az előző kód a következő hibaüzenetet jeleníti meg:

Msg 111233, 16. szint, állapot 1, sor 1 111233; Az aktuális tranzakció meg lett szakítva, és a függőben lévő módosítások vissza lettek állítva. Ennek a hibának az az oka, hogy egy csak visszaállítási állapotban lévő tranzakció nem állítható vissza explicit módon a DDL, a DML vagy a SELECT utasítás előtt.

A ERROR_ * függvények kimenete nem jelenik meg.

Az SQL-készletben a kódot kis mértékben módosítani kell:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

A várt viselkedés már meg van figyelve. A tranzakció hibája felügyelt, és a ERROR_ * functions a várt módon adja meg az értékeket.

Az összes módosult, hogy a tranzakció visszagörgetése még azelőtt történt, hogy megtörténjen a hiba információinak olvasása a CATCH blokkban.

## <a name="error_line-function"></a>Error_Line () függvény

Azt is érdemes megjegyezni, hogy az SQL-készlet nem implementálja és nem támogatja a ERROR_LINE () függvényt. Ha rendelkezik a kóddal, akkor azt el kell távolítania, hogy az megfeleljen az SQL-készletnek.

Az egyenértékű funkciók megvalósítása helyett használja a kódban a lekérdezési címkéket. További részletekért tekintse meg a [címkét](sql-data-warehouse-develop-label.md) ismertető cikket.

## <a name="using-throw-and-raiserror"></a>A THROW és a RAISERROR használata

Az SQL-készletben a kivételek növelésének modern implementációja, de a RAISERROR is támogatott. Van néhány eltérés, amely azonban érdemes odafigyelni.

* A felhasználó által definiált hibaüzenetek száma nem lehet az 100 000-150 000 tartományban a THROW
* A RAISERROR-hibaüzenetek a következő időpontban vannak kijavítva: 50 000
* A sys. messages használata nem támogatott

## <a name="limitations"></a>Korlátozások

Az SQL-készletnek van néhány más korlátozása, amely a tranzakcióhoz kapcsolódik.

Ezek a következők:

* Nincsenek elosztott tranzakciók
* Nincsenek engedélyezett beágyazott tranzakciók
* Nincsenek engedélyezett mentési pontok
* Nincsenek elnevezett tranzakciók
* Nincsenek megjelölt tranzakciók
* Nem támogatott a DDL, például a CREATE TABLE egy felhasználó által definiált tranzakción belül

## <a name="next-steps"></a>Következő lépések

A tranzakciók optimalizálásával kapcsolatos további tudnivalókért tekintse meg a [tranzakciók ajánlott eljárásai](sql-data-warehouse-develop-best-practices-transactions.md)című témakört. További információ az SQL-készletek egyéb ajánlott eljárásairól: [SQL Pool – ajánlott eljárások](sql-data-warehouse-best-practices.md).