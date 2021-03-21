---
title: SSIS Migrálás az Azure SQL felügyelt példányával az adatbázis-munkaterhelés céljaként
description: SSIS Migrálás az Azure SQL felügyelt példányával az adatbázis-munkaterhelés céljaként.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 3d2bc60f8ba7120f8d962500c06be50e905c11a5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373589"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>SSIS Migrálás az Azure SQL felügyelt példányával az adatbázis-munkaterhelés céljaként

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Az adatbázis-munkaterhelések SQL Server példányról az Azure SQL felügyelt példányra történő áttelepítésekor ismernie kell az [Azure adatáttelepítési szolgáltatást](../dms/dms-overview.md)(DMS), valamint az [SQL felügyelt példányok áttelepítésének hálózati topológiáit a DMS használatával](../dms/resource-network-topologies.md).

Ez a cikk a SSIS-katalógusban (SSISDB) tárolt SQL Server Integration Service (SSIS) csomagok áttelepítését, valamint a SSIS-csomagok végrehajtását ütemezhető SQL Server Agent feladatokat ismerteti.

## <a name="migrate-ssis-catalog-ssisdb"></a>SSIS-katalógus migrálása (SSISDB)

A SSISDB áttelepítését a DMS használatával végezheti el a következő cikkben leírtak szerint: [SSIS-csomagok áttelepítése SQL felügyelt példányra](../dms/how-to-migrate-ssis-packages-managed-instance.md).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SSIS feladatok SQL felügyelt példány-ügynökhöz

A felügyelt SQL-példányok natív, első osztályú ütemező szolgáltatással rendelkeznek, akárcsak SQL Server Agent a helyszínen.  A [SSIS-csomagokat az Azure SQL felügyelt példány-ügynök használatával futtathatja](how-to-invoke-ssis-package-managed-instance-agent.md).

Mivel a SSIS-feladatokhoz tartozó áttelepítési eszköz még nem érhető el, a SQL Server Agent a helyszínen át kell telepíteni az SQL felügyelt példány ügynökéhez parancsfájlok/manuális másolás útján.

## <a name="additional-resources"></a>További források

- [Azure Data Factory](./introduction.md)
- [Azure-SSIS integrációs modul](./create-azure-ssis-integration-runtime.md)
- [Azure Database Migration Service](../dms/dms-overview.md)
- [Hálózati topológiák az SQL felügyelt példányainak a DMS használatával történő áttelepítéséhez](../dms/resource-network-topologies.md)
- [SSIS-csomagok migrálása egy felügyelt SQL-példányra](../dms/how-to-migrate-ssis-packages-managed-instance.md)

## <a name="next-steps"></a>Következő lépések

- [Kapcsolódás a SSISDB az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Az Azure-ban üzembe helyezett SSIS-csomagok futtatása](/sql/integration-services/lift-shift/ssis-azure-run-packages)