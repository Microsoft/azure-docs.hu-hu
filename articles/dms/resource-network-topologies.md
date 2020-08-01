---
title: SQL felügyelt példányok áttelepítésének hálózati topológiái
titleSuffix: Azure Database Migration Service
description: Ismerje meg az Azure SQL felügyelt példányainak a Azure Database Migration Service használatával történő áttelepítésének forrás-és cél konfigurációit.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 69926671730e41845cd28df3108ec86b24a57075
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448517"
---
# <a name="network-topologies-for-azure-sql-managed-instance-migrations-using-azure-database-migration-service"></a>Hálózati topológiák az Azure SQL felügyelt példányainak Azure Database Migration Service használatával történő áttelepítéséhez

Ez a cikk ismerteti azokat a különböző hálózati topológiákat, amelyekkel a Azure Database Migration Service együttműködve átfogó áttelepítési élményt biztosít az SQL-kiszolgálókról az Azure SQL felügyelt példányaira.

## <a name="azure-sql-managed-instance-configured-for-hybrid-workloads"></a>Hibrid számítási feladatokhoz konfigurált Azure SQL felügyelt példány 

Akkor használja ezt a topológiát, ha az Azure SQL felügyelt példánya a helyszíni hálózathoz csatlakozik. Ez a megközelítés biztosítja a legtöbb egyszerűsített hálózati útválasztást, és az áttelepítés során maximális adatátviteli sebességet eredményez.

![A hibrid számítási feladatok hálózati topológiája](media/resource-network-topologies/hybrid-workloads.png)

**Követelmények**

- Ebben az esetben az SQL felügyelt példánya és a Azure Database Migration Service példány ugyanabban a Microsoft Azure Virtual Networkban jön létre, de különböző alhálózatokat használ.  
- Az ebben a forgatókönyvben használt virtuális hálózat a helyszíni hálózathoz is csatlakozik a [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával.

## <a name="sql-managed-instance-isolated-from-the-on-premises-network"></a>A helyszíni hálózatból elkülönített SQL felügyelt példány

Akkor használja ezt a hálózati topológiát, ha a környezete a következő esetekben legalább egyet igényel:

- Az SQL felügyelt példánya el van különítve a helyszíni kapcsolattal, de a Azure Database Migration Service-példány a helyszíni hálózathoz csatlakozik.
- Ha az Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) házirendjei vannak érvényben, és korlátozni kell a felhasználókat arra, hogy hozzáférjenek az SQL felügyelt példányát futtató ugyanahhoz az előfizetéshez.
- A felügyelt SQL-példányhoz használt virtuális hálózatok és Azure Database Migration Service különböző előfizetésekben találhatók.

![A helyszíni hálózatról elkülönített felügyelt példány hálózati topológiája](media/resource-network-topologies/mi-isolated-workload.png)

**Követelmények**

- Az ehhez a forgatókönyvhöz Azure Database Migration Service használt virtuális hálózatnak a (vagy a VPN) használatával is csatlakoznia kell a helyszíni https://docs.microsoft.com/azure/expressroute/expressroute-introduction) hálózathoz [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Állítsa be a [VNet hálózati](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) társítást a felügyelt SQL-példányhoz használt virtuális hálózat és a Azure Database Migration Service között.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Felhőből felhőbe történő Migrálás: megosztott virtuális hálózat

Akkor használja ezt a topológiát, ha a forrás SQL Server egy Azure-beli virtuális GÉPEN fut, és ugyanazt a virtuális hálózatot osztja meg az SQL felügyelt példányával és Azure Database Migration Serviceával.

![A felhőből a felhőbe irányuló Migrálás hálózati topológiája megosztott VNet](media/resource-network-topologies/cloud-to-cloud.png)

**Követelmények**

- Nincsenek további követelmények.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Felhőbeli Migrálás: elkülönített virtuális hálózat

Akkor használja ezt a hálózati topológiát, ha a környezete a következő esetekben legalább egyet igényel:

- Az SQL felügyelt példánya elkülönített virtuális hálózatban van kiépítve.
- Ha az Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC-) szabályzatok érvényben vannak, és korlátozni kell a felhasználókat, hogy hozzáférjenek ugyanahhoz az előfizetéshez, amely az SQL felügyelt példányát üzemelteti.
- Az SQL felügyelt példányához használt virtuális hálózatok és a Azure Database Migration Service különböző előfizetésekben találhatók.

![A felhőből a felhőbe irányuló Migrálás hálózati topológiája elkülönített VNet](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Követelmények**

- Állítsa be a [VNet hálózati](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) társítást a felügyelt SQL-példányhoz használt virtuális hálózat és a Azure Database Migration Service között.

## <a name="inbound-security-rules"></a>Bejövő biztonsági szabály

| **NÉV**   | **PORT** | **PROTOKOLL** | **FORRÁS** | **CÉL** | **MŰVELET** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Bármely      | Bármelyik          | DMS-ALHÁLÓZAT | Bármelyik             | Engedélyezés      |

## <a name="outbound-security-rules"></a>Kimenő biztonsági szabályok

| **NÉV**                  | **PORT**                                              | **PROTOKOLL** | **FORRÁS** | **CÉL**           | **MŰVELET** | **Szabály oka**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| felügyelet                | 443, 9354                                              | TCP          | Bármely        | Bármelyik                       | Engedélyezés      | A felügyeleti sík Service Bus és az Azure Blob Storage szolgáltatáson keresztül kommunikál. <br/>(Ha a Microsoft-társak engedélyezve vannak, előfordulhat, hogy nincs szüksége erre a szabályra.)                                                             |
| Diagnosztika               | 12000                                                 | TCP          | Bármely        | Bármelyik                       | Engedélyezés      | A DMS ezt a szabályt használja a diagnosztikai adatok gyűjtéséhez hibaelhárítási célból.                                                                                                                      |
| SQL-forráskiszolgáló         | 1433 (vagy TCP IP-port, amelyet a SQL Server figyel) | TCP          | Bármely        | A helyszíni címtér | Engedélyezés      | SQL Serveri forrás kapcsolódás a DMS-ből <br/>(Ha helyek közötti kapcsolattal rendelkezik, előfordulhat, hogy nincs szüksége erre a szabályra.)                                                                                       |
| Megnevezett példány SQL Server | 1434                                                  | UDP          | Bármely        | A helyszíni címtér | Engedélyezés      | SQL Server megnevezett példány forrásának kapcsolata a DMS-ből <br/>(Ha helyek közötti kapcsolattal rendelkezik, előfordulhat, hogy nincs szüksége erre a szabályra.)                                                                        |
| SMB-megosztás                 | 445                                                   | TCP          | Bármely        | A helyszíni címtér | Engedélyezés      | SMB hálózati megosztás a DMS-hez az adatbázis biztonsági mentési fájljainak áttelepítéséhez az Azure-beli virtuális gépen Azure SQL Database a MI és az SQL Server rendszerre <br/>(Ha helyek közötti kapcsolattal rendelkezik, előfordulhat, hogy nincs szüksége erre a szabályra). |
| DMS_subnet                | Bármely                                                   | Bármelyik          | Bármelyik        | DMS_Subnet                | Engedélyezés      |                                                                                                                                                                                                  |

## <a name="see-also"></a>További információ

- [SQL Server migrálása SQL felügyelt példányra](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [A Azure Database Migration Service használatának előfeltételeinek áttekintése](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Virtuális hálózat létrehozása az Azure Portallal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>További lépések

- A Azure Database Migration Service áttekintése: [Mi a Azure Database Migration Service?](dms-overview.md).
- A Azure Database Migration Service regionális elérhetőségével kapcsolatos aktuális információkért tekintse meg az [elérhető termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) lapon.
