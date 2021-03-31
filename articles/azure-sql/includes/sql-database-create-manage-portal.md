---
author: stevestein
ms.service: sql-database
ms.subservice: operations
ms.topic: include
ms.date: 08/23/2019
ms.author: sstein
ms.openlocfilehash: 3cc8e836d39a38f950e15d31aeb3e3acdca45acc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84045493"
---
## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Azure SQL-erőforrások létrehozása és kezelése a Azure Portal

A Azure Portal egyetlen oldalt biztosít, ahol kezelheti az [összes Azure SQL-erőforrást,](https://go.microsoft.com/fwlink/?linkid=2100641) beleértve az SQL-alapú virtuális gépeket is.

Az **Azure SQL** -oldal eléréséhez a Azure Portal menüben válassza az **Azure SQL** lehetőséget, vagy keresse meg és válassza ki az **Azure SQL** elemet bármely oldalon.

> [!NOTE]
> Az **Azure SQL** gyors és egyszerű módszert kínál a Azure Portal összes SQL-erőforrásának elérésére, beleértve az önálló és a készletezett adatbázist is Azure SQL Database, valamint az azokat üzemeltető logikai SQL Servert, SQL felügyelt példányokat és SQL virtuális gépeket.  Az [Azure SQL](../azure-sql-iaas-vs-paas-what-is-overview.md) nem szolgáltatás vagy erőforrás, hanem az SQL-hez kapcsolódó szolgáltatások családja. 

A meglévő erőforrások kezeléséhez válassza ki a kívánt elemet a listában. Új Azure SQL-erőforrások létrehozásához válassza a **+ Hozzáadás** lehetőséget. 

![Azure SQL-portál lap](./media/sql-database-create-manage-portal/add-azure-sql-resources.png)

A **+ Hozzáadás gombra** kattintva további információkat jeleníthet meg a különböző beállításokról a csempék **részleteinek megjelenítése** lehetőség kiválasztásával.

![adatbázisok csempe részletei](./media/sql-database-create-manage-portal/single-sql-database-deployment-options.png)

Részletes információ:

- [Önálló adatbázis létrehozása](../database/single-database-create-quickstart.md)
- [Rugalmas készlet létrehozása](../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Felügyelt példány létrehozása](../managed-instance/instance-create-quickstart.md)
- [SQL-alapú virtuális gép létrehozása](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)