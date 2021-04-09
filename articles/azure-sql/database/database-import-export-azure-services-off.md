---
title: Azure SQL Database importálása vagy exportálása anélkül, hogy az Azure-szolgáltatások hozzáférjenek a kiszolgálóhoz.
description: Azure SQL Database importálása vagy exportálása anélkül, hogy az Azure-szolgáltatások hozzáférjenek a kiszolgálóhoz.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 3a02876234d43df2e98a3a4e60453fc3f1f74ef6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724169"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Azure SQL Database importálása vagy exportálása az Azure-szolgáltatások a kiszolgálóhoz való hozzáférésének engedélyezése nélkül
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebből a cikkből megtudhatja, hogyan importálhat vagy exportálhat egy Azure SQL Database, ha *engedélyezi az Azure-szolgáltatások* *kikapcsolását* a kiszolgálón. A munkafolyamat egy Azure-beli virtuális gépet használ a SqlPackage futtatására az importálási vagy exportálási művelet végrehajtásához.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>Az Azure-beli virtuális gép létrehozása

Hozzon létre egy Azure-beli virtuális gépet az **üzembe helyezés az Azure-** ban gomb kiválasztásával.

Ez a sablon lehetővé teszi egy egyszerű Windowsos virtuális gép üzembe helyezését néhány különböző lehetőséggel a Windows-verzióhoz a legújabb javított verzió használatával. Ez egy a2 méretű virtuális gépet helyez üzembe az erőforráscsoport helyén, és a virtuális gép teljes tartománynevét adja vissza.
<br><br>

[![Az "üzembe helyezés az Azure-ban" feliratú gombot ábrázoló kép.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json)

További információkért lásd: [Windows rendszerű virtuális gép nagyon egyszerű üzembe helyezése](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows).

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

A következő lépések bemutatják, hogyan csatlakozhat a virtuális géphez távoli asztali kapcsolaton keresztül.

1. Ha az üzembe helyezés elkészült, lépjen a virtuális gép erőforráshoz.

   ![A képernyőfelvételen egy virtuális gép áttekintő lapja látható egy csatlakozási gombbal.](./media/database-import-export-azure-services-off/vm.png)  

2. Válassza a **Kapcsolódás** lehetőséget.

   A virtuális gép nyilvános IP-címét és portszámát tartalmazó RDP protokoll fájl (. rdp fájl) űrlap jelenik meg.

   ![RDP-űrlap](./media/database-import-export-azure-services-off/rdp.png)  

3. Válassza az **RDP-fájl letöltése** lehetőséget.

   > [!NOTE]
   > Az SSH használatával is csatlakozhat a virtuális géphez.

4. Zárjuk be a **Kapcsolódás virtuális géphez** űrlapot.
5. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz.
6. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Mac rendszerben szüksége van egy RDP-kliensre, mint például a Mac App Store áruházban elérhető [távoli asztali ügyfélre](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12).

7. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót, majd kattintson **az OK gombra**.

8. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza az **Igen** vagy a **Folytatás** lehetőséget a kapcsolódás folytatásához.

## <a name="install-sqlpackage"></a>A SqlPackage telepítése

[Töltse le és telepítse a SqlPackage legújabb verzióját](/sql/tools/sqlpackage-download).

További információ: [SqlPackage.exe](/sql/tools/sqlpackage).

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Tűzfalszabály létrehozása a virtuális gép az adatbázishoz való hozzáférésének engedélyezéséhez

Adja hozzá a virtuális gép nyilvános IP-címét a kiszolgáló tűzfalához.

Az alábbi lépéseket követve hozzon létre egy kiszolgálói szintű IP-tűzfalszabály a virtuális gép nyilvános IP-címéhez, és lehetővé teszi a kapcsolódást a virtuális gépről.

1. Válassza ki az **SQL-adatbázisok** elemet a bal oldali menüben, majd válassza ki az adatbázist az **SQL-adatbázisok** lapon. Megnyílik az adatbázis áttekintő lapja, amely megjeleníti a teljes kiszolgálónevet (például **servername.database.Windows.net**), és további konfigurálási lehetőségeket biztosít.

2. Másolja ezt a teljes kiszolgálónevet, amelyet a kiszolgálóhoz és az adatbázisaihoz való csatlakozáskor használ.

   ![kiszolgáló neve](./media/database-import-export-azure-services-off/server-name.png)

3. Válassza a **kiszolgáló tűzfal beállítása** lehetőséget az eszköztáron. Ekkor megnyílik a kiszolgáló **tűzfalbeállítások** lapja.

   ![kiszolgálói szintű IP-tűzfalszabály](./media/database-import-export-azure-services-off/server-firewall-rule.png)

4. Válassza az **ügyfél IP-** címének hozzáadása lehetőséget az eszköztáron, és adja hozzá a virtuális gép nyilvános IP-címét egy új kiszolgálói szintű IP-tűzfalszabály eléréséhez. A kiszolgálói szintű IP-tűzfalszabály egyetlen IP-cím vagy egy IP-címtartomány 1433-as portját nyithatja meg.

5. Kattintson a **Mentés** gombra. A virtuális gép nyilvános IP-címéhez egy kiszolgálói szintű IP-tűzfalszabály jön létre, amely a kiszolgálón a 1433-es portot nyitja meg.

6. A **tűzfalbeállítások** oldalának lezárása.

## <a name="export-a-database-using-sqlpackage"></a>Adatbázis exportálása a SqlPackage használatával

Azure SQL Database a [SqlPackage](/sql/tools/sqlpackage) parancssori segédprogrammal való exportálásával kapcsolatban lásd: [paraméterek és tulajdonságok exportálása](/sql/tools/sqlpackage#export-parameters-and-properties). A SqlPackage segédprogram a [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) és [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)legújabb verzióit, illetve a [SqlPackage](/sql/tools/sqlpackage-download)legújabb verzióját is letölti.

A legtöbb éles környezetben a SqlPackage segédprogram használatát javasoljuk a méretezéshez és a teljesítményhez. További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

Ez a példa azt mutatja be, hogyan lehet exportálni egy adatbázist SqlPackage.exe használatával Active Directory univerzális hitelesítéssel. Cserélje le a értéket a környezetre jellemző értékekre.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-a-database-using-sqlpackage"></a>Adatbázis importálása a SqlPackage használatával

Ha SQL Server adatbázist szeretne importálni a [SqlPackage](/sql/tools/sqlpackage) parancssori segédprogrammal, tekintse meg a [paraméterek és tulajdonságok importálása](/sql/tools/sqlpackage#import-parameters-and-properties)című témakört. A SqlPackage a legújabb [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) és [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt). A [SqlPackage](/sql/tools/sqlpackage-download)legújabb verzióját is letöltheti.

A méretezés és a teljesítmény érdekében javasoljuk, hogy a SqlPackage a legtöbb éles környezetben használja a Azure Portal használata helyett. Az SQL Server Customer tanácsadó csapatának blogja a fájlok használatával történő áttelepítéssel kapcsolatban `BACPAC` lásd: [áttelepítés SQL Serverról Azure SQL DATABASEre BACPAC-fájlok használatával](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

A következő SqlPackage-paranccsal importálhatja a **AdventureWorks2017** -adatbázist a helyi tárolóból egy Azure SQL Databaseba. Létrehoz egy **myMigratedDatabase** nevű új adatbázist egy **prémium** szintű szolgáltatási szinttel és egy **P6** szolgáltatás-célkitűzéssel. A környezetnek megfelelően módosítsa ezeket az értékeket.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Ha a tAzure SQL Database szeretne csatlakozni a vállalati tűzfal mögött, a tűzfalon meg kell nyitni a 1433-es portot.

Ez a példa bemutatja, hogyan importálhat egy adatbázist az SqlPackage használatával Active Directory univerzális hitelesítéssel.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Az exportálási sebesség számos tényezőtől függ (például az adatalakzattól), így nem lehet előre jelezni, hogy milyen sebességgel kellene számolni. A SqlPackage jelentős időt vehet igénybe, különösen a nagyméretű adatbázisok esetében.

A legjobb teljesítmény eléréséhez a következő stratégiákat lehet kipróbálni:

1. Más számítási feladat ne fusson az adatbázison. Hozzon létre egy másolatot az exportálás előtt lehet a legjobb megoldás, hogy más számítási feladatok ne fussanak.
2. Növelje az adatbázis-szolgáltatási szint célkitűzését (SLO) az exportálási munkaterhelés jobb kezelése érdekében (elsődleges olvasási I/O). Ha az adatbázis jelenleg GP_Gen5_4, lehet, hogy egy üzletileg kritikus-szinten segítene az olvasási munkaterheléshez.
3. Győződjön meg arról, hogy vannak fürtözött indexek, különösen a nagyméretű táblákhoz.
4. A virtuális gépeknek ugyanabban a régióban kell lenniük, mint az adatbázisnak a hálózati megkötések elkerülése érdekében.
5. A virtuális gépeknek a blob Storage-ba való feltöltés előtt megfelelő méretű SSD-meghajtóval kell rendelkezniük a temp összetevők generálásához.
6. A virtuális gépeknek megfelelő alapszintű és memória-konfigurációval kell rendelkezniük az adott adatbázishoz.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Az importált vagy exportált tároló tárolása. BACPAC-fájl

A. A BACPAC-fájl tárolhatók az [Azure-blobokban](../../storage/blobs/storage-blobs-overview.md)vagy [Azure Files](../../storage/files/storage-files-introduction.md).

A legjobb teljesítmény eléréséhez használja a Azure Files. A SqlPackage a fájlrendszerrel működik, hogy közvetlenül hozzáférhessen Azure Fileshoz.

A Cost csökkentése érdekében használja az Azure-blobokat, amelyek kisebbek, mint a prémium szintű Azure-fájlmegosztás. Azonban szükség van a másolására is [. BACPAC fájlt](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) a blob és a helyi fájlrendszer között az importálási vagy exportálási művelet előtt. Ennek eredményeképpen a folyamat hosszabb időt vesz igénybe.

A feltöltéshez vagy a letöltéshez. BACPAC-fájlok: az [adatok átvitele a AzCopy és a blob Storage](../../storage/common/storage-use-azcopy-v10.md#transfer-data)szolgáltatással, valamint az [adatok átvitele a AzCopy és a file Storage](../../storage/common/storage-use-azcopy-files.md)szolgáltatással.

A környezettől függően előfordulhat, hogy [konfigurálnia kell az Azure Storage-tűzfalakat és a virtuális hálózatokat](../../storage/common/storage-network-security.md).

## <a name="next-steps"></a>Következő lépések

- Az importált SQL Databasehoz való kapcsolódással és lekérdezéssel kapcsolatos információkért lásd: gyors útmutató [: Azure SQL Database: a SQL Server Management Studio használata a kapcsolódáshoz és az adatlekérdezéshez](connect-query-ssms.md).
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- A teljes SQL Server adatbázis-áttelepítési folyamatról, beleértve a teljesítménnyel kapcsolatos ajánlásokat is, tekintse meg a [SQL Server adatbázis áttelepítésének Azure SQL Database](migrate-to-database-from-sql-server.md).
- A tárolási kulcsok és a közös hozzáférési aláírások biztonságos kezelésével és megosztásával kapcsolatos további információkért lásd: az [Azure Storage biztonsági útmutatója](../../storage/blobs/security-recommendations.md).
