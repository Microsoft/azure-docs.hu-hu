---
title: Az Azure Dev Spaces engedélyezése az AKS-& az ügyféloldali eszközök telepítéséhez
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Megtudhatja, hogyan engedélyezheti az Azure Dev Spacest egy AKS-fürtön, és hogyan telepítheti az ügyféloldali eszközöket.
ms.custom: devx-track-azurecli
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: 079a9e1b28b315457ac20d3aa9e7d29ce28fa077
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505360"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Az Azure Dev Spaces engedélyezése egy AKS-fürtön és az ügyféloldali eszközök telepítése

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Ez a cikk számos módszert mutat be az Azure Dev Spaces AKS-fürtön való engedélyezésére, valamint az ügyféloldali eszközök telepítésére.

## <a name="enable-azure-dev-spaces-using-the-azure-cli"></a>Az Azure Dev Spaces engedélyezése az Azure CLI használatával

Ahhoz, hogy a CLI-t használva engedélyezze a Dev Spacest, a következőre lesz szüksége:
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot][az-portal-create-account].
* [Az Azure CLI telepítette a következőt:][install-cli].
* [Egy AKS-fürt][create-aks-cli] egy [támogatott régióban.][supported-regions]

Az `use-dev-spaces` paranccsal engedélyezze a Dev Spacest az AKS-fürtön, és kövesse az utasításokat.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

A fenti parancs engedélyezi a Dev Spacest a *myResourceGroup csoportban* található *myAKSCluster* fürtön, és létrehoz egy *alapértelmezett* Dev Spaces-teret.

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

A parancs az Azure Dev Spaces parancssori `use-dev-spaces` felületét is telepíti.

## <a name="install-the-client-side-tools"></a>Az ügyféloldali eszközök telepítése

Az Azure Dev Spaces ügyféloldali eszközeivel a helyi gépről kommunikálhat az AKS-fürtön található Dev Spaces-térekkel. Az ügyféloldali eszközök többféleképpen is telepíthetők:

* Az [Visual Studio Code-ban][vscode]telepítse az [Azure Dev Spaces bővítményt.][vscode-extension]
* A [2019 Visual Studio ban telepítse][visual-studio]az Azure Development (Azure-fejlesztés) számítási feladatot.
* Töltse le és telepítse a [Windows,][cli-win] [Mac][cli-mac]vagy [Linux cli-t.][cli-linux]

## <a name="remove-azure-dev-spaces-using-the-azure-cli"></a>Az Azure Dev Spaces eltávolítása az Azure CLI használatával

Ha el szeretné távolítani az Azure Dev Spacest az AKS-fürtből, használja az `azds remove` parancsot.

```azurecli
azds remove -g MyResourceGroup -n MyAKS
```

Az alábbi példakimenet az Azure Dev Spaces *MyAKS-fürtből* való eltávolítását mutatja be.

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Az Azure Dev Spaces használatával létrehozott névterek a számítási feladatokkal együtt maradnak, de a névterekben található új számítási feladatok nem lesznek az Azure Dev Spacesben bevetve. Emellett, ha újraindítja az Azure Dev Spaces által kitervelt meglévő podokat, hibákat láthat. Ezeket a podokat az Azure Dev Spaces-eszközök nélkül kell újra üzembehelyezésre végezni. Ha teljesen el szeretné távolítani az Azure Dev Spacest a fürtből, törölje az összes podot minden olyan névtérből, amelyben engedélyezve volt az Azure Dev Spaces.

## <a name="next-steps"></a>Következő lépések

További információ az Azure Dev Spacesről.

> [!div class="nextstepaction"]
> [Az Azure Dev Spaces működése](../how-dev-spaces-works.md)

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[install-cli]: /cli/azure/install-azure-cli
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
