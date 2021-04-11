---
title: 'Oktatóanyag: SQL Server online migrálása a felügyelt SQL-példányra'
titleSuffix: Azure Database Migration Service
description: Megtudhatja, hogyan végezheti el a SQL Server online áttelepítését egy felügyelt Azure SQL-példányra Azure Database Migration Service használatával.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 08/04/2020
ms.openlocfilehash: 5585eb210b54dfa016d25c430256508e1b0b9f61
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076887"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-online-using-dms"></a>Oktatóanyag: SQL Server migrálása Azure SQL felügyelt példányra online a DMS használatával

A Azure Database Migration Service segítségével telepítheti át az adatbázisokat egy SQL Server példányból egy [felügyelt Azure SQL-példányra](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) minimális állásidővel. A manuális beavatkozást igénylő további módszerekhez tekintse meg az [Azure SQL felügyelt példányának SQL Server példányának áttelepítését](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md)ismertető cikket.

Ebben az oktatóanyagban áttelepíti a **Adventureworks2012** -adatbázist a SQL Server helyszíni példányáról egy SQL felügyelt példányra, minimális állásidővel Azure Database Migration Service használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet, és indítsa el az online áttelepítést Azure Database Migration Service használatával.
> * Az áttelepítés monitorozása.
> * Ha elkészült, hajtsa végre az áttelepítési átváltás.

> [!IMPORTANT]
> Az SQL Serverról az SQL felügyelt példányra Azure Database Migration Service használatával történő online áttelepítéshez meg kell adnia a teljes adatbázis biztonsági mentését és az azt követő biztonsági másolatokat abban az SMB-hálózati megosztásban, amelyet a szolgáltatás az adatbázisok áttelepítésére használhat. A Azure Database Migration Service nem kezdeményez biztonsági mentést, hanem meglévő biztonsági másolatokat használ, amelyek már a vész-helyreállítási terv részeként is megjelenhetnek az áttelepítés során.
> Ügyeljen arra, hogy [biztonsági másolatokat készítsen a with ellenőrzőösszeg](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?preserve-view=true&view=sql-server-2017)paranccsal. Ügyeljen arra is, hogy ne fűzze több biztonsági mentést (azaz a teljes és a t-log-t) egyetlen biztonsági mentési adathordozóra; készítsen biztonsági mentést egy külön biztonságimásolat-fájlon. Végezetül a tömörített biztonsági mentések segítségével csökkentheti a nagyméretű biztonsági mentések áttelepítéséhez kapcsolódó lehetséges problémák előfordulásának valószínűségét.

> [!NOTE]
> A Azure Database Migration Service használata az online áttelepítés végrehajtásához a prémium szintű díjszabás alapján kell létrehoznia egy példányt.

> [!IMPORTANT]
> Az optimális áttelepítési élmény érdekében a Microsoft azt javasolja, hogy Azure Database Migration Service-példányt hozzon létre ugyanabban az Azure-régióban, mint a célként megadott adatbázis. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

> [!IMPORTANT]
> A lehető legnagyobb mértékben csökkentheti az online áttelepítési folyamat időtartamát, hogy minimalizálja a példányok újrakonfigurálásának vagy tervezett karbantartásának okozta megszakítás kockázatát. Ilyen esemény esetén az áttelepítési folyamat kezdete az elejétől kezdődik. Tervezett karbantartás esetén az áttelepítési folyamat újraindítása előtt 36 óra türelmi időszak áll rendelkezésre.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk a SQL Serverról egy SQL felügyelt példányra történő online áttelepítést ismerteti. Offline áttelepítés esetén lásd: [SQL Server áttelepítése egy SQL felügyelt példányra a DMS használatával](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Service számára a Azure Resource Manager üzemi modell használatával, amely helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgáló számára a [ExpressRoute](../expressroute/expressroute-introduction.md) vagy a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)használatával. [Ismerje meg a hálózati topológiákat az SQL felügyelt példányok áttelepítéséhez Azure Database Migration Service használatával](./resource-network-topologies.md). A virtuális hálózatok létrehozásával kapcsolatos további információkért tekintse meg a [Virtual Network dokumentációt](../virtual-network/index.yml), és különösen a gyors üzembe helyezési cikkeket részletesen ismerteti.

    > [!NOTE]
    > Ha a virtuális hálózat beállítása során ExpressRoute használ a Microsoft számára, adja hozzá a következő szolgáltatási [végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md) ahhoz az alhálózathoz, amelyben a szolgáltatást kiépíti:
    >
    > * Céladatbázis végpontja (például SQL-végpont, Cosmos DB végpont stb.)
    > * Tárolási végpont
    > * Service Bus-végpont
    >
    > Erre a konfigurációra azért van szükség, mert Azure Database Migration Service nem rendelkezik internetkapcsolattal.
    >
    >Ha nem rendelkezik helyek közötti kapcsolattal a helyszíni hálózat és az Azure között, vagy ha a helyek közötti kapcsolat sávszélessége korlátozott, érdemes lehet Azure Database Migration Service hibrid módban (előzetes verzió) használni. A hibrid üzemmód egy helyszíni áttelepítési feldolgozót használ a felhőben futó Azure Database Migration Service egy példányával együtt. Azure Database Migration Service hibrid módban való létrehozásához tekintse meg a [Azure Database Migration Service-példány létrehozása hibrid módban a Azure Portal használatával](./quickstart-create-data-migration-service-portal.md)című cikket.

    > [!IMPORTANT]
    > A Migrálás részeként használt Storage-fiókkal kapcsolatban a következők valamelyikét kell megadnia:
    > * A Storage-fiók elérésének engedélyezéséhez válassza az összes hálózat lehetőséget.
    > * Kapcsolja be az [alhálózat-delegálást](../virtual-network/manage-subnet-delegation.md) a mi alhálózaton, és frissítse a Storage-fiók tűzfalszabályok beállításait az alhálózat engedélyezéséhez.

* Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport szabályai nem gátolják meg a ServiceTag kimenő 443-es portját a ServiceBus, a Storage és a AzureMonitor esetében. A Virtual Network NSG-forgalom szűrésével kapcsolatos további információkért tekintse meg a [hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/virtual-network-vnet-plan-design-arm.md)című cikket.
* Konfigurálja a [Windows tűzfalat a forrásadatbázis-motorhoz való hozzáféréshez](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat, hogy a Azure Database Migration Service hozzáférhessen a forrás SQL Serverhoz, amely alapértelmezés szerint a 1433-as TCP-port. Ha az alapértelmezett példány egy másik portot figyel, adja hozzá azt a tűzfalhoz.
* Ha több elnevezett SQL Server példányt futtat dinamikus portok használatával, akkor előfordulhat, hogy engedélyezni szeretné a SQL Browser szolgáltatást, és engedélyezni szeretné a 1434-as UDP-port elérését a tűzfalakon keresztül, így Azure Database Migration Service csatlakozhat a forráskiszolgálón megnevezett példányhoz.
* Ha tűzfalat használ a forrásadatbázis előtt, akkor előfordulhat, hogy a tűzfalszabályok hozzáadásával engedélyezni Azure Database Migration Service a forrás-adatbázis (ok) hoz való hozzáférést az áttelepítéshez, valamint a fájlokat a 445-es SMB-porton keresztül.
* Hozzon létre egy SQL felügyelt példányt a [Azure Portalban található SQL felügyelt példány létrehozása](../azure-sql/managed-instance/instance-create-quickstart.md)című cikkben ismertetett részletességgel.
* Győződjön meg arról, hogy a forrás-SQL Server és a célként megadott SQL felügyelt példány összekapcsolásához használt bejelentkezések a sysadmin (rendszergazda) kiszolgálói szerepkör tagjai.
* Adjon meg egy olyan SMB-hálózati megosztást, amely tartalmazza az adatbázis teljes adatbázis-biztonságimásolat-fájljait és az azt követő tranzakciónapló biztonsági mentési fájljait, amelyeket a Azure Database Migration Service az adatbázis áttelepítésére használhat.
* Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példányt futtató szolgáltatásfiók írási, a forrásként szolgáló kiszolgáló számítógépes fiókja pedig olvasási és írási jogosultságokkal rendelkezik az Ön által létrehozott hálózati megosztáson.
* Jegyezzen fel egy olyan Windows-felhasználót (és jelszót), amely teljes körű jogosultságokkal rendelkezik az Ön által korábban létrehozott hálózati megosztáson. Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat, hogy a biztonsági mentési fájlokat feltöltse az Azure Storage-tárolóba a visszaállítási művelethez.
* Hozzon létre egy Azure Active Directory alkalmazás-azonosítót, amely létrehoz egy olyan alkalmazás-azonosító kulcsot, amelyet a Azure Database Migration Service használhat a cél Azure Database felügyelt példányához és az Azure Storage-tárolóhoz való kapcsolódáshoz. További információ: [Azure Active Directory-alkalmazás és -szolgáltatásnév létrehozása a portálon erőforrások eléréséhez](../active-directory/develop/howto-create-service-principal-portal.md)

  > [!NOTE]
  > Azure Database Migration Service a megadott alkalmazás-AZONOSÍTÓhoz a közreműködői engedélyt kell adni az előfizetéshez. Azt is megteheti, hogy egyéni szerepköröket hoz létre, amelyek megadják a Azure Database Migration Service által igényelt konkrét engedélyeket. Az egyéni szerepkörök használatával kapcsolatos részletes útmutatásért tekintse meg az [SQL felügyelt példányok online áttelepítéséhez SQL Server egyéni szerepkörök](./resource-custom-roles-sql-db-managed-instance.md)című cikket.

* Hozzon létre vagy jegyezzen fel egy **standard teljesítményszintű** Azure Storage-fiókot, amelybe a DMS-szolgáltatás feltöltheti az adatbázis biztonsági mentési fájljait, majd felhasználhatja azokat az adatbázisok migrálásakor.  Győződjön meg arról, hogy az Azure Storage-fiókot abban a régióban hozza létre, ahol a Azure Database Migration Service példányt létrehozták.

  > [!NOTE]
  > Ha [transzparens adattitkosítás](../azure-sql/database/transparent-data-encryption-tde-overview.md) által védett adatbázist telepít át egy felügyelt példányra az online áttelepítés használatával, a helyszíni vagy az Azure-beli virtuális gép SQL Server példányának megfelelő tanúsítványát át kell telepíteni az adatbázis-visszaállítás előtt. A részletes lépésekért lásd: [TDE-tanúsítvány áttelepítése felügyelt példányra](../azure-sql/database/transparent-data-encryption-tde-overview.md).

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

    ![Portál-előfizetések megtekintése](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a Azure Database Migration Service példányát, majd válassza az **erőforrás-szolgáltatók** lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd a **Microsoft. DataMigration** jobb oldalán válassza a **regisztráció** lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service-példány létrehozása

1. A Azure Portal válassza az + **erőforrás létrehozása** lehetőséget, keresse meg a **Azure Database Migration Service**, majd válassza a **Azure Database Migration Service** elemet a legördülő listából.

     ![Azure Piactér](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, ahol a DMS példányát létre szeretné hozni.

5. Válasszon ki egy meglévő virtuális hálózatot, vagy hozzon létre egyet.

    A virtuális hálózat Azure Database Migration Service hozzáférést biztosít a forrás-SQL Server és a célként megadott SQL felügyelt példányhoz.

    A virtuális hálózatok Azure Portalban való létrehozásával kapcsolatos további információkért tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/quick-create-portal.md)című cikket.

    További részletekért tekintse meg a [hálózati topológiák az SQL felügyelt példányok áttelepítéséhez Azure Database Migration Service használatával](./resource-network-topologies.md)című cikket.

6. Válasszon ki egy SKU-t a prémium szintű díjszabási csomagból.

    > [!NOTE]
    > Az online áttelepítések csak a prémium szint használata esetén támogatottak.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![DMS-szolgáltatás létrehozása](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

Keresse meg a létrehozott szolgáltatáspéldányt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![Azure Database Migration Service összes példányának megkeresése](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Az **Azure Database Migration Service** képernyőjén keresse meg a létrehozott példány nevét, és válassza ki a példányt.

3. Válassza a + **Új migrálási projekt** lehetőséget.

4. Az **új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **forráskiszolgáló típusa** szövegmezőben válassza a **SQL Server** lehetőséget, a **CÉLKISZOLGÁLÓ típusa** szövegmezőben válassza az **Azure SQL felügyelt példány** lehetőséget, majd a **tevékenység típusa** beállításnál válassza az **online adatáttelepítés** lehetőséget.

   ![Azure Database Migration Service projekt létrehozása](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Migrálási forrás adatai** képernyőn adja meg a forrásként szolgáló SQL Server kapcsolati adatait.

2. Ha nem telepített megbízható tanúsítványt a kiszolgálón, jelölje be a **Kiszolgálótanúsítvány megbízhatósága** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítvány, az SQL Server a példány indításakor előállít egy önaláírt tanúsítványt. A rendszer ezt a tanúsítványt használja az ügyfélkapcsolatok hitelesítő adatainak titkosítására.

    > [!CAUTION]
    > Az önaláírt tanúsítvánnyal titkosított TLS-kapcsolatok nem biztosítanak erős biztonságot. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Az önaláírt tanúsítványokat nem szabad a TLS-t használni éles környezetben vagy az internethez csatlakozó kiszolgálókon.

   ![Forrás részletei](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Kattintson a **Mentés** gombra.

4. A **Forrásadatbázisok kiválasztása** képernyőn válassza ki az **Adventureworks2012** adatbázist a migráláshoz.

   ![Forrásadatbázisok kiválasztása](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Ha SQL Server Integration Servicest (SSIS) használ, a DMS jelenleg nem támogatja a katalógus-adatbázis áttelepítését a SSIS-projektekhez/-csomagokhoz (SSISDB) a SQL Server és az SQL felügyelt példánya között. A SSIS azonban Azure Data Factory (ADF) is kiépítheti, és újból üzembe helyezheti a SSIS-projekteket/csomagokat a felügyelt SQL-példány által üzemeltetett SSISDB. A SSIS-csomagok áttelepítésével kapcsolatos további információkért tekintse [meg SQL Server Integration Services csomagok migrálása az Azure-ba](./how-to-migrate-ssis-packages.md)című cikket.

5. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Az **áttelepítési cél részletei** képernyőn válassza ki az **alkalmazás azonosítóját** és **kulcsát** , amelyet a DMS-példány használhat az SQL felügyelt példány és az Azure Storage-fiók cél példányához való kapcsolódáshoz.

    További információ: [Azure Active Directory-alkalmazás és -szolgáltatásnév létrehozása a portálon erőforrások eléréséhez](../active-directory/develop/howto-create-service-principal-portal.md)

2. Válassza ki azt az **előfizetést** , amely az SQL felügyelt példányának célként megadott példányát tartalmazza, majd válassza ki a célként megadott példányt.

    Ha még nem telepítette az SQL felügyelt példányát, válassza ki a [hivatkozást](../azure-sql/managed-instance/instance-create-quickstart.md) , amely segít a példány kiépítésében. Ha az SQL felügyelt példánya elkészült, térjen vissza ehhez az adott projekthez az áttelepítés végrehajtásához.

3. Adja meg az SQL- **felhasználó** és a **jelszó** megadását a felügyelt SQL-példányhoz való kapcsolódáshoz.

    ![Cél kiválasztása](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Kattintson a **Mentés** gombra.

## <a name="select-source-databases"></a>Forrásadatbázisok kiválasztása

1. A **Forrásadatbázisok kiválasztása** képernyőn válassza ki a migrálni kívánt forrásadatbázist.

    ![Forrásadatbázisok kiválasztása](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Kattintson a **Mentés** gombra.

## <a name="configure-migration-settings"></a>Migrálási beállítások konfigurálása

1. A **Migrálási beállítások konfigurálása** képernyőn adja meg a következő adatokat:

    | Paraméter | Leírás |
    |--------|---------|
    |**SMB hálózatihely-megosztás** | A helyi SMB hálózati megosztás vagy az Azure-fájlmegosztás, amely tartalmazza a teljes adatbázis biztonsági mentési fájljait és a tranzakciónapló biztonsági mentési fájljait, amelyeket Azure Database Migration Service használhat az áttelepítéshez. A forrásként szolgáló SQL Server-példányt futtató szolgáltatásfióknak olvasási és írási jogosultságokkal kell rendelkeznie ehhez a hálózati megosztáshoz. Adja meg a hálózati megosztáson található kiszolgáló FQDN- vagy IP-címét, például \\\servername.domainname.com\backupfolder vagy \\\IP address\backupfolder. A jobb teljesítmény érdekében ajánlott külön mappát használni az áttelepíteni kívánt adatbázisokhoz. Az adatbázis-szintű fájlmegosztás elérési útját a **Speciális beállítások** lehetőség használatával adhatja meg. Ha az SMB-megosztáshoz való kapcsolódással kapcsolatos problémákba ütközik, tekintse [meg az SMB-megosztást](known-issues-azure-sql-db-managed-instance-online.md#smb-file-share-connectivity). |
    |**Felhasználónév** | Győződjön meg arról, hogy a Windows-felhasználó teljes körű jogosultságokkal rendelkezik a fent megadott hálózati megosztáson. A Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat, hogy feltöltse a biztonságimásolat-fájlokat az Azure Storage-tárolóba a visszaállítási művelethez. Ha az Azure-fájlmegosztást használja, használja az AZURE \ gyobb mértékben nevű Storage-fiók nevét a felhasználónévként. |
    |**Jelszó** | A felhasználó jelszava. Ha az Azure-fájlmegosztást használja, használja a Storage-fiók kulcsát jelszóként. |
    |**Az Azure Storage-tárfiók előfizetése** | Válassza ki az Azure Storage-tárfiókot tartalmazó előfizetést. |
    |**Azure Storage-fiók** | Válassza ki az Azure Storage-tárfiókot, amelybe a DMS feltöltheti az SMB hálózati megosztásból származó biztonsági mentési fájlokat, majd felhasználhatja azokat a migráláskor.  Az optimális fájlfeltöltési teljesítmény érdekében javasoljuk, hogy a tárfiók ugyanabban a régióban legyen, mint a DMS-szolgáltatás. |

    ![Migrálási beállítások konfigurálása](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Ha Azure Database Migration Service a "rendszerhiba 53" vagy a "rendszerhiba 57" hibaüzenetet jeleníti meg, az ok miatt előfordulhat, hogy az Azure Database Migration Service nem tud hozzáférni az Azure-fájlmegosztás eléréséhez. Ha ezen hibák valamelyikével találkozik, adjon hozzáférést a virtuális hálózat Storage-fiókjához az [itt](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)leírt utasítások alapján.

    > [!IMPORTANT]
    > Ha a visszacsatolási ellenőrzési funkció engedélyezve van, és a forrás SQL Server és a fájlmegosztás ugyanazon a számítógépen található, akkor a forrás nem fogja tudni elérni a fájlokat a teljes tartománynév használatával. A probléma megoldásához tiltsa le a visszacsatolási ellenőrzés funkcióit az [itt](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd)leírt utasítások alapján.

2. Kattintson a **Mentés** gombra.

## <a name="review-the-migration-summary"></a>A migrálás összefoglalásának áttekintése

1. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét.

2. Tekintse át és hagyja jóvá a migrálási projekttel kapcsolatos részleteket.

    ![Migrálási projekt áttekintése](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>A migrálás futtatása és monitorozása

1. Válassza a **Migrálás futtatása** lehetőséget.

2. A migrálási tevékenység ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez.

   ![A migrálási tevékenység folyamatban van](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    Az adatbázisok és a bejelentkezések kategóriáit is kibonthatja a kapcsolódó kiszolgálói objektumok migrálási állapotának nyomon követéséhez.

   ![Áttelepítési tevékenység állapota](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Átállásos migrálás végrehajtása

Miután a teljes adatbázis biztonsági mentését visszaállította az SQL felügyelt példányának cél példányán, az adatbázis elérhető az áttelepítési átváltás végrehajtásához.

1. Ha készen áll az adatbázis online migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

2. Állítsa le a forrásadatbázisok összes bejövő forgalmát.

3. Vegye a [naplóvég biztonsági mentését], tegye elérhetővé a biztonságimásolat-fájlt az SMB hálózati megosztáson, majd várjon, amíg a rendszer helyreállítja ennek a végső tranzakciónaplónak a biztonsági másolatát.

    Ekkor a **Függőben lévő módosítások** száma 0-ra vált.

4. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.

    ![Teljes átállás előkészítése](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

    > [!IMPORTANT]
    > A átváltás követően a felügyelt példányok rendelkezésre állása csak üzletileg kritikus szolgáltatási szinten tarthat, mint általános célú, mivel három másodlagos replikát kell bevezetni a magas rendelkezésre állási csoport AlwaysOn. A művelet időtartama az adatok méretétől függ, további információ: [felügyeleti műveletek időtartama](../azure-sql/managed-instance/management-operations-overview.md#duration).

5. Ha az adatbázis-áttelepítési **állapot megjelenik,** az alkalmazásait az SQL felügyelt példányának új célként megadott példányához kell összekötnie.

    ![Az átállás befejeződött](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Következő lépések

* Ha egy oktatóanyag azt mutatja be, hogyan lehet áttelepíteni egy adatbázist az SQL felügyelt példányára a T-SQL Restore paranccsal, tekintse meg a [biztonsági mentés visszaállítása SQL felügyelt példányra a Restore paranccsal](../azure-sql/managed-instance/restore-sample-database-quickstart.md)című témakört.
* További információ az SQL felügyelt példányáról: [Mi az SQL felügyelt példánya](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
* Az alkalmazások SQL felügyelt példányhoz való csatlakoztatásával kapcsolatos információkért lásd: [alkalmazások csatlakoztatása](../azure-sql/managed-instance/connect-application-instance.md).
