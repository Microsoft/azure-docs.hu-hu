---
title: SSIS-csomagok migrálása SQL felügyelt példányra
titleSuffix: Azure Database Migration Service
description: Megtudhatja, hogyan telepíthet át SQL Server Integration Services (SSIS) csomagokat és projekteket egy Azure SQL felügyelt példányra az Azure Database Migration Service vagy a Data Migration Assistant használatával.
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
ms.openlocfilehash: e3e2aa055baf3dfb4bee0629040fc7c140844637
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094009"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-managed-instance"></a>SQL Server Integration Services csomagok migrálása egy felügyelt Azure SQL-példányra
Ha SQL Server Integration Servicest (SSIS) használ, és szeretné áttelepíteni a SSIS-projekteket/csomagokat a SQL Server által üzemeltetett SSISDB a felügyelt Azure SQL-példányok által üzemeltetett cél SSISDB, használhatja a Azure Database Migration Service.

Ha a használt SSIS-verzió korábbi, mint 2012, vagy ha nem SSISDB-csomag típusú tárolót használ, a SSIS-projektek/csomagok áttelepítése előtt át kell alakítania azokat az integrációs szolgáltatások projekt-átalakítási varázslójával, amely a SSMS is elindítható. További információ: [projektek konvertálása a projekt üzembe helyezési modelljére](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> A Azure Database Migration Service (DMS) jelenleg nem támogatja a Azure SQL Database cél áttelepítési célként. A SSIS-projektek/csomagok Azure SQL Databaseba való újbóli üzembe helyezéséhez tekintse meg [SQL Server Integration Services csomagok újbóli üzembe helyezése Azure SQL Database](./how-to-migrate-ssis-packages.md).

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
>
> * A forrás-SSIS projektjeinek/csomagjainak értékelése.
> * SSIS-projektek/csomagok migrálása az Azure-ba.

## <a name="prerequisites"></a>Előfeltételek

A lépések elvégzéséhez a következőkre lesz szüksége:

* Microsoft Azure Virtual Network létrehozása a Azure Database Migration Service számára a Azure Resource Manager üzembe helyezési modell használatával, amely helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgálóról a [ExpressRoute](../expressroute/expressroute-introduction.md) vagy a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)használatával. További információkért tekintse meg a [hálózati topológiák az SQL felügyelt példányok áttelepítése Azure Database Migration Service használatával]( https://aka.ms/dmsnetworkformi)című cikket. A virtuális hálózatok létrehozásával kapcsolatos további információkért tekintse meg a [Virtual Network dokumentációt](../virtual-network/index.yml), és különösen a gyors üzembe helyezési cikkeket részletesen ismerteti.
* Annak biztosítása érdekében, hogy a virtuális hálózati hálózati biztonsági csoport szabályai ne tiltsa le a ServiceTag kimenő 443-es portját a ServiceBus, a Storage és a AzureMonitor esetében. A Virtual Network NSG-forgalom szűrésével kapcsolatos további információkért tekintse meg a [hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/virtual-network-vnet-plan-design-arm.md)című cikket.
* A [Windows tűzfal konfigurálása a forrás-adatbázismotor eléréséhez](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* A Windows tűzfal megnyitásával engedélyezheti a Azure Database Migration Service számára a forrás SQL Server elérését, amely alapértelmezés szerint a 1433-as TCP-port.
* Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
* Ha tűzfalberendezést használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz, valamint a fájlokhoz a 445-ös SMB-porton keresztül.
* SQL felügyelt példány a SSISDB üzemeltetéséhez. Ha létre kell hoznia egyet, kövesse az [Azure SQL felügyelt példány létrehozása](../azure-sql/managed-instance/instance-create-quickstart.md)című cikk részletes ismertetését.
* Annak biztosítása érdekében, hogy a forrás SQL Server és a célként felügyelt példány összekapcsolásához használt bejelentkezési adatok a sysadmin (rendszergazda) kiszolgálói szerepkör tagjai legyenek.
* Annak ellenőrzéséhez, hogy a SSIS Azure Data Factory (Azure-SSIS Integration Runtime ADF) van-e kiépítve egy SQL felügyelt példány által üzemeltetett SSISDB (az [Azure-SSIS Integration Runtime létrehozása a Azure Data Factory](../data-factory/create-azure-ssis-integration-runtime.md)-ben) című cikkben leírtak szerint.

## <a name="assess-source-ssis-projectspackages"></a>Forrás SSIS projektek/csomagok értékelése

Bár a forrás-SSISDB értékelése még nincs integrálva az adatbázis Migration Assistantba (DMA), a SSIS-projekteket/csomagokat a rendszer értékeli/érvényesíti, mivel azokat újra üzembe helyezi a felügyelt Azure SQL-példányon üzemeltetett SSISDB.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

    ![Portál-előfizetések megtekintése](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a Azure Database Migration Service példányát, majd válassza az **erőforrás-szolgáltatók** lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd a **Microsoft. DataMigration** jobb oldalán válassza a **regisztráció** lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service-példány létrehozása

1. A Azure Portal válassza az + **erőforrás létrehozása** lehetőséget, keresse meg a **Azure Database Migration Service**, majd válassza a **Azure Database Migration Service** elemet a legördülő listából.

     ![Azure Piactér](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, ahol a DMS példányát létre szeretné hozni.

5. Válasszon ki egy meglévő virtuális hálózatot, vagy hozzon létre egyet.

    A virtuális hálózat Azure Database Migration Service hozzáférést biztosít a forrás-SQL Server és a felügyelt Azure SQL-példányhoz.

    A virtuális hálózatok Azure Portalban való létrehozásával kapcsolatos további információkért tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/quick-create-portal.md)című cikket.

    További részletekért tekintse meg a [hálózati topológiák az Azure SQL felügyelt példányok áttelepítése a Azure Database Migration Service használatával](./resource-network-topologies.md)című cikket.

6. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![DMS-szolgáltatás létrehozása](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

Keresse meg a létrehozott szolgáltatáspéldányt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![Az Azure Database Migration Service minden példányának megkeresése](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. Az **Azure Database Migration Service** képernyőjén keresse meg a létrehozott példány nevét, és válassza ki a példányt.

3. Válassza a + **Új migrálási projekt** lehetőséget.

4. Az **új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **forráskiszolgáló típusa** szövegmezőben válassza a **SQL Server** lehetőséget, a **CÉLKISZOLGÁLÓ típusa** szövegmezőben válassza az **Azure SQL felügyelt példány** lehetőséget, majd a **tevékenység típusa** beállításnál válassza a **SSIS-csomag áttelepítése** lehetőséget.

   ![DMS-projekt létrehozása](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Migrálási forrás adatai** képernyőn adja meg a forrásként szolgáló SQL Server kapcsolati adatait.

2. Ha nem telepített megbízható tanúsítványt a kiszolgálón, jelölje be a **Kiszolgálótanúsítvány megbízhatósága** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítvány, az SQL Server a példány indításakor előállít egy önaláírt tanúsítványt. A rendszer ezt a tanúsítványt használja az ügyfélkapcsolatok hitelesítő adatainak titkosítására.

    > [!CAUTION]
    > Az önaláírt tanúsítvánnyal titkosított TLS-kapcsolatok nem biztosítanak erős biztonságot. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Az önaláírt tanúsítványokat nem szabad a TLS-t használni éles környezetben vagy az internethez csatlakozó kiszolgálókon.

   ![Forrás részletei](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Az **áttelepítési cél részletei** képernyőn határozza meg a cél kapcsolati adatait.

     ![Cél részletei](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Válassza a **Mentés** lehetőséget.

## <a name="review-the-migration-summary"></a>A migrálás összefoglalásának áttekintése

1. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét.

2. A **SSIS projekt (ek) és a környezet (ek) felülírására szolgáló beállításnál** határozza meg, hogy felülírja vagy figyelmen kívül hagyja-e a meglévő SSIS-projekteket és-környezeteket.

    ![Migrálási projekt áttekintése](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Tekintse át és hagyja jóvá a migrálási projekttel kapcsolatos részleteket.

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

## <a name="next-steps"></a>Következő lépések

* Tekintse át az áttelepítési útmutatót a Microsoft [Database áttelepítési útmutatójában](https://datamigration.microsoft.com/).