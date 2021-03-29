---
title: SSIS-csomagok újbóli üzembe helyezése az SQL Single Database-ben
titleSuffix: Azure Database Migration Service
description: Megtudhatja, hogyan telepíthet át vagy telepíthet át SQL Server Integration Services csomagokat és projekteket úgy, hogy az Azure Database Migration Service és Data Migration Assistant használatával egyetlen adatbázist Azure SQL Database.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: e42b5ab2bbdebb2584b38531bd6458a98563ef42
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643744"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>SSIS-csomagok újbóli üzembe helyezése Azure SQL Databasere Azure Database Migration Service

Ha SQL Server Integration Servicest (SSIS) használ, és a SQL Server által a Azure SQL Database által üzemeltetett forrás-SSISDB szeretné áttelepíteni a SSIS-projekteket/csomagokat, akkor az Integration Services Deployment Wizard használatával újra üzembe helyezheti azokat. A varázslót SQL Server Management Studioon (SSMS) belül is elindíthatja.

Ha a használt SSIS-verzió korábbi, mint 2012, a SSIS-projektek/csomagok projekt-telepítési modellbe való újbóli üzembe helyezése előtt először konvertálnia kell őket az integrációs szolgáltatások projekt-átalakítási varázslójával, amely a SSMS is elindítható. További információ: [projektek konvertálása a projekt üzembe helyezési modelljére](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#convert).

> [!NOTE]
> A Azure Database Migration Service (DMS) jelenleg nem támogatja a forrás-SSISDB áttelepítését Azure SQL Databasere, de a következő folyamat használatával újra üzembe helyezheti a SSIS-projekteket/csomagokat.

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
>
> * A forrás-SSIS projektjeinek/csomagjainak értékelése.
> * SSIS-projektek/csomagok migrálása az Azure-ba.

## <a name="prerequisites"></a>Előfeltételek

A lépések elvégzéséhez a következőkre lesz szüksége:

* A SSMS 17,2-es vagy újabb verziója.
* A célként megadott adatbázis-kiszolgáló egy példánya a SSISDB üzemeltetéséhez. Ha még nem rendelkezik ilyennel, hozzon létre egy [logikai SQL Servert](../azure-sql/database/logical-servers.md) (adatbázis nélkül) a Azure Portal használatával a SQL Server (csak logikai kiszolgáló) [űrlapra](https://ms.portal.azure.com/#create/Microsoft.SQLServer)való navigálással.
* A SSIS-et Azure Data Factory (ADF)-ben kell kiépíteni, amely tartalmazza Azure-SSIS Integration Runtime (IR) és a SQL Database által üzemeltetett célként megadott SSISDB (a [Azure-SSIS Integration Runtime kiépítése Azure Data Factory](../data-factory/tutorial-deploy-ssis-packages-azure.md)) című cikkben leírtak szerint.

## <a name="assess-source-ssis-projectspackages"></a>Forrás SSIS projektek/csomagok értékelése

Bár a forrás SSISDB értékelése még nincs integrálva az adatbázis Migration Assistant (DMA) vagy a Azure Database Migration Service (DMS) szolgáltatásba, a SSIS-projekteket/csomagokat a rendszer értékeli/érvényesíti, mivel azokat a Azure SQL Database által üzemeltetett cél SSISDB telepíti újra.

## <a name="migrate-ssis-projectspackages"></a>SSIS-projektek/csomagok migrálása

A SSIS-projektek/csomagok Azure SQL Databaseba való áttelepítéséhez hajtsa végre a következő lépéseket.

1. Nyissa meg a SSMS, majd válassza a **Beállítások lehetőséget** a **Kapcsolódás a kiszolgálóhoz** párbeszédpanel megjelenítéséhez.

2. A **login (bejelentkezés** ) lapon határozza meg a cél SSISDB futtató kiszolgálóhoz való kapcsolódáshoz szükséges adatokat.

    ![SSIS bejelentkezés lap](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. A **kapcsolat tulajdonságai** lap **Kapcsolódás az adatbázishoz** szövegmezőben válassza ki vagy írja be a **SSISDB**, majd válassza a **Kapcsolódás** lehetőséget.

    ![SSIS-kapcsolatok tulajdonságai lap](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. A SSMS Object Explorer bontsa ki az **Integration Services-katalógusok** csomópontot, bontsa ki a **SSISDB** elemet, és ha nincsenek meglévő mappák, kattintson a jobb gombbal a **SSISDB** elemre, és hozzon létre egy új mappát.

5. A **SSISDB** alatt bontsa ki bármelyik mappát, kattintson a jobb gombbal a **projektek** elemre, majd válassza a **projekt üzembe helyezése** elemet.

    ![SSIS SSISDB csomópontja kibontva](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. Az integrációs szolgáltatások telepítése varázslóban a **Bevezetés** lapon tekintse át az adatokat, majd kattintson a **tovább** gombra.

    ![Központi telepítési varázsló – Bevezetés lap](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. A **forrás kiválasztása** lapon adja meg a telepíteni kívánt meglévő SSIS-projektet.

    Ha a SSMS a forrás SSISDB futtató SQL Server is csatlakozik, válassza az **Integration Services Catalog** lehetőséget, majd adja meg a kiszolgáló nevét és a projekt elérési útját a katalógusban a projekt közvetlen üzembe helyezéséhez.

    Másik lehetőségként válassza a **projekt központi telepítési fájlja** lehetőséget, majd adja meg egy meglévő projekt központi telepítési fájljának elérési útját (. ispac) a projekt telepítéséhez.

    ![Központi telepítés varázsló forrás kiválasztása lap](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Kattintson a **Tovább** gombra.
9. A **cél kiválasztása** lapon adja meg a projekt célját.

    a. A kiszolgáló neve szövegmezőbe írja be a teljes kiszolgálónevet (<server_name>. database.windows.net).

    b. Adja meg a hitelesítési adatokat, majd válassza a **kapcsolat** lehetőséget.

    ![Központi telepítés varázsló célhely kiválasztása oldal](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Válassza a **Tallózás** lehetőséget a SSISDB megadásához, majd kattintson a **tovább** gombra.

    > [!NOTE]
    > A **tovább** gomb csak a **kapcsolat** kiválasztása után van engedélyezve.

10. Az **Érvényesítés** lapon tekintse meg az esetleges hibákat és figyelmeztetéseket, majd szükség esetén módosítsa a csomagokat ennek megfelelően.

    ![Központi telepítési varázsló – ellenőrzés lap](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Kattintson a **Tovább** gombra.

12. Az **Áttekintés** lapon tekintse át a telepítési beállításokat.

    > [!NOTE]
    > A beállításokat a **Previous (előző** ) vagy a bal oldali ablaktábla bármelyik lépés hivatkozásának kiválasztásával módosíthatja.

13. Az üzembe helyezési folyamat elindításához válassza a **telepítés** lehetőséget.

14. Az üzembe helyezési folyamat befejeződése után megtekintheti az eredmények lapot, amely megjeleníti az egyes központi telepítési műveletek sikerességét vagy sikertelenségét.
    a. Ha bármilyen művelet meghiúsult, az **eredmény** oszlopban válassza a **nem sikerült** lehetőséget a hiba magyarázatának megjelenítéséhez.
    b. Ha szeretné, válassza a **jelentés mentése** lehetőséget, hogy az eredményeket XML-fájlba mentse.

15. A **Bezárás** gombra kattintva lépjen ki az integrációs szolgáltatások központi telepítése varázslóból.

Ha a projekt telepítése nem sikerül, akkor kiválaszthatja a Azure-SSIS IRon való futtatáshoz szükséges csomagokat.

## <a name="next-steps"></a>Következő lépések

* Tekintse át az áttelepítési útmutatót a Microsoft [Database áttelepítési útmutatójában](https://datamigration.microsoft.com/).