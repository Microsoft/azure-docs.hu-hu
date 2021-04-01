---
title: Több lépésből álló feladat felépítés, tesztelés & patch-rendszerkép
description: A többlépéses feladatok bemutatása, amely a Azure Container Registry ACR-feladatok egyik funkciója, amely feladaton alapuló munkafolyamatokat biztosít a felhőben található tároló-lemezképek létrehozásához, teszteléséhez és javításához.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 0dcd38559d3f50715f982de4c9c80bfe9c6c8433
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "78399698"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Több lépésből álló Build-, tesztelési és javítási feladatok futtatása az ACR-feladatokban

A többlépéses feladatok több lépésből álló, többtárolós alapú munkafolyamatokkal bővítik az ACR-feladatok egyetlen Rendszerképbeli Build-és leküldéses képességeit. Több lépésből álló feladatok használatával több lemezképet hozhat létre és küldhet le sorozatos vagy párhuzamosan. Ezután futtassa a lemezképeket parancsként egyetlen feladat futtatásával. Az egyes lépések egy tároló-rendszerkép létrehozási vagy leküldéses műveletét határozzák meg, valamint meghatározhatják egy tároló végrehajtását is. A többlépéses tevékenységek minden lépése egy tárolót használ végrehajtási környezetként.

> [!IMPORTANT]
> Ha korábban már létrehozott feladatokat az előzetes verzióban az `az acr build-task` paranccsal, azokat a feladatokat újra létre kell hoznia az [az acr task][az-acr-task] paranccsal.

Futtathat például egy feladatot olyan lépésekkel, amelyek automatizálják a következő logikát:

1. Webalkalmazás-rendszerkép összeállítása
1. A webalkalmazás-tároló futtatása
1. Webalkalmazás-tesztelési rendszerkép összeállítása
1. Futtassa a webalkalmazás-teszt tárolót, amely teszteket hajt végre a futó alkalmazás tárolóján
1. Ha a tesztek sikeresek, hozzon létre egy Helm diagram archiválási csomagot
1. `helm upgrade`Az új Helm chart Archive csomag használata

Az Azure-on belül minden lépés elvégezhető, és kiszervezi a munkát az Azure számítási erőforrásaira, és felszabadítja az infrastruktúra felügyeletét. Az Azure Container Registry mellett csak a felhasznált erőforrásokért kell fizetnie. A díjszabással kapcsolatos információkért tekintse meg a **tároló-összeállítás** szakaszt [Azure Container Registry díjszabását][pricing].


## <a name="common-task-scenarios"></a>Gyakori feladatok forgatókönyvei

A többlépéses feladatok a következő logikához hasonló forgatókönyveket tesznek lehetővé:

* Egy vagy több tároló lemezképének létrehozása, címkézése és leküldése sorozatos vagy párhuzamosan.
* Az egység tesztelése és a kód lefedettségi eredményeinek futtatása és rögzítése.
* Funkcionális tesztek futtatása és rögzítése. Az ACR-feladatok több tároló futtatását is lehetővé teszi, és ezek között több kérést hajtanak végre.
* Feladat-alapú végrehajtás végrehajtása, beleértve a tárolók rendszerképének létrehozása előtti/utáni lépéseket is.
* Helyezzen üzembe egy vagy több tárolót a kedvenc telepítési motorján a célként megadott környezetben.

## <a name="multi-step-task-definition"></a>Több lépésből álló feladat definíciója

Az ACR-feladatok több lépésből álló feladatai egy YAML-fájlban lévő lépések sorozata. Az egyes lépések egy vagy több előző lépés sikeres befejezésére vonatkozó függőségeket is meghatározhatnak. A következő feladathoz tartozó lépések típusai érhetők el:

* [`build`](container-registry-tasks-reference-yaml.md#build): Hozzon létre egy vagy több tárolót a jól ismert `docker build` szintaxissal, sorozatban vagy párhuzamosan.
* [`push`](container-registry-tasks-reference-yaml.md#push): Kiépített lemezképek leküldése egy tároló-beállításjegyzékbe. A privát beállításjegyzékek, például a Azure Container Registry támogatottak, csakúgy, mint a nyilvános Docker hub.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Futtasson egy tárolót úgy, hogy az a futó feladat kontextusában is működhet. Paramétereket adhat át a tárolóhoz `[ENTRYPOINT]` , és meghatározhatja a tulajdonságokat, például az ENV, a leválasztás és az egyéb ismerős `docker run` paramétereket. A `cmd` lépés típusa lehetővé teszi az egység és a funkcionális tesztelést a tárolók párhuzamos végrehajtásával.

A következő kódrészletek azt mutatják be, hogyan egyesítheti ezeket a feladattípus-típusokat. A többlépéses feladatok olyan egyszerűek lehetnek, mint a Docker egyetlen rendszerképének létrehozása és a beállításjegyzékbe való továbbítása, a következőhöz hasonló YAML-fájllal:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

Vagy összetettebb, például ez a fiktív többlépéses definíció, amely tartalmazza a Build, a test, a Helm Package és a Helm üzembe helyezésének lépéseit (a Container Registry és a Helm adattár konfigurációja nem látható):

```yml
version: v1.1.0
steps:
  - id: build-web
    build: -t $Registry/hello-world:$ID .
    when: ["-"]
  - id: build-tests
    build -t $Registry/hello-world-tests ./funcTests
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

Számos forgatókönyv esetén tekintse meg a több lépésből álló YAML-fájlok és-Dockerfiles kapcsolatos [példákat](container-registry-tasks-samples.md) .

## <a name="run-a-sample-task"></a>Minta feladat futtatása

A feladatok a manuális végrehajtást, a "gyors futtatást" és az automatikus végrehajtást támogatják a git-véglegesítő vagy az alaprendszerkép frissítése során.

Feladat futtatásához először meg kell adnia a feladat lépéseit egy YAML-fájlban, majd végre kell hajtania az Azure CLI-parancsot az [ACR Run][az-acr-run]paranccsal.

Íme egy példa egy Azure CLI-parancsra, amely egy feladatot futtat egy YAML-fájl használatával. A lépései felépítve, majd leküldenek egy képet. `\<acrName\>`A parancs futtatása előtt frissítse az Azure Container Registry nevét.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A feladat futtatásakor a kimenetnek a YAML fájlban meghatározott egyes lépések előrehaladását kell megjelenítenie. A következő kimenetben a lépések a következőképpen jelennek meg: `acb_step_0` és `acb_step_1` .

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

További információ a git-véglegesítő vagy az alaprendszerkép-frissítés automatizált buildekről: a [rendszerkép-buildek automatizálása](container-registry-tutorial-build-task.md) és az [alaprendszerkép frissítése](container-registry-tutorial-base-image-update.md) című oktatóanyag cikkei.

## <a name="next-steps"></a>Következő lépések

A többlépéses feladatok hivatkozását és a példákat itt találja:

* [Feladat leírása](container-registry-tasks-reference-yaml.md) – a feladat lépésének típusai, tulajdonságai és használata.
* Példa a [példákra](container-registry-tasks-samples.md) `task.yaml` és a Docker-fájlokra több forgatókönyv esetén – egyszerű és összetett.
* [Cmd](https://github.com/AzureCR/cmd) -tárház – a tárolók gyűjteménye az ACR-feladatok parancsaiként.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task