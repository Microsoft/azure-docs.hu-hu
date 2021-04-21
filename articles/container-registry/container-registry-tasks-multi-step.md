---
title: Többlépéses feladat a javítás & tesztelése
description: Bevezetés a többlépéses feladatokba, amely a ACR-feladatok szolgáltatása Azure Container Registry, amely feladatalapú munkafolyamatokat biztosít a tároló-rendszerképek felhőben történő végrehajtásához, tesztelésére és javításához.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: d57044fa8a0db7d661eb50284b34a6bbec9a1879
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781018"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Többlépéses összeállítási, tesztelési és javítási feladatok futtatása a ACR-feladatok

A többlépéses feladatok többlépéses, többtárolós munkafolyamatokkal bővítik ACR-feladatok rendszerkép-összeállítási és -leküldési képességeit. Többlépéses feladatok használatával több rendszerképet is felépíthet és lekullhat sorozatban vagy párhuzamosan. Ezután futtassa ezeket a rendszerképeket parancsként egyetlen feladatfutatáson belül. Minden lépés egy tároló rendszerkép-összeállítási vagy -leküldési műveletét definiálja, és egy tároló végrehajtását is meghatározhatja. Egy többlépéses feladat minden lépése egy tárolót használ végrehajtási környezetként.

> [!IMPORTANT]
> Ha korábban már létrehozott feladatokat az előzetes verzióban az `az acr build-task` paranccsal, azokat a feladatokat újra létre kell hoznia az [az acr task][az-acr-task] paranccsal.

Futtathat például olyan feladatokat, amelyek a következő logikát automatizálják:

1. Webalkalmazás-rendszerkép összeállítása
1. A webalkalmazás-tároló futtatása
1. Webalkalmazás-tesztkép összeállítása
1. A webalkalmazás-teszttároló futtatása, amely teszteket végez a futó alkalmazástárolón
1. Ha a tesztek megfelelőek, készítsen egy Helm-diagram archívumcsomagot
1. Művelet végrehajtása `helm upgrade` az új Helm-diagram archívumcsomaggal

Minden lépés az Azure-ban történik, a munka kiszervezése az Azure számítási erőforrásaira, és az infrastruktúra-kezelés felszabadítása. Az Azure Container Registry mellett csak a használt erőforrásokért kell fizetnie. A díjszabásról további információt  a Díjszabás című Azure Container Registry [tartalmaz.][pricing]


## <a name="common-task-scenarios"></a>Gyakori feladatforgatókönyvek

A többlépéses feladatok az alábbi logikához hasonló forgatókönyveket engedélyeznek:

* Egy vagy több tároló rendszerképének összeállítása, címkézése és leküldése sorozatban vagy párhuzamosan.
* Egységtesztek és kódbefedettség-eredmények futtatása és rögzítése.
* Funkcionális tesztek futtatása és rögzítése. ACR-feladatok támogatja egynél több tároló futtatását, és kérések sorozatát futtatja közöttük.
* Feladatalapú végrehajtást hajt végre, beleértve a tároló rendszerkép-összeállításának előkészítő és utólagos lépéseit.
* Üzembe helyezhet egy vagy több tárolót kedvenc üzembe helyezési motorral a célkörnyezetben.

## <a name="multi-step-task-definition"></a>Többlépéses feladatdefiníció

A folyamat többlépéses ACR-feladatok YAML-fájlon belüli lépések sorozatának határozzák meg. Minden lépés függőségeket határoz meg egy vagy több korábbi lépés sikeres elvégzésétől. A következő feladatlépés-típusok érhetők el:

* [`build`](container-registry-tasks-reference-yaml.md#build): Állítson össze egy vagy több tároló rendszerképet a már ismert `docker build` szintaxissal, sorozatban vagy párhuzamosan.
* [`push`](container-registry-tasks-reference-yaml.md#push): Beépített rendszerképek leküldése egy tároló-beállításjegyzékbe. A privát regisztrációs Azure Container Registry és a nyilvános regisztrációs Docker Hub.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Futtatassa a tárolót úgy, hogy az függvényként működhet a futó feladat környezetében. Paramétereket átadhat a tároló parancsának, és olyan tulajdonságokat is megadhat, mint `[ENTRYPOINT]` az env, a detach és az egyéb ismert `docker run` paraméterek. A `cmd` lépéstípus lehetővé teszi az egység- és funkcionális tesztelést, egyidejű tároló-végrehajtással.

Az alábbi kódrészletek mutatják be, hogyan kombinálhatja ezeket a feladatlépés-típusokat. A többlépéses feladatok egyszerűek is, például egyetlen rendszerkép létrehozása egy Docker-fájlból, majd a tárolójegyzékbe való lekésés, a következő yaml-fájllal:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

Vagy összetettebb, például ez a fiktív többlépéses definíció, amely a build, a tesztelés, a Helm-csomag és a Helm üzembe helyezésének lépéseit tartalmazza (a tároló-beállításjegyzék és a Helm-adattár konfigurációja nem jelenik meg):

```yml
version: v1.1.0
steps:
  - id: build-web
    build: -t $Registry/hello-world:$ID .
    when: ["-"]
  - id: build-tests
    build: -t $Registry/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["$Registry/helloworld:$ID"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: $Registry/helloworld:$ID
  - id: funcTests
    cmd: $Registry/helloworld:$ID
    env: ["host=helloworld:80"]
  - cmd: $Registry/functions/helm package --app-version $ID -d ./helm ./helm/helloworld/
  - cmd: $Registry/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image=$Registry/helloworld:$ID
```

A [többlépéses](container-registry-tasks-samples.md) feladatok YAML-fájljairól és Docker-fájljairól számos forgatókönyvben talál példákat.

## <a name="run-a-sample-task"></a>Mintafeladat futtatása

A feladatok támogatják a manuális végrehajtást, az úgynevezett "gyors futtatás" és az automatikus végrehajtást a Git-véglegesítésen vagy az alapként futtatott rendszerkép frissítéseken.

Feladat futtatásához először definiálja a feladat lépéseit egy YAML-fájlban, majd hajtsa végre az az acr run Azure [CLI-parancsot.][az-acr-run]

Példa Azure CLI-parancsra, amely egy feladatot futtat egy mintafeladat YAML-fájlja használatával. A lépések buildet, majd leküldést is építenek egy rendszerképet. A parancs futtatása előtt frissítsen a saját `\<acrName\>` Azure Container Registry-adatbázisának nevére.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A feladat futtatásakor a kimenetnek meg kell mutatnia a YAML-fájlban meghatározott egyes lépés előrehaladását. A következő kimenetben a lépések a következőként jelennek meg: `acb_step_0` és `acb_step_1` .

```azurecli
az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

```output
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

A Git-véglegesítés vagy az alapként való rendszerképfrissítés automatizált buildjére vonatkozó további információkért tekintse meg a rendszerkép-összeállítások automatizálásával és az alapként való rendszerkép-frissítés buildjére vonatkozó [](container-registry-tutorial-build-task.md) [oktatóanyagokat.](container-registry-tutorial-base-image-update.md)

## <a name="next-steps"></a>Következő lépések

A többlépéses feladatokra vonatkozó referenciát és példákat itt találja:

* [Feladathivatkozás](container-registry-tasks-reference-yaml.md) – A feladatlépések típusai, tulajdonságaik és használata.
* [Példák feladatra](container-registry-tasks-samples.md) – Példák `task.yaml` és Docker-fájlok számos forgatókönyvhöz, egyszerűtől összetettig.
* [Cmd-adattára](https://github.com/AzureCR/cmd) – Tárolók gyűjteménye parancsként az ACR-feladatokhoz.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-task]: /cli/azure/acr/task
