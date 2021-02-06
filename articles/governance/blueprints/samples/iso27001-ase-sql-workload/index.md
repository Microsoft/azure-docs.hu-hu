---
title: Az ISO 27001 ASE/SQL-számításifeladat tervmintája – Áttekintés
description: Az ISO 27001 App Service Environment/SQL Database-számításifeladat tervmintájának áttekintése és architektúrája.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 61e62c9a9099b0c84a98f1840743f05469e46b62
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627516"
---
# <a name="overview-of-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Az ISO 27001 App Service Environment/SQL Database-számításifeladat tervmintájának áttekintése

Az ISO 27001 Azure Blueprints App Service Environment/SQL Database-számításifeladat tervmintája kiegészítő infrastruktúrát biztosít az [ISO 27001 Megosztott szolgáltatások](../iso27001-shared/index.md) tervmintához.
E terv segítségével az ügyfelek olyan felhőalapú architektúrákat helyezhetnek üzembe, amelyek akkreditációs vagy megfelelőségi követelményekkel rendelkező alkalmazási helyzetekre kínálnak megoldást.

Két ISO 27001 tervminta áll rendelkezésre: ez a minta és az [ISO 27001 Megosztott szolgáltatások](../iso27001-shared/index.md) tervminta.

> [!IMPORTANT]
> A jelen minta az [ISO 27001 Megosztott szolgáltatások](../iso27001-shared/index.md) tervmintával üzembe helyezett infrastruktúrával van függőségi viszonyban. Először ezt a mintát kell üzembe helyezni.

## <a name="architecture"></a>Architektúra

Az ISO 27001 App Service Environment/SQL Database-számításifeladat tervmintája szolgáltatásalapú webes környezetként helyezi üzembe a platformot. Ez a környezet több, ISO 27001 szabványoknak megfelelő webalkalmazás, webes API és SQL Database-példány üzemeltetésére alkalmas. Ez a tervminta az [ISO 27001 Megosztott szolgáltatások](../iso27001-shared/index.md) tervmintával van függőségi viszonyban.

:::image type="content" source="../../media/sample-iso27001-ase-sql-workload/iso27001-ase-sql-workload-blueprint-sample-design.png" alt-text="Az ISO 27001 ASE/SQL-számításifeladat tervmintájának kialakítása" border="false":::

Ez a környezet több Azure-szolgáltatásból épül fel, és ISO 27001 szabványokon alapuló, biztonságos, teljes körűen monitorozott, vállalati használatra kész számításifeladat-infrastruktúrát biztosít. A környezet összetevői:

- A DevOps nevű [Azure-szerepkör](../../../../role-based-access-control/overview.md), amely jogosultsággal rendelkezik az erőforrások üzembe helyezéséhez és kezeléséhez a tervmintával üzembe helyezett [Azure App Service Environment-környezetekben](../../../../app-service/environment/intro.md)
- [Azure Policy](../../../policy/overview.md)-definíciók, amelyekkel rögzíthető, hogy mely szolgáltatások helyezhetők üzembe a környezetben, illetve megtagadható a nyilvános IP-cím- (PIP-) erőforrások létrehozása
- Egyetlen alhálózatot tartalmazó virtuális hálózat, amely egy már meglévő [megosztott szolgáltatási](../iso27001-shared/index.md)környezethez van társítva, és arra kényszeríti a teljes adatforgalmat, hogy a [megosztott szolgáltatások](../iso27001-shared/index.md) tűzfalán haladjon át. A virtuális hálózaton a következő erőforrások találhatók:
  - Egy vagy több webalkalmazás, webes API vagy funkció üzemeltetésére használható [Azure App Service Environment-környezet](../../../../app-service/environment/intro.md)
  - Az [Azure Key Vault](../../../../key-vault/general/overview.md) VNet-szolgáltatásvégpontot használó példánya, amely a számítási feladat környezetében futó alkalmazások titkos kulcsainak tárolására szolgál
  - Az [Azure SQL Database](../../../../azure-sql/database/sql-database-paas-overview.md) VNet-szolgáltatásvégpontot használó kiszolgálópéldánya, amely a számítási feladat környezetében futó alkalmazásokhoz használt adatbázisok üzemeltetésére szolgál

## <a name="next-steps"></a>További lépések

Ezzel megismerte az ISO 27001 App Service Environment/SQL Database-számításifeladat tervmintájának áttekintését és architektúráját. Következő lépésként tekintse meg az alábbi cikkeket, amelyek a vezérlőelem-leképezést és a minta üzembe helyezését ismertetik:

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL Database-számításifeladat terve – Vezérlőelem-leképezés](./control-mapping.md)
> [ISO 27001 App Service Environment/SQL Database-számításifeladat terve – Üzembehelyezési lépések](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
