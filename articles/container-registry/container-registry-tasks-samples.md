---
title: ACR-feladatok mintái
description: Minta Azure Container Registry feladatok (ACR-feladatok) a tároló-lemezképek létrehozásához, futtatásához és javításához
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74456082"
---
# <a name="acr-tasks-samples"></a>ACR-feladatok mintái

Ez a cikk `task.yaml` több [Azure Container Registry feladat](container-registry-tasks-overview.md) (ACR-feladat) esetében a fájlokra és a kapcsolódó Dockerfiles mutató hivatkozásokat tartalmaz. 

További példákért tekintse meg az [Azure Samples][task-examples] tárházat.

## <a name="scenarios"></a>Forgatókönyvek

* **Rendszerkép létrehozása**  -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Tároló futtatása**  -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* Rendszerkép létrehozása és **leküldése**  -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* Rendszerkép létrehozása és **futtatása**  -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Több rendszerkép**  -   létrehozása és leküldése [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Képek létrehozása és tesztelése párhuzamosan**  -   [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Képek létrehozása és leküldése több**  -  beállításjegyzékbe [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Következő lépések

További információ az ACR-feladatokról:

* [Többlépéses feladatok](container-registry-tasks-multi-step.md) – az ACR-alapú munkafolyamatok a Felhőbeli tároló-lemezképek létrehozásához, teszteléséhez és javításához.
* [Feladat leírása](container-registry-tasks-reference-yaml.md) – a feladat lépésének típusai, tulajdonságai és használata.
* [Cmd](https://github.com/AzureCR/cmd) -tárház – a tárolók gyűjteménye az ACR-feladatok parancsaiként.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
