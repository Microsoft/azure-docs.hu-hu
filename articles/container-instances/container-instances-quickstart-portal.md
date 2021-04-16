---
title: Rövid útmutató – Docker-tároló üzembe helyezése tárolópéldányon – Portal
description: Ebben a rövid útmutatóban a Azure Portal egy elkülönített Azure-tárolópéldányon futó tárolóba helyezett webalkalmazás gyors üzembe helyezéséhez
ms.date: 08/24/2020
ms.topic: quickstart
ms.custom:
- mvc
- devx-track-js
- mode-portal
ms.openlocfilehash: c0189bbd04e454205b34a6415ab6109f95b6f51a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536232"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Rövid útmutató: Tárolópéldány üzembe helyezése az Azure-ban a Azure Portal

A Azure Container Instances egyszerűséggel és sebességgel futtathat kiszolgáló nélküli Docker-tárolókat az Azure-ban. Igény szerint helyezhet üzembe egy alkalmazást egy tárolópéldányon, ha nincs szüksége egy teljes körű tárolóvezénylési platformra, például a Azure Kubernetes Service.

Ebben a rövid útmutatóban a Azure Portal üzembe helyez egy elkülönített Docker-tárolót, és elérhetővé teszi az alkalmazást egy teljes tartománynévvel (FQDN). Néhány beállítás konfigurálása és a tároló üzembe helyezése után tallózhat a futó alkalmazásban:

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben":::

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot][azure-free-account].

## <a name="create-a-container-instance"></a>Tárolópéldány létrehozása

Válassza a **Create a resource**  >  **Containers (Erőforrástárolók**  >  **létrehozása) Container Instances.**

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-01.png" alt-text="Új tárolópéldány létrehozásának megkezdése az Azure Portalon":::

Az Alapvető **beállítások lapon** adja meg a következő értékeket az Erőforráscsoport, a Tároló **neve** és a **Tároló rendszerkép** szövegmezőiben.  A többi értéket hagyja az alapértelmezett értéken, majd válassza az **OK** lehetőséget.

* Erőforráscsoport: **Új létrehozása** > `myresourcegroup`
* Tároló neve: `mycontainer`
* Rendszerkép forrása: **Gyorsindítási képek**
* Tároló rendszerképe: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-03.png" alt-text="Új tárolópéldány alapbeállításainak konfigurálása az Azure Portalon":::

Ebben a rövid útmutatóban az alapértelmezett beállítások használatával telepíti a nyilvános `aci-helloworld` Microsoft-rendszerképet. Ez a Linux-rendszerképminta egy statikus HTML-oldalt Node.js webalkalmazást tartalmaz. Saját tároló rendszerképeket is tárolhat a Azure Container Registry, Docker Hub tárolóregisztrálókban.

A Hálózat **lapon** adjon meg egy **DNS-névcímkét** a tárolóhoz. A névnek egyedinek kell lennie abban az Azure-régióban, ahol a tárolópéldányt létrehozza. A tároló nyilvánosan elérhető az alábbi helyen: `<dns-name-label>.<region>.azurecontainer.io`. Ha „DNS-névcímke nem érhető el” hibaüzenetet kap, próbálkozzon másik DNS-névcímkével.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-04.png" alt-text="Új tárolópéldány hálózati beállításainak konfigurálása a Azure Portal":::

Hagyja meg a többi beállítás alapértelmezett beállítását, majd válassza az **Áttekintés + létrehozás lehetőséget.**

Miután az érvényesítés befejeződött, egy összefoglalás jelenik meg a tároló beállításairól. Válassza **a Létrehozás lehetőséget** a tároló üzembehelyre helyezési kérelmének elküldéhez.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-05.png" alt-text="Új tárolópéldány beállításainak összefoglalása az Azure Portalon":::

Az üzembe helyezés indításakor megjelenik egy értesítés, amely jelzi, hogy az üzembe helyezés folyamatban van. Amikor a tárolócsoport üzembe helyezése megtörtént, egy újabb értesítés jelenik meg.

Nyissa meg a tárolócsoport áttekintését a  >  **myresourcegroup**  >  **mycontainer** erőforráscsoport megnyitásával. Jegyezze fel a tárolópéldány teljes tartománynevét (**FQDN**) és **Állapotát**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-06.png" alt-text="Tárolócsoport áttekintése az Azure Portalon":::

Ha az **Állapot***Fut* értékre vált, navigáljon a tároló teljes tartománynevére a böngészőjében.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben":::

Gratulálunk! Csupán néhány beállítás konfigurálásával üzembe helyezett egy nyilvánosan hozzáférhető alkalmazást az Azure Container Instances szolgáltatásban.

## <a name="view-container-logs"></a>Tárolónaplók megtekintése

A tárolópéldányok naplóinak megtekintése hasznos lehet a tárolóval vagy az azon futtatott alkalmazással kapcsolatos hibák elhárítása során.

A tároló naplóinak megtekintéséhez a Beállítások alatt válassza a **Tárolók,** majd a **Naplók lehetőséget.**  Látnia kell a HTTP GET kérést is, amely akkor jött létre, amikor megtekintette az alkalmazást a böngészőjében.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-11.png" alt-text="Tárolónaplók az Azure Portalon":::


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a tárolóval, válassza ki az **Áttekintés** lehetőséget a *mycontainer* tárolópéldányhoz, majd válassza a **Törlés** lehetőséget.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-09.png" alt-text="A tárolópéldány törlése a Azure Portal]":::

Válassza az **Igen** lehetőséget, amikor a megerősítési párbeszédpanel megjelenik.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-10.png" alt-text="Tárolópéldány megerősítésének törlése a Azure Portal]":::

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Azure-tárolópéldányt egy nyilvános Microsoft-rendszerképből. Ha szeretne létrehozni és üzembe helyezni egy tárolórendszerképet egy privát Azure-tárolóregisztrációs adatbázisból, lépjen tovább az Azure Container Instances oktatóanyagára.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/
