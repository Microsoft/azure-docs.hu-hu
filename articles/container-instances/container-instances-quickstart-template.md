---
title: Rövid útmutató – Tárolópéldány létrehozása – Azure Resource Manager sablon
description: Ebben a rövid útmutatóban egy Azure Resource Manager egy elkülönített Azure-tárolópéldányban futó tárolóba helyezett webalkalmazás gyors üzembe helyezéséhez.
services: azure-resource-manager
ms.date: 04/30/2020
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom:
- subject-armqs
- devx-track-js
- mode-arm
ms.openlocfilehash: 2f57e86421f7522467a3f4adf4a4e9a21456ba4c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878739"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-an-arm-template"></a>Rövid útmutató: Tárolópéldány üzembe helyezése az Azure-ban ARM-sablon használatával

A Azure Container Instances egyszerűséggel és sebességgel futtathat kiszolgáló nélküli Docker-tárolókat az Azure-ban. Igény szerint helyezhet üzembe egy alkalmazást egy tárolópéldányon, ha nincs szüksége egy teljes körű tárolóvezénylési platformra, például a Azure Kubernetes Service. Ebben a rövid útmutatóban egy Azure Resource Manager sablont (ARM-sablont) fog használni egy elkülönített Docker-tároló üzembe helyezéséhez, és a webalkalmazás nyilvános IP-címmel való elérhetővé helyezéséhez.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aci-linuxcontainer-public-ip%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-aci-linuxcontainer-public-ip/) közül származik.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.containerinstance/aci-linuxcontainer-public-ip/azuredeploy.json":::

A sablon a következő erőforrást definiálja:

* **[Microsoft.ContainerInstance/containerGroups:](/azure/templates/microsoft.containerinstance/containergroups)** hozzon létre egy Azure-tárolócsoportot. Ez a sablon egy egyetlen tárolópéldányból álló csoportot definiál.

További Azure Container Instances sablonmintákat a gyorsindítási [sablongyűjteményben talál.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerinstance&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

 1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy beállításjegyzéket és egy replikát egy másik helyen.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aci-linuxcontainer-public-ip%2Fazuredeploy.json)

 2. Válassza ki vagy adja meg a következő értékeket.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport:** válassza az **Új létrehozása** lehetőséget, adjon egyedi nevet az erőforráscsoportnak, majd kattintson az **OK gombra.**
    * **Hely**: válasszon egy helyet az erőforráscsoportnak. Például: **USA középső középső országa.**
    * **Név:** fogadja el a példány létrehozott nevét, vagy adjon meg egy nevet.
    * **Kép:** fogadja el az alapértelmezett rendszerképnevet. Ez a Linux-rendszerképminta egy statikus HTML-oldalt Node.js webalkalmazást tartalmaz. 

    Fogadja el az alapértelmezett értékeket a többi tulajdonsághoz.

    Tekintse át a használati feltételeket. Ha elfogadja, válassza az Elfogadom a fenti feltételeket és **feltételeket** lehetőséget.

    ![Sablontulajdonságok](media/container-instances-quickstart-template/template-properties.png)

 3. A példány sikeres létrehozása után értesítést kap:

    ![Portálértesítés](media/container-instances-quickstart-template/deployment-notification.png)

 Az Azure Portalon helyezhető üzembe a sablon. A Azure Portal mellett használhatja a Azure PowerShell, az Azure CLI és a REST API. További információ az egyéb üzembe helyezési módszerekről: [Sablonok üzembe helyezése.](../azure-resource-manager/templates/deploy-cli.md)

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A tárolópéldány Azure Portal vagy egy olyan eszközzel, mint az [Azure CLI,](container-instances-quickstart.md) tekintse át a tárolópéldány tulajdonságait.

1. A portálon keressen rá a Container Instances, és válassza ki a létrehozott tárolópéldányt.

1. Az Áttekintés **lapon** jegyezze fel a **példány állapotát** és **IP-címét.**

    ![Példány áttekintése](media/container-instances-quickstart-template/aci-overview.png)

2. Ha az állapota *Fut,* keresse meg az IP-címet a böngészőben. 

    ![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben](media/container-instances-quickstart-template/view-application-running-in-an-azure-container-instance.png)

### <a name="view-container-logs"></a>Tárolónaplók megtekintése

A tárolópéldányok naplóinak megtekintése hasznos lehet a tárolóval vagy az azon futtatott alkalmazással kapcsolatos hibák elhárítása során.

A tároló naplóinak megtekintéséhez a Beállítások **alatt válassza** a **Tárolónaplók**  >  **lehetőséget.** Látnia kell a HTTP GET kérést is, amely akkor jött létre, amikor megtekintette az alkalmazást a böngészőjében.

![Tárolónaplók az Azure Portalon](media/container-instances-quickstart-template/aci-logs.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a tárolóval, a tárolópéldány **Áttekintés** lapján válassza a **Törlés lehetőséget.** A rendszer kérésére erősítse meg a törlést.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Azure-tárolópéldányt egy nyilvános Microsoft-rendszerképből. Ha szeretne létrehozni és üzembe helyezni egy tárolórendszerképet egy privát Azure-tárolóregisztrációs adatbázisból, lépjen tovább az Azure Container Instances oktatóanyagára.

> [!div class="nextstepaction"]
> [Oktatóanyag: Tároló rendszerképének létrehozása a Azure Container Instances](./container-instances-tutorial-prepare-app.md)

A sablonok létrehozásának folyamatát részletesen ismertető oktatóanyagért lásd:

> [!div class="nextstepaction"]
> [Oktatóanyag: Az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
