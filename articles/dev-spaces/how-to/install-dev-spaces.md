---
title: Az Azure dev Spaces használatának engedélyezése az AK-ban & az ügyféloldali eszközök telepítése
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Ismerje meg, hogyan engedélyezheti az Azure dev Spaces szolgáltatást egy AK-fürtön, és hogyan telepítheti az ügyféloldali eszközöket.
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
ms.openlocfilehash: 177496a53d204306b2b655b8736ce063dedf0f61
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202246"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Az Azure dev-helyek engedélyezése egy AK-fürtön és az ügyféloldali eszközök telepítése

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Ebből a cikkből megtudhatja, hogyan engedélyezheti az Azure dev Spaces szolgáltatást egy AK-fürtön, valamint telepítheti az ügyféloldali eszközöket.

## <a name="enable-azure-dev-spaces-using-the-cli"></a>Az Azure dev Spaces használatának engedélyezése a parancssori felületről

Ahhoz, hogy a parancssori felületről engedélyezzék a dev Spaces használatát, a következőkre lesz szüksége:
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot][az-portal-create-account].
* [Az Azure CLI telepítve van][install-cli].
* Egy [AK-fürt][create-aks-cli] egy [támogatott régióban][supported-regions].

A `use-dev-spaces` parancs használatával engedélyezze a fejlesztői szóközöket az AK-fürtön, és kövesse az utasításokat.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

A fenti parancs lehetővé teszi a dev Spaces használatát a *myAKSCluster* -fürtön a *myResourceGroup* csoportban, és létrehoz egy *alapértelmezett* fejlesztői helyet.

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

A `use-dev-spaces` parancs az Azure dev Spaces CLI-t is telepíti.

## <a name="install-the-client-side-tools"></a>Az ügyféloldali eszközök telepítése

Az Azure dev Spaces ügyféloldali eszközeivel a helyi gépről a fejlesztői tereket egy AK-fürtön keresztül használhatja. Az ügyféloldali eszközök több módon is telepíthetők:

* A [Visual Studio Code][vscode]-ban telepítse az [Azure dev Spaces bővítményt][vscode-extension].
* A [Visual Studio 2019][visual-studio]-es verziójában telepítse az Azure-fejlesztési számítási feladatot.
* Töltse le és telepítse a [Windows][cli-win], [Mac][cli-mac]vagy [Linux][cli-linux] parancssori felületet.

## <a name="remove-azure-dev-spaces-using-the-cli"></a>Az Azure dev Spaces eltávolítása a parancssori felületről

Ha el szeretné távolítani az Azure dev-helyeket az AK-fürtből, használja az `azds remove` parancsot.

```azurecli
azds remove -g MyResourceGroup -n MyAKS
```

Az alábbi példában a kimenetben az Azure dev Spaces eltávolítása látható a *MyAKS* -fürtből.

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Az Azure dev Spaces szolgáltatással létrehozott névterek a számítási feladatokkal együtt maradnak, de ezekben a névterekben az új munkaterhelések nem lesznek kialakítva az Azure dev Spaces szolgáltatással. Továbbá, ha újraindítja az Azure dev Spaces szolgáltatással létrehozott meglévő hüvelyeket, hibákat tapasztalhat. Ezeket a hüvelyeket az Azure dev Spaces-eszközök nélkül kell újratelepíteni. Ha teljes mértékben el szeretné távolítani az Azure dev-helyeket a fürtből, törölje az összes olyan névtér összes hüvelyét, amelyben az Azure dev Spaces engedélyezve volt.

## <a name="next-steps"></a>Következő lépések

További információ az Azure dev Spaces működéséről.

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
