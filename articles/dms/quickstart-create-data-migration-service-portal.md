---
title: 'Gyors útmutató: példány létrehozása a Azure Portal használatával'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service példányának létrehozásához használja a Azure Portal.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/29/2021
ms.openlocfilehash: 6232c842514c10a5440e574621ca74e2f4867d86
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99981624"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Rövid útmutató: Azure Database Migration Service-példány létrehozása az Azure Portalon

Ebben a rövid útmutatóban a Azure Portal használatával hozza létre Azure Database Migration Service példányát. A példány létrehozása után több adatbázisból származó adatok áttelepíthetők az Azure-beli adatplatformokra, például a SQL Serverról Azure SQL Databasere vagy a SQL Server egy Azure SQL felügyelt példányra.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Nyissa meg a webböngészőjét, keresse fel a [Microsoft Azure Portalt](https://portal.azure.com/), majd adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

> [!NOTE]
> Régiónként legfeljebb 10 példányt hozhat létre előfizetéssel. Ha több példányra van szüksége, hozzon létre egy támogatási jegyet.

## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

A Database Migration Service első példányának létrehozása előtt regisztrálja a Microsoft.DataMigration erőforrás-szolgáltatót.

1. A Azure Portal keresse meg és válassza ki az **előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/quickstart-create-data-migration-service-portal/portal-select-subscription.png)

2. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a Azure Database Migration Service példányát, majd válassza az **erőforrás-szolgáltatók** lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/quickstart-create-data-migration-service-portal/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd válassza a **regisztráció** a **Microsoft. DataMigration** lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>A szolgáltatás egy példányának létrehozása

1. A Azure Portal menüben vagy a **kezdőlapon** válassza az **erőforrás létrehozása** lehetőséget. Keresse meg és válassza ki a **Azure Database Migration Service**.

    ![Azure Piactér](media/quickstart-create-data-migration-service-portal/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/quickstart-create-data-migration-service-portal/dms-create.png)

3. Az **áttelepítési szolgáltatás** alapjai képernyőn:

     - Válassza ki az előfizetést.
     - Hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévőt.
     - Adja meg a Azure Database Migration Service példányának nevét.
     - Válassza ki azt a helyet, amelyben létre szeretné hozni a Azure Database Migration Service példányát.
     - Válassza ki az **Azure** -t szolgáltatási módként.
     - Válasszon tarifacsomagot. További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.
     
    ![Azure Database Migration Service példány alapvető beállításainak konfigurálása](media/quickstart-create-data-migration-service-portal/dms-create-basics.png)

     - Válassza a Tovább: Hálózatkezelés lehetőséget.

4. Az **áttelepítési szolgáltatás** hálózatkezelésének létrehozása képernyőn:

    - Válasszon egy meglévő virtuális hálózatot, vagy hozzon létre egy újat. A virtuális hálózat Azure Database Migration Service biztosít a forrás-adatbázis és a célként megadott környezet eléréséhez. Ha további információt szeretne arról, hogyan hozhat létre virtuális hálózatot a Azure Portalban, tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/quick-create-portal.md)című cikket.

    ![Azure Database Migration Service példány hálózati beállításainak konfigurálása](media/quickstart-create-data-migration-service-portal/dms-network-settings.png)

    - Válassza a **felülvizsgálat + létrehozás** lehetőséget a szolgáltatás létrehozásához. 
    
    - Néhány pillanat múlva létrejön az Azure Database Migration Service-példány, és készen áll a használatra:

    ![Létrehozott migrálási szolgáltatás](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a rövid útmutatóban létrehozott erőforrásokat az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md)törlésével távolíthatja el. Az erőforráscsoport törléséhez keresse meg a létrehozott Azure Database Migration Service-példányt. Válassza ki az **Erőforráscsoport** nevét, majd válassza az **Erőforráscsoport törlése** elemet. A művelettel az erőforráscsoport összes elemét és magát a csoportot is törli.

## <a name="next-steps"></a>Következő lépések

* [SQL Server offline migrálása Azure SQL Database-re](tutorial-sql-server-to-azure-sql.md)
* [SQL Server online migrálása Azure SQL Database-re](tutorial-sql-server-azure-sql-online.md)
* [SQL Server migrálása egy felügyelt Azure SQL-példányra offline](tutorial-sql-server-to-managed-instance.md)
* [SQL Server migrálása egy online Azure SQL felügyelt példányra](tutorial-sql-server-managed-instance-online.md)