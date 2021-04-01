---
title: Kapcsolódás Excellel
description: Megtudhatja, hogyan csatlakoztatható a Microsoft Excel egy adatbázishoz Azure SQL Database vagy az Azure SQL felügyelt példányában. Adatok importálása Excelbe jelentésekhez és adatok áttekintéséhez.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 304ad51bc40c8165c2dc11bb70287491e3d6dfb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91444027"
---
# <a name="connect-excel-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance-and-create-a-report"></a>Az Excel csatolása Azure SQL Database vagy Azure SQL felügyelt példányban található adatbázishoz, és jelentés létrehozása
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Az Excelt összekapcsolhatja egy adatbázissal, majd importálhatja az adatait, és létrehozhat táblákat és diagramokat az adatbázis értékei alapján. Ebben az oktatóanyagban csatlakoztatjuk az Excelt az adatbázistáblához, elmentjük az adatokat tároló fájlt és az Excelre vonatkozó kapcsolatadatokat, és kimutatásdiagramot hozunk létre az adatbázis értékeiből.

Az első lépések előtt létre kell hoznia egy adatbázist. Ha még nem rendelkezik ilyennel, tekintse meg az [adatbázis létrehozása a Azure SQL Database-ben](single-database-create-quickstart.md) és a [kiszolgálói szintű IP-tűzfal létrehozása](firewall-create-server-level-portal-quickstart.md) című témakört, amely néhány percen belül beolvashatja az adatbázist a mintaadatok használatával.

Ebben a cikkben a mintaadatok importálása az Excel programba című cikkből származnak, de a saját adataival hasonló lépéseket is követheti.

Az Excelnek is telepítve kell lennie. Ebben a cikkben a [Microsoft Excel 2016](https://products.office.com/) verziót vettük alapul.

## <a name="connect-excel-and-load-data"></a>Az Excel és az adatterhelés összekötése

1. Az Excel SQL Database-adatbázishoz való összekapcsolásához nyissa meg az Excelt, majd hozzon létre egy új munkafüzetet, vagy nyisson meg egy meglévő Excel-munkafüzetet.
2. A lap tetején lévő menüsorban válassza az **adatok** fület, válassza az **adatok lekérése** lehetőséget, válassza ki az Azure-ból lehetőséget, majd válasszon **a következőből: Azure SQL Database**.

   ![Adatforrás kiválasztása: az Excel csatolása SQL Databasehoz.](./media/connect-excel/excel_data_source.png)

3. A **SQL Server adatbázis** párbeszédpanelen írja be a **kiszolgáló nevét** , amelyhez csatlakozni szeretne a következő formában: <*servername* > **. database.Windows.net**. Például: **msftestserver.database.Windows.net**. Igény szerint megadhatja az adatbázis nevét. A hitelesítő adatok ablak megnyitásához kattintson **az OK gombra** .

   ![Kapcsolódás az adatbázis-kiszolgálóhoz párbeszédpanel](./media/connect-excel/server-name.png)

4. A **SQL Server adatbázis** párbeszédpanelen válassza a bal oldalon **található adatbázis** lehetőséget, majd írja be annak a kiszolgálónak a **felhasználónevét** és **jelszavát** , amelyhez csatlakozni szeretne. A **navigátor** megnyitásához válassza a **Kapcsolódás** lehetőséget.

   ![Kiszolgálónév és hitelesítő adatok megadása](./media/connect-excel/connect-to-server.png)

   > [!TIP]
   > A hálózati környezettől függően előfordulhat, hogy nem tud csatlakozni, vagy elveszti a kapcsolatot, ha a kiszolgáló nem engedélyezi a forgalmat az ügyfél IP-címéről. Lépjen az [Azure Portalhoz](https://portal.azure.com/), kattintson az SQL Server-példányok lehetőségre, majd a saját kiszolgálójára, ezután a beállítások alatt a tűzfalra, és adja hozzá ügyfél IP-címét. A részleteket a [Tűzfal beállításainak konfigurálása](firewall-configure.md) részben találja meg.

5. **A** kezelőben válassza ki a listából a használni kívánt adatbázist, válassza ki a használni kívánt táblákat vagy nézeteket ( **vGetAllCategories**), majd válassza a Load ( **Betöltés** ) lehetőséget az adatok adatbázisból az Excel-számolótáblába való áthelyezéséhez.

    ![Válasszon ki egy adatbázist és egy táblát.](./media/connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Adatok importálása Excelbe és kimutatásdiagram létrehozása

Most, hogy létrehozta a kapcsolódást, számos különböző lehetőség áll rendelkezésére az adattöltéshez. Az alábbi lépések például egy kimutatást hoznak létre az adatbázisban található, SQL Databaseban található adatai alapján.

1. Kövesse az előző szakaszban leírt lépéseket, de ezúttal, a **Betöltés helyett** válassza a **Betöltés** lehetőséget a **Betöltés legördülő listából** .
2. Ezután válassza ki, hogyan szeretné megtekinteni ezeket az adatfájlokat a munkafüzetben. Válassza a **Kimutatásdiagram** lehetőséget. Az **Új munkalap** kiválasztásával új munkalapot is létrehozhat, vagy választhatja az **Adatok hozzáadása adatmodellhez** beállítást is. Az adatmodellekről további információkat az [Adatmodell létrehozása Excelben](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B) részben talál.

    ![Az Excelben szereplő adatok formátumának kiválasztása](./media/connect-excel/import-data.png)

    A munkalapon most egy üres kimutatástábla és -diagram van.
3. A **Kimutatástábla mezői** alatt jelölje ki az összes megtekinteni kívánt mező jelölőnégyzetét.

    ![Konfigurálja az adatbázis-jelentést.](./media/connect-excel/power-pivot-results.png)

> [!TIP]
> Ha más Excel-munkafüzeteket és-munkalapokat szeretne összekötni az adatbázissal, **válassza az** adatlapot, és válassza a **legutóbbi** források lehetőséget a **legutóbbi források** párbeszédpanel elindításához. Válassza ki a listából a létrehozott kapcsolatokat, majd kattintson a **Megnyitás** gombra.
> ![Legutóbbi források párbeszédpanel](./media/connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Állandó kapcsolatok létrehozása. odc-fájl használatával

Ha véglegesen szeretné menteni a kapcsolat részleteit, létrehozhat egy. odc-fájlt, és ezt a kapcsolatot kiválasztható lehetőséggel teheti meg a **meglévő kapcsolatok** párbeszédpanelen.

1. A lap tetején lévő menüsorban válassza az **adatlapot, majd válassza a** **meglévő kapcsolatok** lehetőséget a **létező kapcsolatok** párbeszédpanel elindításához.
   1. Az **adatforrás kiválasztása** párbeszédpanel megnyitásához válassza a **Tallózás** lehetőséget.
   2. Válassza ki a **+ NewSqlServerConnection. odc** fájlt, majd kattintson a **Megnyitás** elemre az **adatelérési varázsló** megnyitásához.

      ![Új kapcsolatok párbeszédpanel](./media/connect-excel/new-connection.png)

2. Az **adatkapcsolatok varázslóban** adja meg a kiszolgáló nevét és a SQL Database hitelesítő adatait. Kattintson a **Tovább** gombra.
   1. Válassza ki azt az adatbázist, amely az adatait tartalmazza a legördülő listából.
   2. Válassza ki azt a táblát vagy nézetet, amelyre kíváncsi. VGetAllCategories választottunk.
   3. Kattintson a **Tovább** gombra.

      ![Adatkapcsolatok varázsló](./media/connect-excel/data-connection-wizard.png)

3. Válassza ki a fájl helyét, a **fájlnevet** és a **felhasználóbarát nevet** az adatelérési varázsló következő képernyőjén. Azt is megteheti, hogy elmenti a jelszót a fájlban, de ez potenciálisan elérhetővé teheti az adatait a nemkívánatos hozzáférés érdekében. Ha elkészült, válassza a **Befejezés** lehetőséget.

    ![Adatkapcsolatok mentése](./media/connect-excel/save-data-connection.png)

4. Válassza ki, hogyan szeretné importálni az adatait. Úgy döntöttünk, hogy kimutatást teszünk. A kapcsolatok tulajdonságait a **Tulajdonságok** lehetőség kiválasztásával is módosíthatja. Ha elkészült, kattintson **az OK gombra** . Ha nem választotta a jelszó mentését a fájllal, a rendszer kérni fogja a hitelesítő adatok megadását.

    ![Adatok importálása](./media/connect-excel/import-data2.png)

5. Ellenőrizze, **hogy az új** kapcsolat mentve lett-e, és válassza ki az adatlapot, majd a **meglévő kapcsolatok** elemet.

    ![Létező kapcsolatok](./media/connect-excel/existing-connection.png)

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [csatlakozhat és lekérdezéseket SQL Server Management Studio](connect-query-ssms.md) a speciális lekérdezésekhez és elemzésekhez.
* Tudjon meg többet a [rugalmas készletek](elastic-pool-overview.md) előnyeiről.
* Megtudhatja, hogyan [hozhat létre olyan webalkalmazást, amely a háttérbeli Azure SQL Databasehoz csatlakozik](../../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
