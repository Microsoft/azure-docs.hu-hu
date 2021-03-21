---
title: A séma változásainak SQL-adatszinkronizálásban való replikálásának automatizálása
description: Ismerje meg, hogyan automatizálható a séma módosításainak replikálása az Azure SQL-adatszinkronizálásban.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/14/2018
ms.openlocfilehash: f9997ea737b96185b31a7f51996a396fb5fc46c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790185"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>A séma változásainak az Azure-ban való replikálásának automatizálása SQL-adatszinkronizálás
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

SQL-adatszinkronizálás lehetővé teszi a felhasználók számára az adatszinkronizálást Azure SQL Database és SQL Server példányok között egy irányban vagy mindkét irányban. SQL-adatszinkronizálás jelenlegi korlátozásai közül az egyik jelenleg nem támogatott a séma módosításainak replikálásához. Minden alkalommal, amikor módosítja a táblázat sémáját, manuálisan kell alkalmaznia a módosításokat az összes végponton, beleértve a hubot és az összes tagot, majd frissítenie kell a szinkronizálási sémát.

Ez a cikk egy olyan megoldást mutat be, amely automatikusan replikálja a séma módosításait az összes SQL-adatszinkronizálás-végpontra.

1. Ez a megoldás DDL-triggert használ a séma változásainak nyomon követéséhez.
1. Az trigger beszúrja a séma módosítása parancsokat egy követési táblába.
1. Ezt a nyomkövetési táblázatot a rendszer az adatszinkronizálási szolgáltatással szinkronizálja az összes végpontra.
1. A DML-triggerek a beillesztés után lesznek alkalmazva a séma módosítására a többi végponton.

Ez a cikk a MÓDOSÍTÁSi táblázatot használja példaként a séma módosítására, de ez a megoldás más típusú sémákban is működik.

> [!IMPORTANT]
> Javasoljuk, hogy figyelmesen olvassa el ezt a cikket, különösen a [hibaelhárítási](#troubleshoot) és [egyéb szempontokat](#other)ismertető szakaszt, mielőtt megkezdené az automatikus séma-módosítási replikáció megvalósítását a szinkronizálási környezetben. Azt is javasoljuk, hogy a [szinkronizálási információk több Felhőbeli és helyszíni adatbázison](sql-data-sync-data-sql-server-sql-database.md)is beolvashatók legyenek SQL-adatszinkronizálás. Egyes adatbázis-műveletek megszüntetik a jelen cikkben ismertetett megoldást. A problémák elhárításához szükség lehet a SQL Server és a Transact-SQL további tartományi ismeretére.

![A séma módosításainak replikálásának automatizálása](./media/sql-data-sync-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Automatikus séma-módosítás replikálásának beállítása

### <a name="create-a-table-to-track-schema-changes"></a>Táblázat létrehozása a séma változásainak nyomon követéséhez

Táblázat létrehozása a séma változásainak nyomon követéséhez a szinkronizálási csoportban lévő összes adatbázisban:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Ennek a táblának van egy azonosító oszlopa, amely nyomon követheti a séma változásainak sorrendjét. Ha szükséges, további mezőket adhat hozzá a további információk naplózásához.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Táblázat létrehozása a séma változásainak előzményeinek nyomon követéséhez

Az összes végponton hozzon létre egy táblázatot a legutóbb alkalmazott séma-módosítási parancs AZONOSÍTÓjának nyomon követéséhez.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Hozzon létre egy ALTER TABLE DDL-triggert abban az adatbázisban, amelyben a séma módosítása történik

DDL-trigger létrehozása az ALTER TABLE műveletekhez. Ezt az triggert csak abban az adatbázisban kell létrehoznia, ahol a séma megváltozik. Az ütközések elkerülése érdekében csak a séma módosításait engedélyezze egy szinkronizálási csoportban lévő adatbázison.

```sql
CREATE TRIGGER AlterTableDDLTrigger
ON DATABASE
FOR ALTER_TABLE
AS

-- You can add your own logic to filter ALTER TABLE commands instead of replicating all of them.

IF NOT (EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'nvarchar(512)') like 'DataSync')

INSERT INTO SchemaChanges (SqlStmt, Description)
    VALUES (EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)'), 'From DDL trigger')
```

Az trigger beszúr egy rekordot a séma módosítása követési táblába az egyes ALTER TABLE parancsokhoz. Ez a példa egy szűrőt hoz létre, amellyel elkerülhető a séma- **DataSync** alatt végzett séma-módosítások replikálása, mivel ezeket az adatszinkronizálási szolgáltatás legvalószínűbben végzi. Ha csak bizonyos típusú sémákat szeretne replikálni, vegyen fel további szűrőket.

További eseményindítókat is hozzáadhat a séma más típusú változásainak replikálásához. Hozzon létre például CREATE_PROCEDURE, ALTER_PROCEDURE és DROP_PROCEDURE eseményindítókat a tárolt eljárások módosításainak replikálásához.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Trigger létrehozása más végpontokon a séma módosításának a Beszúrás során történő alkalmazásához

Ez az indító a séma módosítása parancsot hajtja végre, ha más végpontokhoz van szinkronizálva. Ezt az triggert az összes végponton létre kell hoznia, kivéve a séma módosításának helyét (azaz abban az adatbázisban, amelyben az előző lépésben létrehozta a DDL-triggert `AlterTableDDLTrigger` ).

```sql
CREATE TRIGGER SchemaChangesTrigger
ON SchemaChanges
AFTER INSERT
AS
DECLARE \@lastAppliedId bigint
DECLARE \@id bigint
DECLARE \@sqlStmt nvarchar(max)
SELECT TOP 1 \@lastAppliedId=LastAppliedId FROM SchemaChangeHistory
SELECT TOP 1 \@id = id, \@SqlStmt = SqlStmt FROM SchemaChanges WHERE id \> \@lastAppliedId ORDER BY id
IF (\@id = \@lastAppliedId + 1)
BEGIN
    EXEC sp_executesql \@SqlStmt
        UPDATE SchemaChangeHistory SET LastAppliedId = \@id
    WHILE (1 = 1)
    BEGIN
        SET \@id = \@id + 1
        IF exists (SELECT id FROM SchemaChanges WHERE ID = \@id)
            BEGIN
                SELECT \@sqlStmt = SqlStmt FROM SchemaChanges WHERE ID = \@id
                EXEC sp_executesql \@SqlStmt
                UPDATE SchemaChangeHistory SET LastAppliedId = \@id
            END
        ELSE
            BREAK;
    END
END
```

Ez az aktiválás a Beszúrás után fut, és ellenőrzi, hogy az aktuális parancsnak a következőnek kell futnia. A kód logikája biztosítja, hogy a rendszer ne ugorja át a séma módosításait, és a rendszer az összes módosítást alkalmazza, még akkor is, ha a Beszúrás nem sorrendben történik.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>A séma változás-követési táblázatának szinkronizálása az összes végponttal

A séma módosítása nyomon követése táblát az összes végpontra szinkronizálhatja a meglévő szinkronizálási csoport vagy egy új szinkronizálási csoport használatával. Győződjön meg arról, hogy a követési tábla változásai szinkronizálva vannak az összes végponttal, különösen akkor, ha egyirányú szinkronizálást használ.

Ne szinkronizálja a séma változási előzményei táblát, mert a tábla különböző állapotokat tart fenn különböző végpontokon.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>A séma módosításainak alkalmazása szinkronizálási csoportban

A rendszer csak a DDL-triggert létrehozó adatbázisban lévő séma-módosításokat replikálja. A más adatbázisokban végrehajtott sémák módosítása nem replikálódik.

Miután a séma módosítása replikálódik az összes végpontra, további lépéseket kell tennie a szinkronizálási séma frissítéséhez az új oszlopok szinkronizálásának elindításához vagy leállításához.

#### <a name="add-new-columns"></a>Új oszlopok hozzáadása

1.  Állítsa be a séma módosítását.

1.  Kerülje az adatváltozások helyét, ahol az új oszlopok bekerülnek, amíg el nem végezte az triggert létrehozó lépést.

1.  Várjon, amíg a séma módosításai az összes végpontra érvénybe lépnek.

1.  Frissítse az adatbázis-sémát, és adja hozzá az új oszlopot a szinkronizálási sémához.

1.  A következő szinkronizálási művelet során a rendszer szinkronizálja az új oszlopba tartozó adatfájlokat.

#### <a name="remove-columns"></a>Oszlopok eltávolítása

1.  Távolítsa el az oszlopokat a szinkronizálási sémából. Az adatszinkronizálás leállítja az adatszinkronizálást ezekben az oszlopokban.

1.  Állítsa be a séma módosítását.

1.  Frissítse az adatbázis-sémát.

#### <a name="update-data-types"></a>Adattípusok frissítése

1.  Állítsa be a séma módosítását.

1.  Várjon, amíg a séma módosításai az összes végpontra érvénybe lépnek.

1.  Frissítse az adatbázis-sémát.

1.  Ha az új és a régi adattípusok nem teljes mértékben kompatibilisek – például akkor, ha a verzióról `int` `bigint` szinkronizálásra vált, a triggerek létrehozásához szükséges lépések végrehajtása előtt sikertelen lehet. A szinkronizálás az újrapróbálkozást követően sikeres lesz.

#### <a name="rename-columns-or-tables"></a>Oszlopok vagy táblák átnevezése

Az oszlopok vagy táblázatok átnevezése lehetővé teszi az adatszinkronizálás leállását. Hozzon létre egy új táblázatot vagy oszlopot, backfill az adattípust, majd törölje a régi táblát vagy oszlopot az Átnevezés helyett.

#### <a name="other-types-of-schema-changes"></a>Egyéb típusú séma-változások

Más típusú sémák változásaihoz – például tárolt eljárások létrehozása vagy index eldobása – a szinkronizálási séma frissítése nem szükséges.

## <a name="troubleshoot-automated-schema-change-replication"></a><a name="troubleshoot"></a> Automatikus séma-módosítási replikáció hibáinak megoldása

A cikkben ismertetett replikációs logika bizonyos helyzetekben leáll – például ha olyan helyszíni adatbázisban hajtott végre sémát, amely Azure SQL Database nem támogatott. Ebben az esetben a séma módosításának követése tábla szinkronizálása sikertelen. A problémát manuálisan kell kijavítani:

1.  Tiltsa le a DDL-triggert, és kerülje a további sémák módosításait a probléma javítása előtt.

1.  Az Endpoint adatbázisban, ahol a probléma történik, tiltsa le a Beszúrás utáni triggert azon a végponton, ahol a séma módosítása nem végezhető el. Ez a művelet lehetővé teszi a séma módosítási parancsának szinkronizálását.

1.  Indítsa el a szinkronizálást a séma módosításának követése tábla szinkronizálásához.

1.  A probléma eseményének végpont-adatbázisában kérdezze le a séma változási előzményei táblát a legutóbbi alkalmazott séma-módosítási parancs AZONOSÍTÓjának lekéréséhez.

1.  Az előző lépésben lekért azonosító értéknél nagyobb AZONOSÍTÓval rendelkező összes parancs listázása a séma módosításának nyomon követése táblában.

    a.  Hagyja figyelmen kívül azokat a parancsokat, amelyek nem hajthatók végre az Endpoint adatbázisban. A séma inkonzisztenciát kell kezelnie. Visszaállíthatja az eredeti séma módosításait, ha az inkonzisztencia hatással van az alkalmazásra.

    b.  Alkalmazza manuálisan ezeket a parancsokat.

1.  Frissítse a séma változási előzményei táblát, és állítsa be a legutolsó alkalmazott azonosítót a megfelelő értékre.

1.  Duplán ellenőrizze, hogy a séma naprakész-e.

1.  Engedélyezze újra a következő BESZÚRÁSi triggert a második lépésben.

1.  Engedélyezze újra az első lépésben letiltott DDL-triggert.

Ha meg szeretné tisztítani a rekordokat a séma módosításainak nyomon követése táblában, a CSONKÍTás helyett a TÖRLÉSt kell használnia. DBCC CHECKIDENT használatával soha ne használja újra a séma-módosítás követése tábla Identity oszlopát. Új séma-módosítási nyomon követési táblákat hozhat létre, és frissítheti a táblázat nevét a DDL-triggerben, ha újravetés szükséges.

## <a name="other-considerations"></a><a name="other"></a> Egyéb megfontolások

-   Az olyan adatbázis-felhasználóknak, akik a központi és a tagok adatbázisait konfigurálták, elegendő engedélyekkel kell rendelkezniük a séma-módosítási parancsok végrehajtásához.

-   A DDL-triggerben további szűrőket adhat hozzá, hogy csak a kiválasztott táblákban vagy műveletekben replikálja a séma módosításait.

-   A séma módosításait csak abban az adatbázisban végezheti el, amelyben a DDL-trigger létrejött.

-   Ha módosítást végez egy SQL Server adatbázisban, győződjön meg arról, hogy a séma módosítása támogatott a Azure SQL Databaseban.

-   Ha a séma módosítása a DDL-triggert létrehozó adatbázison kívül más adatbázisokon történik, a módosítások nem replikálódnak. A probléma elkerüléséhez DDL-eseményindítókat hozhat létre a többi végpont változásainak blokkolásához.

-   Ha módosítania kell a séma módosítása követése tábla sémáját, tiltsa le a DDL-triggert a módosítás előtt, majd manuálisan alkalmazza a módosítást az összes végpontra. Ha a sémát egy, az adott táblába való BESZÚRÁSi trigger után frissíti, nem működik.

-   Ne használja az Identity oszlopot DBCC CHECKIDENT használatával.

-   Ne használja a CSONKÍTás lehetőséget a séma-változások követése táblában lévő adattörléshez.

## <a name="next-steps"></a>Következő lépések

További információ az SQL Data Syncről:

-   Áttekintés – az [adatszinkronizálás több felhőalapú és helyszíni adatbázis között az Azure SQL-adatszinkronizálás](sql-data-sync-data-sql-server-sql-database.md)
-   Adatszinkronizálás beállítása
    - A portálon – [oktatóanyag: SQL-adatszinkronizálás beállítása az Azure SQL Database és a SQL Server közötti adatszinkronizáláshoz](sql-data-sync-sql-server-configure.md)
    - A PowerShell-lel
        -  [Több adatbázis közötti szinkronizálás a Azure SQL Database-ben a PowerShell használatával](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Azure SQL Database és egy SQL Server-példányban található adatbázis közötti szinkronizálás a PowerShell használatával](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Adatszinkronizálási ügynök – [Az Azure SQL-adatszinkronizálás adatszinkronizálási ügynöke](sql-data-sync-agent-overview.md)
-   Ajánlott eljárások – [ajánlott eljárások az Azure SQL-adatszinkronizálás](sql-data-sync-best-practices.md)
-   Figyelő – [SQL-adatszinkronizálás figyelése Azure monitor naplókkal](./monitor-tune-overview.md)
-   Hibaelhárítás – [Az Azure SQL-adatszinkronizálás hibáinak elhárítása]()
-   A szinkronizálási séma frissítése
    -   A PowerShell [használatával – egy meglévő szinkronizálási csoportban lévő szinkronizálási séma frissítéséhez használja a PowerShellt](scripts/update-sync-schema-in-sync-group.md) .