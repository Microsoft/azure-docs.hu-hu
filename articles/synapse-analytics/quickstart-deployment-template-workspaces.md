---
title: 'Gyors útmutató: Azure szinapszis-munkaterület létrehozása Azure Resource Manager sablon alapján'
description: Megtudhatja, hogyan hozhat létre egy szinapszis-munkaterületet Azure Resource Manager sablon (ARM-sablon) használatával.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 08/07/2020
ms.openlocfilehash: 2b05220081dde4db6f07f21fb9978d9e466235ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92740374"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-an-arm-template"></a>Rövid útmutató: Azure szinapszis-munkaterület létrehozása ARM-sablonnal

Ez a Azure Resource Manager sablon (ARM-sablon) egy Azure-beli szinapszis-munkaterületet hoz létre, amely mögöttes Data Lake Storage. Az Azure szinapszis munkaterület biztonságos együttműködési határ az Azure szinapszis Analytics elemzési folyamataihoz.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure 1-ben](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

A sablon áttekintéséhez válassza a **Megjelenítés** hivatkozást. Ezután válassza a **Sablon szerkesztése** lehetőséget.

[![Vizualizáció](../media/template-deployments/template-visualize-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

A sablon két erőforrást határoz meg:

- A(z)
- Munkaterület

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza ki az alábbi rendszerképet az Azure-ba való bejelentkezéshez és a sablon megnyitásához. Ez a sablon egy szinapszis-munkaterületet hoz létre.

   [![Üzembe helyezés az Azure 2-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. Adja meg vagy frissítse a következő értékeket:

   - **Előfizetés**: válasszon ki egy Azure-előfizetést.
   - **Erőforráscsoport**: válassza az **új létrehozása** elemet, és adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK gombra**. Egy új erőforráscsoport segíti az erőforrás karbantartását.
   - **Régió**: válasszon régiót.  Például: **USA középső régiója**.
   - **Név**: adjon meg egy nevet a munkaterületnek.
   - **SQL-rendszergazdai bejelentkezés**: adja meg a SQL Server rendszergazdai felhasználónevét.
   - **SQL-rendszergazda jelszava**: adja meg a SQL Server rendszergazdai jelszavát.
   - **Címke értékei**: fogadja el az alapértelmezett értéket.
   - **Áttekintés és létrehozás**: válassza a lehetőséget.
   - **Létrehozás**: válassza a lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure szinapszis Analyticsről és a Azure Resource Managerról, folytassa az alábbi cikkekkel.

- Az [Azure szinapszis Analytics áttekintése](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)
- További információ az [Azure Resource Managerről](../azure-resource-manager/management/overview.md)
- [Az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
