---
title: Nyelvfelismerés tároló futtatása a Kubernetes szolgáltatásban
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Üzembe helyezheti a nyelvfelismerés tárolót egy futó mintával az Azure Kubernetes szolgáltatásban, és tesztelheti egy böngészőben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6918218d8434c06f59b0738e60cad53b94b0a0b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939838"
---
# <a name="deploy-the-text-analytics-language-detection-container-to-azure-kubernetes-service"></a>A Text Analytics nyelvfelismerés tároló üzembe helyezése az Azure Kubernetes Service-ben

Megtudhatja, hogyan helyezheti üzembe a nyelvfelismerés-tárolót. Ez az eljárás bemutatja, hogyan hozza létre a helyi Docker-tárolókat, leküldheti a tárolókat a saját privát tároló-beállításjegyzékbe, futtathatja a tárolót egy Kubernetes-fürtben, és tesztelheti azt egy böngészőben.

## <a name="prerequisites"></a>Előfeltételek

Ennek az eljárásnak számos olyan eszközre van szüksége, amelyet helyileg kell telepíteni és futtatni. Ne használja az Azure Cloud shellt.

* Azure-előfizetés használata. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services).
* [Git](https://git-scm.com/downloads) az operációs rendszerhez, így az eljárásban használt [minta](https://github.com/Azure-Samples/cognitive-services-containers-samples) klónozása is megtörténik.
* [Azure CLI](/cli/azure/install-azure-cli)-vel.
* A [Docker-motort](https://www.docker.com/products/docker-engine) , és ellenőrizze, hogy a Docker CLI működik-e a konzol ablakban.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* Egy megfelelő árképzési szintű Azure-erőforrás. Nem minden díjszabási csomag működik ezzel a tárolóval:
  * Csak F0 vagy standard árképzési szinttel rendelkező erőforrást **text Analytics** .
  * **Cognitive Services** az erőforrást a S0 díjszabási szintjével.

## <a name="running-the-sample"></a>A minta futtatása

Ez az eljárás betölti és futtatja a Cognitive Services tároló mintát a nyelvfelismerés számára. A mintának két tárolója van, egyet az ügyfélalkalmazás és egyet a Cognitive Services tárolóhoz. Mindkét rendszerképet leküldjük a Azure Container Registry. Ha a saját beállításjegyzékében vannak, hozzon létre egy Azure Kubernetes-szolgáltatást a lemezképek eléréséhez és a tárolók futtatásához. Ha a tárolók futnak, a **kubectl** CLI használatával tekintheti meg a tárolók teljesítményét. Nyissa meg az ügyfélalkalmazás HTTP-kérelemmel, és tekintse meg az eredményeket.

![A minta tárolók futtatásának fogalmi ötlete](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>A minta tárolók

A mintának két tároló képe van, egyet a előtér-webhelyhez. A második rendszerkép a nyelvfelismerés-tároló, amely az észlelt nyelvet (kulturális környezetét) adja vissza. Ha elkészült, mindkét tároló elérhető külső IP-címről.

### <a name="the-language-frontend-container"></a>A Language-frontend tároló

Ez a webhely egyenértékű a saját ügyféloldali alkalmazásával, amely a nyelvfelismerés-végpontra vonatkozó kéréseket tesz lehetővé. Ha az eljárás elkészült, egy karakterlánc felderített nyelvét kapja meg egy böngészőben a alkalmazásban a webhely tárolóban `http://<external-IP>/<text-to-analyze>` . Példa erre az URL-címre `http://132.12.23.255/helloworld!` . Az eredmény a böngészőben `English` .

### <a name="the-language-container"></a>A nyelvi tároló

A nyelvfelismerés-tároló ebben az adott eljárásban bármely külső kérelem számára elérhető. A tároló semmilyen módon nem változott, így a standard Cognitive Services Container-specifikus nyelvi észlelési API elérhető.

Ebben a tárolóban ez az API egy, a nyelvfelismerés utáni kérelem. Ahogy az összes Cognitive Services tárolóhoz hasonlóan, további információkat tudhat meg a tárolóról a kihelyezett hencegő adatairól `http://<external-IP>:5000/swagger/index.html` .

Az 5000-es port a Cognitive Services tárolók által használt alapértelmezett port.

## <a name="create-azure-container-registry-service"></a>Azure Container Registry szolgáltatás létrehozása

Ha a tárolót az Azure Kubernetes szolgáltatásban szeretné üzembe helyezni, a tároló rendszerképeinek elérhetőnek kell lenniük. Hozzon létre saját Azure Container Registry szolgáltatást a lemezképek üzemeltetéséhez.

1. Bejelentkezés az Azure CLI-be

    ```azurecli-interactive
    az login
    ```

1. Hozzon létre egy nevű erőforráscsoportot `cogserv-container-rg` az ebben az eljárásban létrehozott összes erőforrás tárolásához.

    ```azurecli-interactive
    az group create --name cogserv-container-rg --location westus
    ```

1. Hozza létre saját Azure Container Registry a neve formátumával `registry` , például: `pattyregistry` . Ne használjon kötőjelet vagy aláhúzás karaktert a névben.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Mentse az eredményeket a **lekéréséhez** tulajdonság beszerzéséhez. Ez lesz a tárolt tároló címe, amelyet később a `language.yml` fájl használ.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    ```output
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. Jelentkezzen be a tároló-beállításjegyzékbe. Be kell jelentkeznie, mielőtt leküldi a lemezképeket a beállításjegyzékbe.

    ```azurecli-interactive
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>A webhely Docker-rendszerképének beolvasása

1. Az eljárásban használt mintakód a Cognitive Services containers Samples repositoryban található. A tárház klónozásával a minta helyi másolatát is elvégezheti.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Miután a tárház a helyi számítógépen található, keresse meg a webhelyet a [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) könyvtárban. Ez a webhely a nyelvfelismerés-tárolóban üzemeltetett nyelvfelismerés API-t hívó ügyfélalkalmazásként funkcionál.  

1. Hozzon létre egy Docker-rendszerképet ehhez a webhelyhez. Győződjön meg arról, hogy a konzol abban a [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) könyvtárban van, ahol a Docker található, amikor a következő parancsot futtatja:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    A tároló-beállításjegyzék verziójának nyomon követéséhez adja hozzá a címkét a verzió formátumához, például: `v1` .

1. Küldje le a rendszerképet a tároló-beállításjegyzékbe. Ez eltarthat néhány percig.

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Ha `unauthorized: authentication required` hibaüzenetet kap, jelentkezzen be a `az acr login --name <your-container-registry-name>` paranccsal. 

    A folyamat elvégzése után az eredmények a következőhöz hasonlóak:

    ```output
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>Nyelvfelismerés Docker-rendszerkép lekérése

1. A Docker-rendszerkép legújabb verziójának lekérése a helyi gépre. Ez eltarthat néhány percig. Ha a tároló újabb verziója van, módosítsa az értéket az `1.1.006770001-amd64-preview` újabb verzióra.

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. A rendszerkép címkézése a tároló beállításjegyzékével. Keresse meg a legújabb verziót, és cserélje le a verziót, `1.1.006770001-amd64-preview` Ha újabb verzióval rendelkezik. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Küldje le a rendszerképet a tároló-beállításjegyzékbe. Ez eltarthat néhány percig.

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Container Registry hitelesítő adatok beolvasása

A következő lépések szükségesek ahhoz, hogy a tároló-beállításjegyzéket az eljárás későbbi részében létrehozott Azure Kubernetes szolgáltatással lehessen összekapcsolásához szükséges információk beszerzéséhez.

1. Egyszerű szolgáltatásnév létrehozása.

    ```azurecli-interactive
    az ad sp create-for-rbac --skip-assignment
    ```

    Mentse a `appId` befoglaló paraméter eredmény értékét a 3. lépésben `<appId>` . Mentse a `password` következő szakasz Client-Secret paraméterét `<client-secret>` .

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Szerezze be a tároló beállításjegyzék-AZONOSÍTÓját.

    ```azurecli-interactive
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Mentse a hatókör-paraméter értékének kimenetét a `<acrId>` következő lépésben. A következőképpen néz ki:

    ```output
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Mentse a szakasz 3. lépésének teljes értékét.

1. Hozzon létre egy szerepkör-hozzárendelést ahhoz, hogy megfelelő hozzáférést biztosítson az AK-fürt számára a tároló beállításjegyzékében tárolt rendszerképek használatához. Cserélje le az `<appId>` és az `<acrId>` értéket az előző két lépésben összegyűjtött értékekre.

    ```azurecli-interactive
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Azure Kubernetes szolgáltatás létrehozása

1. Hozza létre a Kubernetes-fürtöt. Az összes paraméter értéke az előző szakaszban szerepel, kivéve a name paramétert. Válasszon egy nevet, amely azt jelzi, hogy ki hozta létre, és a célja, például: `patty-kube` .

    ```azurecli-interactive
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Ez a lépés néhány percet is igénybe vehet. Az eredmény a következőket eredményezi:

    ```output
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    A szolgáltatás létrejött, de még nem rendelkezik a webhely-tárolóval vagy a nyelvfelismerés-tárolóval.  

1. A Kubernetes-fürt hitelesítő adatainak beolvasása.

    ```azurecli-interactive
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>A Kubernetes szolgáltatásba betöltheti a hangszerelési definíciót

Ez a szakasz a **kubectl** CLI használatával kommunikál az Azure Kubernetes szolgáltatással.

1. A hangszerelési definíció betöltése előtt győződjön meg arról, hogy a **kubectl** hozzáfér a csomópontokhoz.

    ```console
    kubectl get nodes
    ```

    A válasz a következőre hasonlít:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Másolja a következő fájlt, és nevezze el `language.yml` . A fájl tartalmaz egy `service` szakaszt és egy `deployment` szakaszt a két típusú tárolóhoz, a `language-frontend` webhely-tárolóhoz és az `language` észlelési tárolóhoz.

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. A következő táblázat alapján módosítsa a Language-frontend központi telepítési sorokat a `language.yml` saját tároló beállításjegyzék-rendszerkép nevének, az ügyfél titkos kódjának és a szöveges elemzési beállításoknak a megadásához.

    Nyelv – előtér-telepítési beállítások|Cél|
    |--|--|
    |32. sor<br> `image` tulajdonság|Rendszerképek helye a Container Registry<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |44. sor<br> `name` tulajdonság|Container Registry a rendszerkép titkát, amelyet `<client-secret>` egy korábbi szakaszban is említett.|

1. Módosítsa a nyelvi telepítési sorokat a `language.yml` következő táblázat alapján, hogy hozzáadja a saját tároló beállításjegyzék-rendszerképének nevét, az ügyfél titkos kulcsát és a szöveges elemzési beállításokat.

    |Nyelvi telepítési beállítások|Cél|
    |--|--|
    |78. sor<br> `image` tulajdonság|A Container Registryban található nyelvi rendszerkép rendszerképének helye<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |95. sor<br> `name` tulajdonság|Container Registry a rendszerkép titkát, amelyet `<client-secret>` egy korábbi szakaszban is említett.|
    |91. sor<br> `apiKey` tulajdonság|A Text Analytics-erőforrás kulcsa|
    |92. sor<br> `billing` tulajdonság|A szöveges elemzési erőforrás számlázási végpontja.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    Mivel a **apiKey** és a **Számlázási végpont** a Kubernetes-előkészítési definíció részeként van beállítva, a webhely-tárolónak nem kell tudnia ezeket, vagy át kell adnia őket a kérelem részeként. A webhely-tároló a Orchestrator neve alapján a nyelvfelismerés-tárolóra hivatkozik `language` .

1. Töltse be a minta előkészítési definíciós fájlját abban a mappában, ahová a fájlt létrehozta és mentette `language.yml` .

    ```console
    kubectl apply -f language.yml
    ```

    A válasz:

    ```output
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Külső IP-címek beolvasása tárolók számára

A két tároló esetében ellenőrizze, hogy futnak-e a `language-frontend` és a `language` szolgáltatások, és kérje le a külső IP-címet.

```console
kubectl get all
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

Ha a `EXTERNAL-IP` szolgáltatáshoz függőben állapot jelenik meg, futtassa újra a parancsot, amíg az IP-cím meg nem jelenik a következő lépésre való áttérés előtt.

## <a name="test-the-language-detection-container"></a>Nyelvfelismerés-tároló tesztelése

Nyisson meg egy böngészőt, és navigáljon a tároló külső IP-címéhez `language` az előző szakaszban: `http://<external-ip>:5000/swagger/index.html` . Az `Try it` API funkciója segítségével tesztelheti a nyelvfelismerés végpontját.

![A tároló felvágási dokumentációjának megtekintése](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Az ügyfélalkalmazás tárolójának tesztelése

Módosítsa a böngészőben található URL-címet a tároló külső IP-címére `language-frontend` a következő formátumban: `http://<external-ip>/helloworld` . Az angol kulturális szöveg a `helloworld` következőképpen van megjósolva: `English` .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a fürttel, törölje az Azure-erőforráscsoportot.

```azurecli-interactive
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Kapcsolódó információk

* [kubectl a Docker-felhasználók számára](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Cognitive Services-tárolók](../cognitive-services-container-support.md)
