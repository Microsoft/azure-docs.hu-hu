---
title: Az Azure Virtual Machines for Linux SQL Server áttekintése | Microsoft Docs
description: Ismerje meg, hogyan futtathat teljes SQL Server kiadásokat az Azure Virtual Machines for Linux rendszeren. Ebben a cikkben közvetlen hivatkozásokat találhat az SQL Servert tartalmazó összes linuxos virtuálisgép-rendszerképhez és a kapcsolódó tartalmakhoz.
services: virtual-machines-sql
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: overview
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3613b8417c78d0a01582defb7396cc65521e2f4c
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449481"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Az SQL Server használatának áttekintése az Azure Virtual Machines szolgáltatásban (Linux rendszeren)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

Az Azure Virtual Machines SQL Server lehetővé teszi a felhőalapú SQL Server teljes verziójának használatát anélkül, hogy bármilyen helyszíni hardvert kellene kezelnie. Az SQL Servert futtató virtuális gépekkel a használatalapú licencelési költségek is leegyszerűsödnek.

Világszerte számos [földrajzi régióban](https://azure.microsoft.com/regions/) üzemelnek Azure virtuális gépek. Több különböző [gépméret](../../../virtual-machines/sizes.md) is elérhető. A virtuális gépek rendszerkép-katalógusából a megfelelő verziójú, kiadású és operációs rendszerű, SQL Servert futtató virtuális gépet hozhat létre. Ez számos különböző SQL Server számítási feladatra teszi alkalmassá a virtuális gépeket. 

Ha még nem ismeri az Azure SQL-t, tekintse meg SQL Server az Azure-beli *virtuális gépek áttekintését ismertető* videót a részletes [Azure SQL-videós sorozatban](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="get-started-with-sql-server-vms"></a><a id="create"></a> Ismerkedés a SQL Server virtuális gépekkel

Első lépésként válassza ki a megfelelő verziójú, kiadású és operációs rendszerű SQL Server virtuális gép rendszerképét. Az alábbi szakaszokban közvetlen hivatkozások találhatók az Azure Portalra az SQL Server virtuálisgép-katalógus rendszerképeinek letöltéséhez.

> [!TIP]
> A SQL Server rendszerképek díjszabásának megismeréséhez tekintse meg [a SQL Server rendszert futtató linuxos virtuális gépek díjszabási oldalát](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Verzió | Operációs rendszer | Kiadás |
| --- | --- | --- |
| **SQL Server 2019** | Ubuntu 18.04 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804enterprise-ARM), [standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804standard-ARM), [web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804sqldev-ARM) | 
| **SQL Server 2019** | Red Hat Enterprise Linux (RHEL) 8 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8enterprise-ARM), [standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8standard-ARM), [web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8sqldev-ARM)|
| **SQL Server 2019** | SUSE Linux Enterprise Server (SLES) V12 SP5 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5enterprise-ARM), [standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5standard-ARM), [web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5sqldev-ARM)|
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> A Windows rendszerhez elérhető SQL Server virtuálisgép-rendszerképek megtekintéséhez lásd: az [Azure Virtual Machines (Windows) SQL Server áttekintése](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

## <a name="installed-packages"></a><a id="packages"></a> Telepített csomagok

SQL Server on Linux konfigurálásakor az adatbázismotor-csomagot, majd a követelményektől függően számos opcionális csomagot kell telepítenie. A Linux rendszerű SQL Server-virtuálisgépek rendszerképei automatikusan telepítik a legtöbb csomagot. A következő táblázat ismerteti, hogy melyik disztribúció melyik csomagokat telepíti.

| Disztribúció | [Adatbázismotor](/sql/linux/sql-server-linux-setup) | [Eszközök](/sql/linux/sql-server-linux-setup-tools) | [SQL Server ügynök](/sql/linux/sql-server-linux-setup-sql-agent) | [Teljes szöveges keresés](/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](/sql/linux/sql-server-linux-setup-ssis) | [Magas rendelkezésreállási bővítmény](/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![RHEL és adatbázismotor](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL és eszközök](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL és SQL Server ügynök](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL és teljes szöveges keresés](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL és SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL és HA bővítmény](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |
| SLES | ![SLES és adatbázismotor](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES és eszközök](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES és SQL Server ügynök](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES és teljes szöveges keresés](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES és SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![SLES és HA bővítmény](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png)|
| Ubuntu | ![Ubuntu és adatbázismotor](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu és eszközök](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu és SQL Server ügynök](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu és teljes szöveges keresés](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu és SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu és HA bővítmény](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>Kapcsolódó termékek és szolgáltatások

### <a name="linux-virtual-machines"></a>Linux rendszerű virtuális gépek

* [Az Azure Virtual Machines áttekintése](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>Tárolás

* [A Microsoft Azure Storage bemutatása](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Hálózatkezelés

* [Virtual Network áttekintése](../../../virtual-network/virtual-networks-overview.md)
* [IP-címek az Azure-ban](../../../virtual-network/public-ip-addresses.md)
* [Teljes tartománynév létrehozása az Azure Portalon](../../../virtual-machines/create-fqdn.md)

### <a name="sql"></a>SQL

* [SQL Server on Linux dokumentáció](/sql/linux)
* [Az Azure SQL Database összehasonlítása](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Következő lépések

Ismerkedés a SQL Server on Linux Virtual Machines szolgáltatással:

* [SQL Servert futtató virtuális gép létrehozása az Azure Portalon](sql-vm-create-portal-quickstart.md)

Válaszok a Linux rendszerű SQL Server virtuális gépekkel kapcsolatos gyakori kérdésekre:

* [Az SQL Server használata Azure virtuális gépeken – gyakori kérdések](frequently-asked-questions-faq.md)