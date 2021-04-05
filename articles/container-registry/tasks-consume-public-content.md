---
title: A nyilvános beállításjegyzék tartalmának kezelésére szolgáló feladat munkafolyamata
description: Hozzon létre egy automatizált Azure Container Registry feladatok munkafolyamatot, amellyel nyomon követheti, kezelheti és felhasználhatja a nyilvános rendszerkép tartalmát egy privát Azure Container registryben.
author: SteveLasker
ms.topic: article
ms.author: stevelas
ms.date: 10/29/2020
ms.custom: ''
ms.openlocfilehash: 4fba6290b4973e797c13943fc9be4fadb19f3274
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96349282"
---
# <a name="how-to-consume-and-maintain-public-content-with-azure-container-registry-tasks"></a>Nyilvános tartalmak felhasználása és karbantartása Azure Container Registry feladatokkal

Ez a cikk egy minta munkafolyamatot tartalmaz Azure Container Registry a nyilvános tartalmak felhasználásának és karbantartásának kezelése érdekében:

1. A függő nyilvános lemezképek helyi másolatainak importálása.
1. A nyilvános rendszerképek ellenőrzése a biztonsági ellenőrzés és a funkcionális tesztelés segítségével.
1. Népszerűsítse a lemezképeket a belső használatra szolgáló privát beállításjegyzékekben.
1. A nyilvános tartalomtól függő alkalmazások alapszintű rendszerkép-frissítéseinek elindítása.
1. A munkafolyamat automatizálásához [Azure Container Registry feladatok](container-registry-tasks-overview.md) használhatók.

A munkafolyamat összegzése a következő képen látható:

![Nyilvános tartalom munkafolyamata](./media/tasks-consume-public-content/consuming-public-content-workflow.png)

A kezdeményezett importálási munkafolyamat segíti a szervezet függőségeit a külsőleg felügyelt összetevőkön – például a nyilvános beállításjegyzékből forrásozott lemezképek, például a [Docker hub][docker-hub], a [GCR][gcr], a [Quay][quay], a [GitHub Container Registry][ghcr], a [Microsoft Container Registry][mcr], vagy akár más [Azure Container-jegyzékek][acr]esetében is. 

A nyilvános tartalommal kapcsolatos függőségek által bevezetett kockázatokról, valamint a Azure Container Registry használatának enyhítéséről a nyilvános tartalmak [OCI fogyasztása][oci-consuming-public-content] és a [nyilvános tartalmak kezelése a Azure Container Registry](buffer-gate-public-content.md)használatával című cikk nyújt tájékoztatást.

Az útmutató végrehajtásához használhatja az Azure CLI Azure Cloud Shell vagy helyi telepítését. Az Azure CLI 2,10-es vagy újabb verziója ajánlott. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-cli].

## <a name="scenario-overview"></a>A forgatókönyv áttekintése

![munkafolyamat-összetevők importálása](./media/tasks-consume-public-content/consuming-public-content-objects.png)

Ez az útmutató a következőket állítja be:

1. Három **tároló-nyilvántartó**, amely a következőket jelöli:
   * Szimulált [Docker hub][docker-hub] ( `publicregistry` ) az alaprendszerkép módosításának támogatásához
   * A csapat beállításjegyzéke ( `contoso` ) a privát képek megosztásához
   * Vállalati vagy csoportos megosztott beállításjegyzék ( `baseartifacts` ) az importált nyilvános tartalomhoz
1. Egy **ACR-feladat** minden beállításjegyzékben. A feladatok:  
   1. Szimulált nyilvános rendszerkép létrehozása `node`
   1. A rendszerkép importálása és érvényesítése `node` a vállalat/csapat megosztott beállításjegyzékbe
   1. A rendszerkép létrehozása és üzembe helyezése `hello-world`
1. **ACR-feladatok definíciói**, beleértve a következő konfigurációkat:
1. **Beállításjegyzékbeli hitelesítő adatok** gyűjteménye, amelyek egy kulcstartóra mutató mutatók.
1. **Titkos** kulcsok gyűjteménye, `acr-task.yaml` amely egy kulcstartón található
1. Egy adott **értéken belül használt konfigurált értékek** gyűjteménye `acr-task.yaml`
1. **Azure Key Vault** az összes titok védelméhez
1. Egy **Azure Container-példány**, amely a `hello-world` Build alkalmazást üzemelteti

## <a name="prerequisites"></a>Előfeltételek

A következő lépésekkel konfigurálhatja az útmutatóban létrehozott és használt erőforrások értékeit.

### <a name="set-environment-variables"></a>Környezeti változók beállítása

Konfigurálja a környezet egyedi változóit. A véletlen törlés minimalizálásához ajánlott eljárásokat követve az erőforrások tartós tartalommal való elhelyezése a saját erőforráscsoporthoz. Ezeket azonban egy erőforráscsoporthoz helyezheti el, ha szükséges.

A cikkben szereplő példák a bash-rendszerhéjra vannak formázva.

```bash
# Set the three registry names, must be globally unique:
REGISTRY_PUBLIC=publicregistry
REGISTRY_BASE_ARTIFACTS=contosobaseartifacts
REGISTRY=contoso

# set the location all resources will be created in:
RESOURCE_GROUP_LOCATION=eastus

# default resource groups
REGISTRY_PUBLIC_RG=${REGISTRY_PUBLIC}-rg
REGISTRY_BASE_ARTIFACTS_RG=${REGISTRY_BASE_ARTIFACTS}-rg
REGISTRY_RG=${REGISTRY}-rg

# fully qualified registry urls
REGISTRY_DOCKERHUB_URL=docker.io
REGISTRY_PUBLIC_URL=${REGISTRY_PUBLIC}.azurecr.io
REGISTRY_BASE_ARTIFACTS_URL=${REGISTRY_BASE_ARTIFACTS}.azurecr.io
REGISTRY_URL=${REGISTRY}.azurecr.io

# Azure key vault for storing secrets, name must be globally unique
AKV=acr-task-credentials
AKV_RG=${AKV}-rg

# ACI for hosting the deployed application
ACI=hello-world-aci
ACI_RG=${ACI}-rg
```

### <a name="git-repositories-and-tokens"></a>Git-adattárak és-tokenek

A környezet szimulálása érdekében a következő git-adattárakat a felügyelni kívánt adattárakba. 

* https://github.com/importing-public-content/base-image-node.git
* https://github.com/importing-public-content/import-baseimage-node.git
* https://github.com/importing-public-content/hello-world.git

Ezután frissítse a következő változókat az elágazó adattárakhoz.

A `:main` git URL-címek végén lévő Hozzáfűzés az alapértelmezett tárház ágat jelöli.

```bash
GIT_BASE_IMAGE_NODE=https://github.com/<your-fork>/base-image-node.git#main
GIT_NODE_IMPORT=https://github.com/<your-fork>/import-baseimage-node.git#main
GIT_HELLO_WORLD=https://github.com/<your-fork>/hello-world.git#main
```

Egy GitHub- [hozzáférési tokenre (Pat)][git-token] van szüksége az ACR-feladatokhoz és a git-webhookok létrehozásához. A saját tárházhoz szükséges engedélyekkel rendelkező jogkivonat létrehozásához kövesse a [GitHub hozzáférési token létrehozása](container-registry-tutorial-build-task.md#create-a-github-personal-access-token)című témakört. 

```bash
GIT_TOKEN=<set-git-token-here>
```

### <a name="docker-hub-credentials"></a>Docker hub hitelesítő adatai  
Ha el szeretné kerülni a szabályozást és az identitás iránti kérelmeket a Docker hub-ból, hozzon létre egy [Docker hub-tokent][docker-hub-tokens]. Ezután állítsa be az alábbi környezeti változókat:

```bash
REGISTRY_DOCKERHUB_USER=<yourusername>
REGISTRY_DOCKERHUB_PASSWORD=<yourtoken>
```

### <a name="create-registries"></a>Kibocsátásiegység-forgalmi jegyzékek létrehozása

Az Azure CLI-parancsok használatával három prémium szintű tároló-beállításjegyzéket hozhat létre, amelyek mindegyike saját erőforráscsoporthoz tartozik:

```azurecli-interactive
az group create --name $REGISTRY_PUBLIC_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_PUBLIC_RG --name $REGISTRY_PUBLIC --sku Premium

az group create --name $REGISTRY_BASE_ARTIFACTS_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_BASE_ARTIFACTS_RG --name $REGISTRY_BASE_ARTIFACTS --sku Premium

az group create --name $REGISTRY_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_RG --name $REGISTRY --sku Premium
```

### <a name="create-key-vault-and-set-secrets"></a>Key Vault létrehozása és titkok beállítása

Key Vault létrehozása:

```azurecli-interactive
az group create --name $AKV_RG --location $RESOURCE_GROUP_LOCATION
az keyvault create --resource-group $AKV_RG --name $AKV
```

Állítsa be a Docker hub felhasználónevét és tokenjét a Key vaultban:

```azurecli-interactive
az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-user \
--value $REGISTRY_DOCKERHUB_USER

az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-password \
--value $REGISTRY_DOCKERHUB_PASSWORD
```

A Key vaultban a git PAT beállítása és ellenőrzése:

```azurecli-interactive
az keyvault secret set --vault-name $AKV --name github-token --value $GIT_TOKEN

az keyvault secret show --vault-name $AKV --name github-token --query value -o tsv
```

### <a name="create-resource-group-for-an-azure-container-instance"></a>Erőforráscsoport létrehozása Azure Container-példányhoz

Ez az erőforráscsoport egy későbbi feladatban használatos a lemezkép telepítésekor `hello-world` .

```azurecli-interactive
az group create --name $ACI_RG --location $RESOURCE_GROUP_LOCATION
```

## <a name="create-public-node-base-image"></a>Nyilvános `node` alaprendszerkép létrehozása

A `node` Docker hub rendszerképének szimulálásához hozzon létre egy [ACR-feladatot][acr-task] a nyilvános rendszerkép létrehozásához és karbantartásához. Ez a beállítás lehetővé teszi a képkarbantartók általi módosítások szimulálása `node` .

```azurecli-interactive
az acr task create \
  --name node-public \
  -r $REGISTRY_PUBLIC \
  -f acr-task.yaml \
  --context $GIT_BASE_IMAGE_NODE \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_DOCKERHUB_URL}/ \
  --assign-identity
```

A Docker-szabályozás elkerüléséhez adja hozzá a [Docker hub hitelesítő adatait][docker-hub-tokens] a feladathoz. Az [ACR-feladat hitelesítő][acr-task-credentials] adatai parancs használatával a Docker hitelesítő adatait bármely beállításjegyzékbe továbbíthatja, beleértve a Docker hub-t is.

```azurecli-interactive
az acr task credential add \
  -n node-public \
  -r $REGISTRY_PUBLIC \
  --login-server $REGISTRY_DOCKERHUB_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-dockerhub-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-dockerhub-password \
  --use-identity [system]
```

Adja meg a feladat elérését a Key vaultban lévő olvasási értékekhez:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name node-public \
                  --registry $REGISTRY_PUBLIC \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

A [feladatok][acr-task-triggers] elindíthatók a git-véglegesítő, az alaprendszerkép-frissítések, az időzítők vagy a manuális futtatások alapján. 

Futtassa manuálisan a feladatot a rendszerkép létrehozásához `node` :

```azurecli-interactive
az acr task run -r $REGISTRY_PUBLIC -n node-public
```

A szimulált nyilvános beállításjegyzék rendszerképének listázása:

```azurecli-interactive
az acr repository show-tags -n $REGISTRY_PUBLIC --repository node
```

## <a name="create-the-hello-world-image"></a>A `hello-world` rendszerkép létrehozása

A szimulált nyilvános rendszerkép alapján `node` hozzon létre egy `hello-world` rendszerképet.

### <a name="create-token-for-pull-access-to-simulated-public-registry"></a>Jogkivonat létrehozása a szimulált nyilvános beállításjegyzékhez való lekéréses hozzáféréshez

Hozzon létre egy [hozzáférési jogkivonatot][acr-tokens] a szimulált nyilvános beállításjegyzékbe, amelynek hatóköre a következő: `pull` . Ezután állítsa be a Key vaultban:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-user" \
  --value "registry-${REGISTRY_PUBLIC}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_PUBLIC}-user" \
              --registry $REGISTRY_PUBLIC \
              --scope-map _repositories_pull \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-token-for-pull-access-by-azure-container-instances"></a>Jogkivonat létrehozása a lekéréses hozzáféréshez Azure Container Instances

Hozzon létre egy [hozzáférési jogkivonatot][acr-tokens] a `hello-world` rendszerképet futtató beállításjegyzékbe, a lekérés hatóköre szerint. Ezután állítsa be a Key vaultban:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-user" \
  --value "registry-${REGISTRY}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY}-user" \
              --registry $REGISTRY \
              --repository hello-world content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-task-to-build-and-maintain-hello-world-image"></a>Rendszerkép létrehozására és karbantartására szolgáló feladat létrehozása `hello-world`

A következő parancs egy feladatot hoz létre a definíciójában a tárházban `acr-tasks.yaml` `hello-world` . A feladat lépései felépítik a `hello-world` lemezképet, majd központilag telepítik Azure Container instances. A Azure Container Instances erőforráscsoport egy előző szakaszban lett létrehozva. Ha a `az container create` feladatban csak a különbségeket hívja meg `image:tag` , a feladat a jelen útmutatóban szereplő ugyanazon példányra települ.

```azurecli-interactive
az acr task create \
  -n hello-world \
  -r $REGISTRY \
  -f acr-task.yaml \
  --context $GIT_HELLO_WORLD \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --set KEYVAULT=$AKV \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG \
  --assign-identity
```

Hitelesítő adatok hozzáadása a feladathoz a szimulált nyilvános beállításjegyzékhez:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Adja meg a feladat elérését a Key vaultban lévő olvasási értékekhez:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name hello-world \
                  --registry $REGISTRY \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Adja meg a feladat elérését Azure Container Instances létrehozásához és kezeléséhez az erőforráscsoporthoz való hozzáférés megadásával:

```azurecli-interactive
az role assignment create \
  --assignee $(az acr task show \
  --name hello-world \
  --registry $REGISTRY \
  --query identity.principalId --output tsv) \
  --scope $(az group show -n $ACI_RG --query id -o tsv) \
  --role owner
```

A létrehozott és konfigurált feladat futtatásával futtassa a következő feladatot a rendszerkép létrehozásához és üzembe helyezéséhez `hello-world` :

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

A létrehozás után szerezze be a rendszerképet üzemeltető tároló IP-címét `hello-world` .

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

A böngészőben nyissa meg az IP-címet a futó alkalmazás megtekintéséhez.

## <a name="update-the-base-image-with-a-questionable-change"></a>Az alaprendszerkép frissítése a "megkérdőjelezhető" módosítással

Ez a szakasz szimulálja az alaprendszerkép változását, amely problémákat okozhat a környezetben.

1. Nyissa meg `Dockerfile` az elágazó tárházban `base-image-node` .
1. Módosítsa a `BACKGROUND_COLOR` értékre a `Orange` módosítás szimulálása érdekében.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Orange
```

Véglegesítse a változást, és figyelje meg az ACR-feladatokat, hogy automatikusan kezdjék meg az összeállítást.

A végrehajtás megkezdéséhez tekintse meg a következő feladatot:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Végül a következő TRIGGER alapján kell megjelennie az ÁLLAPOTnak `Succeeded` `Commit` :

```azurecli-interactive
RUN ID    TASK      PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------  ----------  ---------  ---------  --------------------  ----------
ca4       hub-node  linux       Succeeded  Commit     2020-10-24T05:02:29Z  00:00:22
```

Írja be a **CTRL + C** billentyűkombinációt a Watch parancs bezárásához, majd tekintse meg a legutóbbi Futtatás naplóit:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

A `node` rendszerkép befejezése után `watch` az ACR-feladatok automatikusan megkezdik a rendszerkép felépítése `hello-world` :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Végül a következő TRIGGER alapján kell megjelennie az ÁLLAPOTnak `Succeeded` `Image Update` :

```azurecli-interactive
RUN ID    TASK         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  -----------  ----------  ---------  ------------  --------------------  ----------
dau       hello-world  linux       Succeeded  Image Update  2020-10-24T05:08:45Z  00:00:31
```

Írja be a **CTRL + C** billentyűkombinációt a Watch parancs bezárásához, majd tekintse meg a legutóbbi Futtatás naplóit:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Ha elkészült, szerezze be a frissített rendszerképet üzemeltető hely IP-címét `hello-world` :

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

A böngészőben nyissa meg a webhelyet, ahol narancssárga (megkérdőjelezhető) háttérrel kell rendelkeznie.

### <a name="checking-in"></a>Ellenőrzés folyamatban

Ekkor létrehozott egy `hello-world` rendszerképet, amely automatikusan a git-véglegesíti és az alaprendszerkép módosításaira épül `node` . Ebben a példában a feladat a Azure Container Registry egy alaprendszerképére épül, de minden támogatott beállításjegyzék használható.

Az alaprendszerkép frissítése automatikusan újraaktiválja a feladatot a `node` rendszerkép frissítésekor. Ahogy az itt is látható, nem minden frissítés lenne kívánatos.

## <a name="gated-imports-of-public-content"></a>Nyilvános tartalom vezérelt importálása

Ha meg szeretné akadályozni, hogy a kritikus fontosságú számítási feladatokból a felsőbb rétegbeli változások is megtörténjenek, a biztonsági ellenőrzés és a működési tesztek

Ebben a szakaszban egy ACR-feladatot hoz létre a következőhöz:

* Rendszerkép létrehozása
* Funkcionális tesztelési parancsfájl futtatása `./test.sh` a tesztelési képpel
* Ha a rendszerkép sikeresen tesztelve lett, importálja a nyilvános rendszerképet a **baseimages** -beállításjegyzékbe.

### <a name="add-automation-testing"></a>Automation-tesztelés hozzáadása

A felsőbb rétegbeli tartalmak bevezetéséhez automatizált tesztelést kell megvalósítani. Ebben a példában egy `test.sh` van megadva, amely ellenőrzi a következőt: `$BACKGROUND_COLOR` . Ha a teszt sikertelen, a rendszer egy olyan értéket `EXIT_CODE` `1` ad vissza, amelynek hatására az ACR-feladat lépése meghiúsul, és befejezi a feladat futtatását. A tesztek bármilyen formájú eszközön bővíthetők, beleértve a naplózási eredményeket is. A kaput a szkriptben a Pass/Fail válasz kezeli, amely itt található:

```bash
if [ ""$(echo $BACKGROUND_COLOR | tr '[:lower:]' '[:upper:]') = 'RED' ]; then
    echo -e "\e[31mERROR: Invalid Color:\e[0m" ${BACKGROUND_COLOR}
    EXIT_CODE=1
else
  echo -e "\e[32mValidation Complete - No Known Errors\e[0m"
fi
exit ${EXIT_CODE}
```
### <a name="task-yaml"></a>Feladat YAML 

Tekintse át az alábbi lépéseket a tárházban `acr-task.yaml` `import-baseimage-node` :

1. Hozza létre a tesztelési alap rendszerképét a következő Docker használatával:
    ```dockerfile
    ARG REGISTRY_FROM_URL=
    FROM ${REGISTRY_FROM_URL}node:15-alpine
    WORKDIR /test
    COPY ./test.sh .
    CMD ./test.sh
    ```
1. Ha elkészült, érvényesítse a rendszerképet a tároló futtatásával, amely fut `./test.sh`
1. Csak akkor, ha sikeresen befejeződött, futtassa az importálás lépéseit, amelyek a következővel vannak leképezve `when: ['validate-base-image']`

```yaml
version: v1.1.0
steps:
  - id: build-test-base-image
    # Build off the base image we'll track
    # Add a test script to do unit test validations
    # Note: the test validation image isn't saved to the registry
    # but the task logs captures log validation results
    build: >
      --build-arg REGISTRY_FROM_URL={{.Values.REGISTRY_FROM_URL}}
      -f ./Dockerfile
      -t {{.Run.Registry}}/node-import:test
      .
  - id: validate-base-image
    # only continues if node-import:test returns a non-zero code
    when: ['build-test-base-image']
    cmd: "{{.Run.Registry}}/node-import:test"
  - id: pull-base-image
    # import the public image to base-artifacts
    # Override the stable tag,
    # and create a unique tag to enable rollback
    # to a previously working image
    when: ['validate-base-image']
    cmd: >
        docker pull {{.Values.REGISTRY_FROM_URL}}node:15-alpine
  - id: retag-base-image
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine
  - id: retag-base-image-unique-tag
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine-{{.Run.ID}}
  - id: push-base-image
    when: ['retag-base-image', 'retag-base-image-unique-tag']
    push:
    - "{{.Run.Registry}}/node:15-alpine"
    - "{{.Run.Registry}}/node:15-alpine-{{.Run.ID}}"
```

### <a name="create-task-to-import-and-test-base-image"></a>Az alap rendszerkép importálására és tesztelésére szolgáló feladat létrehozása

```azurecli-interactive
  az acr task create \
  --name base-import-node \
  -f acr-task.yaml \
  -r $REGISTRY_BASE_ARTIFACTS \
  --context $GIT_NODE_IMPORT \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --assign-identity
```

Hitelesítő adatok hozzáadása a feladathoz a szimulált nyilvános beállításjegyzékhez:

```azurecli-interactive
az acr task credential add \
  -n base-import-node \
  -r $REGISTRY_BASE_ARTIFACTS \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Adja meg a feladat elérését a Key vaultban lévő olvasási értékekhez:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name base-import-node \
                  --registry $REGISTRY_BASE_ARTIFACTS \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Futtassa az importálási feladatot:

```azurecli-interactive
az acr task run -n base-import-node -r $REGISTRY_BASE_ARTIFACTS
```

> [!NOTE]
> Ha a feladat végrehajtása a miatt meghiúsul, győződjön meg arról, `./test.sh: Permission denied` hogy a parancsfájl végrehajtási engedélyekkel rendelkezik, és véglegesítse a git-tárházat:
>```bash
>chmod +x ./test.sh
>```

## <a name="update-hello-world-image-to-build-from-gated-node-image"></a>`hello-world`Rendszerkép frissítése a kiépített `node` rendszerképből való létrehozáshoz

Hozzon létre egy [hozzáférési jogkivonatot][acr-tokens] az alap-összetevők beállításjegyzékének eléréséhez, amely hatóköre `read` az adattárból van kiosztva `node` . Ezután állítsa be a Key vaultban:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
  --value "registry-${REGISTRY_BASE_ARTIFACTS}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
              --registry $REGISTRY_BASE_ARTIFACTS \
              --repository node content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

Adja hozzá a hitelesítő adatokat a **Hello-World** feladathoz az alapvető összetevők beállításjegyzékében:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_BASE_ARTIFACTS_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-password \
  --use-identity [system]
```

Frissítse a feladatot, hogy az a `REGISTRY_FROM_URL` beállításjegyzék használatára legyen módosítva `BASE_ARTIFACTS`

```azurecli-interactive
az acr task update \
  -n hello-world \
  -r $REGISTRY \
  --set KEYVAULT=$AKV \
  --set REGISTRY_FROM_URL=${REGISTRY_BASE_ARTIFACTS_URL}/ \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG
```

Futtassa a **Hello-World** feladatot az alap rendszerkép-függőségének módosításához:

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

## <a name="update-the-base-image-with-a-valid-change"></a>Az alaprendszerkép frissítése "érvényes" módosítással

1. Nyissa meg a a tárházban `Dockerfile` `base-image-node` .
1. Módosítsa a változót `BACKGROUND_COLOR` `Green` egy érvényes módosítás szimulálása céljából.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Green
```

Véglegesítse a módosítást, és figyelje a frissítések sorozatot:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

A futtatást követően írja be a **CTRL + C billentyűkombinációt** , és figyelje a naplókat:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Ha elkészült, figyelje az **Alap-rendszerkép-importálási** feladatot:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

A futtatást követően írja be a **CTRL + C billentyűkombinációt** , és figyelje a naplókat:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

Ha elkészült, figyelje a **Hello-World** feladatot:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

A futtatást követően írja be a **CTRL + C billentyűkombinációt** , és figyelje a naplókat:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Ha elkészült, szerezze be a frissített rendszerképet üzemeltető hely IP-címét `hello-world` :

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

A böngészőben nyissa meg a webhelyet, ahol zöld (érvényes) háttérrel kell rendelkeznie.

### <a name="view-the-gated-workflow"></a>A levezérelt munkafolyamat megtekintése

Végezze el újra az előző szakaszban leírt lépéseket a piros háttérszínsel.

1. `Dockerfile`A tárház megnyitása `base-image-node`
1. Módosítsa a `BACKGROUND_COLOR` -t a értékre `Red` egy érvénytelen módosítás szimulálása érdekében.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Red
```

Véglegesítse a módosítást, és figyelje a frissítések sorozatot:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

A futtatást követően írja be a **CTRL + C billentyűkombinációt** , és figyelje a naplókat:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Ha elkészült, figyelje az **Alap-rendszerkép-importálási** feladatot:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

A futtatást követően írja be a **CTRL + C billentyűkombinációt** , és figyelje a naplókat:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

Ezen a ponton meg kell jelennie az **Alap-importálás-csomópont feladat-** végrehajtás sikertelen érvényesítésének, és a folyamat leállítása a frissítés közzétételéhez `hello-world` . A kimenet a következőhöz hasonló:

```console
[...]
2020/10/30 03:57:39 Launching container with name: validate-base-image
Validating Image
NODE_VERSION: 15-alpine
BACKGROUND_COLOR: Red
ERROR: Invalid Color: Red
2020/10/30 03:57:40 Container failed during run: validate-base-image. No retries remaining.
failed to run step ID: validate-base-image: exit status 1
```

### <a name="publish-an-update-to-hello-world"></a>Frissítés közzététele a következőn `hello-world`

A rendszerkép módosításai `hello-world` továbbra is az utolsó érvényesített `node` képet használják.

Az `node` alaprendszerképnek a megtalált érvényesítést átengedő további módosításai kiváltják a rendszerkép alaprendszerképének frissítéseit `hello-world` .

## <a name="cleaning-up"></a>A felesleges elemek eltávolítása

Ha már nincs rá szükség, törölje a cikkben használt erőforrásokat.

```azurecli-interactive
az group delete -n $REGISTRY_RG --no-wait -y
az group delete -n $REGISTRY_PUBLIC_RG --no-wait -y
az group delete -n $REGISTRY_BASE_ARTIFACTS_RG --no-wait -y
az group delete -n $AKV_RG --no-wait -y
az group delete -n $ACI_RG --no-wait -y
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben. az ACR-feladatok használatával automatizált kapuzás-munkafolyamatot hozott létre, amely frissített alaplemezképeket vezet be a környezetbe. Tekintse meg a kapcsolódó információkat a rendszerképek Azure Container Registryban való kezeléséhez.


* [A tároló lemezképének címkézésére és verziószámozására vonatkozó javaslatok](container-registry-image-tag-version.md)
* [Tároló rendszerképének zárolása egy Azure Container registryben](container-registry-image-lock.md)

[install-cli]:                  /cli/azure/install-azure-cli
[acr]:                          https://aka.ms/acr
[acr-repo-permissions]:         ./container-registry-repository-scoped-permissions.md
[acr-task]:                     ./container-registry-tasks-overview.md
[acr-task-triggers]:            container-registry-tasks-overview.md#task-scenarios
[acr-task-credentials]:       container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task
[acr-tokens]:                   ./container-registry-repository-scoped-permissions.md
[aci]:                          https://aka.ms/aci
[alpine-public-image]:          https://hub.docker.com/_/alpine
[docker-hub]:                   https://hub.docker.com
[docker-hub-tokens]:            https://hub.docker.com/settings/security
[git-token]:                    https://github.com/settings/tokens
[gcr]:                          https://cloud.google.com/container-registry
[ghcr]:                         https://docs.github.com/en/free-pro-team@latest/packages/getting-started-with-github-container-registry/about-github-container-registry
[helm-charts]:                  https://helm.sh
[mcr]:                          https://aka.ms/mcr
[nginx-public-image]:           https://hub.docker.com/_/nginx
[oci-artifacts]:                ./container-registry-oci-artifacts.md
[oci-consuming-public-content]: https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/
[opa]:                          https://www.openpolicyagent.org/
[quay]:                         https://quay.io