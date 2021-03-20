---
title: Kubernetes-fürt létrehozása az Azure dev Spaces szolgáltatással engedélyezve – Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Megtudhatja, hogyan hozhat létre gyorsan egy Kubernetes-fürtöt az Azure dev Spaces szolgáltatáshoz közvetlenül a böngészőből anélkül, hogy bármit kellene telepítenie.
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
ms.openlocfilehash: ce73f46a2451dfa87751d90f9cd6b31d5c80683f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91963550"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Kubernetes-fürt létrehozása az Azure dev Spaces szolgáltatással Azure Cloud Shell

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

A [Azure Cloud Shell](/azure/cloud-shell) használatával létrehozhat egy Azure Kubernetes Service-fürtöt az oldal **kipróbálása** gombjának használatával. Ha nincs bejelentkezve, kövesse az utasításokat az Azure-fiókkal való bejelentkezéshez, majd írja be a parancsokat a Azure Cloud Shell üzenet megjelenésekor.

## <a name="create-the-cluster"></a>A fürt létrehozása

Először hozza létre az erőforráscsoportot egy olyan [régióban, amely támogatja az Azure dev Spaces használatát][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Hozzon létre egy Kubernetes-fürtöt az alábbi paranccsal:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

A fürt létrehozása néhány percet vesz igénybe.  Ha elkészült, a kimenet JSON formátumban jelenik meg. Keresse meg és ellenőrizze a következőt: `provisioningState` `Succeeded` .

## <a name="next-steps"></a>Következő lépések

A teljes oktatóanyagokra mutató hivatkozásokat az [Azure dev Spaces](../index.yml) című részben tekintheti meg.

> [!IMPORTANT]
> Az Azure dev Spaces rövid útmutatók és oktatóanyagok az Azure dev Spaces CLI használatával végeznek műveleteket. Az Azure dev Spaces CLI nem telepíthető a Azure Cloud Shell.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
