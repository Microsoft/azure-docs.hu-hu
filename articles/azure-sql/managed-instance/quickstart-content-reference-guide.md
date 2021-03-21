---
title: Az első lépések tartalmi referenciája
titleSuffix: Azure SQL Managed Instance
description: 'Olyan tartalomra mutató hivatkozás, amely segítséget nyújt az Azure SQL felügyelt példányának megkezdéséhez. '
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 07/11/2019
ms.openlocfilehash: c84c81fa81cf4ba9be454eea5782927a09c3252a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690959"
---
# <a name="getting-started-with-azure-sql-managed-instance"></a>Az Azure SQL felügyelt példányának első lépései
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az [Azure SQL felügyelt példánya](sql-managed-instance-paas-overview.md) egy olyan adatbázist hoz létre, amely közel 100%-os kompatibilitással rendelkezik a legújabb SQL Server (Enterprise Edition) adatbázis-kezelővel, amely egy olyan natív [virtuális hálózati (VNet)](../../virtual-network/virtual-networks-overview.md) implementációt biztosít, amely az általános biztonsági kérdésekre és a meglévő SQL Server ügyfelek számára kedvező [üzleti modellre](https://azure.microsoft.com/pricing/details/sql-database/) épül.

Ebben a cikkben olyan tartalmakra mutató hivatkozásokat talál, amelyek megtanítják, hogyan lehet gyorsan konfigurálni és létrehozni egy SQL felügyelt példányt, és hogyan telepíthet át adatbázisokat.

## <a name="quickstart-overview"></a>Gyors üzembe helyezés áttekintése

A következő rövid útmutatók segítségével gyorsan létrehozhat egy felügyelt SQL-példányt, konfigurálhat egy virtuális gépet, vagy megtekintheti az ügyfélalkalmazás VPN-kapcsolatát, és egy fájl használatával visszaállíthat egy adatbázist az új SQL felügyelt példányra `.bak` .

### <a name="configure-environment"></a>Környezet konfigurálása

Első lépésként létre kell hoznia az első SQL-beli felügyelt példányát a hálózati környezettel, ahol a rendszer elhelyezi és engedélyezi a kapcsolódást a számítógép vagy a virtuális gép között, ahol lekérdezéseket hajt végre az SQL felügyelt példányán. A következő útmutatók használhatók:

- [Hozzon létre egy felügyelt SQL-példányt a Azure Portal használatával](instance-create-quickstart.md). A Azure Portal a szükséges paramétereket (felhasználónév/jelszó, a magok számát és a maximális tárterületet) konfigurálja, és automatikusan létrehozza az Azure hálózati környezetét anélkül, hogy ismernie kellene a hálózatkezelés részleteit és az infrastruktúra követelményeit. Győződjön meg arról, hogy rendelkezik olyan [előfizetési típussal](resource-limits.md#supported-subscription-types) , amely jelenleg jogosult SQL felügyelt példány létrehozására. Ha van saját hálózata, amelyet használni szeretne, vagy testre szeretné szabni a hálózatot, tekintse meg a [meglévő virtuális hálózat konfigurálása az Azure SQL felügyelt példányhoz](vnet-existing-add-subnet.md) vagy [virtuális hálózat létrehozása az Azure SQL felügyelt példányhoz](virtual-network-subnet-create-arm-template.md)című témakört.
- A felügyelt SQL-példányok a saját VNet, nyilvános végpont nélkül jönnek létre. Az ügyfélalkalmazások hozzáféréséhez **létrehozhat egy virtuális gépet ugyanabban a VNet (más alhálózatban)** , vagy **létrehozhat egy pont – hely típusú VPN-kapcsolatot az ügyfélszámítógép VNet** az alábbi rövid útmutatók egyikével:
  - Engedélyezze a [nyilvános végpontot](public-endpoint-configure.md) az SQL felügyelt példányán az adatok közvetlenül a környezetből való eléréséhez.
  - Hozzon létre [Azure-beli virtuális gépet a felügyelt SQL-példányok VNet](connect-vm-instance-configure.md) az ügyfél-alkalmazási kapcsolathoz, beleértve a SQL Server Management Studio.
  - Hozzon létre [pont – hely VPN-kapcsolatot az SQL felügyelt példányához](point-to-site-p2s-configure.md) az ügyfélszámítógépen, amelyen SQL Server Management Studio és más ügyfélkapcsolati alkalmazásokat. Ez az SQL felügyelt példányához és annak VNet való csatlakozás két lehetőségből áll.

  > [!NOTE]
  > - A helyi hálózatról Express Route vagy helyek közötti kapcsolat is használható, de ezek a módszerek nem tartoznak a rövid útmutatók körébe.
  > - Ha a megőrzési időszakot 0 (korlátlan megőrzés) értékre módosítja bármely más értékre, vegye figyelembe, hogy az adatmegőrzés csak a megőrzési érték módosítását követően írt naplókra vonatkozik

A felügyelt SQL-példány manuális létrehozása helyett használhatja a [PowerShellt](scripts/create-configure-managed-instance-powershell.md), [a PowerShellt Resource Manager-sablonnal](./create-template-quickstart.md)vagy az [Azure CLI](/cli/azure/sql/mi#az-sql-mi-create) -vel a folyamat parancsfájlhoz és automatizálásához.

### <a name="migrate-your-databases"></a>Adatbázisok migrálása

A felügyelt SQL-példányok létrehozása és a hozzáférés konfigurálása után megkezdheti a SQL Server-adatbázisok áttelepítését. Az áttelepítés sikertelen lehet, ha az áttelepíteni kívánt forrásadatbázis nem támogatott funkciói vannak. A hibák elkerülése és a kompatibilitás ellenőrzése érdekében [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) segítségével elemezheti az adatbázisait SQL Server és megkeresheti az SQL felügyelt példányra való áttelepítést megakadályozó problémákat, például a [FileStream](/sql/relational-databases/blob/filestream-sql-server) vagy több naplófájlt. Ha megoldja ezeket a problémákat, az adatbázisok készen állnak az SQL felügyelt példányra való áttelepítésre. [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) egy másik hasznos eszköz, amely rögzíti a munkaterhelést SQL Server és visszajátszhatja azt egy SQL felügyelt példányon, így megállapítható, hogy az SQL-ben felügyelt példányra való Migrálás során probléma merül fel.

Ha biztos benne, hogy áttelepítheti az adatbázist egy felügyelt SQL-példányra, a natív SQL Server visszaállítási képességeivel állíthatja vissza az adatbázist egy SQL felügyelt példányra egy `.bak` fájlból. Ezzel a módszerrel áttelepítheti az adatbázisokat a helyszíni vagy az Azure Virtual Machines telepített SQL Server-adatbázismotor használatával. Gyors útmutató: visszaállítás a [biztonsági másolatból egy SQL-felügyelt példányra](restore-sample-database-quickstart.md). Ebben a rövid útmutatóban egy, `.bak` Az Azure Blob Storage-ban tárolt fájlból kell visszaállítani a `RESTORE` Transact-SQL parancs használatával.

> [!TIP]
> Ha a `BACKUP` Transact-SQL parancs használatával szeretne biztonsági másolatot készíteni az adatbázisról az Azure Blob Storage-ban, tekintse [meg SQL Server biztonsági mentés URL-címére](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Ezek a rövid útmutatók lehetővé teszik az adatbázis biztonsági másolatának gyors létrehozását, konfigurálását és visszaállítását egy SQL-felügyelt példányra. Bizonyos esetekben testre kell szabnia vagy automatizálni kell az SQL felügyelt példányának és a szükséges hálózati környezetnek a telepítését. A következő forgatókönyvek leírását alább találja.

## <a name="customize-network-environment"></a>Hálózati környezet testreszabása

Bár a VNet/alhálózat automatikusan konfigurálható, ha a példányt [a Azure Portal használatával hozza létre](instance-create-quickstart.md), érdemes lehet létrehozni, mielőtt megkezdené a példányok LÉTREHOZÁSÁT az SQL felügyelt példányban, mert beállíthatja a VNet és az alhálózat paramétereit. A hálózati környezet létrehozásának és konfigurálásának legegyszerűbb módja az [Azure erőforrás-telepítési](virtual-network-subnet-create-arm-template.md) sablon használata, amely létrehozza és konfigurálja a hálózatot és az alhálózatot, ahol a példány el lesz helyezve. Csak a Azure Resource Manager üzembe helyezése gombot kell megnyomnia, és fel kell töltenie az űrlapot paraméterekkel.

Ezt a [PowerShell-szkriptet](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) is használhatja a hálózat létrehozásának automatizálására.

Ha már rendelkezik egy VNet és alhálózattal, ahol telepíteni szeretné az SQL felügyelt példányát, meg kell győződnie arról, hogy a VNet és az alhálózat megfelel a [hálózatkezelési követelményeknek](connectivity-architecture-overview.md#network-requirements). Ezzel a [PowerShell-parancsfájllal ellenőrizheti, hogy az alhálózat megfelelően van-e konfigurálva](vnet-existing-add-subnet.md). Ez a szkript érvényesíti a hálózatot, és jelentést készít az esetleges problémákról, és közli, hogy mit kell módosítania, majd a VNet/alhálózatban elérhetővé tenni a szükséges módosításokat. Futtassa ezt a parancsfájlt, ha nem szeretné manuálisan konfigurálni a VNet/alhálózatot. A hálózati infrastruktúra bármely jelentős újrakonfigurálását követően is futtathatja azt. Ha saját hálózatot szeretne létrehozni és konfigurálni, olvassa el a [kapcsolati architektúra](connectivity-architecture-overview.md) és ez a [végső útmutató az SQL felügyelt példány-környezet létrehozásához és konfigurálásához](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)című témakört.

## <a name="migrate-to-a-sql-managed-instance"></a>Migrálás SQL felügyelt példányra

A korábban említett rövid útmutatók lehetővé teszik egy SQL felügyelt példány gyors beállítását, és az adatbázisok áthelyezését a natív `RESTORE` képesség használatával. Ez jó kiindulási pont, ha gyors ellenőrzéseket szeretne végezni, és ellenőrizze, hogy a megoldás tud-e dolgozni a felügyelt példányon.

Ahhoz azonban, hogy a termelési adatbázist vagy akár fejlesztési, illetve tesztelési adatbázisokat is át szeretne telepíteni az egyes teljesítménytesztekhez, érdemes megfontolnia néhány további módszer használatát, például a következőket:

- Teljesítményteszt – mérje fel a forrás SQL Server példányának alapteljesítmény-mérőszámait, és hasonlítsa össze azokat a célként megadott SQL felügyelt példány teljesítmény-metrikákkal, ahol az adatbázist áttelepítette. További információ a [teljesítmény-összehasonlítással kapcsolatos ajánlott eljárásokról](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).
- Online áttelepítés – a `RESTORE` jelen cikkben ismertetett natív módon meg kell várnia az adatbázisok visszaállítását (és az Azure Blob Storage-ba való másolását, ha ott még nem tárolja őket). Ez az alkalmazás bizonyos állásidőt okoz, különösen nagyobb adatbázisok esetén. Az éles adatbázis áthelyezéséhez használja az [adatáttelepítési szolgáltatást (DMS)](../../dms/tutorial-sql-server-to-managed-instance.md?toc=%2fazure%2fsql-database%2ftoc.json) az adatbázis minimális állásidővel való áttelepítéséhez. A DMS ezt úgy hajtja végre, hogy a forrásadatbázis módosításait a visszaállított SQL felügyelt példány-adatbázisra fokozatosan kikényszeríti. Így gyorsan átválthatja az alkalmazást a forrásról a cél adatbázisra minimális állásidővel.

További információ az [ajánlott áttelepítési folyamatról](migrate-to-instance-from-sql-server.md).

## <a name="next-steps"></a>Következő lépések

- Tekintse meg az [SQL felügyelt példányának támogatott funkcióinak magas szintű listáját itt](../database/features-comparison.md) , valamint az [itt olvasható részleteket és ismert problémákat](transact-sql-tsql-differences-sql-server.md).
- Ismerje meg [az SQL felügyelt példányának technikai jellemzőit](resource-limits.md#service-tier-characteristics).
- A [felügyelt SQL-példányok használatát](how-to-content-reference-guide.md)részletesebben is megtalálhatja.
- [Azonosítsa az Azure SQL felügyelt példányának megfelelő SKU-](/sql/dma/dma-sku-recommend-sql-db/)t a helyszíni adatbázishoz.