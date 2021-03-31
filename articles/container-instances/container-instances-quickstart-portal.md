---
title: Rövid útmutató – Docker-tároló üzembe helyezése a Container instance-portálon
description: Ebben a rövid útmutatóban a Azure Portal használatával gyorsan üzembe helyezhet egy elkülönített Azure Container-példányon futó tároló-webalkalmazást
ms.topic: quickstart
ms.date: 08/24/2020
ms.custom: seodec18, mvc, devx-track-js
ms.openlocfilehash: c8477bd91c3a02a2cd02d341c38c16da251902ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004806"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Gyors útmutató: tároló-példány üzembe helyezése az Azure-ban a Azure Portal használatával

A Azure Container Instances használatával a kiszolgáló nélküli Docker-tárolókat az Azure-ban, az egyszerűség és a gyorsaság segítségével futtathatja. Igény szerint üzembe helyezhet egy alkalmazást egy tároló-példányon, ha nincs szüksége a teljes Container-előkészítési platformra, például az Azure Kubernetes szolgáltatásra.

Ebben a rövid útmutatóban egy elkülönített Docker-tároló üzembe helyezéséhez, valamint az alkalmazás teljes tartománynévvel (FQDN) való elérhetővé tételéhez használja a Azure Portal. Néhány beállítás konfigurálása és a tároló üzembe helyezése után tallózhat a futó alkalmazásban:

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben":::

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot][azure-free-account].

## <a name="create-a-container-instance"></a>Tárolópéldány létrehozása

Válassza az **erőforrás létrehozása**  >  **tárolók**  >  **Container instances** elemet.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-01.png" alt-text="Új tárolópéldány létrehozásának megkezdése az Azure Portalon":::

Az **alapvető beállítások** lapon adja meg a következő értékeket az **erőforráscsoport**, a **tároló neve** és a **tároló képe** szövegmezőben. A többi értéket hagyja az alapértelmezett értéken, majd válassza az **OK** lehetőséget.

* Erőforráscsoport: **új létrehozása** > `myresourcegroup`
* Tároló neve: `mycontainer`
* Kép forrása: rövid útmutató **rendszerképek**
* Tároló képe: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-03.png" alt-text="Új tárolópéldány alapbeállításainak konfigurálása az Azure Portalon":::

Ebben a rövid útmutatóban az alapértelmezett beállítások használatával telepítheti a nyilvános Microsoft- `aci-helloworld` rendszerképet. Ez a minta linuxos rendszerkép egy olyan kisméretű webalkalmazást csomagol, amely egy statikus HTML-oldalt kiszolgáló Node.js. Saját tároló-lemezképeket is használhat Azure Container Registry, Docker hub vagy más beállításjegyzékben.

A **hálózatkezelés** lapon adja meg a tároló **DNS-neve címkéjét** . A névnek egyedinek kell lennie azon az Azure-régión belül, ahol létrehozza a Container-példányt. A tároló nyilvánosan elérhető az alábbi helyen: `<dns-name-label>.<region>.azurecontainer.io`. Ha „DNS-névcímke nem érhető el” hibaüzenetet kap, próbálkozzon másik DNS-névcímkével.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-04.png" alt-text="Új Container-példány hálózati beállításainak konfigurálása a Azure Portal":::

Hagyja meg a többi beállítást az alapértelmezett értékeken, majd válassza a **felülvizsgálat + létrehozás** elemet.

Miután az érvényesítés befejeződött, egy összefoglalás jelenik meg a tároló beállításairól. Válassza a **Létrehozás** lehetőséget a tároló üzembe helyezési kérelmének elküldéséhez.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-05.png" alt-text="Új tárolópéldány beállításainak összefoglalása az Azure Portalon":::

Az üzembe helyezés indításakor megjelenik egy értesítés, amely jelzi, hogy a központi telepítés folyamatban van. Amikor a tárolócsoport üzembe helyezése megtörtént, egy újabb értesítés jelenik meg.

Nyissa meg a tároló csoport áttekintését, ehhez navigáljon az **erőforráscsoportok**  >  **myresourcegroup**  >  **mycontainer**. Jegyezze fel a tárolópéldány teljes tartománynevét (**FQDN**) és **Állapotát**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-06.png" alt-text="Tárolócsoport áttekintése az Azure Portalon":::

Ha az **Állapot***Fut* értékre vált, navigáljon a tároló teljes tartománynevére a böngészőjében.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben":::

Gratulálunk! Csupán néhány beállítás konfigurálásával üzembe helyezett egy nyilvánosan hozzáférhető alkalmazást az Azure Container Instances szolgáltatásban.

## <a name="view-container-logs"></a>Tárolónaplók megtekintése

A tárolópéldányok naplóinak megtekintése hasznos lehet a tárolóval vagy az azon futtatott alkalmazással kapcsolatos hibák elhárítása során.

A tároló naplóinak megtekintéséhez a **Beállítások** területen válassza a **tárolók**, majd a **naplók** lehetőséget. Látnia kell a HTTP GET kérést is, amely akkor jött létre, amikor megtekintette az alkalmazást a böngészőjében.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-11.png" alt-text="Tárolónaplók az Azure Portalon":::


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a tárolóval, válassza ki az **Áttekintés** lehetőséget a *mycontainer* tárolópéldányhoz, majd válassza a **Törlés** lehetőséget.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-09.png" alt-text="A Container példány törlése a Azure Portal]":::

Válassza az **Igen** lehetőséget, amikor a megerősítési párbeszédpanel megjelenik.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-10.png" alt-text="Tároló példány jóváhagyásának törlése a Azure Portal]":::

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container-példányt egy nyilvános Microsoft-rendszerképből. Ha szeretne létrehozni és üzembe helyezni egy tárolórendszerképet egy privát Azure-tárolóregisztrációs adatbázisból, lépjen tovább az Azure Container Instances oktatóanyagára.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/