---
title: Adatátviteli összetevők
description: Lemezképek vagy más összetevők gyűjteményének átvitele egy tároló-beállításjegyzékből egy másik beállításjegyzékbe az Azure Storage-fiókokat használó adatátviteli folyamat létrehozásával
ms.topic: article
ms.date: 10/07/2020
ms.custom: ''
ms.openlocfilehash: 4fe36366011fb790d25419ac46a54c4bf5ad94bf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785818"
---
# <a name="transfer-artifacts-to-another-registry"></a>Összetevők átvitele másik beállításjegyzékbe

Ez a cikk bemutatja, hogyan lehet átvinni a lemezképek vagy más beállításjegyzék-összetevők gyűjteményeit egy Azure Container registryből egy másik beállításjegyzékbe. A forrás-és a célként megadott beállításjegyzékek lehetnek azonos vagy eltérő előfizetésekben, Active Directory bérlők, Azure-felhők vagy fizikailag leválasztott felhők. 

Az összetevők átviteléhez létre kell hoznia egy *adatátviteli* folyamatot, amely a [blob Storage](../storage/blobs/storage-blobs-introduction.md)használatával replikálja a két beállításjegyzék közötti összetevőket:

* A forrás-beállításjegyzékből származó összetevők a forrásként szolgáló Storage-fiókban lévő blobba lesznek exportálva 
* A blobot a rendszer átmásolja a forrás Storage-fiókból egy célként megadott Storage-fiókba.
* A célként megadott Storage-fiókban lévő blobot a rendszer összetevőkként importálja a cél beállításjegyzékbe. Beállíthatja az importálási folyamatot úgy, hogy az a cél tárolóban lévő blob-frissítések indításakor aktiválja.

Az átvitel ideális megoldás a tartalom másolására két, fizikailag leválasztott felhőben található Azure Container-nyilvántartó között, az egyes Felhőbeli Storage-fiókok által közvetítve. Ha ehelyett lemezképeket szeretne másolni a csatlakoztatott felhőkben lévő tároló-beállításjegyzékből, beleértve a Docker hub-t és más felhőalapú szállítókat, a [Képimportálás](container-registry-import-images.md) ajánlott.

Ebben a cikkben Azure Resource Manager sablon központi telepítéseit használja az átviteli folyamat létrehozásához és futtatásához. Az Azure CLI a társított erőforrások, például a tárolási titkok kiépítésére szolgál. Az Azure CLI 2.2.0 vagy újabb verziója ajánlott. Ha telepíteni vagy frissíteni szeretné a parancssori felületet, olvassa el [az Azure CLI telepítését][azure-cli] ismertető témakört.

Ez a funkció a **prémium** szintű Container Registry szolgáltatási szinten érhető el. További információ a beállításjegyzék szolgáltatási szintjeiről és korlátairól: [Azure Container Registry rétegek](container-registry-skus.md).

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="prerequisites"></a>Előfeltételek

* **Tároló-nyilvántartók** – szükség van egy meglévő forrás-beállításjegyzékre az átvinni kívánt összetevőkkel és a célként megadott beállításjegyzékkel. Az ACR-átvitel a fizikailag leválasztott felhők közötti mozgásra szolgál. A teszteléshez a forrás-és a célként megadott beállításjegyzékek lehetnek azonosak vagy egy másik Azure-előfizetésben, Active Directory bérlőben vagy felhőben. 

   Ha létre kell hoznia egy beállításjegyzéket, olvassa el a következőt: rövid útmutató [: privát tároló beállításjegyzékének létrehozása az Azure CLI használatával](container-registry-get-started-azure-cli.md). 
* **Storage-fiókok** – forrás-és célként szolgáló Storage-fiókok létrehozása egy előfizetésben és egy tetszőleges helyen. Tesztelési célból használhatja ugyanazt az előfizetést vagy előfizetést, mint a forrás-és a cél-nyilvántartó. A Felhőbeli forgatókönyvek esetében általában külön Storage-fiókot hoz létre minden felhőben. 

  Ha szükséges, hozzon létre egy Storage-fiókot az [Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli) -vel vagy más eszközökkel. 

  Hozzon létre egy BLOB-tárolót az összetevők átviteléhez az egyes fiókokban. Hozzon létre például egy *átvitel* nevű tárolót. Két vagy több adatátviteli folyamat is megoszthatja ugyanazt a Storage-fiókot, de eltérő tárolási tároló-hatóköröket kell használnia.
* **Kulcstartók** – kulcstartók szükségesek a forrás-és cél Storage-fiókok eléréséhez használt sas-jogkivonat-titkok tárolásához. Hozza létre a forrás és a cél kulcstartót ugyanabban az Azure-előfizetésben vagy előfizetésben, mint a forrás-és a cél-nyilvántartó. Demonstrációs célokra a cikkben használt sablonok és parancsok azt is feltételezik, hogy a forrás-és a célként megadott kulcstároló a forrás-és a cél-nyilvántartóval megegyező erőforráscsoportokat található. A gyakori erőforráscsoportok használata nem kötelező, de leegyszerűsíti a cikkben használt sablonokat és parancsokat.

   Ha szükséges, hozzon létre kulcstartókat az [Azure CLI](../key-vault/secrets/quick-create-cli.md) -vel vagy más eszközökkel.

* **Környezeti változók** – a cikkben szereplő parancsokhoz például a következő környezeti változók állíthatók be a forrás-és a célként megadott környezetekben. Az összes példa a bash-rendszerhéjra van formázva.
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>A forgatókönyv áttekintése

A következő három folyamat-erőforrást hozza létre a kibocsátásiegység-forgalmi jegyzékek közötti képátvitelhez. Mindegyik a PUT műveletek használatával jön létre. Ezek az erőforrások a *forrás* -és a *cél* -nyilvántartásokban és a Storage-fiókokban működnek. 

A Storage-hitelesítés SAS-jogkivonatokat használ, amelyeket a Key vaultban titkos kulcsként kezel. A folyamatok felügyelt identitásokkal olvassák el a titkos kulcsokat a tárolókban.

* **[ExportPipeline](#create-exportpipeline-with-resource-manager)** – tartós erőforrás, amely magas szintű adatokat tartalmaz a *forrás* -és a Storage-fiókról. Ez az információ tartalmazza a forrás tároló blob-tárolójának URI azonosítóját és a forrás SAS-tokent kezelő kulcstartót. 
* **[ImportPipeline](#create-importpipeline-with-resource-manager)** – hosszú élettartamú erőforrás, amely magas szintű adatokat tartalmaz a *cél* beállításjegyzékével és a Storage-fiókkal kapcsolatban. Ez az információ tartalmazza a cél tároló blob-tárolójának URI azonosítóját és a cél SAS-tokent kezelő kulcstartót. Az importálási trigger alapértelmezés szerint engedélyezve van, így a folyamat automatikusan elindul, amikor egy összetevő blobja a cél Storage-tárolóban landol. 
* **[PipelineRun](#create-pipelinerun-for-export-with-resource-manager)** – egy ExportPipeline-vagy ImportPipeline-erőforrás meghívására szolgáló erőforrás.  
  * A ExportPipeline manuálisan futtathatja egy PipelineRun-erőforrás létrehozásával és az exportálandó összetevők megadásával.  
  * Ha engedélyezve van egy importálási trigger, a ImportPipeline automatikusan lefut. A PipelineRun használatával manuálisan is futtatható. 
  * Jelenleg legfeljebb **50** összetevő helyezhető át az egyes PipelineRun.

### <a name="things-to-know"></a>Tudnivalók
* A ExportPipeline és a ImportPipeline általában a forrás és a cél felhőkhöz társított különböző Active Directory-bérlők között lesznek. Ehhez a forgatókönyvhöz külön felügyelt identitások és kulcstartók szükségesek az Exportálás és az importálás erőforrásaihoz. Tesztelési célból ezek az erőforrások ugyanahhoz a felhőhöz, megosztási identitásokhoz is helyezhetők.
* Alapértelmezés szerint a ExportPipeline és a ImportPipeline sablonok lehetővé teszik, hogy a rendszer által hozzárendelt felügyelt identitások hozzáférjenek a Key Vault titkos kulcsaihoz. A ExportPipeline és a ImportPipeline-sablonok egy felhasználó által megadott identitást is támogatnak. 

## <a name="create-and-store-sas-keys"></a>SAS-kulcsok létrehozása és tárolása

Az átvitel közös hozzáférésű aláírás (SAS) jogkivonatokkal fér hozzá a forrás-és a célként megadott környezetekben lévő Storage-fiókokhoz. A következő szakaszokban leírtak szerint hozhatja elő és tárolja a jogkivonatokat.  

### <a name="generate-sas-token-for-export"></a>SAS-jogkivonat előállítása exportáláshoz

Futtassa az az [Storage Container regenerált-sas][az-storage-container-generate-sas] parancsot, hogy LÉTREHOZZON egy sas-jogkivonatot a tárolóhoz a forrásként szolgáló Storage-fiókban, amely az összetevők exportálására szolgál.

*Ajánlott jogkivonat-engedélyek*: olvasás, írás, Listázás, Hozzáadás. 

A következő példában a parancs kimenete hozzá van rendelve a EXPORT_SAS környezeti változóhoz, a "?" karakterrel előre meghatározva. Frissítse a `--expiry` környezet értékét:

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>Az SAS-jogkivonat tárolása exportáláshoz

Az SAS-token tárolása a forrás Azure Key vaultban az [az kulcstartó Secret set][az-keyvault-secret-set]használatával:

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>SAS-jogkivonat előállítása importáláshoz

Futtassa az az [Storage Container regenerált-sas][az-storage-container-generate-sas] parancsot, hogy LÉTREHOZZON egy sas-jogkivonatot a tárolóhoz a cél Storage-fiókban, amely az összetevők importálására szolgál.

*Ajánlott jogkivonat-engedélyek*: olvasás, törlés, Listázás

A következő példában a parancs kimenete hozzá van rendelve a IMPORT_SAS környezeti változóhoz, a "?" karakterrel előre meghatározva. Frissítse a `--expiry` környezet értékét:

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>Az SAS-jogkivonat tárolása importáláshoz

Tárolja az SAS-jogkivonatot a cél Azure Key vaultban az [az kulcstartó Secret set][az-keyvault-secret-set]paranccsal:

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>ExportPipeline létrehozása a Resource Managerrel

Hozzon létre egy ExportPipeline-erőforrást a forrás-tároló beállításjegyzékéhez Azure Resource Manager sablon központi telepítésének használatával.

Másolja az ExportPipeline Resource Manager- [sablonfájlokat](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines) egy helyi mappába.

Adja meg a következő paraméterérték értékét a fájlban `azuredeploy.parameters.json` :

|Paraméter  |Érték  |
|---------|---------|
|registryName     | A forrás tároló beállításjegyzékének neve      |
|exportPipelineName     |  Az exportálási folyamathoz választott név       |
|Targeturi értékében     |  A forrás-környezet (az exportálási folyamat célja) tároló tárolójának URI azonosítója.<br/>Például: `https://sourcestorage.blob.core.windows.net/transfer`       |
|keyVaultName     |  A forrás kulcstároló neve  |
|sasTokenSecretName  | Az SAS-jogkivonat titkos kulcsának neve a forrás kulcstartóban <br/>Példa: acrexportsas

### <a name="export-options"></a>Exportálási beállítások

Az `options` exportálási folyamatok tulajdonsága támogatja a nem kötelező logikai értékeket. A következő értékek ajánlottak:

|Paraméter  |Érték  |
|---------|---------|
|beállítások | OverwriteBlobs – meglévő cél Blobok felülírása<br/>ContinueOnErrors – folytassa a fennmaradó összetevők exportálását a forrás beállításjegyzékben, ha az egyik összetevő exportálása sikertelen.

### <a name="create-the-resource"></a>Az erőforrás létrehozása

Futtassa az [az üzembe helyezési csoport létrehozása][az-deployment-group-create] parancsot az *exportPipeline* nevű erőforrás létrehozásához az alábbi példákban látható módon. Alapértelmezés szerint az első beállítással a példa sablon lehetővé teszi a rendszer által hozzárendelt identitás használatát a ExportPipeline-erőforrásban. 

A második lehetőséggel megadhatja az erőforrást egy felhasználó által hozzárendelt identitással. (A felhasználó által hozzárendelt identitás létrehozása nem látható.)

Bármelyik beállítással a sablon úgy konfigurálja az identitást, hogy hozzáférjen az SAS-tokenhez az exportálási kulcstartóban. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>1. lehetőség: erőforrás létrehozása és rendszer által hozzárendelt identitás engedélyezése

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>2. lehetőség: erőforrás létrehozása és felhasználó által hozzárendelt identitás megadása

Ebben a parancsban adja meg a felhasználó által hozzárendelt identitás erőforrás-AZONOSÍTÓját további paraméterként.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

A parancs kimenetében jegyezze fel a folyamat erőforrás-AZONOSÍTÓját ( `id` ). Ezt az értéket egy környezeti változóban is tárolhatja későbbi használatra az az [Deployment Group show][az-deployment-group-show]paranccsal. Például:

```azurecli
EXPORT_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>ImportPipeline létrehozása a Resource Managerrel 

Hozzon létre egy ImportPipeline-erőforrást a cél tároló beállításjegyzékében Azure Resource Manager sablon üzembe helyezésével. Alapértelmezés szerint a folyamat automatikusan importálható, ha a célként megadott környezetben lévő Storage-fióknak van egy összetevő-blobja.

Másolja az ImportPipeline Resource Manager- [sablonfájlokat](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines) egy helyi mappába.

Adja meg a következő paraméterérték értékét a fájlban `azuredeploy.parameters.json` :

Paraméter  |Érték  |
|---------|---------|
|registryName     | A tároló-beállításjegyzék neve      |
|importPipelineName     |  Az importálási folyamathoz választott név       |
|sourceUri     |  A tároló tárolási tárolójának URI-ja (az importálási folyamat forrása).<br/>Például: `https://targetstorage.blob.core.windows.net/transfer/`|
|keyVaultName     |  A célként megadott Key Vault neve |
|sasTokenSecretName     |  Az SAS-jogkivonat titkos kulcsának neve a célként megadott kulcstartóban<br/>Példa: ACR importsas |

### <a name="import-options"></a>Importálási lehetőségek

Az `options` importálási folyamat tulajdonsága támogatja a nem kötelező logikai értékeket. A következő értékek ajánlottak:

|Paraméter  |Érték  |
|---------|---------|
|beállítások | OverwriteTags – meglévő címkék felülírása<br/>DeleteSourceBlobOnSuccess – a forrás-tárolási blob törlése a cél beállításjegyzékbe való sikeres importálás után<br/>ContinueOnErrors – folytassa a fennmaradó összetevők importálását a cél beállításjegyzékben, ha az egyik összetevő importálása sikertelen.

### <a name="create-the-resource"></a>Az erőforrás létrehozása

Futtassa az [az üzembe helyezési csoport létrehozása][az-deployment-group-create] parancsot az *importPipeline* nevű erőforrás létrehozásához az alábbi példákban látható módon. Alapértelmezés szerint az első beállítással a példa sablon lehetővé teszi a rendszer által hozzárendelt identitás használatát a ImportPipeline-erőforrásban. 

A második lehetőséggel megadhatja az erőforrást egy felhasználó által hozzárendelt identitással. (A felhasználó által hozzárendelt identitás létrehozása nem látható.)

Bármelyik beállítással a sablon úgy konfigurálja az identitást, hogy hozzáférjen az SAS-jogkivonathoz az import Key vaultban. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>1. lehetőség: erőforrás létrehozása és rendszer által hozzárendelt identitás engedélyezése

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json 
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>2. lehetőség: erőforrás létrehozása és felhasználó által hozzárendelt identitás megadása

Ebben a parancsban adja meg a felhasználó által hozzárendelt identitás erőforrás-AZONOSÍTÓját további paraméterként.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Ha manuálisan szeretné futtatni az importálást, jegyezze fel a folyamat erőforrás-AZONOSÍTÓját ( `id` ). Ezt az értéket egy környezeti változóban is tárolhatja későbbi használatra az az [Deployment Group show][az-deployment-group-show] parancs futtatásával. Például:

```azurecli
IMPORT_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>PipelineRun létrehozása a Resource Managerrel való exportáláshoz 

Hozzon létre egy PipelineRun-erőforrást a forrás-tároló beállításjegyzékéhez Azure Resource Manager sablon központi telepítésének használatával. Ez az erőforrás futtatja a korábban létrehozott ExportPipeline-erőforrást, és a tároló-beállításjegyzékből a megadott összetevőket a forrás Storage-fiókba blobként exportálja.

Másolja az PipelineRun Resource Manager- [sablonfájlokat](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export) egy helyi mappába.

Adja meg a következő paraméterérték értékét a fájlban `azuredeploy.parameters.json` :

|Paraméter  |Érték  |
|---------|---------|
|registryName     | A forrás tároló beállításjegyzékének neve      |
|pipelineRunName     |  A futtatáshoz választott név       |
|pipelineResourceId     |  Az exportálási folyamat erőforrás-azonosítója.<br/>Például: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|targetName     |  A forrás-Storage-fiókba exportált összetevők blobjának neve, például *myblob*
|leletek | Az átvinni kívánt forrásoldali összetevők tömbje címkék vagy jegyzékfájl-kivonatok formájában<br/>Például: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

Ha azonos tulajdonságokkal rendelkező PipelineRun-erőforrást telepít újra, akkor a [forceUpdateTag](#redeploy-pipelinerun-resource) tulajdonságot is használni kell.

Futtassa az az [üzembe helyezési csoport létrehozása][az-deployment-group-create] parancsot a PipelineRun-erőforrás létrehozásához. A következő példa az üzembe helyezési *exportPipelineRun* nevezi át.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

A későbbi használat érdekében tárolja a folyamat futtatásának erőforrás-AZONOSÍTÓját egy környezeti változóban:

```azurecli
EXPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Az összetevők exportálására több percet is igénybe vehet. Az üzembe helyezés sikeres befejezése után ellenőrizze az összetevők exportálását, ha az exportált blobot a forrás Storage-fiók *átvitel* tárolójában található. Futtassa például az az [Storage blob List][az-storage-blob-list] parancsot:

```azurecli
az storage blob list \
  --account-name $SOURCE_SA \
  --container transfer \
  --output table
```

## <a name="transfer-blob-optional"></a>BLOB átvitele (nem kötelező) 

A AzCopy eszköz vagy más módszerek használatával a [blob-adatok átvitele](../storage/common/storage-use-azcopy-v10.md#transfer-data) a forrás Storage-fiókból a cél Storage-fiókba.

A következő parancs például a [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md) myblob lévő *átvitel* tárolóból másolja át az *átvitel* tárolóba a cél fiókba. Ha a blob létezik a cél fiókban, az felülírva van. A hitelesítés SAS-jogkivonatokat használ a forrás és a cél tárolók megfelelő engedélyeivel. (A jogkivonatok létrehozásának lépései nem jelennek meg.)

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>ImportPipeline-erőforrás elindítása

Ha engedélyezte a `sourceTriggerStatus` ImportPipeline paraméterét (az alapértelmezett érték), a folyamat a blobnak a célként megadott Storage-fiókba való másolását követően aktiválódik. Az összetevők importálása több percet is igénybe vehet. Ha az importálás sikeresen befejeződött, ellenőrizze az összetevők importálását a tároló beállításjegyzékében lévő adattárak listázásával. Futtassa például [az az ACR adattár List][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

Ha nem engedélyezte az `sourceTriggerStatus` importálási folyamat paraméterét, futtassa manuálisan a ImportPipeline-erőforrást az alábbi szakaszban látható módon. 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>PipelineRun létrehozása a Resource Managerrel való importáláshoz (nem kötelező) 
 
PipelineRun-erőforrást is használhat, amellyel elindíthat egy ImportPipeline a tároló beállításjegyzékbe való importáláshoz.

Másolja az PipelineRun Resource Manager- [sablonfájlokat](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import) egy helyi mappába.

Adja meg a következő paraméterérték értékét a fájlban `azuredeploy.parameters.json` :

|Paraméter  |Érték  |
|---------|---------|
|registryName     | A tároló-beállításjegyzék neve      |
|pipelineRunName     |  A futtatáshoz választott név       |
|pipelineResourceId     |  Az importálási folyamat erőforrás-azonosítója.<br/>Például: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|sourceName     |  Az exportált összetevők meglévő blobjának neve a Storage-fiókban, például *myblob*

Ha azonos tulajdonságokkal rendelkező PipelineRun-erőforrást telepít újra, akkor a [forceUpdateTag](#redeploy-pipelinerun-resource) tulajdonságot is használni kell.

Futtassa az az [üzembe helyezési csoport létrehozása][az-deployment-group-create] parancsot az erőforrás futtatásához.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

A későbbi használat érdekében tárolja a folyamat futtatásának erőforrás-AZONOSÍTÓját egy környezeti változóban:

```azurecli
IMPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Az üzembe helyezés sikeres befejezése után ellenőrizze az összetevők importálását a tároló beállításjegyzékében található adattárak listázásával. Futtassa például [az az ACR adattár List][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="redeploy-pipelinerun-resource"></a>PipelineRun-erőforrás újbóli üzembe helyezése

Ha *azonos tulajdonságokkal* rendelkező PipelineRun-erőforrást telepít újra, ki kell használnia a **forceUpdateTag** tulajdonságot. Ez a tulajdonság azt jelzi, hogy a PipelineRun-erőforrást újra létre kell hozni, még akkor is, ha a konfiguráció nem változott. Győződjön meg arról, hogy a forceUpdateTag minden alkalommal más, amikor újratelepíti a PipelineRun-erőforrást. Az alábbi példa újból létrehoz egy PipelineRun az exportáláshoz. Az aktuális datetime érték a forceUpdateTag beállítására szolgál, így biztosítva, hogy ez a tulajdonság mindig egyedi legyen.

```console
CURRENT_DATETIME=`date +"%Y-%m-%d:%T"`
```

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json \
  --parameters forceUpdateTag=$CURRENT_DATETIME
```

## <a name="delete-pipeline-resources"></a>Folyamat erőforrásainak törlése

A következő példa az az [erőforrás törlése][az-resource-delete] paranccsal törli a jelen cikkben létrehozott folyamat-erőforrásokat. Az erőforrás-azonosítókat korábban környezeti változók tárolták.

```
# Delete export resources
az resource delete \
--resource-group $SOURCE_RG \
--ids $EXPORT_RES_ID $EXPORT_RUN_RES_ID \
--api-version 2019-12-01-preview

# Delete import resources
az resource delete \
--resource-group $TARGET_RG \
--ids $IMPORT_RES_ID $IMPORT_RUN_RES_ID \
--api-version 2019-12-01-preview
```

## <a name="troubleshooting"></a>Hibaelhárítás

* **Hibák vagy hibák Template deployment**
  * Ha egy folyamat futtatása sikertelen, tekintse meg a `pipelineRunErrorMessage` futtatási erőforrás tulajdonságát.
  * A sablonok általános telepítési hibáiért lásd: [ARM-sablonok központi telepítésének hibaelhárítása](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
* **A tárolási Blobok exportálásával vagy importálásával kapcsolatos problémák**
  * Lehet, hogy az SAS-jogkivonat lejárt, vagy nem rendelkezik megfelelő engedélyekkel a megadott exportálási vagy importálási futtatáshoz.
  * Előfordulhat, hogy a forrás Storage-fiókban meglévő Storage-blob nem lesz felülírva több exportálási futtatás során. Győződjön meg arról, hogy a OverwriteBlob beállítás be van állítva az Exportálás futtatása elemre, és az SAS-jogkivonat rendelkezik a megfelelő engedélyekkel.
  * Előfordulhat, hogy a tárolási blob a cél Storage-fiókban nem törlődik az importálás sikeres futtatása után. Győződjön meg arról, hogy a DeleteBlobOnSuccess beállítás be van állítva az importálási futtatásban, és az SAS-jogkivonat rendelkezik a megfelelő engedélyekkel.
  * A tárolási blob nem lett létrehozva vagy törölve. Győződjön meg arról, hogy az exportálási vagy importálási futtatáshoz megadott tároló létezik, vagy létezik a megadott tárolási blob a manuális importálási futtatáshoz. 
* **AzCopy kapcsolatos problémák**
  * Lásd: [AzCopy kapcsolatos problémák elhárítása](../storage/common/storage-use-azcopy-configure.md#troubleshoot-issues).  
* **Összetevők átvitelével kapcsolatos problémák**
  * Nem minden összetevő, vagy nincs, át van adva. Győződjön meg róla, hogy az exportálási folyamatban lévő összetevők helyesírása és a blob neve az exportálási és importálási futtatásokban. Erősítse meg, hogy legfeljebb 50 összetevőt helyez át.
  * Lehet, hogy a folyamat futása nem fejeződött be. Az exportálási vagy importálási Futtatás hosszabb időt is igénybe vehet. 
  * Az egyéb folyamatokkal kapcsolatos problémák esetén adja meg az Exportálás futtatásának vagy importálásának a Azure Container Registry csapatnak való futtatásához szükséges telepítési [korrelációs azonosítót](../azure-resource-manager/templates/deployment-history.md) .
* **Problémák a rendszerkép egy fizikailag elszigetelt környezetben való kihúzásakor**
  * Ha a külső rétegekkel kapcsolatos hibákat látja, vagy megkísérli feloldani a mcr.microsoft.com, amikor a rendszerképet fizikailag elkülönített környezetben kísérli meg lekérni, a rendszerkép jegyzékfájlja valószínűleg nem terjeszthető réteggel rendelkezik. A fizikailag elszigetelt környezet jellegéből adódóan ezek a lemezképek gyakran nem fognak lekérni. A külső beállításjegyzékre mutató hivatkozásokat a rendszerkép jegyzékfájljának ellenőrzésével ellenőrizheti. Ebben az esetben a nem terjeszthető rétegeket a nyilvános felhőben lévő ACR-be kell küldenie az adott lemezképhez tartozó exportálási folyamat telepítése előtt. Ennek módjáról a nem [terjeszthető rétegek leküldése hogyan a beállításjegyzékbe](./container-registry-faq.md#how-do-i-push-non-distributable-layers-to-a-registry) című témakörben talál útmutatást.

## <a name="next-steps"></a>Következő lépések

Ha egyetlen tároló lemezképét szeretné importálni egy Azure Container registrybe egy nyilvános beállításjegyzékből vagy egy másik privát beállításjegyzékből, tekintse meg az az [ACR import][az-acr-import] parancs-referenciát.

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/



<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-login]: /cli/azure/reference-index#az-login
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-secret-show]: /cli/azure/keyvault/secret#az-keyvault-secret-show
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-storage-container-generate-sas]: /cli/azure/storage/container#az-storage-container-generate-sas
[az-storage-blob-list]: /cli/azure/storage/blob#az-storage-blob-list
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-deployment-group-delete]: /cli/azure/deployment/group#az-deployment-group-delete
[az-deployment-group-show]: /cli/azure/deployment/group#az-deployment-group-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-import]: /cli/azure/acr#az-acr-import
[az-resource-delete]: /cli/azure/resource#az-resource-delete
