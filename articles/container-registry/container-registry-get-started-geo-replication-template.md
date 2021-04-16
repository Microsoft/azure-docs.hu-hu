---
title: Rövid útmutató – Georeplikált beállításjegyzék létrehozása – Azure Resource Manager sablon
description: Megtudhatja, hogyan hozhat létre georeplikált Azure Container Registryt egy Azure Resource Manager használatával.
services: azure-resource-manager
author: dlepow
ms.author: danlep
ms.date: 10/06/2020
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: c59c7897054b2ad65a76353e6d886af46cac91e0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537439"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-an-arm-template"></a>Rövid útmutató: Georeplikált tárolójegyzék létrehozása ARM-sablonnal

Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure Container Registry-példányt egy Azure Resource Manager sablon (ARM-sablon) használatával. A sablon egy georeplikált regisztrációs adatbázist állít be, amely automatikusan szinkronizálja a regisztrációs adatbázis tartalmát több [Azure-régióban.](container-registry-geo-replication.md) A georeplikáció lehetővé teszi a regionális üzemelő példányok rendszerképeihez való hálózati közel hozzáférést, miközben egyetlen felügyeleti élményt nyújt. Ez a Prémium beállításjegyzék [szolgáltatásszint](container-registry-skus.md) egyik szolgáltatása.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/) közül származik. A sablon beállítja a regisztrációs adatbázist és egy további regionális replikát.

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json":::

A sablon a következő erőforrásokat definiálja:

* **[Microsoft.ContainerRegistry/registry:](/azure/templates/microsoft.containerregistry/registries)** Azure Container Registry létrehozása
* **[Microsoft.ContainerRegistry/registries/replications:](/azure/templates/microsoft.containerregistry/registries/replications)** tárolóregisztrációs adatbázis replikájának létrehozása

További Azure Container Registry sablonmintákat a gyorsindítási [sablongyűjteményben talál.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

 1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 1. Válassza ki vagy adja meg a következő értékeket.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport:** válassza az **Új létrehozása** lehetőséget, adjon egyedi nevet az erőforráscsoportnak, majd kattintson az **OK gombra.**
    * **Régió:** válassza ki az erőforráscsoport helyét. Például: **USA középső középső országa.**
    * **Acr Name**: fogadja el a regisztrációs adatbázis létrehozott nevét, vagy adjon meg egy nevet. Globálisan egyedinek kell lennie.
    * **Acr Admin User Enabled (Acr rendszergazdai felhasználó engedélyezve):** fogadja el az alapértelmezett értéket.
    * **Hely:** fogadja el a regisztrációs adatbázis kezdőlapreplikához létrehozott helyet, vagy adjon meg egy helyet, például az **USA középső középső helyét.** 
    * **Acr Sku:** fogadja el az alapértelmezett értéket.
    * **Acr Replica Location (Acr-replika** helye): adja meg a beállításjegyzék-replika helyét a régió rövid nevével. A beállításjegyzék helyétől eltérőnek kell lennie. Például: **westeurope**.

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="Sablontulajdonságok":::

1. Válassza **a Felülvizsgálat + létrehozás** lehetőséget, majd tekintse át a használati feltételeket. Ha elfogadja, válassza a **Létrehozás lehetőséget.**

1. A beállításjegyzék sikeres létrehozása után értesítést kap:

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="Portálértesítés":::

 Az Azure Portalon helyezhető üzembe a sablon. A Azure Portal mellett használhatja a Azure PowerShell, az Azure CLI és a REST API. További információ az egyéb üzembe helyezési módszerekről: [Sablonok üzembe helyezése.](../azure-resource-manager/templates/deploy-cli.md)

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A tárolójegyzék Azure Portal vagy egy olyan eszközzel, mint az Azure CLI, áttekinti a tároló-beállításjegyzék tulajdonságait.

1. A portálon keressen rá a Tárolóregisztrálójegyzékek kifejezésre, és válassza ki a létrehozott tárolóregisztráló adatbázist.

1. Az Áttekintés **lapon** jegyezze fel a beállításjegyzék **bejelentkezési** kiszolgálóját. Akkor használja ezt az URI-t, ha a Docker használatával címkéz és lekultál rendszerképeket a regisztrációs adatbázisba. További információ: Az első [rendszerkép leküldése a Docker CLI használatával.](container-registry-get-started-docker-cli.md)

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="A beállításjegyzék áttekintése":::

1. A **Replikációk lapon** erősítse meg a kezdőlapreplika és a sablonon keresztül hozzáadott replika helyét. Ha szükséges, adjon hozzá további replikákat ezen az oldalon.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="Beállításjegyzék-replikációk":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szüksége, törölje az erőforráscsoportot, a beállításjegyzéket és a beállításjegyzék-replikát. Ezt a következő gombra Azure Portal, válassza ki a beállításjegyzéket tartalmazó erőforráscsoportot, majd válassza az **Erőforráscsoport törlése lehetőséget.**

:::image type="content" source="media/container-registry-get-started-geo-replication-template/delete-resource-group.png" alt-text="Erőforráscsoport törlése":::

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registry ARM-sablonnal, és konfigurált egy beállításjegyzék-replikát egy másik helyen. Folytassa az Azure Container Registry oktatóanyagokkal, hogy mélyebben is megnézzük az ACR-t.

> [!div class="nextstepaction"]
> [Azure Container Registry oktatóanyagok](container-registry-tutorial-prepare-registry.md)

A sablonok létrehozásának folyamatát bemutató részletes oktatóanyagért lásd:

> [!div class="nextstepaction"]
> [Oktatóanyag: Az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
