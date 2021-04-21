---
title: Gyakori kérdések – Azure Database Migration Service
description: Gyakori kérdések a Azure Database Migration Service adatbázis-áttelepítések végrehajtásához.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 29240f371d39ef2d3234e3207cc91768478bf3ff
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748502"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Gyakori kérdések a Azure Database Migration Service

Ez a cikk a gyakori kérdéseket sorolja fel a Azure Database Migration Service és a kapcsolódó válaszok használatával kapcsolatban.

## <a name="overview"></a>Áttekintés

**K. Mi az Azure Database Migration Service?**
Azure Database Migration Service egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a több adatbázisforrásból azure-adatplatformra történő zökkenőmentes migrálást minimális állásidővel. A szolgáltatás jelenleg általánosan elérhető, és a folyamatban lévő fejlesztési erőfeszítések a következőre összpontosítanak:

* Megbízhatóság és teljesítmény.
* Forrás–cél párok iteratív összeadása.
* Folyamatos befektetés a zökkenőmentes migrálásba.

**K. Jelenleg milyen forrás-/célpárokat Azure Database Migration Service a rendszer?**
A szolgáltatás jelenleg többféle forrás-/célpárt vagy migrálási forgatókönyvet támogat. Az egyes elérhető áttelepítési forgatókönyvek állapotának teljes listáját a következő cikkben talál: Az áttelepítési forgatókönyvek állapota, amelyet a [Azure Database Migration Service.](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/dms/resource-scenario-status.md)

**K. A SQL Server melyik Azure Database Migration Service támogatja forrásként?**
A 2005 SQL Server 2005 és 2019 Azure Database Migration Service támogatott források SQL Server át a SQL Server támogatott forrásokra.

**K: A Azure Database Migration Service mi a különbség az offline és az online migrálás között?**
A kapcsolat nélküli Azure Database Migration Service online migráláshoz használhatja a Azure Database Migration Service is. Offline *migrálás esetén* az alkalmazás leállása a migrálás indításakor kezdődik. Online *migrálás* esetén az állásidő a migrálás végén átveszi az időt. Azt javasoljuk, hogy próbálja ki az offline migrálást, és határozza meg, hogy elfogadható-e az állásidő; ha nem, végezzen online migrálást.

> [!NOTE]
> Az Azure Database Migration Service online migrálás végrehajtásához egy példányt kell létrehozni a Prémium tarifacsomag alapján. További információt a díjszabást Azure Database Migration Service [oldalon.](https://azure.microsoft.com/pricing/details/database-migration/)

**K. Mi a Azure Database Migration Service a Microsoft más adatbázis-migrálási eszközeivel, például a Database Migration Assistant (DMA) vagy SQL Server Migration Assistant (SSMA)?**
Azure Database Migration Service adatbázis-migrálás előnyben részesített módszere Microsoft Azure nagy léptékű adatbázis-áttelepítéshez. A Microsoft Azure Database Migration Service más adatbázis-migrálási eszközeivel való összehasonlításával kapcsolatos további részletekért és a szolgáltatás különböző forgatókönyvekben való használatával kapcsolatos javaslatokért tekintse meg a Microsoft Database [Migration Tools and Services differentiating Microsoft's Database Migration Tools and Services](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529)(A Microsoft adatbázis-migrálási eszközeinek és szolgáltatásainak különbségei) blogbejegyzését.

**K. Miben Azure Database Migration Service hasonlítható össze a Azure Migrate ajánlat?**
Azure Migrate helyszíni virtuális gépek Azure IaaS-be való migrálását segíti. A szolgáltatás felméri a migrálásra való alkalmasságot és a teljesítményalapú méretezést, és költségbecslést ad a helyszíni virtuális gépek Azure-ban való futtatásához. Azure Migrate a helyszíni virtuálisgép-alapú számítási feladatok Azure IaaS virtuális gépekre való átemléses migrálásában hasznos. A Azure Database Migration Service azonban Azure Migrate nem egy speciális adatbázis-migrálási szolgáltatás az Azure PaaS relációsadatbázis-platformokhoz, például Azure SQL Database vagy Azure SQL Managed Instance.

**K. Tárolnak Database Migration Service ügyféladatokat?**
Nem. Database Migration Service nem tárol ügyféladatokat.

## <a name="setup"></a>Beállítás

**K. Milyen előfeltételeknek kell megfeleltetni a Azure Database Migration Service?**
Számos előfeltétel szükséges annak biztosításához, hogy Azure Database Migration Service az adatbázis-áttelepítések végrehajtása során. Néhány előfeltétel a szolgáltatás által támogatott összes forgatókönyvre (forrás-cél párra) vonatkozik, míg más előfeltételek csak egy adott forgatókönyvre vonatkoznak.

Azure Database Migration Service összes támogatott migrálási forgatókönyvben gyakori előfeltételek közé tartozik a következőkre vonatkozó szükség:

* Hozzon létre egy Microsoft Azure Virtual Network-Azure Database Migration Service a Azure Resource Manager üzembe helyezési modelljével, amely [expressRoute](../expressroute/expressroute-introduction.md) vagy VPN használatával biztosít hely–hely kapcsolatot a helyszíni [forráskiszolgálókkal.](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport szabályai nem blokkolják a 443-as portot a ServiceBus, a Storage és az AzureMonitor ServiceTags (Szolgáltatáscímkék) számára. A virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részletekért tekintse meg a Hálózati forgalom szűrése hálózati [biztonsági csoportokkal cikket.](../virtual-network/virtual-network-vnet-plan-design-arm.md)
* Ha tűzfalberendezést használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia, hogy a Azure Database Migration Service hozzáfér a forrásadatbázis(ok)hoz a migráláshoz.

Az egyes migrálási forgatókönyvek Azure Database Migration Service használatával való versengéséhez szükséges összes előfeltételt az [](./dms-overview.md) Azure Database Migration Service dokumentációjának kapcsolódó oktatóanyagai docs.microsoft.com.

**K. Hogyan ip-címét, hogy Azure Database Migration Service létre tudjak hozni egy engedélyezőlistát a forrásadatbázishoz való migráláshoz használt tűzfalszabályokhoz?**
Előfordulhat, hogy tűzfalszabályokat kell hozzáadnia, Azure Database Migration Service hozzáférést a forrásadatbázishoz a migráláshoz. A szolgáltatás IP-címe dinamikus, de ha ExpressRoute-et használ, ezt a címet a vállalati hálózat privát módon rendeli hozzá. A megfelelő IP-cím azonosításának legegyszerűbb módja, ha ugyanabban az erőforráscsoportban keres, mint a kiépített Azure Database Migration Service, és megkeresi a társított hálózati adaptert. A hálózati adapter erőforrásának neve általában a hálózati adapter előtaggal kezdődik, amelyet egy egyedi karakter és számsorozat követ, például NIC-jj6tnztnmarpsskr82rbndyp. A hálózati adapter erőforrásának kiválasztásával láthatja azt az IP-címet, amelyet fel kell venni az erőforrás áttekintési oldalán található Azure Portal listájába.

Előfordulhat, hogy azt a portforrást is meg kell SQL Server, amely az engedélyező listán figyel. Alapértelmezés szerint ez az 1433-as port, de a forrás SQL Server más portok figyelére is konfigurálható. Ebben az esetben ezeket a portokat is fel kell foglalnia az engedélyezőlistára. Dinamikus felügyeleti nézet lekérdezésével SQL Server meg a portot, amelyet a rendszer figyel:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

A portot, amelyet a SQL Server figyel, a hibanapló lekérdezése SQL Server is meghatározhatja:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**K. Hogyan egy új Microsoft Azure Virtual Network?**
Bár több, a virtuális hálózat beállítását bemutató Microsoft-oktatóanyagot is talál, a hivatalos dokumentáció az [Azure Virtual Network.](../virtual-network/virtual-networks-overview.md)

## <a name="usage"></a>Használat

**K. Milyen lépések szükségesek az adatbázis-Azure Database Migration Service végrehajtásához?**
Egy tipikus, egyszerű adatbázis-migrálás során a következőt kell:

1. Céladatbázis(ak) létrehozása.
2. Mérje fel a forrásadatbázis(okat).
    * Homogén migrálások esetén mérje fel a meglévő adatbázis(okat) a [DMA használatával.](https://www.microsoft.com/download/details.aspx?id=53595)
    * Heterogén (versenyforrásokból származó) migrálások esetén mérje fel meglévő adatbázis(okat) az [SSMA-val.](/sql/ssma/sql-server-migration-assistant) Az SSMA használatával adatbázis-objektumokat konvertál, és a sémát a célplatformra mikalálhatja.
3. Hozzon létre egy Azure Database Migration Service-példányt.
4. Hozzon létre egy migrálási projektet, amely megadja a forrásadatbázis(okat), a céladatbázis(okat) és a migrálni kívánt táblákat.
5. Indítsa el a teljes betöltést.
6. Válassza ki az ezt követő érvényesítést.
7. Manuális váltás az éles környezetről az új felhőalapú adatbázisra.

## <a name="troubleshooting-and-optimization"></a>Hibaelhárítás és optimalizálás

**K. Migrálási projektet hozok létre a DMS-ban, és nem tudok csatlakozni a forrásadatbázishoz. Mit tegyek?**
Ha nem tudja csatlakozni a forrásadatbázist a migrálás során, hozzon létre egy virtuális gépet a virtuális hálózat ugyanazon alhálózatán, amellyel a DMS-példányt beállította. A virtuális gépen képesnek kell lennie csatlakozási teszt futtatására, például egy UDL-fájllal tesztelni a kapcsolatot az SQL Server-hez, vagy le kell töltenie a Robo 3T-t a MongoDB-kapcsolatok teszteléséhez. Ha a kapcsolati teszt sikeres, nem kell problémát jelentsen a forrásadatbázishoz való csatlakozáskor. Ha a kapcsolati teszt sikertelen, forduljon a hálózati rendszergazdához.

**K. Miért nem érhető Azure Database Migration Service vagy áll le a rendszer?**
Ha a felhasználó kifejezetten leállítja a Azure Database Migration Service (DMS), vagy ha a szolgáltatás 24 órán át inaktív, a szolgáltatás leállított vagy automatikusan szüneteltetett állapotban lesz. A szolgáltatás minden esetben elérhetetlen lesz, és leállított állapotú.  Az aktív áttelepítések folytatásához indítsa újra a szolgáltatást.

**K. Vannak javaslatok a szolgáltatások teljesítményének optimalizálására Azure Database Migration Service?**
Az adatbázis-migrálás felgyorsítása érdekében használhatja a szolgáltatást:

* Használja a több CPU-általános célú tarifacsomagot a szolgáltatáspéldány létrehozásakor, hogy a szolgáltatás több vCPU-t használjon a párhuzamosításhoz és a gyorsabb adatátvitelhez.
* Ideiglenesen skálázhatja fel Azure SQL Database célpéldányt Azure SQL Database prémium szintű termékváltozatra az adatáttelepítési művelet során, hogy minimalizálja az adatátviteli tevékenységeket az alacsonyabb szintű termékváltozatok használata esetén esetleg hatással lehet az adatátviteli tevékenységekre.

## <a name="next-steps"></a>Következő lépések

Az áttekintést a Azure Database Migration Service és a regionális rendelkezésre állásról a What [is the Azure Database Migration Service](dms-overview.md).
