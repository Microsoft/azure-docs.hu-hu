---
title: A tranzakciós replikáció konfigurálása az Azure SQL felügyelt példánya és a SQL Server között
description: Egy oktatóanyag, amely a közzétevő felügyelt példánya, a terjesztő felügyelt példánya, valamint egy Azure-beli virtuális gépen SQL Server előfizető közötti replikációt, valamint a szükséges hálózati összetevőket, például a magánhálózati DNS-zónát és a VNet-társítást konfigurálja.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/21/2019
ms.openlocfilehash: d2b45f5b51f4656294632aa46f679a7a09c06ed3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94593926"
---
# <a name="tutorial-configure-transactional-replication-between-azure-sql-managed-instance-and-sql-server"></a>Oktatóanyag: tranzakciós replikáció konfigurálása az Azure SQL felügyelt példánya és SQL Server között
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A tranzakciós replikáció lehetővé teszi az adatok replikálását az egyik adatbázisból egy másikba SQL Server vagy az [Azure SQL felügyelt példányain](sql-managed-instance-paas-overview.md). Az SQL felügyelt példánya lehet közzétevő, terjesztő vagy előfizető a replikációs topológiában. Lásd: [tranzakciós replikációs konfigurációk](replication-transactional-overview.md#common-configurations) az elérhető konfigurációkhoz. 

A tranzakciós replikáció jelenleg nyilvános előzetes verzióban érhető el a felügyelt SQL-példányhoz. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> - Felügyelt példány konfigurálása replikációs közzétevőként.
> - Felügyelt példány konfigurálása replikációs terjesztőként.
> - SQL Server előfizetőként való konfigurálása.

![A felügyelt példányok közzétevője, a felügyelt példányok terjesztője és a SQL Server előfizető közötti replikáció](./media/replication-two-instances-and-sql-server-configure-tutorial/sqlmi-to-sql-replication.png)

Ez az oktatóanyag egy tapasztalt közönség számára készült, és feltételezi, hogy a felhasználó ismeri az Azure-beli felügyelt példányok és SQL Server virtuális gépek üzembe helyezését és csatlakozását. 


> [!NOTE]
> Ez a cikk a [tranzakciós replikáció](/sql/relational-databases/replication/transactional/transactional-replication) használatát ismerteti az Azure SQL felügyelt példányain. Nem kapcsolódik a [feladatátvételi csoportokhoz](../database/auto-failover-group-overview.md), az Azure SQL felügyelt példányának szolgáltatása, amely lehetővé teszi az egyes példányok teljes olvasható replikáinak létrehozását. A [tranzakciós replikáció feladatátvételi csoportokkal való](replication-transactional-overview.md#with-failover-groups)konfigurálásakor további szempontokat is figyelembe kell venni.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következő előfeltételekkel:

- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- A két felügyelt példány ugyanazon a virtuális hálózaton belüli üzembe helyezésének élménye.
- Egy SQL Server előfizető, akár helyszíni, akár egy Azure-beli virtuális gépen. Ez az oktatóanyag egy Azure-beli virtuális gépet használ.  
- [SQL Server Management Studio (SSMS) 18,0 vagy újabb](/sql/ssms/download-sql-server-management-studio-ssms).
- A [Azure PowerShell](/powershell/azure/install-az-ps)legújabb verziója.
- Az 445-es és a 1433-es portok lehetővé teszik az SQL-forgalom használatát az Azure tűzfalon és a Windows tűzfalon is.

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Hozzon létre egy új erőforráscsoportot a következő PowerShell-kódrészlet használatával:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="create-two-managed-instances"></a>Két felügyelt példány létrehozása

Hozzon létre két felügyelt példányt az új erőforráscsoporthoz a [Azure Portal](https://portal.azure.com)használatával.

- A közzétevő felügyelt példányának neve legyen `sql-mi-publisher` (a véletlenszerűség néhány karakterrel együtt), a virtuális hálózat nevének pedig a következőnek kell lennie: `vnet-sql-mi-publisher` .
- A terjesztő felügyelt példányának neve (a `sql-mi-distributor` véletlenszerűség néhány karakterével együtt), és _a közzétevő felügyelt példányával megegyező virtuális hálózatban_ kell lennie.

   ![A terjesztőhöz tartozó közzétevő VNet használata](./media/replication-two-instances-and-sql-server-configure-tutorial/use-same-vnet-for-distributor.png)

A felügyelt példányok létrehozásával kapcsolatos további információkért lásd: [felügyelt példány létrehozása a portálon](instance-create-quickstart.md).

  > [!NOTE]
  > Az egyszerűség kedvéért, és mivel ez a leggyakoribb konfiguráció, ez az oktatóanyag azt javasolja, hogy a terjesztő felügyelt példányát a közzétevővel megegyező virtuális hálózaton helyezze el. Azonban lehetséges, hogy a terjesztőt külön virtuális hálózatban hozza létre. Ehhez konfigurálnia kell a VNet-társítást a közzétevő és a terjesztő virtuális hálózatai között, majd konfigurálnia kell a VNet-társítást a terjesztő és az előfizető virtuális hálózatai között.

## <a name="create-a-sql-server-vm"></a>SQL Server VM létrehozása

Hozzon létre egy SQL Server virtuális gépet a [Azure Portal](https://portal.azure.com)használatával. A SQL Server virtuális gépnek a következő tulajdonságokkal kell rendelkeznie:

- Név: `sql-vm-sub`
- Rendszerkép: SQL Server 2016 vagy újabb
- Erőforráscsoport: ugyanaz, mint a felügyelt példány
- Virtuális hálózat: `sql-vm-sub-vnet`

SQL Server VM Azure-beli üzembe helyezésével kapcsolatos további információkért lásd: gyors útmutató [: SQL Server VM létrehozása](../virtual-machines/windows/sql-vm-create-portal-quickstart.md).

## <a name="configure-vnet-peering"></a>VNet-társítás konfigurálása

Konfigurálja a VNet-társítást a két felügyelt példány virtuális hálózata és a SQL Server virtuális hálózata közötti kommunikáció engedélyezéséhez. Ehhez használja ezt a PowerShell-kódrészletet:

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VNet peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VNet peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

A VNet-társítás létrehozása után tesztelje a kapcsolatot SQL Server Management Studio (SSMS) SQL Server és a felügyelt példányokhoz való csatlakozás elindításával. A felügyelt példányok SSMS használatával történő csatlakoztatásával kapcsolatos további információkért lásd: a [SSMS használata az SQL felügyelt példányhoz való csatlakozáshoz](point-to-site-p2s-configure.md#connect-with-ssms).

![A felügyelt példányok kapcsolatának tesztelése](./media/replication-two-instances-and-sql-server-configure-tutorial/test-connectivity-to-mi.png)

## <a name="create-a-private-dns-zone"></a>Privát DNS-zóna létrehozása

A privát DNS-zónák lehetővé teszik a DNS-útválasztást a felügyelt példányok és a SQL Server között.

### <a name="create-a-private-dns-zone"></a>Privát DNS-zóna létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Az új Azure-erőforrás létrehozásához válassza **az erőforrás létrehozása** lehetőséget.
1. Keressen rá az `private dns zone` Azure Marketplace-en.
1. Válassza ki a Microsoft által közzétett **saját DNS zóna** erőforrást, majd válassza a **Létrehozás** lehetőséget a DNS-zóna létrehozásához.
1. Válassza ki az előfizetést és az erőforráscsoportot a legördülő menüből.
1. Adjon meg egy tetszőleges nevet a DNS-zónához, például: `repldns.com` .

   ![Privát DNS-zóna létrehozása](./media/replication-two-instances-and-sql-server-configure-tutorial/create-private-dns-zone.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Tekintse át a saját DNS-zóna paramétereit, majd válassza a **Létrehozás** lehetőséget az erőforrás létrehozásához.

### <a name="create-an-a-record"></a>Rekord létrehozása

1. Nyissa meg az új **saját DNS zónát** , és válassza az **Áttekintés** lehetőséget.
1. Válassza a **+ rekordazonosító** lehetőséget egy új rekord létrehozásához.
1. Adja meg a SQL Server VM nevét, valamint a magánhálózati belső IP-címet.

   ![Rekord konfigurálása](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-a-record.png)

1. Az A rekord létrehozásához kattintson **az OK gombra** .

### <a name="link-the-virtual-network"></a>A virtuális hálózat összekapcsolása

1. Nyissa meg az új **saját DNS zónát** , és válassza a **virtuális hálózati kapcsolatok** elemet.
1. Válassza a **+ Hozzáadás** lehetőséget.
1. Adja meg a hivatkozás nevét, például: `Pub-link` .
1. Válassza ki az előfizetést a legördülő menüből, majd válassza ki a közzétevő felügyelt példányának virtuális hálózatát.
1. Jelölje be az **automatikus regisztráció engedélyezése** melletti jelölőnégyzetet.

   ![VNet-hivatkozás létrehozása](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-vnet-link.png)

1. A virtuális hálózat összekapcsolásához kattintson **az OK gombra** .
1. Ismételje meg ezeket a lépéseket, ha hozzá szeretne adni egy hivatkozást az előfizetői virtuális hálózathoz, amelynek neve például: `Sub-link` .

## <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása

[Hozzon létre egy Azure Storage-fiókot](../../storage/common/storage-account-create.md#create-a-storage-account) a munkakönyvtár számára, majd hozzon létre egy [fájlmegosztást](../../storage/files/storage-how-to-create-file-share.md) a Storage-fiókon belül.

Másolja a fájlmegosztás elérési útját a (z) formátumban: `\\storage-account-name.file.core.windows.net\file-share-name`

Például: `\\replstorage.file.core.windows.net\replshare`

Másolja a Storage-hozzáférési kulcs kapcsolati karakterláncát a formátuma: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Például: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

További információ: a [Storage-fiók elérési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md).

## <a name="create-a-database"></a>Adatbázis létrehozása

Hozzon létre egy új adatbázist a közzétevő felügyelt példányán. Ehhez kövesse az alábbi lépéseket:

1. SQL Server SQL Server Management Studio elindítása.
1. Kapcsolódjon a `sql-mi-publisher` felügyelt példányhoz.
1. Nyisson meg egy **új lekérdezési** ablakot, és hajtsa végre az alábbi T-SQL-lekérdezést az adatbázis létrehozásához.

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
   ID INT NOT NULL PRIMARY KEY,
   c1 VARCHAR(100) NOT NULL,
   dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="configure-distribution"></a>Eloszlás konfigurálása

Miután létrejött a kapcsolat, és van egy mintaadatbázis, a felügyelt példányon is konfigurálhatja a terjesztést `sql-mi-distributor` . Ehhez kövesse az alábbi lépéseket:

1. SQL Server SQL Server Management Studio elindítása.
1. Kapcsolódjon a `sql-mi-distributor` felügyelt példányhoz.
1. Nyisson meg egy **új lekérdezési** ablakot, és futtassa a következő Transact-SQL-kódot a terjesztés konfigurálásához a terjesztő felügyelt példányán:

   ```sql
   EXEC sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   
   EXEC sp_adddistributiondb @database = N'distribution'
   
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > Ügyeljen arra, hogy `\` a paraméterhez csak fordított perjel () legyen használatban @working_directory . A Forward perjel ( `/` ) használata hibát okozhat a fájlmegosztáshoz való csatlakozáskor.

1. Kapcsolódjon a `sql-mi-publisher` felügyelt példányhoz.
1. Nyisson meg egy **új lekérdezési** ablakot, és futtassa a következő Transact-SQL-kódot a terjesztő közzétevőhöz való regisztrálásához:

   ```sql
   Use MASTER
   EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   ```

## <a name="create-the-publication"></a>A kiadvány létrehozása

A terjesztés konfigurálása után már létrehozhatja a kiadványt. Ehhez kövesse az alábbi lépéseket:

1. SQL Server SQL Server Management Studio elindítása.
1. Kapcsolódjon a `sql-mi-publisher` felügyelt példányhoz.
1. A **Object Explorer** bontsa ki a **replikálás** csomópontot, majd kattintson a jobb gombbal a **helyi közzétételi** mappára. **Új kiadvány** kiválasztása...
1. Kattintson a **tovább** gombra, ha az üdvözlőlapot át szeretné helyezni.
1. A **kiadvány-adatbázis** lapon válassza ki a `ReplTutorial` korábban létrehozott adatbázist. Kattintson a **Tovább** gombra.
1. A **kiadvány típusa** lapon válassza a **tranzakciós kiadvány** lehetőséget. Kattintson a **Tovább** gombra.
1. A **cikkek** lapon jelölje be a **táblák** melletti jelölőnégyzetet. Kattintson a **Tovább** gombra.
1. A **tábla sorainak szűrése** lapon a szűrők hozzáadása nélkül válassza a **Next (tovább** ) lehetőséget.
1. A **Pillanatkép-ügynök** lapon jelölje be a pillanatkép azonnali létrehozása elem melletti jelölőnégyzetet, **és az előfizetések inicializálásához tartsa elérhetővé a pillanatképet**. Kattintson a **Tovább** gombra.
1. Az **ügynök biztonsága** lapon válassza a **biztonsági beállítások...** lehetőséget. Adja meg SQL Server bejelentkezési hitelesítő adatait a pillanatkép-ügynökhöz, és kapcsolódjon a közzétevőhöz. Kattintson az **OK** gombra a **Pillanatkép-ügynök biztonsági** oldalának bezárásához. Kattintson a **Tovább** gombra.

   ![A Snapshot Agent biztonságának konfigurálása](./media/replication-two-instances-and-sql-server-configure-tutorial/snapshot-agent-security.png)

1. A **varázsló műveletei** lapon **a kiadvány létrehozásához** és (opcionálisan) válassza ki a kiadvány létrehozásához **szükséges lépéseket** , ha később szeretné menteni ezt a parancsfájlt.
1. A **varázsló befejezése** lapon nevezze el a kiadványt, `ReplTest` és kattintson a **tovább** gombra a kiadvány létrehozásához.
1. Miután létrehozta a kiadványt, frissítse a **replikálás** csomópontot a **Object Explorerban** , és bontsa ki a **helyi kiadványokat** az új kiadvány megjelenítéséhez.

## <a name="create-the-subscription"></a>Előfizetés létrehozása

A kiadvány létrehozása után létrehozhatja az előfizetést. Ehhez kövesse az alábbi lépéseket:

1. SQL Server SQL Server Management Studio elindítása.
1. Kapcsolódjon a `sql-mi-publisher` felügyelt példányhoz.
1. Nyisson meg egy **új lekérdezési** ablakot, és futtassa a következő Transact-SQL-kódot az előfizetés és a terjesztési ügynök hozzáadásához. Használja a DNS-t az előfizető nevének részeként.

```sql
use [ReplTutorial]
exec sp_addsubscription
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub',
@subscription_type = N'Push',
@sync_type = N'automatic',
@article = N'all',
@update_mode = N'read only',
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub',
@job_login = N'azureuser',
@job_password = '<Complex Password>',
@subscriber_security_mode = 0,
@subscriber_login = N'azureuser',
@subscriber_password = '<Complex Password>',
@dts_package_location = N'Distributor'
GO
```

## <a name="test-replication"></a>Replikáció tesztelése

Miután konfigurálta a replikálást, tesztelheti úgy, hogy új elemeket szúr be a közzétevőbe, és figyeli a módosításokat az előfizetőnek.

A következő T-SQL-kódrészlet futtatásával tekintheti meg az előfizető sorait:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Futtassa az alábbi T-SQL-kódrészletet további sorok beszúrásához a közzétevőn, majd jelölje be újra a sorokat az előfizetőn.

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

1. Navigáljon az erőforráscsoporthoz a [Azure Portal](https://portal.azure.com).
1. Válassza ki a felügyelt példány (oka) t, majd válassza a **Törlés** lehetőséget. Írja be `yes` a szövegmezőbe, és erősítse meg, hogy törölni kívánja az erőforrást, majd válassza a **Törlés** lehetőséget. Ez a folyamat hosszabb időt is igénybe vehet a háttérben, és amíg el nem végzi a műveletet, nem fogja tudni törölni a *virtuális fürtöt* vagy bármely más függő erőforrást. Figyelje meg a törlést a **tevékenység** lapon a felügyelt példány törlésének megerősítéséhez.
1. A felügyelt példány törlése után törölje a *virtuális fürtöt* úgy, hogy kiválasztja az erőforráscsoportot, majd a **Törlés** lehetőséget választja. Írja be `yes` a szövegmezőbe, és erősítse meg, hogy törölni kívánja az erőforrást, majd válassza a **Törlés** lehetőséget.
1. Törölje a többi erőforrást. Írja be `yes` a szövegmezőbe, és erősítse meg, hogy törölni kívánja az erőforrást, majd válassza a **Törlés** lehetőséget.
1. Törölje az erőforráscsoportot az **erőforráscsoport törlése** elem kiválasztásával, írja be az erőforráscsoport nevét, majd válassza a `myResourceGroup` **Törlés** lehetőséget.

## <a name="known-errors"></a>Ismert hibák

### <a name="windows-logins-are-not-supported"></a>A Windows-bejelentkezések nem támogatottak

`Exception Message: Windows logins are not supported in this version of SQL Server.`

Az ügynököt Windows-bejelentkezéssel konfigurálták, és egy SQL Server bejelentkezést kell használnia helyette. A **közzétételi tulajdonságok** **ügynök biztonsági** lapján módosíthatja a bejelentkezési hitelesítő adatokat egy SQL Server bejelentkezési azonosítóra.

### <a name="failed-to-connect-to-azure-storage"></a>Sikertelen csatlakozás az Azure Storage-hoz

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21: a 05.07 Azure Storage kapcsolati karakterláncot kapott a következőhöz: replstorage 2019-11-19 02:21:05.07 kapcsolódás a következőhöz: Azure Files Storage ' \\ replstorage. file. Core. Windows. net\replshare ' 2019-11-19 02:21:31.21 nem sikerült csatlakozni az Azure Storage-hoz () operációs rendszerbeli hiba: 53.

Ez valószínűleg azért van, mert az 445-es port le van zárva az Azure tűzfalon, a Windows tűzfalon vagy mindkettőn.

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

Ezt a hibát okozhatja, ha a fájlmegosztás elérési útjának fordított perjel helyett perjelet használ.
  
  - Ez rendben van: `\\replstorage.file.core.windows.net\replshare`
  - Ez egy OS 55-es hibát eredményezhet: `'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Nem lehet csatlakozni az előfizetőhöz

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Lehetséges megoldások:

- Győződjön meg arról, hogy a 1433-es port nyitva van.
- Győződjön meg arról, hogy a TCP/IP engedélyezve van az előfizetőn.
- Erősítse meg, hogy a DNS-nevet használták az előfizető létrehozásakor.
- Ellenőrizze, hogy a virtuális hálózatok megfelelően vannak-e csatolva a magánhálózati DNS-zónában.
- Ellenőrizze, hogy a rekord megfelelően van-e konfigurálva.
- Ellenőrizze, hogy a VNet-társítás megfelelően van-e konfigurálva.

### <a name="no-publications-to-which-you-can-subscribe"></a>Nincsenek előfizethető kiadványok

Amikor új előfizetést ad hozzá az **új előfizetés** varázslóval, a **kiadvány** lapon láthatja, hogy nincsenek elérhető adatbázisok és kiadványok a rendelkezésre álló lehetőségek közül, és a következő hibaüzenet jelenhet meg:

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`

Habár lehetséges, hogy ez a hibaüzenet pontos, és valójában nem érhető el kiadványok az Ön által csatlakoztatott közzétevőn, vagy ha nem rendelkezik megfelelő engedélyekkel, ezt a hibát a SQL Server Management Studio egy régebbi verziója is okozhatja. Próbálja meg frissíteni a SQL Server Management Studio 18,0-es vagy újabb verziójára, hogy kizárja a problémát kiváltó okként.

## <a name="next-steps"></a>Következő lépések

### <a name="enable-security-features"></a>Biztonsági funkciók engedélyezése

Tekintse meg a [Mi az Azure SQL felügyelt példány?](sql-managed-instance-paas-overview.md#advanced-security-and-compliance) című cikket, amely az adatbázis biztonságossá tételének részletes listáját tartalmazza. A következő biztonsági funkciókat tárgyaljuk:

- [SQL felügyelt példányok naplózása](auditing-configure.md)
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Fenyegetések észlelése](threat-detection-configure.md)
- [Dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking)
- [Sorszintű biztonság](/sql/relational-databases/security/row-level-security)
- [Transzparens adattitkosítás (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>SQL felügyelt példány képességei

A felügyelt példányok képességeinek teljes áttekintését lásd:

> [!div class="nextstepaction"]
> [SQL felügyelt példány képességei](sql-managed-instance-paas-overview.md)