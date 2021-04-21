---
title: Első lépések – tartalomre vonatkozó referencia
titleSuffix: Azure SQL Managed Instance
description: 'Referencia a tartalomhoz, amely segít a Azure SQL Managed Instance. '
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
ms.openlocfilehash: ec767e5f3a88c52f9686eec8f7e458ab517ee35f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784334"
---
# <a name="getting-started-with-azure-sql-managed-instance"></a>Ismerkedés a Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) olyan adatbázist hoz létre, amely közel 100%-os kompatibilitást biztosít [a](https://azure.microsoft.com/pricing/details/sql-database/) legújabb SQL Server (Enterprise kiadás) adatbázismotorral, és natív virtuális hálózati [(VNet)](../../virtual-network/virtual-networks-overview.md) implementációt biztosít, amely a gyakori biztonsági problémákra választ ad, valamint egy kedvező üzleti modellt a meglévő SQL Server-ügyfelek számára.

Ebben a cikkben olyan tartalmakra mutató hivatkozásokat talál, amelyekből megtanulhatja, hogyan lehet gyorsan konfigurálni és létrehozni egy SQL Managed Instance és az adatbázisokat.

## <a name="quickstart-overview"></a>Gyors üzembe helyezés áttekintése

Az alábbi rövid útmutatók segítségével gyorsan létrehozhat egy SQL Managed Instance-t, konfigurálhat egy virtuális gépet vagy pont–hely VPN-kapcsolatot az ügyfélalkalmazáshoz, és egy fájl használatával visszaállíthat egy adatbázist az SQL Managed Instance új virtuális `.bak` gépre.

### <a name="configure-environment"></a>Környezet konfigurálása

Első lépésként létre kell hoznia az első SQL Managed Instance-t azzal a hálózati környezettel, ahol el szeretné helyezni, és engedélyeznie kell a csatlakozást a számítógépről vagy virtuális gépről, ahol lekérdezéseket hajt SQL Managed Instance. A következő útmutatókat használhatja:

- [Hozzon létre egy SQL Managed Instance az Azure Portal.](instance-create-quickstart.md) A Azure Portal konfigurálja a szükséges paramétereket (felhasználónév/jelszó, magok száma és a maximális tárterület mennyisége), és automatikusan létrehozza az Azure-beli hálózati környezetet anélkül, hogy adatokat kellene tudni a hálózatról és az infrastruktúra követelményeiről. Csak győződjön meg arról, hogy [olyan](resource-limits.md#supported-subscription-types) előfizetési típussal rendelkezik, amely számára jelenleg engedélyezett a SQL Managed Instance. Ha a saját hálózatát szeretné használni, vagy testre szeretné [](vnet-existing-add-subnet.md) szabni a hálózatot, tekintse meg a meglévő virtuális hálózat Azure SQL Managed Instance számára való konfigurálásról vagy a virtuális hálózat létrehozásáról a [Azure SQL Managed Instance.](virtual-network-subnet-create-arm-template.md)
- A SQL Managed Instance a saját virtuális hálózatában jön létre, nyilvános végpont nélkül. Az ügyfélalkalmazások eléréséhez létrehozhat egy virtuális gépet ugyanazon **a VNeten (eltérő alhálózaton),** vagy **pont–hely VPN-kapcsolatot** hozhat létre a virtuális hálózattal az ügyfélszámítógépről az alábbi rövid útmutatók egyikével:
  - Engedélyezze [a nyilvános végpontot](public-endpoint-configure.md) a SQL Managed Instance, hogy közvetlenül a környezetből fér hozzá az adatokhoz.
  - Hozzon [létre Azure-beli virtuális SQL Managed Instance a](connect-vm-instance-configure.md) virtuális hálózaton az ügyfélalkalmazások csatlakoztatásához, beleértve a SQL Server Management Studio.
  - Állítson [be pont–hely VPN-kapcsolatot](point-to-site-p2s-configure.md) a SQL Managed Instance az ügyfélszámítógépről, amelyen SQL Server Management Studio más ügyfélkapcsolati alkalmazásokat. Ez a két másik lehetőség a kapcsolat a SQL Managed Instance és annak virtuális hálózata között.

  > [!NOTE]
  > - A helyi hálózatról expressz útvonalat vagy hely–hely kapcsolatot is használhat, de ezek a megközelítések ezen rövid útmutatók hatókörén kívül esnek.
  > - Ha a megőrzési időszakot 0-ra (korlátlan megőrzésre) módosítja, vegye figyelembe, hogy a megőrzési idő csak a megőrzési érték módosítása után írt naplókra vonatkozik (a korlátlan megőrzési időszak alatt írt naplók megmaradnak, még a megőrzés engedélyezése után is).

A manuális létrehozás alternatívájaként használhatja SQL Managed Instance [PowerShellt,](scripts/create-configure-managed-instance-powershell.md)a [PowerShellt](./create-template-quickstart.md)Resource Manager-sablonnal vagy az Azure [CLI-t](/cli/azure/sql/mi#az_sql_mi_create) a folyamat szkriptek írásához és automatizálásához.

### <a name="migrate-your-databases"></a>Adatbázisok áttelepítése

Miután létrehozott egy SQL Managed Instance és konfigurálta a hozzáférést, elkezdheti a SQL Server áttelepítését. Az áttelepítés meghiúsulhat, ha a forrásadatbázis néhány nem támogatott szolgáltatását át szeretné migrálni. A hibák elkerülése és a kompatibilitás ellenőrzése érdekében a [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) használatával elemezheti az SQL Server-adatbázisokat, és megkeresheti az SQL Managed Instance-be való migrálást esetlegesen blokkoló problémákat, például a [FileStream](/sql/relational-databases/blob/filestream-sql-server) vagy több naplófájl meglétét. Ha megoldja ezeket a problémákat, az adatbázisok készen állnak a SQL Managed Instance. [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) egy másik hasznos eszköz, amely rögzítheti a számítási feladatokat az SQL Server-on, és visszajátszhatja azt egy SQL Managed Instance-on, így megállapíthatja, hogy lesznek-e teljesítménybeli problémák a számítási feladatok SQL Managed Instance.

Ha biztos abban, hogy az adatbázist át tudja SQL Managed Instance-re, a natív SQL Server-visszaállítási képességekkel visszaállíthatja az adatbázist egy fájlból SQL Managed Instance `.bak` helyre. Ezzel a módszerrel adatbázisokat telepíthet SQL Server helyszíni vagy Azure-beli virtuális gépre telepített adatbázismotorból Virtual Machines. Rövid útmutató: [Visszaállítás](restore-sample-database-quickstart.md)biztonsági másolatból egy SQL Managed Instance. Ebben a rövid útmutatóban egy Azure Blob Storage-ban tárolt fájlból hoz létre visszaállítást a `.bak` `RESTORE` Transact-SQL paranccsal.

> [!TIP]
> Ha a Transact-SQL paranccsal biztonsági másolatot készít az adatbázisról az Azure Blob Storage-ban, tekintse meg a SQL Server URL-címre való biztonsági `BACKUP` [mentését.](/sql/relational-databases/backup-restore/sql-server-backup-to-url)

Ezekkel a rövid útmutatóval gyorsan létrehozhatja, konfigurálhatja és visszaállíthatja az adatbázis biztonsági másolatát egy SQL Managed Instance. Bizonyos esetekben testre kell szabni vagy automatizálni kell a hálózati környezet SQL Managed Instance telepítését. Ezeket a forgatókönyveket az alábbiakban ismertetjük.

## <a name="customize-network-environment"></a>Hálózati környezet testreszabása

Bár a virtuális hálózat/alhálózat automatikusan konfigurálható a [példány Azure Portal](instance-create-quickstart.md)használatával való létrehozásakor, érdemes lehet létrehozni, mielőtt a SQL Managed Instance-ban példányokat hoz létre, mert konfigurálhatja a VNet és az alhálózat paramétereit. A hálózati környezet létrehozásához és konfigurálásához a legegyszerűbben az [Azure Resource deployment](virtual-network-subnet-create-arm-template.md) sablont kell használni, amely létrehozza és konfigurálja a hálózatot és az alhálózatot, ahol a példány el lesz helyezve. Elég megnyomni a Azure Resource Manager gombra, és paraméterekkel feltölteni az űrlapot.

Alternatív megoldásként használhatja ezt a [PowerShell-szkriptet](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) is a hálózat létrehozásának automatizálásához.

Ha már rendelkezik olyan virtuális hálózattal és alhálózattal, ahol üzembe szeretné helyezni a SQL Managed Instance, meg kell győződni arról, hogy a virtuális hálózat és az alhálózat megfelel a hálózati [követelményeknek.](connectivity-architecture-overview.md#network-requirements) Ezzel a [PowerShell-szkripttel ellenőrizheti, hogy az alhálózat megfelelően van-e konfigurálva.](vnet-existing-add-subnet.md) Ez a szkript ellenőrzi a hálózatot, és jelenti a problémákat, és tudatja, hogy mit kell módosítani, majd felajánlja a szükséges módosításokat a virtuális hálózaton/alhálózaton. Ha nem szeretné manuálisan konfigurálni a VNetet/alhálózatot, futtassa ezt a szkriptet. A hálózati infrastruktúra bármilyen nagyobb újrakonfigurálását követően is futtathatja. Ha saját hálózatot szeretne létrehozni és [](connectivity-architecture-overview.md) konfigurálni, olvassa el a kapcsolati architektúrát és ezt a végső útmutatót egy új környezet [SQL Managed Instance konfigurálásához.](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)

## <a name="migrate-to-a-sql-managed-instance"></a>Áttelepítés SQL Managed Instance

A korábban említett gyorsútmutatók segítségével gyorsan állíthat be SQL Managed Instance és áthelyezheti az adatbázisokat a natív `RESTORE` képesség használatával. Ez jó kiindulási pont, ha gyors próbakontrasztokat szeretne végrehajtani, és ellenőrizni szeretné, hogy a megoldás működik-e a felügyelt példányon.

Az éles vagy akár fejlesztési/tesztelési adatbázisok teljesítményteszthez használni kívánt áttelepítéséhez azonban érdemes megfontolni néhány további technika használatát, például a következő technikákat:

- Teljesítményteszt – Mérje meg az alapteljesítmény-metrikákat a forrás SQL Server-példányon, és hasonlítsa össze azokat a célhelyen SQL Managed Instance, ahová az adatbázist migrálta. További információ a [teljesítmény-összehasonlításhoz ajánlott eljárásokról.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)
- Online migrálás – A cikkben ismertetett natív megoldásokkal meg kell várnia az adatbázisok visszaállítását (és az Azure Blob Storage-ba való másolását, ha az még nincs `RESTORE` ott tárolva). Ez az alkalmazás némi leállását okozza, különösen nagyobb adatbázisoknál. Az éles adatbázis áthelyezéséhez a [Data Migration Service (DMS)](../../dms/tutorial-sql-server-to-managed-instance.md?toc=%2fazure%2fsql-database%2ftoc.json) használatával migrálja az adatbázist a minimális állásidővel. A DMS ezt úgy valósítja meg, hogy növekményesen leküldi a forrásadatbázisban végrehajtott módosításokat a SQL Managed Instance adatbázisba. Így gyorsan átválthat az alkalmazás forrásról céladatbázisra minimális állásidővel.

További információ az ajánlott [migrálási folyamatról:](migrate-to-instance-from-sql-server.md).

## <a name="next-steps"></a>Következő lépések

- A támogatott [szolgáltatások magas](../database/features-comparison.md) szintű listáját itt találja SQL Managed Instance és az [ismert problémákat itt találja.](transact-sql-tsql-differences-sql-server.md)
- Ismerje meg [a SQL Managed Instance.](resource-limits.md#service-tier-characteristics)
- A speciálisabb bemutatja, hogyan [használhatja a](how-to-content-reference-guide.md)SQL Managed Instance.
- [Azonosítsa a Azure SQL Managed Instance adatbázishoz megfelelő termékváltozatot.](/sql/dma/dma-sku-recommend-sql-db/)