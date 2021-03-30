---
title: Adattárak megtekintése a portálon
description: A Azure Portal használatával megtekintheti Azure Container Registry adattárakat, amelyek a Docker-tárolók lemezképeit és más támogatott összetevőket tárolnak.
ms.topic: article
ms.date: 01/05/2018
ms.openlocfilehash: 1da72706d2554610a685f71199ab14af5e30ce1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "74456294"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Tároló beállításjegyzékbeli Tárházak megtekintése a Azure Portal

Azure Container Registry lehetővé teszi a Docker-tárolók rendszerképének tárolását a tárházban. A képek tárházban történő tárolásával különálló környezetekben tárolhatja a rendszerképek (vagy a lemezképek verziói) csoportjait. Ezeket a adattárakat akkor adhatja meg, ha képeket küld a beállításjegyzékbe, és megtekinti a tartalmukat a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

* **Container Registry**: hozzon létre egy tároló-beállításjegyzéket az Azure-előfizetésében. Használja például a [Azure Portal](container-registry-get-started-portal.md) vagy az [Azure CLI](container-registry-get-started-azure-cli.md)-t.
* **Docker CLI**: telepítse a [Docker][docker-install] -t a helyi gépre, amely a Docker parancssori felületét biztosítja.
* **Tároló képe**: a rendszerkép leküldése a tároló-beállításjegyzékbe. A képek leküldésével és lekérésével kapcsolatos útmutatásért lásd: [leküldéses és lekéréses lemezképek](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Adattárak megtekintése Azure Portal

Megtekintheti a lemezképeket tároló adattárak listáját, valamint a képcímkéket a Azure Portal.

Ha követte a [rendszerképek leküldése és lekérése](container-registry-get-started-docker-cli.md) című témakör lépéseit (és ezt követően nem törli a képet), akkor egy Nginx-rendszerképet kell tartalmaznia a tároló beállításjegyzékében. Az ebben a cikkben szereplő utasítások azt adták meg, hogy a képet egy névtérrel, a "Samples" címkével lássa el `/samples/nginx` . Frissítőként a cikkben megadott [Docker leküldéses][docker-push] parancs a következők voltak:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Mivel a Azure Container Registry támogatja az ilyen többszintű adattárbeli névtereket, az adott alkalmazáshoz vagy alkalmazások gyűjteményéhez kapcsolódó rendszerképeket a különböző fejlesztési vagy operatív csapatokhoz is felhasználhatja. A tároló-beállításjegyzékben található adattárakkal kapcsolatos további információkért lásd: [privát Docker-tárolók az Azure-ban](container-registry-intro.md).

Adattár megtekintése:

1. Jelentkezzen be az [Azure Portalra][portal]
1. Válassza ki azt a **Azure Container Registry** , amelyhez az Nginx-rendszerképet leküldte
1. Válassza ki a **Tárházak** elemet a beállításjegyzékben található lemezképeket tartalmazó adattárak listájának megtekintéséhez.
1. Válasszon ki egy tárházat az adott tárházban található képcímkék megtekintéséhez.

Ha például leküldte az Nginx-rendszerképet a [leküldéses és lekéréses rendszerképben](container-registry-get-started-docker-cli.md), a következőhöz hasonlónak kell megjelennie:

![Adattárak a portálon](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri a Tárházak megtekintésének és használatának alapjait a portálon, próbálkozzon Azure Container Registry [Azure Kubernetes Service (ak)](../aks/tutorial-kubernetes-prepare-app.md) -fürttel való használatával.

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
