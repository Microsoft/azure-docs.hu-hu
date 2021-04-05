---
title: Azure Functions a Kubernetes és a KEDA
description: Megtudhatja, hogyan futtathat Azure Functions a felhőben vagy a helyszínen a Kubernetes-ben a KEDA, Kubernetes-alapú Event vezérelt automatikus skálázás használatával.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 525635ef40437fe308c52e2d5aba2c97ed8f20e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92927532"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions a Kubernetes és a KEDA

A Azure Functions futtatókörnyezet rugalmasságot biztosít a üzemeltetésben, ahol és így tovább.  A [KEDA](https://keda.sh) (Kubernetes-alapú eseményvezérelt automatikus skálázás) párok zökkenőmentesen használhatók a Azure functions futtatókörnyezettel és az Kubernetes-alapú eseményvezérelt méretezés biztosításához.

## <a name="how-kubernetes-based-functions-work"></a>A Kubernetes-alapú függvények működése

A Azure Functions szolgáltatás két kulcsfontosságú összetevőből áll: egy futtatókörnyezetből és egy méretezési vezérlőből.  A functions futtatókörnyezet futtatja és végrehajtja a kódot.  A futtatókörnyezet logikát tartalmaz a függvények végrehajtásának elindításához, naplózásához és kezeléséhez.  A Azure Functions futtatókörnyezet *bárhol* futhat.  A másik összetevő egy méretezési vezérlő.  A skálázási vezérlő figyeli a függvényt célzó események arányát, és proaktív módon méretezi az alkalmazást futtató példányok számát.  További információ: [Azure functions skálázás és üzemeltetés](functions-scale.md).

A Kubernetes-alapú függvények egy [Docker-tárolóban](functions-create-function-linux-custom-image.md) lévő functions futtatókörnyezetet biztosítanak eseményvezérelt skálázással az KEDA-on keresztül.  A KEDA 0 példányra (ha nincs esemény) és *n* példányra is méretezhető. Ezt a Kubernetes autoscaleer (horizontális Pod autoskálázás) egyéni metrikáinak kiosztásával teszi meg.  A functions-tárolók és a KEDA lehetővé teszi a kiszolgáló nélküli függvények replikálását bármely Kubernetes-fürtön.  Ezek a függvények az [Azure Kubernetes Services (ak) virtuális csomópontok](../aks/virtual-nodes-cli.md) szolgáltatásával is üzembe helyezhetők a kiszolgáló nélküli infrastruktúra számára.

## <a name="managing-keda-and-functions-in-kubernetes"></a>KEDA és függvények kezelése a Kubernetes-ben

A függvények a Kubernetes-fürtön való futtatásához telepítenie kell a KEDA összetevőt. Ezt az összetevőt [Azure functions Core Tools](functions-run-local.md)használatával telepítheti.

### <a name="installing-with-helm"></a>Telepítés a Helmtal

A KEDA különböző módokon telepíthető bármely Kubernetes-fürtben, beleértve a Helm-t is.  Az üzembe helyezési lehetőségek dokumentálva vannak a [KEDA webhelyen](https://keda.sh/docs/1.4/deploy/).

## <a name="deploying-a-function-app-to-kubernetes"></a>Function-alkalmazás üzembe helyezése a Kubernetes

A KEDA-t futtató Kubernetes-fürtökön bármely Function alkalmazást üzembe helyezhet.  Mivel a függvények Docker-tárolóban futnak, a projekthez a szükséges `Dockerfile` .  Ha még nem rendelkezik ilyennel, hozzáadhat egy Docker az alábbi parancs futtatásával a functions projekt gyökerében:

> [!NOTE]
> Az alapvető eszközök automatikusan létrehozzák a .NET, node, Python vagy PowerShell nyelven írt Azure Functions Docker. A Java-ban írt Function apps esetében a Docker manuálisan kell létrehozni. Az Azure-függvény alapjául szolgáló megfelelő rendszerkép megtalálásához használja a Azure Functions [rendszerkép listáját](https://github.com/Azure/azure-functions-docker) .

```cli
func init --docker-only
```

A következő parancs futtatásával hozzon létre egy rendszerképet, és telepítse a függvényeket a Kubernetes:

> [!NOTE]
> A központi eszközök kihasználják a Docker CLI-t a lemezkép összeállításához és közzétételéhez. Győződjön meg arról, hogy a Docker már telepítve van, és csatlakoztatva van a fiókjához a használatával `docker login` .

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Cserélje le a helyére `<name-of-function-deployment>` a Function alkalmazás nevét.

Az üzembe helyezési parancs több műveletet hajt végre:
1. A korábban létrehozott Docker egy helyi rendszerkép létrehozásához használható a Function alkalmazáshoz.
2. A helyi rendszerkép címkézve van, és a rendszer leküldi azt a tároló-beállításjegyzékbe, ahol a felhasználó be van jelentkezve.
3. A rendszer létrehoz egy jegyzékfájlt, és alkalmazza azt a fürtre, amely meghatározza a Kubernetes- `Deployment` erőforrást, az `ScaledObject` erőforrást és a `Secrets` fájlt, amely tartalmazza a fájlból importált környezeti változókat `local.settings.json` .

### <a name="deploying-a-function-app-from-a-private-registry"></a>Function-alkalmazás üzembe helyezése privát beállításjegyzékből

A fenti folyamat privát kibocsátásiegység-forgalmi jegyzékekhez is használható.  Ha egy privát beállításjegyzékből húz le egy tárolót, a Kubernetes titkos kulcsra hivatkozó jelzőt is adja meg a `--pull-secret` futtatáskor `func kubernetes deploy` .

## <a name="removing-a-function-app-from-kubernetes"></a>Function-alkalmazás eltávolítása a Kubernetes-ből

A telepítés után eltávolíthatja a függvényt a társított, a létrehozott elem eltávolításával `Deployment` `ScaledObject` `Secrets` .

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>A KEDA eltávolítása a Kubernetes-ből

A KEDA eltávolításának lépései dokumentálva vannak [a KEDA webhelyen](https://keda.sh/docs/1.4/deploy/).

## <a name="supported-triggers-in-keda"></a>Támogatott eseményindítók a KEDA-ben

A KEDA a következő Azure Function triggereket támogatja:

* [Azure Storage-üzenetsorok](functions-bindings-storage-queue.md)
* [Azure Service Bus-üzenetsorok](functions-bindings-service-bus.md)
* [Azure Event/IoT hubok](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [RabbitMQ-várólista](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>HTTP-trigger támogatása

Használhatja a HTTP-eseményindítókat közzétevő Azure Functionsokat, de a KEDA nem kezeli közvetlenül.  A KEDA Prometheus trigger kihasználható a [http-Azure functions 1 és *n* példány közötti méretezésére](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42).

## <a name="next-steps"></a>Következő lépések
További információkat találhat az alábbi forrásokban:

* [Függvény létrehozása egyéni rendszerkép használatával](functions-create-function-linux-custom-image.md)
* [Az Azure Functions helyi kódolása és tesztelése](functions-develop-local.md)
* [Az Azure Function fogyasztási terv működése](functions-scale.md)