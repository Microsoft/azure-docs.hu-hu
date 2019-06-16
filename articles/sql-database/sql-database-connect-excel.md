---
title: Excel csatlakoztatása egy Azure SQL Database-adatbázis |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat egy Azure SQL Database-adatbázis a Microsoft Excel. Adatok importálása Excelbe jelentésekhez és adatok áttekintéséhez.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joseidz
ms.author: craigg
ms.reviewer: ''
manager: craigg
ms.date: 02/12/2019
ms.openlocfilehash: a6e0adc6b4abbb58504b6f56c8def72440ad370d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67061401"
---
# <a name="connect-excel-to-a-single-database-in-azure-sql-database-and-create-a-report"></a>Excel csatlakoztatása egy Azure SQL Database-adatbázis és a egy jelentés létrehozása

Excel csatlakoztatása egy Azure SQL Database-adatbázis és importálhat adatokat, és létre táblázatokat és diagramokat az adatbázisban levő értékek alapján. Ebben az oktatóanyagban csatlakoztatjuk az Excelt az adatbázistáblához, elmentjük az adatokat tároló fájlt és az Excelre vonatkozó kapcsolatadatokat, és kimutatásdiagramot hozunk létre az adatbázis értékeiből.

Szüksége lesz egy önálló adatbázis használatának megkezdése előtt. Ha még nincs fiókja, [egy önálló adatbázis létrehozása](sql-database-single-database-get-started.md) és [kiszolgálószintű IP-tűzfalszabály létrehozása](sql-database-server-level-firewall-rule.md) beolvasni egy adatbázist mintaadatokkal, néhány perc alatt ütembe helyezheti.

Ebben a cikkben azt importálunk mintaadatokat az Excelbe az említett cikkből, de az adatainak Ön a tulajdonosa végrehajthatja a leírt lépéseket.

Az Excelnek is telepítve kell lennie. Ebben a cikkben a [Microsoft Excel 2016](https://products.office.com/) verziót vettük alapul.

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Az Excel összekapcsolása a egy SQL database és az adatok betöltése

1. Az Excel SQL-adatbázishoz való csatlakoztatásához nyissa meg az Excelt, majd hozzon létre egy új munkafüzetet, vagy nyisson meg egy meglévő Excel-munkafüzetet.
2. A lap felső menüsávban válassza a **adatok** lapon jelölje be **adatok lekérése**, az Azure, majd válassza ki és **az Azure SQL Database**. 

   ![Adatforrás kiválasztása: Excel csatlakoztatása SQL Database-adatbázishoz.](./media/sql-database-connect-excel/excel_data_source.png)

   Megnyílik az Adatkapcsolat varázsló.
3. A **Kapcsolódás adatbázis-kiszolgálóhoz** párbeszédpanelen írja be annak az SQL Database adatbázisnak a **Kiszolgálónevét**, amelyhez csatlakozni szeretne <*kiszolgálónév*> **. database.windows.net** formában. Ha például **msftestserver.database.windows.net**. Szükség esetén adja meg be az adatbázis nevét. Válassza ki **OK** a hitelesítő adatok ablak megnyitásához. 

   ![Csatlakozás adatbázishoz párbeszédpanel](media/sql-database-connect-excel/server-name.png)

4. Az a **SQL Server-adatbázis** párbeszédpanelen jelölje ki **adatbázis** a bal oldali oldalán, és írja be a a **felhasználónév** és **jelszó** a a SQL Database-kiszolgáló, amelyhez csatlakozni kíván. Válassza ki **Connect** megnyitásához a **kezelő**. 

   ![Kiszolgálónév és hitelesítő adatok megadása](./media/sql-database-connect-excel/connect-to-server.png)

   > [!TIP]
   > A hálózati környezettől függően előfordulhat, hogy nem tud csatlakozni, vagy megszakad a kapcsolat, ha az SQL-adatbáziskiszolgáló nem engedélyezi az ügyfél IP-címről érkező forgalmat. Lépjen az [Azure Portalhoz](https://portal.azure.com/), kattintson az SQL Server-példányok lehetőségre, majd a saját kiszolgálójára, ezután a beállítások alatt a tűzfalra, és adja hozzá ügyfél IP-címét. A részleteket a [Tűzfal beállításainak konfigurálása](sql-database-configure-firewall-settings.md) részben találja meg.

5. Az a **kezelő**, jelölje be az adatbázis működik együtt a listából válassza ki a táblák vagy nézetek dolgozni szeretne (választottuk **vGetAllCategories**), majd válassza ki **terhelés**, helyezze át az adatokat az adatbázisból az Excel-táblázatban.

    ![Válasszon ki egy adatbázist és egy táblát.](./media/sql-database-connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Adatok importálása Excelbe és kimutatásdiagram létrehozása

Most, hogy létrehozta a kapcsolatot, több lehetőség közül választhat másik módját az adatok betöltéséhez. Ha például az alábbi lépéseket az SQL-adatbázisban található adatok alapján kimutatásdiagram létrehozása. 

1. Kövesse az előző szakaszban, de ezúttal, ne pedig a **terhelés**, jelölje be **betöltés** a a **terhelés** legördülő.
2. Ezután válassza ki az adatok megtekintéséhez a munkafüzetet a módját. Válassza a **Kimutatásdiagram** lehetőséget. Az **Új munkalap**kiválasztásával új munkalapot is létrehozhat, vagy választhatja az **Adatok hozzáadása adatmodellhez** beállítást is. Az adatmodellekről további információkat az [Adatmodell létrehozása Excelben](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B) részben talál. 

    ![Az Excelben szereplő adatok formátumának kiválasztása](./media/sql-database-connect-excel/import-data.png)

    A munkalapon most egy üres kimutatástábla és -diagram van.
3. A **Kimutatástábla mezői** alatt jelölje ki az összes megtekinteni kívánt mező jelölőnégyzetét.

    ![Konfigurálja az adatbázis-jelentést.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Ha más Excel-munkafüzeteket és munkalapokat kapcsolódni az adatbázishoz, válassza ki a **adatok** lapot, majd **legutóbbi források** elindításához a **legutóbbi források** párbeszédpanel bezárásához. Itt válassza ki a listából a létrehozott kapcsolatot, és kattintson **nyílt**.
> ![Legutóbbi források párbeszédpanel](media/sql-database-connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Hozzon létre egy állandó kapcsolatot .odc-fájl segítségével

A kapcsolat adatai véglegesen mentéséhez .odc fájl létrehozása és a kapcsolat egyik lehetősége a **meglévő kapcsolatok** párbeszédpanel bezárásához. 

1. Az oldal felső menüsávban válassza a **adatok** lapra, és válassza ki **meglévő kapcsolatok** elindításához a **meglévő kapcsolatok** párbeszédpanel bezárásához. 
   1. Válassza ki **Továbbiak keresése** megnyitásához a **adatforrás kiválasztása** párbeszédpanel bezárásához.   
   2. Válassza ki a **+NewSqlServerConnection.odc** fájlt, és válassza ki **nyissa meg a** megnyitásához a **Adatkapcsolat varázsló**.

      ![Új kapcsolat párbeszédpanel](media/sql-database-connect-excel/new-connection.png)

2. Az a **Adatkapcsolat varázsló**, írja be a kiszolgáló nevét és az SQL-adatbázis hitelesítő adatait. Kattintson a **Tovább** gombra. 
   1. Válassza ki a legördülő menüből az adatokat tartalmazó adatbázisban. 
   2. Válassza ki a tábla vagy nézet érdekli. VGetAllCategories választottuk.
   3. Kattintson a **Tovább** gombra. 

      ![Adatkapcsolat varázsló](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Válassza ki a fájl helyét a **Fájlnév**, és a **rövid név** az Adatkapcsolat varázsló a következő képernyőn. Azt is beállíthatja a jelszó mentéséhez a fájlban, azonban ez potenciálisan felfedheti az adatok nem kívánt hozzáférésnek. Válassza ki **Befejezés** Ha készen áll. 

    ![Adatbázis-kapcsolat mentése](media/sql-database-connect-excel/save-data-connection.png)

4. Válassza ki, hogyan szeretné importálni az adatokat. Ehhez egy kimutatást választottuk. Jelöljön ki a kapcsolat tulajdonságait is módosíthatja **tulajdonságok**. Válassza ki **OK** Ha készen áll. Ha nem választotta a fájlt a jelszó mentéséhez, majd kéri a hitelesítő adatok megadását. 

    ![Adatok importálása](media/sql-database-connect-excel/import-data2.png)

5. Győződjön meg arról, hogy megtörtént-e az új kapcsolat kibontásával a **adatok** lapra, majd válassza ki **meglévő kapcsolatok**. 

    ![Meglévő kapcsolat](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>További lépések

* [Kapcsolódás az SQL Database adatbázishoz az SQL Server Management Studio használatával](sql-database-connect-query-ssms.md) speciális lekérdezés és elemzés céljából.
* Tudjon meg többet a [rugalmas készletek](sql-database-elastic-pool.md) előnyeiről.
* [A háttérben SQL Database adatbázishoz kapcsolódó webalkalmazás létrehozása](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
