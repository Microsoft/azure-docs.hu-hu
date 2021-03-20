---
title: Rövid útmutató – Docker-tároló üzembe helyezése tároló példánya – Docker CLI
description: Ebben a rövid útmutatóban a Docker CLI használatával gyorsan üzembe helyezhet egy elkülönített Azure Container-példányban futó tároló-webalkalmazást
ms.topic: quickstart
ms.date: 09/14/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9904fb8c2142816196a1939a16445318bdb245d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91262314"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>Gyors útmutató: tároló-példány üzembe helyezése az Azure-ban a Docker CLI használatával

A Azure Container Instances használatával a kiszolgáló nélküli Docker-tárolókat az Azure-ban, az egyszerűség és a gyorsaság segítségével futtathatja. A Felhőbeli natív alkalmazások fejlesztésekor igény szerint üzembe helyezhető egy tároló-példányon, és zökkenőmentesen válthat a helyi fejlesztésből a felhőbe történő üzembe helyezésig.

Ebben a rövid útmutatóban natív Docker CLI-parancsokat használ a Docker-tárolók üzembe helyezéséhez és az alkalmazás elérhetővé tételéhez Azure Container Instances. Ezt a funkciót a [Docker és az Azure integrációja](https://docs.docker.com/engine/context/aci-integration/)teszi lehetővé. Néhány másodperccel a parancs végrehajtása után `docker run` megkeresheti a tárolóban futó alkalmazást:

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben":::

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot][azure-account].

Ebben a rövid útmutatóban a [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) vagy [MacOS](https://desktop.docker.com/mac/edge/Docker.dmg)rendszerhez elérhető Docker Desktop 2.3.0.5 vagy újabb verzióra van szükség. Vagy telepítse a [Docker ACI Integration CLI-t a Linux rendszerhez](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux). 

> [!IMPORTANT]
> A Azure Container Instances egyik funkciója sem támogatott. Küldjön visszajelzést a Docker-Azure integrációról az [ACI-Integration-Beta GitHub-](https://github.com/docker/aci-integration-beta) tárházban felmerülő probléma létrehozásával.

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="create-a-container"></a>Tároló létrehozása

A Docker-környezet létrehozása után létrehozhat egy tárolót az Azure-ban. Ebben a rövid útmutatóban a nyilvános `mcr.microsoft.com/azuredocs/aci-helloworld` rendszerképet használhatja. Ez a rendszerkép egy olyan kisméretű webalkalmazást csomagol, amely a statikus HTML-lapokat Node.js.

Először váltson át az ACI-környezetre. Az összes további Docker-parancs ebben a környezetben fut.

```
docker context use myacicontext
```

Futtassa az alábbi `docker run` parancsot az Azure Container instance létrehozásához az interneten elérhető 80-as porton:

```
docker run -p 80:80 mcr.microsoft.com/azuredocs/aci-helloworld
```

A sikeres üzembe helyezés mintájának kimenete:

```
[+] Running 2/2
 ⠿ hungry-kirch            Created                                                                               5.1s
 ⠿ single--container--aci  Done                                                                                 11.3s
hungry-kirch
```

A futtatásával `docker ps` részletes információkat kaphat a futó tárolóról, beleértve a nyilvános IP-címet is:

```
docker ps
```


A minta kimenet egy nyilvános IP-címet mutat be, ebben az esetben a *52.230.225.232*:

```
CONTAINER ID        IMAGE                                        COMMAND             STATUS              PORTS
hungry-kirch        mcr.microsoft.com/azuredocs/aci-helloworld                       Running             52.230.225.232:80->80/tcp
```

 Most nyissa meg az IP-címet a böngészőben. Ha egy, az alábbihoz hasonló weboldal jelenik meg, gratulálunk! Sikeresen üzembe helyezett egy Docker-tárolóban futó alkalmazást az Azure-ban.

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben":::

## <a name="pull-the-container-logs"></a>A tároló naplóinak lekérése

Amikor hibaelhárítást kell végrehajtania egy tárolón, vagy az abban futtatott alkalmazáson (vagy csak meg kell néznie a kimenetet), először tekintse meg a tárolópéldány naplóit.

Például a parancs futtatásával `docker logs` tekintheti meg az *éhes-Kirch* tároló naplóit az ACI kontextusban:

```azurecli-interactive
docker logs hungry-kirch
```

A kimenet megjeleníti a tároló naplóit, és a HTTP GET kéréseknek is meg kell jelenniük, amelyek akkor jöttek létre, amikor az alkalmazást a böngészőjében megtekintette.

```output
listening on port 80
::ffff:10.240.255.55 - - [07/Jul/2020:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a tárolóval, futtassa a parancsot `docker rm` az eltávolításához. Ez a parancs leállítja és törli az Azure Container-példányt.

```
docker rm hungry-kirch
```


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container-példányt egy nyilvános rendszerképből a Docker és az Azure közötti integráció használatával. További információ az integrációs forgatókönyvekről a [Docker dokumentációjában](https://docs.docker.com/engine/context/aci-integration/). 

A [Docker-bővítményt a Visual Studio Code](https://aka.ms/VSCodeDocker) -hoz is használhatja a tárolók, képek és környezetek fejlesztéséhez, futtatásához és kezeléséhez.

A tároló-példányok létrehozásához és kezeléséhez szükséges Azure-eszközök használatához tekintse meg az [Azure CLI](container-instances-quickstart.md), [Azure PowerShell](container-instances-quickstart-powershell.md), [Azure Portal](container-instances-quickstart-portal.md)és [Azure Resource Manager sablont](container-instances-quickstart-template.md)használó egyéb gyors útmutatókat.

Ha a Docker-összeállítás használatával helyileg szeretné definiálni és futtatni egy többtárolós alkalmazást, majd váltson Azure Container Instancesra, folytassa az Oktatóanyaggal.

> [!div class="nextstepaction"]
> [A Docker-összeállítás oktatóanyaga](./tutorial-docker-compose.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

