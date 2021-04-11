---
title: Fürthöz való kapcsolódás használata az Azure arc-kompatibilis Kubernetes-fürtökhöz való kapcsolódáshoz
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Fürthöz való kapcsolódás használata az Azure arc-kompatibilis Kubernetes-fürtökhöz való biztonságos kapcsolódáshoz
ms.openlocfilehash: c6b6555c7d18c0aa0d2e7c94ad2c32353da19502
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451029"
---
# <a name="use-cluster-connect-to-connect-to-azure-arc-enabled-kubernetes-clusters"></a>Fürthöz való kapcsolódás használata az Azure arc-kompatibilis Kubernetes-fürtökhöz való kapcsolódáshoz

A fürt csatlakoztatásával biztonságosan kapcsolódhat az Azure arc-kompatibilis Kubernetes-fürtökhöz anélkül, hogy engedélyezni kellene a tűzfalon a bejövő portok engedélyezését. Az arc- `apiserver` kompatibilis Kubernetes-fürthöz való hozzáférés a következő forgatókönyveket teszi lehetővé:
* Engedélyezze az interaktív hibakeresést és hibaelhárítást.
* Fürthöz való hozzáférést biztosíthat az Azure-szolgáltatásokhoz [Egyéni helyszínekhez](custom-locations.md) és a rajta létrehozott egyéb erőforrásokhoz.

A szolgáltatás fogalmi áttekintése elérhető a [cluster csatlakozás – Azure arc-kompatibilis Kubernetes](conceptual-cluster-connect.md) cikkben.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Előfeltételek   

- Az [Azure CLI telepítése vagy frissítése](https://docs.microsoft.com/cli/azure/install-azure-cli) a következő verzióra: >= 2.16.0

- Telepítse az `connectedk8s` >= 1.1.0 verzióhoz tartozó Azure CLI-bővítményt:

    ```azurecli
    az extension add --name connectedk8s
    ```
  
    Ha már telepítette a `connectedk8s` bővítményt, frissítse a bővítményt a legújabb verzióra:
    
    ```azurecli
    az extension update --name connectedk8s
    ```

- Egy meglévő Azure arc-kompatibilis Kubernetes csatlakoztatott fürt.
    - Ha még nem csatlakoztatta a fürtöt, használja a gyors üzembe helyezési [útmutatót.](quickstart-connect-cluster.md)
    - [Frissítse az ügynököket](agent-upgrade.md#manually-upgrade-agents) a >= 1.1.0 verzióra.

- Engedélyezze a fürt kapcsolódását bármely Azure arc-kompatibilis Kubernetes-fürtön úgy, hogy futtatja a következő parancsot egy olyan gépen, ahol a `kubeconfig` fájl a problémás fürtre mutat:

    ```azurecli
    az connectedk8s enable-features --features cluster-connect -n <clusterName> -g <resourceGroupName>
    ```

- Engedélyezze az alábbi végpontokat a kimenő hozzáféréshez, továbbá a [Kubernetes-fürt csatlakoztatása az Azure-hoz](quickstart-connect-cluster.md#meet-network-requirements):

    | Végpont | Port |
    |----------------|-------|
    |`*.servicebus.windows.net` | 443 |
    |`*.guestnotificationservice.azure.com` | 443 |

## <a name="usage"></a>Használat

A fürt csatlakozási funkciója két hitelesítési lehetőséget támogat: 
* Azure Active Directory (Azure AD) 
* Szolgáltatásfiók-jogkivonat

### <a name="option-1-azure-active-directory"></a>1. lehetőség: Azure Active Directory

1. Ha a `kubeconfig` fájl a `apiserver` Kubernetes-fürtre mutat, hozzon létre egy ClusterRoleBinding vagy RoleBinding az Azure ad-entitás (egyszerű szolgáltatásnév vagy felhasználó) számára, amely hozzáférést igényel:

    **Felhasználó:**
    
    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<testuser>@<mytenant.onmicrosoft.com>
    ```

    **Azure AD-alkalmazáshoz:**

    1. Szerezze `objectId` be a hozzá tartozó Azure ad-alkalmazást:

        ```azurecli
        az ad sp show --id <id> --query objectId -o tsv
        ```

    1. Hozzon létre egy ClusterRoleBinding vagy RoleBinding a fürt eléréséhez szükséges Azure AD-entitáshoz (egyszerű szolgáltatásnév vagy felhasználó):
       
        ```console
        kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<objectId>
        ```

1. Miután bejelentkezett az Azure CLI-be az érdeklődésre számot tartó Azure AD-entitás használatával, kérje le a fürt kapcsolódását, `kubeconfig` hogy bárhonnan kommunikáljon a fürttel (akár a fürt körüli tűzfalon kívül is):

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name>
    ```

1. A következő paranccsal `kubectl` küldhet kérelmeket a fürtnek:

    ```console
    kubectl get pods
    ```
    
    Ekkor meg kell jelennie a névtérben található összes hüvely listáját tartalmazó fürt válaszának `default` .

### <a name="option-2-service-account-bearer-token"></a>2. lehetőség: szolgáltatásfiók tulajdonosi jogkivonata

1. Ha a `kubeconfig` fájl a `apiserver` Kubernetes-fürtre mutat, hozzon létre egy szolgáltatásfiókot bármely névtérben (az alábbi parancs az alapértelmezett névtérben hozza létre):

    ```console
    kubectl create serviceaccount admin-user
    ```

1. Hozzon létre ClusterRoleBinding vagy RoleBinding, és adja [meg a megfelelő engedélyeket a szolgáltatásnak a fürthöz](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding):

    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
    ```

1. Szerezze be a szolgáltatásfiók tokenjét a következő parancsok használatával

    ```console
    SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
    ```

    ```console
    TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
    ```

1. Kérje le a fürt kapcsolódását, `kubeconfig` hogy bárhonnan kommunikáljon a fürttel (akár a fürt körüli tűzfalon kívül is):

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name> --token $TOKEN
    ```

1. A következő paranccsal `kubectl` küldhet kérelmeket a fürtnek:

    ```console
    kubectl get pods
    ```

    Ekkor meg kell jelennie a névtérben található összes hüvely listáját tartalmazó fürt válaszának `default` .

## <a name="known-limitations"></a>Ismert korlátozások

Ha a Kubernetes-fürtnek küldött kéréseket, ha az Azure AD-entitás több mint 200 csoporthoz tartozik, a rendszer a következő hibát észleli, mivel ez egy ismert korlátozás:

```console
You must be logged in to the server (Error:Error while retrieving group info. Error:Overage claim (users with more than 200 group membership) is currently not supported. 
```

A hiba múltbeli lépései:
1. Hozzon létre egy [egyszerű szolgáltatást](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli), amely kevésbé valószínű, hogy több mint 200 csoport tagja.
1. A parancs futtatása előtt jelentkezzen be az Azure CLI [-be](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal) az egyszerű szolgáltatással `az connectedk8s proxy` .

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure ad-RBAC](azure-rbac.md) beállítása a fürtökön