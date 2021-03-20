---
title: Áruházi Helm-diagramok
description: Megtudhatja, hogyan tárolhatja a Kubernetes-alkalmazásokhoz tartozó Helm-diagramokat a Azure Container Registry-Tárházak használatával
ms.topic: article
ms.date: 06/12/2020
ms.openlocfilehash: 69b16f35589586787e1c31a0e9755b9030af755d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86537867"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>A Helm-diagramok leküldése és lekérése egy Azure Container registrybe

A Kubernetes alkalmazások gyors kezeléséhez és üzembe helyezéséhez használhatja a [nyílt forráskódú Helm Package Managert][helm]. A Helm esetében az alkalmazáscsomag olyan [diagramokként](https://helm.sh/docs/topics/charts/)van definiálva, amelyeket a rendszer a [Helm diagram adattárában](https://helm.sh/docs/topics/chart_repository/)gyűjt és tárol.

Ez a cikk bemutatja, hogyan üzemeltetheti a Helm-diagramok tárházait egy Azure Container registryben, a Helm 3 parancsaival. Számos esetben saját diagramokat hozhat létre és tölthet fel a fejlesztés alatt lévő alkalmazásokhoz. A saját Helm-diagramok létrehozásával kapcsolatos további információkért lásd a [diagram sablon fejlesztői útmutatóját][develop-helm-charts]. Egy másik Helm-tárházból is tárolhat egy meglévő Helm-diagramot.

> [!IMPORTANT]
> A Azure Container Registry Helm-diagramok támogatása jelenleg előzetes verzióban érhető el. Az előzetes verziók abban a feltételben érhetők el, hogy Ön elfogadja a kiegészítő [használati feltételeket][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="helm-3-or-helm-2"></a>3. Helm vagy Helm 2?

A Helm-diagramok tárolásához, kezeléséhez és telepítéséhez egy Helm-ügyfelet és a Helm CLI-t használhatja. A Helm-ügyfél főbb kiadásai között a Helm 3 és a Helm 2 is szerepel. A verziók közötti különbségekről a [Gyakori kérdések](https://helm.sh/docs/faq/)című témakörben talál további információt. 

A Helm 3-at a Azure Container Registry-ban található Helm-diagramok üzemeltetésére kell használni. A Helm 3 esetében:

* Létrehozhat egy vagy több Helm-tárházat egy Azure Container registryben
* A [OCI](container-registry-image-formats.md#oci-artifacts)-összetevőket tartalmazó Helm 3 diagramokat egy beállításjegyzékben tárolja. Jelenleg a Helm 3 támogatja a OCI- *t.*
* A paranccsal végezze el a hitelesítést a beállításjegyzék használatával `helm registry login` .
* `helm chart`A Helm parancssori felületén található parancsok használatával leküldheti, lekérheti és kezelheti a-beállításjegyzékben található Helm-diagramokat
* A használatával `helm install` diagramokat telepíthet egy Kubernetes-fürtre egy helyi adattár-gyorsítótárból.
> [!NOTE]
> A Helm 3 esetében az az [ACR Helm][az-acr-helm] parancsai elavultak a Helm 2 ügyféllel való használatra. Tekintse meg a [termék ütemtervét](https://github.com/Azure/acr/blob/master/docs/acr-roadmap.md#acr-helm-ga). Ha korábban már telepítette a Helm 2 diagramot, tekintse meg [a Helm v2 áttelepítése v3-](https://helm.sh/docs/topics/v2_v3_migration/)re című témakört.

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben a következő erőforrásokra van szükség a forgatókönyvhöz:

- Azure **Container Registry** az Azure-előfizetésében. Ha szükséges, hozzon létre egy beállításjegyzéket a [Azure Portal](container-registry-get-started-portal.md) vagy az [Azure CLI](container-registry-get-started-azure-cli.md)használatával.
- A **Helm-ügyfél verziója 3.1.0 vagy újabb** verzióra `helm version` , hogy megtalálja az aktuális verziót. A Helm telepítésével és frissítésével kapcsolatos további információkért lásd: a [Helm telepítése][helm-install].
- **Egy Kubernetes-fürt** , ahol egy Helm-diagramot fog telepíteni. Ha szükséges, hozzon létre egy [Azure Kubernetes Service-fürtöt][aks-quickstart]. 
- A verzió megkereséséhez futtassa az **Azure CLI 2.0.71 vagy újabb verzióját** `az --version` . Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="enable-oci-support"></a>OCI-támogatás engedélyezése

A `helm version` parancs használatával ellenőrizze, hogy telepítette-e a Helm 3 alkalmazást:

```console
helm version
```

Állítsa be az alábbi környezeti változót, hogy engedélyezze a OCI támogatását a Helm 3 ügyfélen. Jelenleg ez a támogatás kísérleti jellegű. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>Minta diagram létrehozása

Hozzon létre egy teszt diagramot az alábbi parancsokkal:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Alapszintű példaként módosítsa a könyvtárat a `templates` mappára, és először törölje a tartalmát:

```console
cd hello-world/templates
rm -rf *
```

A `templates` mappában hozzon létre egy nevű fájlt a `configmap.yaml` következő parancs futtatásával:

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

A példa létrehozásával és futtatásával kapcsolatos további információkért lásd: [első lépések](https://helm.sh/docs/chart_template_guide/getting_started/) a Helm docs-ban.

## <a name="save-chart-to-local-registry-cache"></a>Diagram mentése helyi beállításjegyzék-gyorsítótárba

Módosítsa a könyvtárat az `hello-world` alkönyvtárra. Ezután futtassa a parancsot a `helm chart save` diagram helyi másolatának mentéséhez, és hozzon létre egy aliast a beállításjegyzék teljes nevével, valamint a cél tárházat és a címkét is. 

A következő példában a beállításjegyzék neve *mycontainerregistry*, a cél tárháza a *Hello-World*, a TARGET diagram címkéje pedig *v1*, de a környezet helyettesítő értékei:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

A futtatásával erősítse meg, `helm chart list` hogy mentette a diagramokat a helyi beállításjegyzék-gyorsítótárban. A kimenet a következőhöz hasonló:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

## <a name="authenticate-with-the-registry"></a>Hitelesítés a beállításjegyzékkel

Futtassa a `helm registry login` parancsot a Helm 3 CLI-ben a forgatókönyvhöz megfelelő hitelesítő adatok használatával történő [hitelesítéshez](container-registry-authentication.md) .

Hozzon létre például egy Azure Active Directory [egyszerű szolgáltatást a lekéréses és leküldéses engedélyekkel](container-registry-auth-service-principal.md#create-a-service-principal) (AcrPush szerepkör) a beállításjegyzékbe. Ezután adja meg az egyszerű szolgáltatás hitelesítő adatait a következőhöz: `helm registry login` . A következő példa környezeti változó használatával szolgáltatja a jelszót:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

## <a name="push-chart-to-registry"></a>Diagram leküldése a beállításjegyzékbe

Futtassa a `helm chart push` parancsot a Helm 3 CLI-ben, hogy a diagramot a teljes cél tárházba küldje:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Sikeres leküldést követően a kimenet a következőhöz hasonló:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

## <a name="list-charts-in-the-repository"></a>A tárházban lévő diagramok listázása

Az Azure Container registryben tárolt rendszerképekhez hasonlóan az [az ACR adattár][az-acr-repository] parancsaival is megjelenítheti a diagramokat üzemeltető adattárakat, valamint a diagram címkéit és jegyzékeit. 

Futtassa például az az [ACR adattár show][az-acr-repository-show] parancsot az előző lépésben létrehozott tárház tulajdonságainak megtekintéséhez:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

A kimenet a következőhöz hasonló:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

A tárházban tárolt diagram részleteinek megtekintéséhez futtassa az az [ACR repository show-Manifests][az-acr-repository-show-manifests] parancsot. Például:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

A példában szereplő kimenet a következőt jeleníti meg `configMediaType` `application/vnd.cncf.helm.config.v1+json` :

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

## <a name="pull-chart-to-local-cache"></a>Diagram lekérése a helyi gyorsítótárba

Ha egy Helm-diagramot szeretne Kubernetes telepíteni, a diagramnak a helyi gyorsítótárban kell lennie. Ebben a példában először a futtatásával `helm chart remove` távolítsa el a nevű meglévő helyi diagramot `mycontainerregistry.azurecr.io/helm/hello-world:v1` :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

A futtatásával `helm chart pull` töltse le a diagramot az Azure Container registryből a helyi gyorsítótárba:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

## <a name="export-helm-chart"></a>Helm-diagram exportálása

A diagram további működéséhez exportálja azt egy helyi könyvtárba a használatával `helm chart export` . Exportálja például a következő könyvtárba húzott diagramot `install` :

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

A tárházban található exportált diagram adatainak megtekintéséhez futtassa a `helm show chart` parancsot abban a könyvtárban, ahol a diagramot exportálta.

```console
cd install
helm show chart hello-world
```

A Helm részletes információkat ad vissza a diagram legújabb verziójáról, ahogy az a következő példában látható:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

## <a name="install-helm-chart"></a>A Helm-diagram telepítése

Futtassa `helm install` a parancsot, és telepítse a helyi gyorsítótárba, majd exportálta a Helm-diagramot. Adja meg a kiadás nevét (például *myhelmtest*), vagy adja át a `--generate-name` paramétert. Például:

```console
helm install myhelmtest ./hello-world
```

A sikeres diagram telepítése utáni kimenet a következőhöz hasonló:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

A telepítés ellenőrzéséhez futtassa a `helm get manifest` parancsot. 

```console
helm get manifest myhelmtest
```

A parancs a sablonfájl YAML adatait adja vissza `configmap.yaml` .

Futtassa `helm uninstall` a parancsot a diagram kiadásának eltávolításához a fürtön:

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>Diagram törlése a beállításjegyzékből

Ha törölni szeretne egy diagramot a tároló-beállításjegyzékből, használja az az [ACR repository delete][az-acr-repository-delete] parancsot. Futtassa a következő parancsot, és erősítse meg a műveletet, amikor a rendszer kéri:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="next-steps"></a>Következő lépések

* A Helm-diagramok létrehozásával és telepítésével kapcsolatos további információkért lásd: [Helm-diagramok fejlesztése][develop-helm-charts].
* További információ az alkalmazások az [Azure Kubernetes szolgáltatásban (ak)](../aks/kubernetes-helm.md)történő telepítéséről.
* A Helm-diagramok a tároló-létrehozási folyamat részeként használhatók. További információ: [Azure Container Registry feladatok használata][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[acr-tasks]: container-registry-tasks-overview.md
