---
title: Átmeneti hibák elhárítása
description: Megtudhatja, hogyan lehet elhárítani, diagnosztizálni és megakadályozni egy SQL-csatlakozási hibát vagy átmeneti hibát, amikor a Azure SQL Databasehoz, az Azure SQL felügyelt példányához és az Azure szinapszis Analyticshez csatlakozik.
keywords: SQL-kapcsolat, kapcsolati karakterlánc, csatlakozási problémák, átmeneti hiba, kapcsolódási hiba
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: troubleshooting
author: dalechen
ms.author: ninarn
ms.reviewer: sstein, vanto
ms.date: 01/14/2020
ms.openlocfilehash: 9f2e755047910aefa89c2f187cda956aca608b98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99093757"
---
# <a name="troubleshoot-transient-connection-errors-in-sql-database-and-sql-managed-instance"></a>A SQL Database és az SQL felügyelt példányának átmeneti csatlakoztatási hibáinak elhárítása

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ez a cikk azt ismerteti, hogyan lehet megakadályozni, elhárítani, diagnosztizálni és enyhíteni a kapcsolódási hibákat és az olyan átmeneti hibákat, amelyeket az ügyfélalkalmazás a Azure SQL Database, az Azure SQL felügyelt példányával és az Azure szinapszis Analytics szolgáltatással való interakció során észlel. Megtudhatja, hogyan konfigurálhatja az újrapróbálkozási logikát, hogyan alakíthatja ki a kapcsolódási karakterláncot, és hogyan módosíthatja más kapcsolatbeállításokat

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Átmeneti hibák (átmeneti hibák)

Az átmeneti hiba, amely átmeneti hibának is nevezik, egy mögöttes oka van, amely hamarosan feloldja magát. Az átmeneti hibák esetenként előfordulnak, amikor az Azure-rendszer gyorsan áthelyezi a hardveres erőforrásokat a különböző számítási feladatok egyensúlyának növelése érdekében. Ezeknek az újrakonfigurálási eseményeknek a többsége kevesebb, mint 60 másodperc. Az újrakonfigurálási időszakban előfordulhat, hogy problémák léptek fel az adatbázishoz való csatlakozáskor SQL Databaseban. Az adatbázishoz csatlakozó alkalmazásokat úgy kell felépíteni, hogy elvárják ezeket az átmeneti hibákat. A kezeléséhez az újrapróbálkozási logikát implementálja a kódban, ahelyett, hogy az alkalmazási hibákat felszínre helyezze őket a felhasználók számára.

Ha az ügyfélalkalmazás a ADO.NET-t használja, a program a **SqlException** eldobásával kapcsolatos átmeneti hibáról tájékoztat.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Kapcsolatok és parancsok

Próbálja megismételni a SQL Database és az SQL felügyelt példányának kapcsolatát, vagy hozza létre újra a következőtől függően:

- **Átmeneti hiba történik a csatlakozás során**

Néhány másodperces késleltetés után próbálja megismételni a kapcsolatokat.

- **Átmeneti hiba történik egy SQL Database és az SQL felügyelt példány lekérdezési parancsa során**

Ne próbálkozzon azonnal újra a paranccsal. Ehelyett a késleltetés után frissen hozza létre a kapcsolatot. Ezután próbálja megismételni a parancsot.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Újrapróbálkozási logika átmeneti hibák esetén

Az olyan ügyfélprogramok, amelyek időnként átmeneti hibába ütköznek, megbízhatóbbak, ha újrapróbálkozási logikát tartalmaznak. Ha a program a SQL Database a harmadik féltől származó közbenső adatforráson keresztül kommunikál az adatbázissal, kérje meg a szállítót, hogy a köztes újrapróbálkozási logikát tartalmaz-e átmeneti hibák esetén.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Újrapróbálkozási alapelvek

- Ha a hiba átmeneti, próbálkozzon újra a kapcsolatok megnyitásával.
- Ne próbálkozzon közvetlenül újra egy olyan SQL Database vagy SQL felügyelt példány `SELECT` utasítással, amely átmeneti hibával meghiúsult. Ehelyett hozzon létre egy új kapcsolatot, majd próbálja megismételni a parancsot `SELECT` .
- Ha egy SQL Database vagy SQL felügyelt példány `UPDATE` utasítása átmeneti hibával meghiúsul, hozzon létre egy új kapcsolatot, mielőtt újra próbálkozik a frissítéssel. Az újrapróbálkozási logikának meg kell győződnie arról, hogy a teljes adatbázis-tranzakció befejeződött, vagy hogy a teljes tranzakció vissza lesz állítva.

### <a name="other-considerations-for-retry"></a>Az Újrapróbálkozással kapcsolatos egyéb megfontolások

- Egy olyan Batch-program, amely automatikusan elindul a munkaidő után, és a reggeli előtt befejeződik, az újrapróbálkozási kísérletek közötti hosszú időintervallumok nagy türelmet biztosítanak.
- A felhasználói felületi programoknak figyelembe kell venniük az emberi hajlamot, hogy túl hosszú várakozás után hagyjon fel. A megoldás csak néhány másodpercenként próbálkozik újra, mert az adott házirend eláraszthatja a rendszer kéréseit.

### <a name="interval-increase-between-retries"></a>Az újrapróbálkozások közötti időközi növekedés

Azt javasoljuk, hogy az első újrapróbálkozás előtt várjon 5 másodpercig. 5 másodpercnél rövidebb idő elteltével próbálkozzon újra a felhőalapú szolgáltatással. Minden további újrapróbálkozás esetén a késleltetés exponenciálisan növekszik, legfeljebb 60 másodpercig.

A ADO.NET-t használó ügyfelek blokkolási időszakának megvitatására a következő témakörben talál további információt: [kapcsolatok készletezése (ADO.net)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

Azt is megteheti, hogy az újrapróbálkozások maximális számát szeretné beállítani a program saját leállítása előtt.

### <a name="code-samples-with-retry-logic"></a>Kód minták az újrapróbálkozási logikával

Az újrapróbálkozási logikával rendelkező példák a következő címen érhetők el:

- [Rugalmas csatlakozás az Azure SQL-hez a ADO.NET használatával][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Rugalmas csatlakozás az Azure SQL-hez a PHP-vel][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Az újrapróbálkozási logika tesztelése

Az újrapróbálkozási logika teszteléséhez szimulálnia vagy hibát kell okoznia, ha a program még fut.

#### <a name="test-by-disconnecting-from-the-network"></a>Tesztelés a hálózatról való leválasztással

Az újrapróbálkozási logikát úgy tesztelheti, hogy leválasztja az ügyfélszámítógépet a hálózatról, amíg a program fut. A hiba a következő:

- **SqlException. Number** = 11001
- Üzenet: "nincs ilyen gazdagép ismert"

Az első újrapróbálkozási kísérlet részeként az ügyfélszámítógépet újra lehet kapcsolni a hálózattal, majd megkísérelni a kapcsolódást.

A teszt gyakorlati elvégzéséhez a program elindítása előtt húzza ki a számítógépet a hálózatból. Ezután a program felismeri a futásidejű paramétereket, amelynek hatására a program a következőket eredményezi:

- Ideiglenesen adja hozzá a 11001-et a hibák listájához, amelyeket átmenetinek kell tekinteni.
- Próbálja ki az első kapcsolatát a szokásos módon.
- A hiba észlelése után távolítsa el a 11001-et a listából.
- Megjelenít egy üzenetet, amely tájékoztatja a felhasználót, hogy csatlakoztassa a számítógépet a hálózathoz.
- A további végrehajtás szüneteltetéséhez használja a **Console. readline** metódust vagy egy OK gombbal rendelkező párbeszédpanelt. A felhasználó a számítógép hálózati csatlakoztatása után megnyomja az ENTER billentyűt.
- Próbálja megismételni a kapcsolódást, és várta a sikerességet.

#### <a name="test-by-misspelling-the-user-name-when-connecting"></a>Tesztelés a Felhasználónév elírásával kapcsolódáskor

A program szándékosan helytelenül betűzheti a felhasználónevet az első kapcsolódási kísérlet előtt. A hiba a következő:

- **SqlException. Number** = 18456
- Üzenet: "a (z)" WRONG_MyUserName "felhasználó bejelentkezése sikertelen."

Az első újrapróbálkozási kísérlet részeként a program képes kijavítani a hibát, majd megpróbál csatlakozni.

A teszt gyakorlati elvégzéséhez a program felismeri a futásidejű paramétereket, amelynek hatására a program a következőket eredményezi:

- Ideiglenesen adja hozzá a 18456-et a hibák listájához, amelyeket átmenetinek kell tekinteni.
- A felhasználónévhez szándékosan adja hozzá a "WRONG_" nevet.
- A hiba észlelése után távolítsa el a 18456-et a listából.
- Távolítsa el a "WRONG_" nevet a felhasználónévből.
- Próbálja megismételni a kapcsolódást, és várta a sikerességet.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>.NET SqlConnection paraméterek a kapcsolatok újrapróbálkozásához

Ha az ügyfélprogram SQL Database a .NET-keretrendszer **System. SqlClient. SqlConnection** használatával csatlakozik az adatbázishoz, használja a .net 4.6.1-es vagy újabb verzióját (vagy a .net Core-t), hogy használhassa a kapcsolati újrapróbálkozási funkciót. A szolgáltatással kapcsolatos további információkért lásd: [SqlConnection. ConnectionString tulajdonság](/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring?view=netframework-4.8&preserve-view=true).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Amikor létrehozza a **SqlConnection** objektumhoz tartozó [kapcsolódási karakterláncot](/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring) , az értékeket a következő paraméterek között kell összehangolni:

- **ConnectRetryCount**: &nbsp; &nbsp; az alapértelmezett érték 1. A tartomány 0 és 255 között van.
- **ConnectRetryInterval**: &nbsp; &nbsp; az alapértelmezett érték 10 másodperc. A tartomány értéke 1 – 60.
- **Kapcsolat időtúllépése**: &nbsp; &nbsp; az alapértelmezett érték 15 másodperc. A tartomány 0 és 2147483647 között van.

Pontosabban a kiválasztott értékeknek a következő egyenlőséget kell elvégezniük: kapcsolat időtúllépése = ConnectRetryCount * ConnectionRetryInterval

Ha például a Count értéke 3, az intervallum pedig 10 másodperc, akkor a csak 29 másodperces időkorlát nem biztosít elegendő időt a rendszernek ahhoz, hogy a harmadik és utolsó újrapróbálkozás újra kapcsolódjon: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Kapcsolatok és parancsok

A **ConnectRetryCount** és a **ConnectRetryInterval** paraméterekkel a **SqlConnection** objektum újra próbálkozik a kapcsolódási művelettel anélkül, hogy elmondja vagy zavarja a programot, például a vezérlés visszaadása a programnak. Az újrapróbálkozások a következő helyzetekben fordulhatnak elő:

- SqlConnection. open metódus hívása
- SqlConnection.ExeCute metódus hívása

Van egy finomság. Ha átmeneti hiba történik a *lekérdezés* végrehajtása közben, a **SqlConnection** objektum nem próbálkozik újra a kapcsolódási művelettel. Bizonyára nem próbálja meg újra a lekérdezést. A **SqlConnection** azonban nagyon gyorsan ellenőrzi a kapcsolódást, mielőtt elküldi a lekérdezést a végrehajtáshoz. Ha a gyors vizsgálat csatlakozási problémát észlel, a **SqlConnection** újrapróbálkozik a csatlakozási művelettel. Ha az Újrapróbálkozás sikeres, a rendszer elküldi a lekérdezést a végrehajtáshoz.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Az alkalmazás-újrapróbálkozási logikával együtt kell ConnectRetryCount

Tegyük fel, hogy az alkalmazás robusztus egyéni újrapróbálkozási logikával rendelkezik. Előfordulhat, hogy a csatlakozási művelet négyszer próbálkozik újra. Ha a **ConnectRetryInterval** és a **ConnectRetryCount** = 3 karakterláncot adja hozzá a kapcsolódási sztringhez, az újrapróbálkozások száma 4 * 3 = 12 újrapróbálkozás után növekszik. Előfordulhat, hogy nem kíván ilyen nagy mennyiségű újrapróbálkozást végrehajtani.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-your-database-in-sql-database"></a>Csatlakozás az adatbázishoz SQL Database

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Kapcsolatok: kapcsolatok karakterlánca

Az adatbázishoz való kapcsolódáshoz szükséges kapcsolati sztring kis mértékben eltér a SQL Serverhoz való kapcsolódáshoz használt karakterlánctól. Az adatbázishoz tartozó kapcsolatok karakterláncát a [Azure Portal](https://portal.azure.com/)másolhatja.

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Kapcsolatok: IP-cím

Az ügyfélprogramot futtató számítógép IP-címéről érkező kommunikáció elfogadásához konfigurálnia kell SQL Database. A konfiguráció beállításához szerkessze a tűzfal beállításait a [Azure Portal](https://portal.azure.com/).

Ha elfelejti az IP-cím konfigurálását, a program egy praktikus hibaüzenettel leáll, amely kimondja a szükséges IP-címet.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../../includes/sql-database-include-ip-address-22-v12portal.md)]

További információ: [a tűzfalbeállítások konfigurálása a SQL Databaseban](firewall-configure.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Csatlakozás: portok

Általában biztosítania kell, hogy csak a 1433-es port legyen nyitva a kimenő kommunikációhoz az ügyfélprogramot futtató számítógépen.

Ha például egy Windows rendszerű számítógépen futtatja az ügyfélszoftvert, a gazdagépen a Windows tűzfal segítségével nyissa meg a 1433-es portot.

1. Nyissa meg a Vezérlőpultot.
2. **A Vezérlőpult összes elemének kijelölése a**  >  **Windows tűzfal**  >  **Speciális beállítások**  >  **Kimenő szabályok**  >  **műveletek**  >  **új szabály**.

Ha az ügyfélprogram egy Azure-beli virtuális gépen (VM) üzemel, olvassa el [a 1433-es portot a ADO.NET 4,5 és a SQL Database](adonet-v12-develop-direct-route-ports.md).

Az adatbázisban található portok és IP-címek konfigurálásával kapcsolatos háttér-információkért lásd: [Azure SQL Database tűzfal](firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Kapcsolatok: ADO.NET 4.6.2 vagy újabb

Ha a program ADO.NET-osztályokat használ, például a **System. SqlClient. SqlConnection** -t, hogy csatlakozhasson a SQL Databasehoz, javasoljuk, hogy a .NET-keretrendszer 4.6.2 vagy újabb verzióját használja.

#### <a name="starting-with-adonet-462"></a>A ADO.NET 4.6.2 kezdve

- A kapcsolódási kísérlet azonnal újrapróbálkozik az Azure SQL-ben, ami javítja a felhőalapú alkalmazások teljesítményét.

#### <a name="starting-with-adonet-461"></a>A ADO.NET 4.6.1-től kezdve

- SQL Database esetén a megbízhatóság akkor javul, ha a **SqlConnection. Open** metódussal nyit meg egy-egy kapcsolódást. A **nyílt** módszer mostantól tartalmazza a legjobb erőfeszítést okozó újrapróbálkozási mechanizmusokat a kapcsolati időtúllépési időszakon belüli bizonyos hibákra adott átmeneti hibákra válaszul.
- A rendszer támogatja a kapcsolatok készletezését, amely hatékony ellenőrzést biztosít a program működéséhez szükséges kapcsolatok objektum számára.

Ha egy kapcsolódási készletből használ egy kapcsolódási objektumot, azt javasoljuk, hogy a program átmenetileg zárja be a kapcsolódást, ha nem azonnal használatban van. Nem drága a kapcsolatok újbóli megnyitása, de új kapcsolatok létrehozása.

Ha a ADO.NET 4,0-es vagy korábbi verzióját használja, javasoljuk, hogy frissítsen a legújabb ADO.NET. Augusztus 2018-én [letöltheti a ADO.net 4.6.2](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnosztika

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnosztika: annak tesztelése, hogy a segédprogramok csatlakozhatnak-e

Ha a program nem tud csatlakozni az adatbázishoz SQL Database-ben, az egyik diagnosztikai lehetőség az, hogy megpróbáljon kapcsolódni egy segédprogram-programhoz. Ideális esetben a segédprogram a program által használttal megegyező kódtár használatával csatlakozik.

Bármelyik Windows-számítógépen kipróbálhatja a következő segédprogramokat:

- SQL Server Management Studio (ssms.exe), amely a ADO.NET használatával csatlakozik
- `sqlcmd.exe`, amely az [ODBC](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server) használatával csatlakozik

Ha a program csatlakoztatva van, tesztelje, hogy egy rövid SQL SELECT lekérdezés működik-e.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnosztika: a nyitott portok keresése

Ha azt gyanítja, hogy a kapcsolódási kísérletek a portok hibái miatt sikertelenek, futtathat egy segédprogramot a számítógépen, amely a portok konfigurációit jelenti.

Linux rendszeren a következő segédprogramok hasznosak lehetnek:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: Módosítsa a példában szereplő értéket az IP-címére.

Windows rendszeren a [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) segédprogram hasznos lehet. Íme egy példa a végrehajtásra, amely lekérdezte a port helyzetét egy SQL Database adatbázisán, és egy hordozható számítógépen futott:

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnosztika: a hibák naplózása

Időnként egy időszakra vagy hetekre vonatkozó általános minta észlelésével egy időnként előforduló problémát diagnosztizálnak.

Az ügyfél az összes észlelt hiba naplózásával segíthet a diagnózisban. Előfordulhat, hogy a naplóbejegyzések összekapcsolhatók a hibákkal, amelyek belsőleg SQL Database naplózzák.

Az Enterprise Library 6 (EntLib60) .NET által felügyelt osztályokat kínál a naplózás támogatásához. További információ [: 5 – egyszerűen csak jelentkezzen be a naplóba: használja a naplózási alkalmazás blokkját](/previous-versions/msp-n-p/dn440731(v=pandp.60)).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnosztika: a rendszernaplók vizsgálata hibák esetén

Íme néhány Transact-SQL SELECT utasítás, amely a hibák naplóit és egyéb információkat kérdezi le.

| Napló lekérdezése | Description |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |A [sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database) nézet az egyes eseményekkel kapcsolatos információkat tartalmaz, amelyekben átmeneti hibák vagy csatlakozási hibák okozhatnak.<br/><br/>Ideális esetben összekapcsolhatja a **start_time** vagy **end_time** értékeket, ha az ügyfélprogram problémát észlelt.<br/><br/>A lekérdezés futtatásához csatlakoznia kell a *Master* adatbázishoz. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |A [sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database) nézet a további diagnosztikai események összesített számát kínálja.<br/><br/>A lekérdezés futtatásához csatlakoznia kell a *Master* adatbázishoz. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnosztika: problémás események keresése a SQL Database naplóban

A SQL Database naplóban megkeresheti a problémás eseményekre vonatkozó bejegyzéseket. Próbálja ki a következő Transact-SQL SELECT utasítást a *Master* adatbázisban:

```sql
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>Néhány visszaadott sor sys.fn_xe_telemetry_blob_target_read_file

Az alábbi példa azt szemlélteti, hogy a visszaadott sor milyen módon nézhet ki. A megjelenített null értékek gyakran nem null értékűek más sorokban.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>6. vállalati könyvtár

Az Enterprise Library 6 (EntLib60) olyan .NET-osztályok keretrendszere, amely segít a Cloud Services robusztus ügyfeleinek megvalósításában, amelyek közül az egyik SQL Database. A EntLib60 által támogatott egyes területek számára fenntartott témakörök megkereséséhez lásd: [Enterprise Library 6 – április 2013](/previous-versions/msp-n-p/dn169621(v=pandp.10)).

Az átmeneti hibák kezelésére szolgáló újrapróbálkozási logika egy olyan rész, amelyben a EntLib60 segíthet. További információkért lásd [: 4 – kitartás, az összes diadal titka: az átmeneti hibák kezelésére szolgáló alkalmazás blokkjának használata](/previous-versions/msp-n-p/dn440719(v=pandp.60)).

> [!NOTE]
> A EntLib60 forráskódja elérhető nyilvános letöltésre a [letöltőközpontban](https://github.com/MicrosoftArchive/enterprise-library). A Microsoft nem tervezi, hogy további frissítéseket vagy karbantartási frissítéseket EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>EntLib60-osztályok átmeneti hibákhoz, és újrapróbálkozás

A következő EntLib60 osztályok különösen hasznosak az újrapróbálkozási logikához. Ezek az osztályok a **Microsoft. Practices. EnterpriseLibrary. TransientFaultHandling** névtérben vagy annak alatt találhatók.

A **Microsoft. Practices. EnterpriseLibrary. TransientFaultHandling** névtérben:

- **RetryPolicy** osztály
  - **ExecuteAction** metódus
- **ExponentialBackoff** osztály
- **SqlDatabaseTransientErrorDetectionStrategy** osztály
- **ReliableSqlConnection** osztály
  - **ExecuteCommand** metódus

A **Microsoft. Practices. EnterpriseLibrary. TransientFaultHandling. TestSupport** névtérben:

- **AlwaysTransientErrorDetectionStrategy** osztály
- **NeverTransientErrorDetectionStrategy** osztály

Íme néhány hivatkozás a EntLib60 kapcsolatos információkra:

- Ingyenes könyv letöltése: [fejlesztői útmutató a Microsoft Enterprise Library-hoz, 2. kiadás](https://www.microsoft.com/download/details.aspx?id=41145).
- Ajánlott eljárások: az [Általános útmutatás](/azure/architecture/best-practices/transient-faults) az újrapróbálkozási logikával kapcsolatos részletes vitát tartalmaz.
- NuGet Letöltés: [Enterprise Library – átmeneti hiba az alkalmazás 6,0-es blokkjában](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: a naplózási blokk

- A naplózási blokk egy rugalmas és konfigurálható megoldás, amelyet a következő célra használhat:
  - A naplófájlok számos különböző helyen hozhatók létre és tárolhatók.
  - Üzenetek kategorizálása és szűrése.
  - A hibakereséshez és nyomon követéshez, valamint a naplózási és általános naplózási követelményekhez hasznos környezetfüggő információk gyűjtése.
- A naplózási blokk elvonta a naplózási funkciót a napló célhelyéről, így az alkalmazás kódja konzisztens, a cél naplózási tárolójának helyétől és típusától függetlenül.

További információ [: 5 – egyszerűen csak jelentkezzen be a naplóba: használja a naplózási alkalmazás blokkját](/previous-versions/msp-n-p/dn440731(v=pandp.60)).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 IsTransient metódus forráskódja

Ezután a **SqlDatabaseTransientErrorDetectionStrategy** osztályból a **IsTransient** metódus C#-forráskódja. A forráskód meghatározza, hogy mely hibák tekinthetők átmenetinek, és érdemes újrapróbálkozni a 2013. április 1-től.

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```

## <a name="next-steps"></a>Következő lépések

- [SQL Database és SQL Serverhoz tartozó kapcsolatok kódtárai](connect-query-content-reference-guide.md#libraries)
- [Kapcsolatok készletezése (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling)
- Az újrapróbálkozások [  egy Pythonban írt, az Apache 2,0 licenccel rendelkező általános célú](https://pypi.python.org/pypi/retrying) újrapróbálkozási könyvtár, amely leegyszerűsíti az újrapróbálkozási viselkedés feladatát a bármit.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: /sql/connect/php/step-4-connect-resiliently-to-sql-with-php