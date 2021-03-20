---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: e208b52c67f173bd0d289715b63562df656b1ec9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88009768"
---
1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

1. A **Közzététel** párbeszédpanelen válassza az **Azure** a **cél** számára lehetőséget, majd kattintson a **tovább** gombra. 

1. Válassza ki az **adott cél** **Azure WebJobs** , majd válassza a **tovább** lehetőséget.

1. Válassza **az új Azure-Webjobs létrehozása** lehetőséget.

   ![Közzététel célhelyének kiválasztása](./media/webjobs-publish-netcore/pick-publish-target.png)

1. A **app Service (Windows)** párbeszédpanelen használja az alábbi táblázat üzemeltetési beállításait.

    | Beállítás      | Ajánlott érték  | Leírás                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Név** | Globálisan egyedi név | Az új függvényalkalmazást azonosító egyedi név. |
    | **Előfizetés** | Válassza ki az előfizetését | A használandó előfizetés. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Az erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. Válassza az **Új** elemet egy új erőforráscsoport létrehozásához.|
    | **[Szolgáltatási csomag](../articles/app-service/overview-hosting-plans.md)** | App Service-csomag | Az alkalmazást tároló webkiszolgálófarm helyét, méretét és funkcióit egy [App Service-csomag](../articles/app-service/overview-hosting-plans.md) határozza meg. Költségeket takaríthat meg, ha beállítja, hogy a webalkalmazások egyetlen közös App Service-csomag használatával több alkalmazást tároljanak. App Service csomagok határozzák meg a régiót, a példány méretét, a méretezési darabszámot és az SKU-t (ingyenes, közös, alapszintű, standard vagy prémium). Új App Service terv létrehozásához válassza az **új** lehetőséget. |

    ![A Create App Service (App Service létrehozása) párbeszédpanel](./media/webjobs-publish-netcore/app-service-dialog.png)

1. Válassza a **Létrehozás** lehetőséget, ha webjobs és kapcsolódó erőforrásokat szeretne létrehozni az Azure-ban ezekkel a beállításokkal és a projekt kódjának üzembe helyezésével.