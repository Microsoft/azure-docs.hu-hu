---
title: Helm-diagramok tárolása
description: Megtudhatja, hogyan tárolható Helm-diagram a Kubernetes-alkalmazásokhoz adattárak használatával a Azure Container Registry
ms.topic: article
ms.date: 04/15/2021
ms.openlocfilehash: 6698eb8f5e18511717e44bf5dc06a51d8f3903b8
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537321"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Helm-diagramok leküldése és leküldése egy Azure Container Registrybe

A Kubernetes-alkalmazások gyors kezeléséhez és üzembe helyezéséhez használhatja a nyílt forráskódú [Helm-csomagkezelőt.][helm] A Helm segítségével az alkalmazáscsomagok diagramokként vannak [definiálva,](https://helm.sh/docs/topics/charts/)amelyek egy Helm-diagramtárban vannak összegyűjtve és [tárolva.](https://helm.sh/docs/topics/chart_repository/)

Ez a cikk bemutatja, hogyan lehet Helm-diagramokat tartalmazó adattárakat üzembe helyezni egy Azure-beli tároló-beállításjegyzékben Helm 3-parancsokkal. Sok esetben saját diagramokat kell összeépítenie és feltöltenie a saját fejlesztésű alkalmazásokhoz. A saját Helm-diagramok felépítésével kapcsolatos további információkért lásd a diagramsablonok fejlesztői [útmutatóját.][develop-helm-charts] Meglévő Helm-diagramot egy másik Helm-adattárból is tárolhat.

## <a name="helm-3-or-helm-2"></a>Helm 3 vagy Helm 2?

Helm-diagramok tárolására, kezelésére és telepítésére a Helm-ügyfelet és a Helm CLI-t használhatja. A Helm-ügyfél főbb kiadásai közé tartozik a Helm 3 és a Helm 2. A verziók eltéréseit a verzióval kapcsolatos gyakori [kérdések között találhatja](https://helm.sh/docs/faq/)meg. 

A Helm 3-ast a Helm-diagramok gazdagépként való Azure Container Registry. A Helm 3-ban a következőt kell:

* Létrehozhat egy vagy több Helm-adattárat egy Azure Container Registryben
* Helm 3-diagramokat tárol egy regisztrációs adatbázisban [OCI-összetevőkként.](container-registry-image-formats.md#oci-artifacts) Azure Container Registry OCI-összetevők általánosan általánosan [támogatottak,](container-registry-oci-artifacts.md)beleértve a Helm-diagramokat is.
* Hitelesítse magát a beállításjegyzékben az `helm registry login` paranccsal.
* Helm-diagramok leküldése, leküldése és kezelése beállításjegyzékben a Helm parancssori felület `helm chart` parancsai használatával
* A `helm install` használatával diagramokat telepíthet egy Kubernetes-fürtre egy helyi adattár-gyorsítótárból.
> [!NOTE]
> A Helm 3-ban a Helm 2-ügyféllel használható [az acr helm][az-acr-helm] parancsok elavultak. A parancs eltávolítása előtt legalább 3 hónapig értesítést biztosítanak. Ha korábban már üzembe helyezett Helm 2-diagramokat, tekintse meg a [Helm v2 3-asra](https://helm.sh/docs/topics/v2_v3_migration/)való áttelepítését.

## <a name="prerequisites"></a>Előfeltételek

A cikkben említett forgatókönyvhöz a következő erőforrások szükségesek:

- **Egy Azure Container Registry az** Azure-előfizetésben. Ha szükséges, hozzon létre egy regisztrációs adatbázist a [Azure Portal](container-registry-get-started-portal.md) vagy az [Azure CLI használatával.](container-registry-get-started-azure-cli.md)
- **Helm-ügyfél 3.1.0-s** vagy újabb verziója – Futtassa az parancs futtatását `helm version` az aktuális verzió megkereshez. A Helm telepítésével és frissítésével kapcsolatos további információkért lásd: [A Helm telepítése.][helm-install]
- **Egy Kubernetes-fürt,** amelyben telepíteni fog egy Helm-diagramot. Szükség esetén hozzon létre egy [Azure Kubernetes Service fürtöt.][aks-quickstart] 
- **Az Azure CLI 2.0.71-es** vagy újabb verziója – Futtassa a következőt `az --version` a verzió megkereshez: . Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="enable-oci-support"></a>OCI-támogatás engedélyezése

Az `helm version` paranccsal ellenőrizze, hogy telepítette-e a Helm 3-t:

```console
helm version
```

Állítsa be a következő környezeti változót az OCI-támogatás engedélyezéséhez a Helm 3-ügyfélben. Ez a támogatás jelenleg kísérleti jellegű. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>Mintadiagram létrehozása

Hozzon létre egy tesztdiagramot az alábbi parancsokkal:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Alapszintű példaként váltsa át a könyvtárat a `templates` mappára, és először törölje annak tartalmát:

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

További információ a példa létrehozásáról és futtatásáról: [Első lépések a](https://helm.sh/docs/chart_template_guide/getting_started/) Helm-dokumentumokban.

## <a name="save-chart-to-local-registry-cache"></a>Diagram mentése a helyi beállításjegyzék gyorsítótárában

Módosítsa a könyvtárat `hello-world` az alkönyvtárra. Ezután a futtatásával mentse helyileg a diagram másolatát, és hozzon létre egy aliast a beállításjegyzék teljes nevével (csak kisbetűs), valamint a céladattár és `helm chart save` -címke nevével. 

A következő példában a regisztrációs adatbázis neve *mycontainerregistry,* a céladattár a *hello-world*, a céldiagram címkéje pedig *v1*, de helyettesítse be a környezet értékeit:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

A helyi beállításjegyzék gyorsítótárában a diagramok mentésének `helm chart list` megerősítéséhez futtassa a következőt: . A kimenet a következőhöz hasonló:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

## <a name="authenticate-with-the-registry"></a>Hitelesítés a beállításjegyzékben

Futtassa az parancsot a Helm 3 cli-ben a regisztrációs adatbázissal való hitelesítéshez a forgatókönyvnek `helm registry login` megfelelő hitelesítő [](container-registry-authentication.md) adatokkal.

Hozzon létre például egy Azure Active Directory [szolgáltatásnév leküldési](container-registry-auth-service-principal.md#create-a-service-principal) és leküldési engedélyekkel (AcrPush szerepkör) a beállításjegyzékbe. Ezután a szolgáltatásnév hitelesítő adatait kell megadnia `helm registry login` a számára. Az alábbi példa egy környezeti változó használatával ad meg jelszót:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

## <a name="push-chart-to-registry"></a>Diagram leküldése a beállításjegyzékbe

Futtassa a parancsot a Helm 3 cli-ben a diagram teljes céltárba `helm chart push` való leküldéshez:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Sikeres leküldés után a kimenet a következőre hasonlít:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

## <a name="list-charts-in-the-repository"></a>Listadiagramok az adattárban

Az Azure Container Registryben tárolt rendszerképekhez hasonló módon az [az acr repository][az-acr-repository] parancsokkal is meg lehet mutatni a diagramokat üzemeltető adattárakat, valamint a diagramcímkéket és jegyzékfájlokat. 

Futtassa például [az az acr repository show][az-acr-repository-show] parancsot az előző lépésben létrehozott adattár tulajdonságainak a megtekintése:

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

Futtassa [az az acr repository show-manifests][az-acr-repository-show-manifests] parancsot az adattárban tárolt diagram részleteinek megtekintése. Például:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

Az ebben a példában rövidített kimenet a következőt jeleníti `configMediaType` `application/vnd.cncf.helm.config.v1+json` meg:

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

## <a name="pull-chart-to-local-cache"></a>Diagram lekértése a helyi gyorsítótárba

Egy Helm-diagram Kubernetesben való telepítéséhez a diagramnak a helyi gyorsítótárban kell lennie. Ebben a példában először futtassa a `helm chart remove` következőt a nevű meglévő helyi diagram eltávolításához: `mycontainerregistry.azurecr.io/helm/hello-world:v1`

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

A `helm chart pull` futtatásával töltse le a diagramot az Azure Container Registryből a helyi gyorsítótárba:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

## <a name="export-helm-chart"></a>Helm-diagram exportálása

A diagram további felhasználásával exportálja azt egy helyi könyvtárba a `helm chart export` használatával. Exportálja például a könyvtárba lekért `install` diagramot:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Az adattárban az exportált diagram információinak megtekintéséhez futtassa az parancsot abban a könyvtárban, `helm show chart` amelybe a diagramot exportálta.

```console
cd install
helm show chart hello-world
```

A Helm részletes információkat ad vissza a diagram legújabb verziójáról, az alábbi kimenetben látható módon:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

## <a name="install-helm-chart"></a>Helm-diagram telepítése

A helyi gyorsítótárba lekért és exportált Helm-diagram telepítéséhez futtassa `helm install` a következőt: . Adjon meg egy kiadási nevet, például *myhelmtest,* vagy adja át a `--generate-name` paramétert. Például:

```console
helm install myhelmtest ./hello-world
```

A sikeres diagramtelepítés utáni kimenet az alábbihoz hasonló:

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

A parancs a sablonfájlban található `configmap.yaml` YAML-adatokat adja vissza.

A `helm uninstall` fürtön a diagram kiadásának eltávolításához futtassa a következőt:

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>Diagram törlése a beállításjegyzékből

Ha törölni szeretne egy diagramot a tároló-beállításjegyzékből, használja az [az acr repository delete parancsot.][az-acr-repository-delete] Futtassa a következő parancsot, és amikor a rendszer kéri, erősítse meg a műveletet:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="next-steps"></a>Következő lépések

* További információ a Helm-diagramok létrehozásáról és üzembe helyezéséről: [Helm-diagramok fejlesztése.][develop-helm-charts]
* További információ az alkalmazások Helm-sel való telepítéséről [a Azure Kubernetes Service (AKS) alkalmazásban.](../aks/kubernetes-helm.md)
* A Helm-diagramok a tárolók buildfolyamatának részeként is használhatók. További információ: [Use Azure Container Registry-feladatok.][acr-tasks]

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
