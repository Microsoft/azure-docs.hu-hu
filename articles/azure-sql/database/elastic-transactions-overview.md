---
title: Elosztott tranzakciók több felhőalapú adatbázisban (előzetes verzió)
description: A Azure SQL Database és az Azure SQL felügyelt példányával Elastic Database tranzakciók áttekintése.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5504b9bc87f78682ff584006255d4e75e5e69fa7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793347"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>Elosztott tranzakciók több felhőalapú adatbázisban (előzetes verzió)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Azure SQL Database és az Azure SQL felügyelt példányának rugalmas adatbázis-tranzakciói lehetővé teszik, hogy több adatbázisra kiterjedő tranzakciókat futtasson. A rugalmas adatbázis-tranzakciók a ADO.NET-t használó .NET-alkalmazások számára érhetők el, és a [System. Transaction](/dotnet/api/system.transactions) osztályok használatával integrálva vannak a megszokott programozási felülettel. A könyvtár beszerzéséhez tekintse meg a [.NET-keretrendszer 4.6.1 (web Installer)](https://www.microsoft.com/download/details.aspx?id=49981)című témakört.
Emellett a felügyelt példányok elosztott tranzakciói a [Transact-SQL-](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql)ben is elérhetők.

A helyszínen egy ilyen forgatókönyvhöz általában a Microsoft Distributed Transaction Coordinator (MSDTC) futtatására van szükség. Mivel az MSDTC nem érhető el az Azure-ban elérhető platform-szolgáltatásként, az elosztott tranzakciók koordinálásának lehetősége már közvetlenül integrálva van SQL Database vagy felügyelt példányba. Az alkalmazások bármely adatbázishoz csatlakozhatnak az elosztott tranzakciók elindításához, és az adatbázisok vagy kiszolgálók egyike transzparens módon koordinálja az elosztott tranzakciót, ahogy az az alábbi ábrán is látható.

Ebben a dokumentumban az "elosztott tranzakciók" és a "rugalmas adatbázis-tranzakciók" kifejezések szinonimának számítanak, és a rendszer szinonimaként fogja használni.

  ![Elosztott tranzakciók Azure SQL Database rugalmas adatbázis-tranzakciók használatával ][1]

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

A rugalmas adatbázis-tranzakciók lehetővé teszik az alkalmazások számára, hogy a különböző adatbázisokban tárolt adatvédelemre vonatkozó atomi módosításokat végezzenek. Az előzetes verzió a C# és a .NET ügyféloldali fejlesztési tapasztalataira koncentrál. Egy kiszolgálóoldali felület (tárolt eljárásokban vagy kiszolgálóoldali parancsfájlokban írt kód) a [Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) használatával csak felügyelt példányhoz érhető el.
> [!IMPORTANT]
> Az előzetes verzióban a rugalmas adatbázis-tranzakciók futtatása Azure SQL Database és az Azure SQL felügyelt példánya között jelenleg nem támogatott. A rugalmas adatbázis-tranzakció csak az SQL-adatbázisok vagy a felügyelt példányok készletei között terjedhet fel.

A rugalmas adatbázis-tranzakciók a következő forgatókönyveket célozzák meg:

* Több adatbázisból álló alkalmazások az Azure-ban: ebben a forgatókönyvben az adat függőlegesen particionálva van SQL Database vagy felügyelt példány több adatbázisa között, így a különböző adattípusok különböző adatbázisokban találhatók. Néhány művelethez szükség van az adatok módosítására, amelyet két vagy több adatbázisban tartanak. Az alkalmazás rugalmas adatbázis-tranzakciókat használ az adatbázisok változásainak koordinálására és az atomenergia biztosítására.
* Az Azure-ban felhasználható adatbázis-alkalmazások: ebben a forgatókönyvben az adatréteg az [Elastic Database ügyféloldali kódtárat](elastic-database-client-library.md) vagy az önálló horizontálisan a SQL Database vagy a felügyelt példányon található számos adatbázis közötti adatfelosztást használja. Az egyik kiemelt használati eset az, hogy szükség van egy több-bérlős, több-bérlős alkalmazásra vonatkozó atomi módosítások végrehajtására, ha a változások a bérlőket használják. Gondolja át, hogy az egyik bérlőről a másikra, a különböző adatbázisokban lévő példányokra is vonatkozik. Egy második eset a nagy bérlők kapacitási igényeinek kielégítésére szolgáló részletes horizontális felskálázás, ami általában azt jelenti, hogy egyes atomi műveleteknek több adatbázisra kell kiterjeszteniük az ugyanahhoz a bérlőhöz használt adatbázisok között. A harmadik esetben az olyan atomi frissítések, amelyek az adatbázisok között replikált információkra vonatkoznak. Az Atomic, a transacted, a következő vonalak mentén végzett műveletek mostantól több adatbázison is összehangolható az előzetes verzió használatával.
  A rugalmas adatbázis-tranzakciók kétfázisú végrehajtással biztosítják a tranzakciók atomi használatát az adatbázisok között. Ez egy olyan tranzakció, amely egy adott tranzakción belül egyszerre kevesebb mint 100 adatbázist tartalmaz. Ezek a korlátok nem érvényesülnek, de az egyiknek a teljesítmény és a sikerességi arányt kell várnia ahhoz, hogy a rugalmas adatbázis-tranzakciók a határértékek meghaladása esetén

## <a name="installation-and-migration"></a>Telepítés és áttelepítés

A rugalmas adatbázis-tranzakciók képességei a .NET-kódtárak System.Data.dll és System.Transactions.dll frissítésein keresztül érhetők el. A DLL-ek biztosítják, hogy a kétfázisú véglegesítés szükség esetén legyen használatban az atomenergia biztosításához. A rugalmas adatbázis-tranzakciókat használó alkalmazások fejlesztésének megkezdéséhez telepítse a [.NET-keretrendszer 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) -es vagy újabb verzióját. Ha a .NET-keretrendszer egy korábbi verzióját futtatja, a tranzakciókat nem lehet előléptetni egy elosztott tranzakcióra, és a rendszer kivételt eredményez.

A telepítés után az elosztott tranzakciós API-kat használhatja a System. Transactions szolgáltatásban SQL Database és felügyelt példánnyal létesített kapcsolatokkal. Ha ezekkel az API-kkal már meglévő MSDTC-alkalmazásokat használ, építse újra a meglévő alkalmazásokat a .NET 4,6-es verzióra a 4.6.1-keretrendszer telepítése után. Ha a projektek a .NET 4,6-es verzióját célozzák meg, akkor a rendszer automatikusan a frissített DLL-eket használja az új keretrendszer-verzió és az elosztott tranzakciók API-hívásai között, SQL Database vagy felügyelt példány kapcsolataival együtt.

Ne feledje, hogy a rugalmas adatbázis-tranzakciók nem igénylik az MSDTC telepítését. Ehelyett a rugalmas adatbázis-tranzakciókat közvetlenül és a szolgáltatáson belül kezelik. Ez jelentősen leegyszerűsíti a Felhőbeli forgatókönyveket, mivel az MSDTC telepítése nem szükséges az elosztott tranzakciók SQL Database vagy felügyelt példánnyal való használatához. A 4. szakasz részletesen ismerteti, hogyan helyezhetők üzembe rugalmas adatbázis-tranzakciók és a szükséges .NET-keretrendszer a felhőalapú alkalmazásokkal együtt az Azure-ban.

## <a name="net-installation-for-azure-cloud-services"></a>.NET-telepítés az Azure Cloud Services

Az Azure számos ajánlatot biztosít a .NET-alkalmazások üzemeltetéséhez. A különböző ajánlatok összehasonlítása [Azure app Service, Cloud Services és Virtual Machines összehasonlításban](/azure/architecture/guide/technology-choices/compute-decision-tree)érhető el. Ha az ajánlat vendég operációs rendszere kisebb, mint a rugalmas tranzakciók esetében szükséges .NET 4.6.1, a vendég operációs rendszert a 4.6.1-es verzióra kell frissíteni.

Azure App Service a vendég operációs rendszerre való frissítés jelenleg nem támogatott. Az Azure Virtual Machines esetében egyszerűen jelentkezzen be a virtuális gépre, és futtassa a telepítőt a legújabb .NET-keretrendszerhez. Az Azure Cloud Services esetében telepítenie kell egy újabb .NET-verziót az üzemelő példány indítási feladataiba. A fogalmakat és a lépéseket a [.NET telepítése Cloud Service-szerepkörre](../../cloud-services/cloud-services-dotnet-install-dotnet.md)című témakör ismerteti.  

Vegye figyelembe, hogy a .NET 4.6.1-es verziójának az Azure Cloud Services rendszerindítási folyamata során több átmeneti tárterületre lehet szüksége, mint a .NET 4,6-es telepítő. A sikeres telepítés érdekében növelje az Azure Cloud Service ideiglenes tárhelyét a ServiceDefinition. csdef fájlban a LocalResources szakaszban, valamint az indítási feladat környezeti beállításait, ahogy az az alábbi példában is látható:

```xml
    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
```

## <a name="net-development-experience"></a>.NET-fejlesztési élmény

### <a name="multi-database-applications"></a>Több adatbázisból álló alkalmazások

A következő mintakód a megszokott programozási felületet használja a .NET System. Transactions használatával. A TransactionScope osztály egy környezeti tranzakciót hoz létre a .NET-ben. (A "környezeti tranzakció" olyan, amely az aktuális szálban él.) A TransactionScope belül megnyitott összes kapcsolat részt vesz a tranzakcióban. Ha a különböző adatbázisok részt vesznek, a tranzakció automatikusan egy elosztott tranzakcióhoz lesz felemelve. A tranzakció eredményét úgy szabályozza, hogy a hatókört a Befejezés értékre állítja, hogy véglegesítse a műveletet.

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

### <a name="sharded-database-applications"></a>Szilánkokra osztott adatbázis-alkalmazások

A SQL Database és a felügyelt példányok rugalmas adatbázis-tranzakciói támogatják az elosztott tranzakciók koordinálását is, amelyek segítségével a rugalmas adatbázis-ügyféloldali kódtár OpenConnectionForKey metódusával megnyithatók a kibővíthető adatcsomag kapcsolatai. Tekintse át azokat az eseteket, ahol a több különböző horizontális Felskálázási kulcsban végrehajtott változások tranzakciós konzisztenciájának garantálása szükséges. A különböző horizontális Felskálázási kulcsokat üzemeltető szegmensekkel létesített kapcsolatok a OpenConnectionForKey használatával vannak közvetítve. Az általános esetben a kapcsolatok különböző szegmensekben lehetnek, így a tranzakciós garanciák biztosításához elosztott tranzakció szükséges.
A következő mintakód ezt a megközelítést mutatja be. Azt feltételezi, hogy egy shardmap nevű változót használ a rugalmas adatbázis ügyféloldali könyvtárának egy szegmenses térképének ábrázolására:

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="transact-sql-development-experience"></a>Transact-SQL-fejlesztési élmény

A Transact-SQL-t használó kiszolgálóoldali elosztott tranzakciók csak az Azure SQL felügyelt példányai esetében érhetők el. Az elosztott tranzakciók csak olyan felügyelt példányok között hajthatók végre, amelyek ugyanahhoz a [kiszolgálói megbízhatósági csoporthoz](../managed-instance/server-trust-group-overview.md)tartoznak. Ebben a forgatókönyvben a felügyelt példányoknak a [csatolt kiszolgálót](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#TsqlProcedure) kell használniuk egymásra való hivatkozáshoz.

A következő példa Transact-SQL-kód az elosztott tranzakció megkezdése az [elosztott](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) tranzakciók indításához.

```Transact-SQL

    -- Configure the Linked Server
    -- Add one Azure SQL Managed Instance as Linked Server
    EXEC sp_addlinkedserver
        @server='RemoteServer', -- Linked server name
        @srvproduct='',
        @provider='sqlncli', -- SQL Server Native Client
        @datasrc='managed-instance-server.46e7afd5bc81.database.windows.net' -- Managed Instance endpoint

    -- Add credentials and options to this Linked Server
    EXEC sp_addlinkedsrvlogin
        @rmtsrvname = 'RemoteServer', -- Linked server name
        @useself = 'false',
        @rmtuser = '<login_name>',         -- login
        @rmtpassword = '<secure_password>' -- password

    USE AdventureWorks2012;
    GO
    SET XACT_ABORT ON;
    GO
    BEGIN DISTRIBUTED TRANSACTION;
    -- Delete candidate from local instance.
    DELETE AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    -- Delete candidate from remote instance.
    DELETE RemoteServer.AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    COMMIT TRANSACTION;
    GO
```

## <a name="combining-net-and-transact-sql-development-experience"></a>.NET-és Transact-SQL-alapú fejlesztési élmény kombinálása

A System. Transaction osztályokat használó .NET-alkalmazások kombinálják a TransactionScope osztályt Transact-SQL-utasítással, és megkezdik az ELOSZTOTT TRANZAKCIÓt. A TransactionScope-en belül a megkezdve megkezdett TRANZAKCIÓt végrehajtó belső tranzakció explicit módon előléptetve lesz az elosztott tranzakcióba. Emellett, ha a második SqlConnecton meg van nyitva a TransactionScope belül, a rendszer implicit módon előlépteti az elosztott tranzakciót. Az elosztott tranzakciók elindítása után az összes további tranzakciós kérelem, függetlenül attól, hogy .NET-vagy Transact-SQL-címről érkeznek, csatlakozik-e a szülő elosztott tranzakcióhoz. Ennek következményeként a BEGIN utasítás által kezdeményezett összes beágyazott tranzakciós hatókör ugyanabban a tranzakcióban fog megjelenni, és a VÉGLEGESÍTés/VISSZAÁLLÍTÁSi utasítások a teljes végeredményre érvényesek lesznek:
 * A VÉGLEGESÍTÉSi utasítás nem befolyásolja a BEGIN utasítás által kezdeményezett tranzakció-hatókört, azaz a Complete () metódus meghívása előtt a TransactionScope objektumon nem lesz végrehajtva eredmény. Ha a TransactionScope objektum megsemmisül a végrehajtás előtt, a hatókörön belüli összes módosítás vissza lesz állítva.
 * A VISSZAÁLLÍTÁSi utasítás a teljes TransactionScope visszaállítását fogja eredményezni. A TransactionScope-en belüli új tranzakciók bekapcsolására tett kísérletek később sikertelenek lesznek, és megkísérlik a Complete () művelet meghívását a TransactionScope objektumon.

Íme egy példa, ahol a tranzakció explicit módon elő van terjesztve az elosztott tranzakcióra a Transact-SQL használatával.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
        
            // Transaction is here promoted to distributed by BEGIN statement
            //
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            // ...
        }
     
        using (SqlConnection conn2 = new SqlConnection(DB1_ConnectionString)
        {
            conn2.Open();
            // ...
        }
        
        s.Complete();
    }
```

A következő példa egy tranzakciót mutat be, amely implicit módon előléptetve van az elosztott tranzakcióra, ha a második SqlConnecton elindult a TransactionScope belül.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
            // ...
        }
        
        using (SqlConnection conn = new SqlConnection(DB1_ConnectionString)
        {
            // Because this is second SqlConnection within TransactionScope transaction is here implicitly promoted distributed.
            //
            conn.Open(); 
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            Helper.ExecuteNonQueryOnOpenConnection(conn, lsQuery);
            // ...
        }
        
        s.Complete();
    }
```

## <a name="transactions-across-multiple-servers-for-azure-sql-database"></a>Több kiszolgáló közötti tranzakciók Azure SQL Database

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

A rugalmas adatbázis-tranzakciók a Azure SQL Database különböző kiszolgálóin is támogatottak. Ha a tranzakciók átlépik a kiszolgálók határait, a résztvevő kiszolgálókat először kölcsönös kommunikációs kapcsolatba kell beírni. A kommunikációs kapcsolat létrejötte után a két kiszolgáló bármelyik adatbázisa részt vehet a másik kiszolgáló adatbázisaival rendelkező rugalmas tranzakciókban. A kettőnél több kiszolgálót érintő tranzakciókkal a kiszolgálók bármelyike esetében kommunikációs kapcsolat szükséges.

Az alábbi PowerShell-parancsmagok segítségével kezelheti a többkiszolgálós kommunikációs kapcsolatokat a rugalmas adatbázis-tranzakciók esetében:

* **New-AzSqlServerCommunicationLink**: ezzel a parancsmaggal új kommunikációs kapcsolatot hozhat létre Azure SQL Database két kiszolgálója között. A kapcsolat szimmetrikus, ami azt jelenti, hogy mindkét kiszolgáló kezdeményezheti a tranzakciókat a másik kiszolgálóval.
* **Get-AzSqlServerCommunicationLink**: ezzel a parancsmaggal kérheti le a meglévő kommunikációs kapcsolatokat és azok tulajdonságait.
* **Remove-AzSqlServerCommunicationLink**: ezzel a parancsmaggal távolíthatja el a meglévő kommunikációs kapcsolatokat.

## <a name="transactions-across-multiple-servers-for-azure-sql-managed-instance"></a>Több kiszolgáló közötti tranzakciók a felügyelt Azure SQL-példányokhoz

Az elosztott tranzakciók az Azure SQL felügyelt példányain különböző kiszolgálókon támogatottak. Ha a tranzakciók határokon átnyúló felügyelt példányokat használ, a résztvevő példányokat először kölcsönös biztonsági és kommunikációs kapcsolatba kell bevinni. Ezt egy [kiszolgálói megbízhatósági csoport](../managed-instance/server-trust-group-overview.md)létrehozásával teheti meg, amely Azure Portal végezhető el. Ha a felügyelt példányok nem ugyanazon a virtuális hálózaton vannak, akkor a [virtuális hálózatokat](../../virtual-network/virtual-network-peering-overview.md) is be kell állítani, és a hálózati biztonsági csoport bejövő és kimenő szabályainak engedélyeznie kell a 5024 és a 11000-12000 portot minden résztvevő virtuális hálózaton.

  ![Kiszolgálói megbízhatósági csoportok az Azure Portalon][3]

Az alábbi ábrán egy felügyelt példányokkal rendelkező kiszolgáló-megbízhatósági csoport látható, amely a .NET vagy a Transact-SQL használatával végezhető elosztott tranzakciók végrehajtására.

  ![Elosztott tranzakciók az Azure SQL felügyelt példányával rugalmas tranzakciók használatával][2]

## <a name="monitoring-transaction-status"></a>Tranzakció állapotának figyelése

A dinamikus felügyeleti nézetek (DMV) segítségével figyelheti a folyamatban lévő rugalmas adatbázis-tranzakciók állapotát és előrehaladását. A tranzakciókkal kapcsolatos összes DMV a SQL Database és a felügyelt példány elosztott tranzakcióinak esetében releváns. Itt megtalálja a DMV megfelelő listáját: [tranzakcióval kapcsolatos dinamikus felügyeleti nézetek és függvények (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql).

Ezek a DMV különösen hasznosak:

* **sys.DM \_ Tran \_ aktív \_ tranzakciók**: felsorolja a jelenleg aktív tranzakciókat és azok állapotát. A UOW (munkaegység) oszlopban azonosíthatók azok a különböző alárendelt tranzakciók, amelyek ugyanahhoz az elosztott tranzakcióhoz tartoznak. Az azonos elosztott tranzakción belüli összes tranzakció ugyanazt a UOW-értéket hajtja végre. További információt a [DMV dokumentációjában](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql)talál.
* **sys.DM \_ Tran \_ adatbázis- \_ tranzakciói**: további információkat nyújt a tranzakciókkal kapcsolatban, például a tranzakció elhelyezéséről a naplóban. További információt a [DMV dokumentációjában](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql)talál.
* **sys.DM \_ Tran \_ zárolásai**: a folyamatban lévő tranzakciók által jelenleg tárolt zárolásokról nyújt információt. További információt a [DMV dokumentációjában](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql)talál.

## <a name="limitations"></a>Korlátozások

A következő korlátozások jelenleg a rugalmas adatbázis-tranzakciókra vonatkoznak SQL Databaseban:

* Csak SQL Database adatbázisok közötti tranzakciók támogatottak. A SQL Databaseon kívüli egyéb [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) erőforrás-szolgáltatók és adatbázisok nem vehetnek részt rugalmas adatbázis-tranzakciókban. Ez azt jelenti, hogy a rugalmas adatbázis-tranzakciók nem terjedhetnek át a helyszíni SQL Serverra és Azure SQL Databasera. A helyszíni elosztott tranzakciók esetében folytassa az MSDTC használatát.
* Csak az ügyfél által koordinált tranzakciók támogatottak a .NET-alkalmazásokból. A T-SQL kiszolgálóoldali támogatása, például a BEGIN DISTRIBUTed TRANSACTION terv, de még nem érhető el.
* A WCF-szolgáltatások közötti tranzakciók nem támogatottak. Tegyük fel, hogy van egy WCF szolgáltatási metódusa, amely tranzakciót hajt végre. A hívás tranzakciós hatókörön belüli befoglalása sikertelen lesz, mint a [System. ServiceModel. ProtocolException](/dotnet/api/system.servicemodel.protocolexception).

A felügyelt példány elosztott tranzakciói esetében jelenleg a következő korlátozások érvényesek:

* Csak a felügyelt példányokban lévő adatbázisok közötti tranzakciók támogatottak. Az Azure SQL felügyelt példányain kívül más [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) erőforrás-szolgáltatók és adatbázisok nem vehetnek részt elosztott tranzakciókban. Ez azt jelenti, hogy az elosztott tranzakciók nem terjeszthetők ki a helyszíni SQL Server és az Azure SQL felügyelt példányai között. A helyszíni elosztott tranzakciók esetében folytassa az MSDTC használatát.
* A WCF-szolgáltatások közötti tranzakciók nem támogatottak. Tegyük fel, hogy van egy WCF szolgáltatási metódusa, amely tranzakciót hajt végre. A hívás tranzakciós hatókörön belüli befoglalása sikertelen lesz, mint a [System. ServiceModel. ProtocolException](/dotnet/api/system.servicemodel.protocolexception).
* Az Azure SQL felügyelt példányának egy [kiszolgálói megbízhatósági csoport](../managed-instance/server-trust-group-overview.md) tagjának kell lennie ahhoz, hogy részt vegyen az elosztott tranzakcióban.
* A [kiszolgálói megbízhatósági csoportok](../managed-instance/server-trust-group-overview.md) korlátai az elosztott tranzakciókat érintik.
* Az elosztott tranzakciókban részt vevő felügyelt példányoknak privát végpontokon keresztül kell csatlakozniuk (magánhálózati IP-címmel kell rendelkezniük a virtuális hálózatról, ahol telepítve vannak), és kölcsönösen hivatkozni kell a privát FQDN használatával. Az ügyfélalkalmazások elosztott tranzakciókat használhatnak privát végpontokon. Továbbá abban az esetben, ha a Transact-SQL a magánhálózati végpontokra hivatkozó csatolt kiszolgálókat használ, az ügyfélalkalmazások a nyilvános végpontokon is használhatják az elosztott tranzakciókat. Ezt a korlátozást a következő ábra ismerteti.
  ![Magánhálózati végponti kapcsolat korlátozása][4]
## <a name="next-steps"></a>Következő lépések

* Ha kérdése van, keressen minket a [Microsoft Q&a SQL Database vonatkozó kérdés oldalán](/answers/topics/azure-sql-database.html).
* A szolgáltatási kérelmek esetében vegye fel őket a [SQL Database visszajelzési fórumba](https://feedback.azure.com/forums/217321-sql-database/) vagy a [felügyelt példányok fórumára](https://feedback.azure.com/forums/915676-sql-managed-instance).



<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
[2]: ./media/elastic-transactions-overview/sql-mi-distributed-transactions.png
[3]: ./media/elastic-transactions-overview/server-trust-groups-azure-portal.png
[4]: ./media/elastic-transactions-overview/managed-instance-distributed-transactions-private-endpoint-limitations.png
