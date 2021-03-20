---
title: Hozzáférés Container Instances
description: Megtudhatja, hogyan biztosíthat hozzáférést a saját tároló-beállításjegyzékben található lemezképekhez Azure Container Instances egy Azure Active Directory egyszerű szolgáltatásnév használatával.
ms.topic: article
ms.date: 04/23/2018
ms.openlocfilehash: b1bc8119c495dea99c6bdc4923db198d041a1e9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74456519"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Hitelesítés Azure Container Registry Azure Container Instances

Az Azure Active Directory (Azure AD) egyszerű szolgáltatásnév használatával hozzáférést biztosíthat a privát tároló-beállításjegyzékhez Azure Container Registryban.

Ebből a cikkből megtudhatja, hogyan hozhat létre és konfigurálhat egy Azure AD-egyszerű szolgáltatást a beállításjegyzékhez a *lekéréses* engedélyekkel. Ezután indítson el egy Azure Container Instances (ACI) tárolót, amely a saját beállításjegyzékből kéri le a rendszerképet a hitelesítéshez az egyszerű szolgáltatásnév használatával.

## <a name="when-to-use-a-service-principal"></a>Mikor kell szolgáltatásnevet használni

Az ACI-hoz való hitelesítéshez használjon egy egyszerű szolgáltatásnevet a **fej nélküli forgatókönyvekben**, például olyan alkalmazásokban vagy szolgáltatásokban, amelyek tároló-példányokat hoznak létre automatizált vagy más módon felügyelet nélkül.

Ha például egy olyan automatizált szkripttel rendelkezik, amely éjjel fut, és egy [feladat-alapú tároló-példányt](../container-instances/container-instances-restart-policy.md) hoz létre egy adott adat feldolgozásához, akkor a csak lekéréses engedélyekkel rendelkező szolgáltatásnév használatával végezheti el a hitelesítést a beállításjegyzékben. Ezután elforgathatja a szolgáltatásnév hitelesítő adatait, vagy teljesen visszavonhatja a hozzáférését anélkül, hogy ez hatással lenne más szolgáltatásokra és alkalmazásokra.

Az egyszerű szolgáltatásokat akkor is használni kell, ha a beállításjegyzék [rendszergazdai felhasználója](container-registry-authentication.md#admin-account) le van tiltva.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Hitelesítés az egyszerű szolgáltatásnév használatával

Ha Azure Container Instances tárolót szeretne elindítani egy egyszerű szolgáltatásnév használatával, a () AZONOSÍTÓját és a hozzá tartozó jelszót kell megadnia `--registry-username` `--registry-password` .

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="sample-scripts"></a>Mintaparancsfájlok

Az előző minta parancsfájlokat az Azure CLI-hez a GitHubon, valamint a Azure PowerShell-verziókhoz is megtalálhatja:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Következő lépések

A következő cikkek további részleteket tartalmaznak az egyszerű szolgáltatások és az ACR használatáról:

* [Azure Container Registry hitelesítés egyszerű szolgáltatásokkal](container-registry-auth-service-principal.md)
* [Hitelesítés Azure Container Registry az Azure Kubernetes Service-ből (ak)](../aks/cluster-container-registry-integration.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
