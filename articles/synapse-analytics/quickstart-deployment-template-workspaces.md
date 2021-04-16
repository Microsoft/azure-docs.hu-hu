---
title: 'Rövid útmutató: Sablon Azure Synapse munkaterület Azure Resource Manager létrehozása'
description: Megtudhatja, hogyan hozhat létre Synapse-munkaterületet egy Azure Resource Manager (ARM-sablon) használatával.
services: azure-resource-manager
author: julieMSFT
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 08/07/2020
ms.openlocfilehash: 7317b7f51c6d0f9d72e3aad81794a569276d2145
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566120"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-an-arm-template"></a>Rövid útmutató: Azure Synapse létrehozása ARM-sablonnal

Ez Azure Resource Manager sablon (ARM-sablon) létrehoz egy Azure Synapse-munkaterületet a mögöttes Data Lake Storage. A Azure Synapse munkaterület biztonságos együttműködési határ az elemzési folyamatok számára a Azure Synapse Analytics.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure 1-ben](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

A sablont a Vizualizáció hivatkozásra **kattintva tudja** áttekinteni. Ezután válassza **a Sablon szerkesztése lehetőséget.**

[![Vizualizáció](../media/template-deployments/template-visualize-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

A sablon két erőforrást határoz meg:

- Tárfiók
- Munkaterület

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Az alábbi kép kiválasztásával jelentkezzen be az Azure-ba, és nyissa meg a sablont. Ez a sablon egy Synapse-munkaterületet hoz létre.

   [![Üzembe helyezés az Azure 2-ben](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. Adja meg vagy frissítse a következő értékeket:

   - **Előfizetés:** Válasszon ki egy Azure-előfizetést.
   - **Erőforráscsoport:** Válassza az **Új létrehozása lehetőséget,** adjon meg egy egyedi nevet az erőforráscsoportnak, majd kattintson az **OK gombra.** Egy új erőforráscsoport megkönnyíti az erőforrások tisztítását.
   - **Régió:** Válasszon ki egy régiót.  Például: **USA középső régiója**.
   - **Név:** Adja meg a munkaterület nevét.
   - **SQL-rendszergazdai bejelentkezés:** Adja meg a rendszergazda felhasználónevét a SQL Server.
   - **SQL-rendszergazda jelszava:** Adja meg a rendszergazdai jelszót a SQL Server.
   - **Címkeértékek:** Fogadja el az alapértelmezett értéket.
   - **Áttekintés és létrehozás:** Válassza ki.
   - **Létrehozás:** Válassza a lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Synapse Analytics és Azure Resource Manager, folytassa az alábbi cikkekkel.

- Olvassa el [a Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)
- További információ az [Azure Resource Managerről](../azure-resource-manager/management/overview.md)
- [Az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
