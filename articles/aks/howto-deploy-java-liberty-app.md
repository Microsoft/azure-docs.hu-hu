---
title: Java-alkalmazás üzembe helyezése Open Liberty vagy WebSphere Liberty szolgáltatással Azure Kubernetes Service (ak) fürtön
description: Java-alkalmazások üzembe helyezése az Open Liberty vagy a WebSphere Liberty szolgáltatással egy Azure Kubernetes Service (ak) fürtön.
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: Java, jakartaee, JavaEE, profil, Open-Liberty, WebSphere-Liberty, AK, kubernetes
ms.openlocfilehash: d0e6f2fea6894378da736ba83a90ee28402ec7f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100007132"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>Java-alkalmazás üzembe helyezése Open Liberty vagy WebSphere Liberty szolgáltatással Azure Kubernetes Service (ak) fürtön

Ez a cikk a következőket mutatja be:  
* A nyílt Liberty vagy a WebSphere Liberty futtatókörnyezetben futtathat Java-, Java EE-, Jakarta EE-vagy profil-alkalmazást.
* Az alkalmazás Docker-rendszerképének létrehozása nyílt Liberty-tárolók használatával.
* A tároló alkalmazás üzembe helyezése egy AK-fürtön az Open Liberty operátor használatával.   

A nyílt Liberty-kezelő leegyszerűsíti a Kubernetes-fürtökön futó alkalmazások üzembe helyezését és felügyeletét. A nyílt Liberty-kezelővel speciális műveleteket is végrehajthat, például nyomkövetési és memóriaképek gyűjtését. 

A nyitott szabadságról a [szabadság projekt megnyitása oldalon](https://openliberty.io/)talál további információt. Az IBM WebSphere Liberty szolgáltatással kapcsolatos további információkért tekintse meg [a WebSphere Liberty-termék oldalát](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Ehhez a cikkhez az Azure CLI legújabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.
* Ha a jelen útmutatóban található parancsokat helyileg futtatja (Azure Cloud Shell helyett):
  * Készítsen elő egy helyi gépet UNIX-szerű operációs rendszerrel (például Ubuntu, macOS, Linux Windows alrendszere).
  * Telepítsen Java SE-implementációt (például [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
  * Telepítse a [Maven](https://maven.apache.org/download.cgi) 3.5.0 vagy újabb verzióját.
  * Telepítse a [Docker](https://docs.docker.com/get-docker/) -t az operációs rendszeréhez.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik.  

Hozzon létre egy *Java-Liberty-Project* nevű erőforráscsoportot az az [Group Create](/cli/azure/group#az_group_create) paranccsal a *eastus* helyen. Ezt az erőforráscsoportot később a Azure Container Registry (ACR) példány és az AK-fürt létrehozásához fogjuk használni. 

```azurecli-interactive
RESOURCE_GROUP_NAME=java-liberty-project
az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-an-acr-instance"></a>ACR-példány létrehozása

Hozzon létre egy ACR-példányt az az [ACR Create](/cli/azure/acr#az_acr_create) paranccsal. A következő példa egy *youruniqueacrname* nevű ACR-példányt hoz létre. Győződjön meg arról, hogy az *youruniqueacrname* egyedi az Azure-on belül.

```azurecli-interactive
REGISTRY_NAME=youruniqueacrname
az acr create --resource-group $RESOURCE_GROUP_NAME --name $REGISTRY_NAME --sku Basic --admin-enabled
```

Rövid idő elteltével a következőt tartalmazó JSON-kimenetnek kell megjelennie:

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>Kapcsolódás az ACR-példányhoz

Be kell jelentkeznie az ACR-példányba, mielőtt leküldi a rendszerképet. Futtassa a következő parancsokat a kapcsolódás ellenőrzéséhez:

```azurecli-interactive
LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

`Login Succeeded`Ha sikeresen bejelentkezett az ACR-példányba, a parancs kimenetének végén látnia kell.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Használja az [az aks create](/cli/azure/aks#az_aks_create) parancsot egy AKS-fürt létrehozásához. A következő példa egy *myAKSCluster* nevű fürtöt hoz létre egy csomóponttal. A művelet végrehajtása több percet is igénybe vehet.

```azurecli-interactive
CLUSTER_NAME=myAKSCluster
az aks create --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --node-count 1 --generate-ssh-keys --enable-managed-identity
```

Néhány perc elteltével a parancs befejeződik, és a fürthöz tartozó JSON-formátumú adatokat adja vissza, beleértve a következőket:

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>Kapcsolódás az AK-fürthöz

A Kubernetes-fürtök kezeléséhez a [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), a Kubernetes parancssori ügyfélprogramot kell használnia. Ha Azure Cloud Shellt használ, `kubectl` már telepítve van. A helyi telepítéshez `kubectl` használja az az [AK install-CLI](/cli/azure/aks#az_aks_install_cli) parancsot:

```azurecli-interactive
az aks install-cli
```

Az [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials) paranccsal konfigurálható `kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --overwrite-existing
```

> [!NOTE]
> A fenti parancs a [Kubernetes konfigurációs fájljának](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)alapértelmezett helyét használja, amely a következő: `~/.kube/config` . Megadhat egy másik helyet a Kubernetes konfigurációs fájljához a *--file* használatával.

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) parancsot a fürtcsomópontok listájának lekéréséhez.

```azurecli-interactive
kubectl get nodes
```

A következő példakimenet az előző lépésekben létrehozott csomópontot mutatja be. Győződjön meg arról, hogy a csomópont állapota *kész*:

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-yyyyyyyyyy   Ready    agent   76s     v1.18.10
```

## <a name="install-open-liberty-operator"></a>A nyílt Liberty-operátor telepítése

Miután létrehozta és csatlakoztatta a fürtöt, a következő parancsok futtatásával telepítse az [Open Liberty operátort](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.0) .

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>Alkalmazás rendszerképének összeállítása

A Liberty-alkalmazás az AK-fürtön való üzembe helyezéséhez és futtatásához tárolóba helyezése az alkalmazást Docker-rendszerképként a [nyílt Liberty-tárolók](https://github.com/OpenLiberty/ci.docker) rendszerképein vagy a [WebSphere Liberty Container-lemezképeken](https://github.com/WASdev/ci.docker).

1. Az útmutató mintakód klónozása. A minta a [githubon](https://github.com/Azure-Samples/open-liberty-on-aks)található.
1. Módosítsa a könyvtárat a `javaee-app-simple-cluster` helyi klónra.
1. Futtassa `mvn clean package` az alkalmazást az alkalmazás előkészítéséhez.
1. Futtassa `mvn liberty:dev` az alkalmazást az alkalmazás teszteléséhez. `The defaultServer server is ready to run a smarter planet.`Ha a művelet sikeres, a parancs kimenetében látnia kell. `CTRL-C`Az alkalmazás leállításához használja a következőt:.
1. Futtassa az alábbi parancsok egyikét az alkalmazás rendszerképének létrehozásához, majd küldje el az ACR-példányba.
   * Az Open Liberty alaprendszerképének használatával kiépítheti az Open Liberty-t egy egyszerű, nyílt forráskódú Java™ futtatókörnyezettel:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME .
     ```

   * Ha az Open Liberty kereskedelmi verzióját szeretné használni, hozzon létre a WebSphere Liberty Base rendszerképpel:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>Alkalmazás üzembe helyezése az AK-fürtön

Kövesse az alábbi lépéseket a Liberty-alkalmazás az AK-fürtön való üzembe helyezéséhez.

1. Hozzon létre egy lekéréses titkot, hogy az AK-fürt hitelesítve legyen a rendszerkép az ACR-példányból való lekéréséhez.

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. Ellenőrizze, hogy az aktuális munkakönyvtár a `javaee-app-simple-cluster` helyi klón-e.
1. Futtassa az alábbi parancsokat a Liberty-alkalmazás üzembe helyezéséhez 3 replikával az AK-fürthöz. A parancs kimenete is beágyazottként jelenik meg.

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-app-simple-cluster"
   cat openlibertyapplication.yaml | sed -e "s/\${Container_Registry_URL}/${LOGIN_SERVER}/g" | sed -e "s/\${REPLICAS}/3/g" | kubectl apply -f -

   openlibertyapplication.openliberty.io/javaee-app-simple-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication javaee-app-simple-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-app-simple-cluster   youruniqueacrname.azurecr.io/javaee-cafe-simple:1.0.0             True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment javaee-app-simple-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-app-simple-cluster   0/3     3            0           20s
   ```

1. Várjon, amíg megjelenik `3/3` az `READY` oszlop alatt és `3` az `AVAILABLE` oszlop alatt, a használatával `CTRL-C` állítsa le a `kubectl` figyelési folyamatot.

### <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor a Kubernetes terheléselosztó szolgáltatás az előtér-szolgáltatást az internethez teszi elérhetővé. Ez a folyamat hosszabb időt is igénybe vehet.

A folyamat állapotának monitorozásához használja [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) parancsot a `--watch` argumentummal.

```azurecli-interactive
kubectl get service javaee-app-simple-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-app-simple-cluster   LoadBalancer   10.0.251.169   52.152.189.57   80:31732/TCP     68s
```

Ha a *külső IP-* cím *függőben* ÁLLAPOTRÓL tényleges nyilvános IP-címről változik, akkor a `CTRL-C` figyelési folyamat leállításához használja a következőt: `kubectl` .

Nyisson meg egy webböngészőt a szolgáltatás külső IP-címére (a `52.152.189.57` fenti példában) az alkalmazás kezdőlapjának megjelenítéséhez. A lap bal felső sarkában megjelenik az alkalmazás-replikák Pod neve. Várjon néhány percet, és frissítse az oldalt, hogy megjelenjen egy másik Pod-név, amely az AK-fürt által biztosított terheléselosztás miatt jelenik meg.

:::image type="content" source="./media/howto-deploy-java-liberty-app/deploy-succeeded.png" alt-text="Java Liberty-alkalmazás sikeresen telepítve az AK-on":::

>[!NOTE]
> - Jelenleg az alkalmazás nem használ HTTPS-t. Javasoljuk, hogy [a TLS-t a saját tanúsítványainak használatával engedélyezze](ingress-own-tls.md).

## <a name="clean-up-the-resources"></a>Az erőforrások eltávolítása

Az Azure-költségek elkerülése érdekében szükségtelen erőforrásokat kell megtisztítani.  Ha a fürtre már nincs szükség, az az [Group delete](/cli/azure/group#az_group_delete) paranccsal távolíthatja el az erőforráscsoportot, a Container Service-t, a tároló-beállításjegyzéket és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME --yes --no-wait
```

## <a name="next-steps"></a>Következő lépések

A jelen útmutatóban használt hivatkozásokból többet is megtudhat:

* [Azure Kubernetes Service](https://azure.microsoft.com/free/services/kubernetes-service/)
* [Szabadság megnyitása](https://openliberty.io/)
* [Szabadság-kezelő megnyitása](https://github.com/OpenLiberty/open-liberty-operator)
* [A Liberty-kiszolgáló konfigurációjának megnyitása](https://openliberty.io/docs/ref/config/)
* [Liberty Maven beépülő modul](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [A Liberty Container-lemezképek megnyitása](https://github.com/OpenLiberty/ci.docker)
* [WebSphere Liberty-tároló lemezképei](https://github.com/WASdev/ci.docker)
