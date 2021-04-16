---
title: Rövid útmutató – Beállításjegyzék létrehozása a portálon
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre privát Azure Container Registryt a Azure Portal.
ms.date: 08/04/2020
ms.topic: quickstart
ms.custom:
- mvc
- mode-portal
ms.openlocfilehash: b59e605ea0484bd70456e278cb712683189b251d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533937"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>Rövid útmutató: Azure Container Registry létrehozása a Azure Portal

Az Azure Container Registry egy privát Docker-beállításjegyzék az Azure-ban, ahol privát Docker-tároló rendszerképeket és kapcsolódó összetevőket tárolhat és kezelhet. Ebben a rövid útmutatóban létrehozhat egy tároló-beállításjegyzéket az Azure Portallal. Ezután Docker-parancsokkal leküld egy tároló rendszerképét a regisztrációs adatbázisba, majd végül leküldi és futtatja a rendszerképet a regisztrációs adatbázisból.

Ahhoz, hogy a tároló rendszerképekkel működjön a regisztrációs adatbázisba való bejelentkezéshez, ehhez a rövid útmutatóhoz az Azure CLI-t kell használnia (2.0.55-ös vagy újabb verzió ajánlott). A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

A Dockert is telepítenie kell helyileg. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Válassza **az Erőforrás létrehozása**  >  **Tárolók Container Registry.**  >  

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="Lépjen a tároló-beállításjegyzékhez a portálon":::

Az Alapvető **beállítások lapon** adja meg az **Erőforráscsoport** és a Beállításjegyzék **neve értékeit.** A tárolóregisztrációs adatbázis nevének egyedinek kell lennie az Azure-ban, és 5–50 alfanumerikus karaktert kell tartalmaznia. Ebben a rövid útmutatóban hozzon létre egy `myResourceGroup` nevű új erőforráscsoportot az `West US` nevű helyen, majd a **Termékváltozat** mezőben válassza az „Alapszintű” lehetőséget.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="Tároló-beállításjegyzék létrehozása a portálon":::

Fogadja el az alapértelmezett értékeket a többi beállításnál. Ezután válassza az **Áttekintés + létrehozás lehetőséget.** A beállítások áttekintése után válassza a **Létrehozás lehetőséget.**

Ebben a rövid útmutatóban egy *Alapszintű* beállításjegyzéket hoz létre, amely egy költségoptimalált lehetőség a fejlesztők számára a Azure Container Registry. Az elérhető szolgáltatásszintekkel (SKUS-okkal) kapcsolatos részletekért lásd: [Tároló-beállításjegyzék szolgáltatási rétegei.][container-registry-skus]

Amikor **megjelenik az Üzembe helyezés sikeres** üzenet, válassza ki a tároló-beállításjegyzéket a portálon. 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="A tároló-beállításjegyzék áttekintése a portálon":::

Jegyezze fel a beállításjegyzék nevét és a bejelentkezési **kiszolgáló értékét.** Ezeket az értékeket a következő lépésekben használhatja, amikor lekért és lekért rendszerképeket a Docker használatával.

## <a name="log-in-to-registry"></a>Bejelentkezés a beállításjegyzékbe

A tároló rendszerképének le- és lehúzása előtt be kell jelentkeznie a beállításjegyzék-példányba. [Jelentkezzen be az Azure CLI-be][get-started-with-azure-cli] a helyi gépen, majd futtassa [az az acr login][az-acr-login] parancsot. Az Azure CLI-val való bejelentkezéskor csak a beállításjegyzék nevét adja meg. Ne használja a bejelentkezési kiszolgáló nevét, amely a tartomány utótagját tartalmazza( `azurecr.io` például ).

```azurecli
az acr login --name <registry-name>
```

Példa:

```azurecli
az acr login --name mycontainerregistry
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Tárolórendszerképek listázása

A regisztrációs adatbázisban található rendszerképek listához lépjen a regisztrációs adatbázisra a portálon, és válassza az **Adattárak** lehetőséget, majd válassza ki a használatával létrehozott **hello-world** adattárat. `docker push`

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="Tároló rendszerképének listása a portálon":::

A **hello-world adattár** kiválasztásával a címkék alatt a `v1` címkézett rendszerkép **látható.**

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforrások megtisztítása érdekében keresse meg a **myResourceGroup** erőforráscsoportot a portálon. Az erőforráscsoport betöltése után kattintson  az Erőforráscsoport törlése elemre az erőforráscsoport, a tároló-beállításjegyzék és az ott tárolt tároló-rendszerképek eltávolításához.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="Erőforráscsoport törlése a portálon":::


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registry a Azure Portal, leküldte a tároló rendszerképét, majd lekérte és futtatta a rendszerképet a regisztrációs adatbázisból. Folytassa az Azure Container Registry oktatóanyagokkal az ACR mélyebb betekeredőbb leírásában.

> [!div class="nextstepaction"]
> [Azure Container Registry oktatóanyagok][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry-feladatok oktatóanyagok][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
