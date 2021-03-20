---
title: Kapcsolódás SQL Database C és C++ használatával
description: Ebben a rövid útmutatóban a mintakód segítségével modern alkalmazást hozhat létre C++ nyelven, és egy hatékony, a felhőben található, a Azure SQL Databaset használó, nagy teljesítményű kapcsolatot biztosító adatbázist támogat.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: cpp
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: e891c5797c9ce93e6cab7a07d2f68de1a9157249
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674758"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Kapcsolódás SQL Database C és C++ használatával
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a bejegyzés a C és C++ fejlesztők számára készült, akik a Azure SQL Databasehoz próbálnak csatlakozni. Szakaszokra bontva, így a legjobban felhasználható szakaszra ugorhat.

## <a name="prerequisites-for-the-cc-tutorial"></a>A C/C++ oktatóanyag előfeltételei

Győződjön meg róla, hogy rendelkezik az alábbi elemekkel:

* Aktív Azure-fiók. Ha még nincs fiókja, regisztrálhat az [Azure ingyenes próbaverziójára](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). A minta létrehozásához és futtatásához telepítenie kell a C++ nyelvi összetevőit.
* A [Visual Studio Linux fejlesztése](/cpp/linux/?view=vs-2019). Ha Linux rendszeren fejleszt, a Visual Studio Linux-bővítményt is telepítenie kell.

## <a name="azure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>Azure SQL Database és SQL Server virtuális gépeken

A Azure SQL Database Microsoft SQL Serverre épül, és magas rendelkezésre állást, teljesítményt és skálázható szolgáltatást biztosít. Számos előnnyel jár az Azure SQL használata a helyszínen futó saját adatbázison. Az Azure SQL-ben nem kell telepítenie, beállítania, karbantartani vagy kezelnie az adatbázist, de csak az adatbázis tartalmát és szerkezetét. Az olyan adatbázisok esetében, mint például a hibatűrés és a redundancia, az olyan általános dolgok, amiket az összes beépített.

Az Azure jelenleg két lehetőséggel rendelkezik az SQL Server számítási feladatainak üzemeltetéséhez: Azure SQL Database, adatbázis-szolgáltatásként és SQL Server on Virtual Machines (VM). A kettő közötti különbségeket nem fogjuk részletesen figyelembe venni, de a Azure SQL Database a legjobb megoldás az új felhőalapú alkalmazások számára, hogy kihasználhassa a Cloud Services által nyújtott költségmegtakarítást és a teljesítmény optimalizálását. Ha a helyszíni alkalmazások felhőbe való áttelepítését vagy kiterjesztését tervezi, az Azure-beli virtuális gépen futó SQL Server jobban dolgozhat Önnek. A cikk egyszerű megtartásához hozzon létre egy Azure SQL Database.

## <a name="data-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>Adatelérési technológiák: ODBC és OLE DB

A Azure SQL Databasehoz való csatlakozás nem különbözik, és jelenleg kétféleképpen lehet csatlakozni az adatbázisokhoz: ODBC (nyílt adatbázis-kapcsolat) és OLE DB (objektum-csatolás és beágyazási adatbázis). Az elmúlt években a Microsoft az ODBC-vel igazította a [natív kapcsolati adathozzáférést](/archive/blogs/sqlnativeclient/microsoft-is-aligning-with-odbc-for-native-relational-data-access). Az ODBC viszonylag egyszerű, és sokkal gyorsabb, mint a OLE DB. Az egyetlen kivétel az, hogy az ODBC a régi C stílusú API-t használja.

## <a name="step-1--creating-your-azure-sql-database"></a><a id="Create"></a>1. lépés: a Azure SQL Database létrehozása

Tekintse meg az [első lépéseket ismertető oldalt](single-database-create-quickstart.md) , amelyből megtudhatja, hogyan hozhat létre egy mintaadatbázis-adatbázist.  Azt is megteheti, hogy ezt a [rövid kétperces videót](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) a Azure Portal használatával hozza létre Azure SQL Database.

## <a name="step-2--get-connection-string"></a><a id="ConnectionString"></a>2. lépés: a kapcsolatok karakterláncának beolvasása

A Azure SQL Database üzembe helyezése után el kell végeznie az alábbi lépéseket a kapcsolódási adatok meghatározásához és az ügyfél IP-címének a tűzfal-hozzáféréshez való hozzáadásához.

A [Azure Portal](https://portal.azure.com/)az adatbázis áttekintés szakaszának részeként megjelenített adatbázis- **kapcsolati karakterláncok megjelenítésével** nyissa meg a Azure SQL Database ODBC kapcsolati karakterláncot:

![ODBCConnectionString](./media/develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/develop-cplusplus-simple/dbconnection.png)

Másolja az **ODBC (Node.js) [SQL Authentication]** sztringet tartalmazó tartalmat. Ezt a karakterláncot később használjuk a C++ ODBC parancssori tolmácsból való kapcsolódáshoz. Ez a karakterlánc részletes adatokat tartalmaz, például az illesztőprogramot, a kiszolgálót és az egyéb adatbázis-kapcsolati paramétereket.

## <a name="step-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>3. lépés: az IP-cím hozzáadása a tűzfalhoz

Nyissa meg a kiszolgáló tűzfal szakaszát, és adja hozzá az [ügyfél IP-címét a tűzfalhoz a következő lépésekkel](firewall-configure.md) , hogy biztosan létre lehessen hozni egy sikeres kapcsolatot:

![AddyourIPWindow](./media/develop-cplusplus-simple/ip.png)

Ekkor konfigurálta a Azure SQL Database, és készen áll a C++-kódból való kapcsolódásra.

## <a name="step-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>4. lépés: csatlakozás Windows C/C++ alkalmazásból

A Visual Studióval építhető [példa használatával könnyedén csatlakozhat a Azure SQL Database az ODBC használatával a Windowsban](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) . A minta egy ODBC parancssori tolmácsot valósít meg, amely a Azure SQL Databasehoz való kapcsolódáshoz használható. Ez a példa egy adatbázis forrásának (DSN) fájlját veszi igénybe parancssori argumentumként, vagy pedig a korábban a Azure Portalból másolt részletes kapcsolatok sztringjét. Hozza létre a projekt tulajdonságlapját, és illessze be a (z) paramétert az itt látható módon:

![DSN Propsfile](./media/develop-cplusplus-simple/props.png)

Győződjön meg arról, hogy a megfelelő hitelesítési adatokat adja meg az adatbázishoz az adatbázis-kapcsolati karakterlánc részeként.

Indítsa el az alkalmazást a létrehozásához. A sikeres kapcsolatok érvényesítéséhez a következő ablaknak kell megjelennie. Az adatbázis-kapcsolat érvényesítéséhez olyan alapszintű SQL-parancsokat is futtathat, mint például a **create Table (tábla létrehozása** ):

![SQL-parancsok](./media/develop-cplusplus-simple/sqlcommands.png)

Azt is megteheti, hogy létrehoz egy DSN-fájlt a varázsló segítségével, amely akkor indul el, ha nincs megadva parancs argumentum. Azt javasoljuk, hogy ezt a lehetőséget is kipróbálhatja. Ezt a DSN-fájlt használhatja az automatizáláshoz és a hitelesítési beállítások védelméhez:

![DSN-fájl létrehozása](./media/develop-cplusplus-simple/datasource.png)

Gratulálunk! Mostantól sikeresen csatlakozott az Azure SQL-hez a C++ és az ODBC használatával Windows rendszeren. A Linux platformon is megteheti az olvasást.

## <a name="step-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>5. lépés: csatlakozás Linux C/C++-alkalmazásból

Ha még nem hallotta a híreket, a Visual Studio mostantól lehetővé teszi a C++ Linux-alkalmazások fejlesztését is. Erről az új forgatókönyvről a [Visual C++ for Linux fejlesztői](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) blogjában olvashat. A Linuxra való kiépítéshez szüksége van egy távoli gépre, amelyen a Linux-disztribúció fut. Ha még nem rendelkezik ilyennel, a [Linux Azure Virtual Machines](../../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)használatával gyorsan beállíthatja a virtuális gépeket.

Ebben az oktatóanyagban tegyük fel, hogy Ubuntu 16,04 Linux-disztribúció van beállítva. A lépéseket az Ubuntu 15,10, a Red Hat 6 és a Red Hat 7 rendszerre is alkalmazni kell.

A következő lépésekkel telepítheti az SQL és az ODBC számára szükséges kódtárakat a disztribúcióban:

```console
    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*
```

Indítsa el a Visual studiót. Az eszközök – > lehetőségek – > platformfüggetlen – > Csatlakozáskezelő, vegyen fel egy kapcsolódást a linuxos Box-hoz:

![Eszközök beállításai](./media/develop-cplusplus-simple/tools.png)

Miután létrejött a kapcsolat az SSH-val, hozzon létre egy üres Project-(Linux-) sablont:

![Új projekt sablonja](./media/develop-cplusplus-simple/template.png)

Ezután hozzáadhat egy [új C-forrásfájlt, és lecserélheti ezt a tartalmat](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Az ODBC API-k SQLAllocHandle, a SQLSetConnectAttr és a SQLDriverConnect használatával inicializálhatja és létrehozhatja a kapcsolatot az adatbázissal.
A Windows ODBC-mintához hasonlóan a SQLDriverConnect hívást is le kell cserélnie az adatbázis-kapcsolati karakterlánc paraméterei közül a Azure Portal korábban átmásolt adatokból.

```c
     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);
```

A fordítás előtt a legutolsó teendő, ha az **ODBC** -t függvénytár-függőségként adja hozzá:

![ODBC hozzáadása bemeneti kódtárként](./media/develop-cplusplus-simple/lib.png)

Az alkalmazás elindításához nyissa meg a Linux-konzolt a **hibakeresési** menüből:

![Linux-konzol](./media/develop-cplusplus-simple/linuxconsole.png)

Ha sikeresen megtörtént a hozzáférés, az aktuális adatbázis neve jelenik meg a Linux-konzolon:

![A Linux-konzol ablakának kimenete](./media/develop-cplusplus-simple/linuxconsolewindow.png)

Gratulálunk! Sikeresen elvégezte az oktatóanyagot, és mostantól csatlakozhat a Azure SQL Database a C++-ból Windows-és Linux-platformokon.

## <a name="get-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a>A teljes C/C++ oktatóanyag-megoldás beszerzése

Az ebben a cikkben szereplő összes mintát tartalmazó GetStarted-megoldás megtalálható a GitHubon:

* [ODBC C++ Windows-minta](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), töltse le a Windows C++ ODBC-mintát az Azure SQL-hez való kapcsolódáshoz
* [ODBC C++ Linux-minta](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), töltse le a Linux C++ ODBC-mintát az Azure SQL-hez való kapcsolódáshoz

## <a name="next-steps"></a>Következő lépések

* Tekintse át a [SQL Database fejlesztés áttekintését](develop-overview.md)
* További információ az [ODBC API-referenciáról](/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>További források

* [Tervezési minták az Azure SQL Database-t használó több-bérlős SaaS-alkalmazásokhoz](saas-tenancy-app-design-patterns.md)
* Fedezze fel az [SQL Database összes képességét](https://azure.microsoft.com/services/sql-database/)