---
title: Privát Helm-adattár használata az Azure dev Spaces szolgáltatásban
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Használjon privát Helm-tárházat egy Azure fejlesztői tárhelyen.
keywords: Docker, Kubernetes, Azure, AK, Azure Container Service, tárolók, Helm
manager: gwallace
ms.openlocfilehash: 7c5f28595df2e552fd48033b44e4e1f0ea4ec306
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960337"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Privát Helm-tárház használata az Azure dev Spaces szolgáltatásban

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

A [Helm][helm] a Kubernetes csomagkezelő. A Helm [diagram][helm-chart] formátumot használ a függőségek kicsomagolásához. A Helm-diagramok tárolása olyan tárházban történik, amely nyilvános vagy magánjellegű lehet. Az Azure dev Spaces csak az alkalmazás futtatásakor kéri le a nyilvános adattárakból származó Helm-diagramokat. Azokban az esetekben, amikor a Helm-adattár magán vagy az Azure dev Spaces nem fér hozzá, hozzáadhat egy diagramot az adott tárházból közvetlenül az alkalmazáshoz. A diagram közvetlen hozzáadása lehetővé teszi, hogy az Azure dev Spaces az alkalmazást anélkül futtassa, hogy hozzá kellene férnie a Private Helm-tárházhoz.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Adja hozzá a Private Helm-tárházat a helyi géphez

A [Helm repo Add][helm-repo-add] and [Helm repo Update][helm-repo-update] paranccsal elérheti a privát Helm-tárházat a helyi gépről.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Diagram hozzáadása az alkalmazáshoz

Navigáljon a projekt könyvtárába, és futtassa a parancsot `azds prep` .

```cmd
azds prep --enable-ingress
```

> [!TIP]
> A `prep` parancs megkísérli [egy Docker és egy Helm-diagram](../how-dev-spaces-works-prep.md#prepare-your-code) létrehozását a projekthez. Az Azure dev Spaces ezeket a fájlokat használja a kód összeállításához és futtatásához, de módosíthatja ezeket a fájlokat, ha módosítani szeretné a projekt felépítésének és futtatásának módját.

Hozzon létre egy [követelmény. YAML][helm-requirements] fájlt a diagrammal az alkalmazás diagramjának könyvtárában. Ha például az alkalmazás neve *App1*, akkor létre kell hoznia a *diagramok/App1/követelmények. YAML*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Navigáljon az alkalmazás diagramjának könyvtárába, és a [Helm függőségi frissítés][helm-dependency-update] használatával frissítse az alkalmazás Helm-függőségeit, és töltse le a diagramot a privát tárházból.

```cmd
helm dependency update
```

Győződjön meg arról, hogy *a diagram* alkönyvtára egy *tgz* -fájllal lett hozzáadva az alkalmazás diagramjának könyvtárába. Például: *Charts/App1/Charts/mychart-0.1.0. tgz*.

A saját Helm-tárházból származó diagram le lett töltve, és hozzá lett adva a projekthez. Távolítsa el a *követelmények. YAML* fájlt, hogy a fejlesztői szóközök ne próbáljanak frissíteni ezt a függőséget.

## <a name="run-your-application"></a>Az alkalmazás futtatása

Navigáljon a projekt gyökérkönyvtárához, és futtassa az `azds up` alkalmazást, és ellenőrizze, hogy az alkalmazás sikeresen fut-e a fejlesztői térben.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>Következő lépések

További információ a [helmről és annak működéséről][helm].

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
