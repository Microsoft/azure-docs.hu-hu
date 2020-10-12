---
title: SSIS Migrálás az Azure SQL felügyelt példányával az adatbázis-munkaterhelés céljaként
description: SSIS Migrálás az Azure SQL felügyelt példányával az adatbázis-munkaterhelés céljaként.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 6de08faee78deeb86117084b420eb5043153f62d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88186046"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>SSIS Migrálás az Azure SQL felügyelt példányával az adatbázis-munkaterhelés céljaként

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Az adatbázis-munkaterhelések SQL Server példányról az Azure SQL felügyelt példányra történő áttelepítésekor ismernie kell az [Azure adatáttelepítési szolgáltatást](https://docs.microsoft.com/azure/dms/dms-overview)(DMS), valamint az [SQL felügyelt példányok áttelepítésének hálózati topológiáit a DMS használatával](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Ez a cikk a SSIS-katalógusban (SSISDB) tárolt SQL Server Integration Service (SSIS) csomagok áttelepítését, valamint a SSIS-csomagok végrehajtását ütemezhető SQL Server Agent feladatokat ismerteti.

## <a name="migrate-ssis-catalog-ssisdb"></a>SSIS-katalógus migrálása (SSISDB)

A SSISDB áttelepítését a DMS használatával végezheti el a következő cikkben leírtak szerint: [SSIS-csomagok áttelepítése SQL felügyelt példányra](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SSIS feladatok SQL felügyelt példány-ügynökhöz

A felügyelt SQL-példányok natív, első osztályú ütemező szolgáltatással rendelkeznek, akárcsak SQL Server Agent a helyszínen.  A [SSIS-csomagokat az Azure SQL felügyelt példány-ügynök használatával futtathatja](how-to-invoke-ssis-package-managed-instance-agent.md).

Mivel a SSIS-feladatokhoz tartozó áttelepítési eszköz még nem érhető el, a SQL Server Agent a helyszínen át kell telepíteni az SQL felügyelt példány ügynökéhez parancsfájlok/manuális másolás útján.

## <a name="additional-resources"></a>További források

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS integrációs modul](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Hálózati topológiák az SQL felügyelt példányainak a DMS használatával történő áttelepítéséhez](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [SSIS-csomagok migrálása egy felügyelt SQL-példányra](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Következő lépések

- [Kapcsolódás a SSISDB az Azure-ban](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Az Azure-ban üzembe helyezett SSIS-csomagok futtatása](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
