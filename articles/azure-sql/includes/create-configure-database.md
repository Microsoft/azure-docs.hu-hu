---
author: jovanpop-msft
ms.service: sql-database
ms.subservice: operations
ms.topic: include
ms.date: 12/17/2020
ms.author: jovanpop
ms.openlocfilehash: d1422d029bc2c5d41330e4ac2d21ff14e4fa82ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97703966"
---
  Az alábbi rövid útmutatók segítségével hozhat létre és konfigurálhat egy adatbázist:

  | Művelet | SQL Database | Felügyelt SQL-példány | Azure virtuális gépen futó SQL Server | Azure Synapse Analytics |
  |:--- |:--- |:---|:---|:---|
  | Létrehozás| [Portál](../database/single-database-create-quickstart.md) | [Portál](../managed-instance/instance-create-quickstart.md) | [Portál](../virtual-machines/windows/sql-vm-create-portal-quickstart.md) | [Portál](../../synapse-analytics/quickstart-create-workspace.md) |
  || [Parancssori felület](../database/scripts/create-and-configure-database-cli.md) | | | [Parancssori felület](../../synapse-analytics/quickstart-create-workspace-cli.md) |
  || [PowerShell](../database/scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md) | [PowerShell](../../synapse-analytics/quickstart-create-workspace-powershell.md) |
  || | | [Üzembehelyezési sablon](../virtual-machines/windows/create-sql-vm-resource-manager-template.md) | [Üzembehelyezési sablon](../../synapse-analytics/quickstart-deployment-template-workspaces.md) | 
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](../database/firewall-create-server-level-portal-quickstart.md)| [Kapcsolódás virtuális gépről](../managed-instance/connect-vm-instance-configure.md)| |
  |||[Helyszíni kapcsolat](../managed-instance/point-to-site-p2s-configure.md) | [Kapcsolódás SQL Server-példányhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md) |
  | Kapcsolatadatok lekérése | [Azure SQL](../database/connect-query-content-reference-guide.md#get-server-connection-information)|[Azure SQL](../database/connect-query-content-reference-guide.md#get-server-connection-information)| [SQL-ALAPÚ VIRTUÁLIS GÉP](../virtual-machines/windows/sql-vm-create-portal-quickstart.md?#connect-to-sql-server)| [Synapse SQL](../../synapse-analytics/sql/connect-overview.md#find-your-server-name)|
  |||||
