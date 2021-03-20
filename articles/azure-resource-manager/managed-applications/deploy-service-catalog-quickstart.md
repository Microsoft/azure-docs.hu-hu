---
title: A Azure Portal használata a Service Catalog alkalmazás üzembe helyezéséhez
description: Megjeleníti a felügyelt alkalmazások felhasználóit, hogyan helyezhet üzembe egy Service Catalog-alkalmazást a Azure Portalon keresztül.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: ce58fc69496f54c078b0a0a55a8a3c7cad82a051
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "81391713"
---
# <a name="quickstart-deploy-service-catalog-app-through-azure-portal"></a>Gyors útmutató: Service Catalog-alkalmazás üzembe helyezése Azure Portal

Az [előző](publish-service-catalog-app.md)rövid útmutatóban közzétett egy felügyelt alkalmazás definícióját. Ebben a rövid útmutatóban létrehoz egy Service Catalog-alkalmazást az adott definícióból.

## <a name="create-service-catalog-app"></a>Service Catalog-alkalmazás létrehozása

A Azure Portal hajtsa végre a következő lépéseket:

1. Válassza az **Erőforrás létrehozása** lehetőséget.

   ![Erőforrás létrehozása](./media/deploy-service-catalog-quickstart/create-new.png)

1. Keressen rá a **Service Catalog által felügyelt alkalmazásra** , és válassza ki az elérhető lehetőségek közül.

   ![Service Catalog-alkalmazás keresése](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Ekkor megjelenik a felügyelt alkalmazás szolgáltatás leírása. Válassza a **Létrehozás** lehetőséget.

   ![Kattintás a Létrehozás gombra](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. A portálon a felügyelt alkalmazás-definíciók láthatók, amelyekhez hozzáférése van. Az elérhető definíciók közül válassza ki azt, amelyet telepíteni kíván. Ebben a rövid útmutatóban az előző rövid útmutatóban létrehozott **felügyelt Storage-fiók** definícióját használja. Válassza a **Létrehozás** lehetőséget.

   ![Telepítendő definíció kiválasztása](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Adja meg az **alapok** lap értékeit. Válassza ki azt az Azure-előfizetést, amelybe telepíteni kívánja a Service Catalog alkalmazást. Hozzon létre egy **alkalmazáscsoport** nevű új erőforráscsoportot. Válassza ki az alkalmazás helyét. Ha elkészült, kattintson **az OK gombra**.

   ![Alapszintű értékek megadása](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Adja meg a Storage-fiók nevének előtagját. Válassza ki a létrehozandó Storage-fiók típusát. Ha elkészült, kattintson **az OK gombra**.

   ![Értékek megadása a tároláshoz](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Tekintse át az összegzést. Az érvényesítés sikeres befejezését követően kattintson **az OK gombra** az üzembe helyezés megkezdéséhez.

   ![Összefoglalás megtekintése](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Eredmények megtekintése

A Service Catalog alkalmazás üzembe helyezését követően két új erőforráscsoport van. Egy erőforráscsoport tartalmazza a Service Catalog alkalmazást. A másik erőforráscsoport tárolja a Service Catalog alkalmazás erőforrásait.

1. Tekintse meg a **alkalmazáscsoport** nevű erőforráscsoportot a Service Catalog alkalmazás megtekintéséhez.

   ![Alkalmazás megtekintése](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Tekintse meg a **alkalmazáscsoport {hash-characters}** nevű erőforráscsoportot a Service Catalog alkalmazás erőforrásainak megtekintéséhez.

   ![Erőforrások megtekintése](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Következő lépések

* A felügyelt alkalmazások definíciós fájljainak létrehozásával kapcsolatos további információkért lásd: [felügyelt alkalmazás definíciójának létrehozása és közzététele](publish-service-catalog-app.md).
* Az Azure CLI esetében lásd: [a Service Catalog alkalmazás üzembe helyezése az Azure CLI-vel](./scripts/managed-application-cli-sample-create-application.md).
* A PowerShell esetében lásd: [a Service Catalog alkalmazás üzembe helyezése a PowerShell](./scripts/managed-application-poweshell-sample-create-application.md)-lel.
