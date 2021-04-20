---
title: Összetevők átvitele
description: Rendszerképek vagy egyéb összetevők gyűjteményének átvitele egy tároló-beállításjegyzékből egy másikba egy átviteli folyamat Azure Storage-fiókokkal történő létrehozásával
ms.topic: article
ms.date: 10/07/2020
ms.custom: ''
ms.openlocfilehash: 7784ce3e5e0171c84fb1f1da6e69f7d38bec9637
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737405"
---
# <a name="transfer-artifacts-to-another-registry"></a>Összetevők átvitele másik beállításjegyzékbe

Ez a cikk bemutatja, hogyan továbbíthatók rendszerképek vagy más beállításjegyzék-összetevők gyűjteményei az egyik Azure Container Registryből egy másikba. A forrás- és célregisztrálók azonos vagy eltérő előfizetésben, bérlőkben, Azure-felhőkben Active Directory vagy fizikailag leválasztott felhőkben is lehetek. 

Az összetevők átviteléhez létre  kell hoznia egy átviteli folyamatot, amely két regisztrációs adatbázis között replikálja az összetevőket a [blobtároló használatával:](../storage/blobs/storage-blobs-introduction.md)

* A forrás-beállításjegyzékből származó összetevők egy forrás tárfiókban lévő blobba vannak exportálva 
* A rendszer átmásolja a blobot a forrás tárfiókból egy cél tárfiókba
* A cél tárfiókban lévő blob összetevőkként lesz importálva a cél beállításjegyzékbe. Beállíthatja, hogy az importálási folyamat akkor aktiválódik, amikor az összetevő blobja frissül a céltárolóban.

Az átvitel ideális két Azure-beli tárolóregisztráló közötti tartalom másolására fizikailag leválasztott felhőkben, az egyes felhők tárfiókja által közvetítve. Ha ehelyett rendszerképeket szeretne másolni a csatlakoztatott felhőkben található tárolóregisztrálókból, beleértve a Docker Hub és más felhőgyártókat [is,](container-registry-import-images.md) a rendszerkép importálása ajánlott.

Ebben a cikkben sablon Azure Resource Manager használatával hozza létre és futtatja az átviteli folyamatot. Az Azure CLI használatával kiépíti a társított erőforrásokat, például a titkos tárolókat. Az Azure CLI 2.2.0-s vagy újabb verziója ajánlott. Ha telepíteni vagy frissíteni szeretné a parancssori felületet, olvassa el [az Azure CLI telepítését][azure-cli] ismertető témakört.

Ez a szolgáltatás a Prémium tároló-beállításjegyzék **szolgáltatási** szinten érhető el. A beállításjegyzék szolgáltatásszintekkel és korlátozásokkal kapcsolatos információkért lásd a Azure Container Registry [rétegeket.](container-registry-skus.md)

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="prerequisites"></a>Előfeltételek

* **Tároló-beállításjegyzékek** – Szükség van egy meglévő forrás-beállításjegyzékre, amely az átvitelhez szükséges összetevőkből és egy céljegyzékből is létezik. Az ACR-átvitel célja a fizikailag leválasztott felhők közötti mozgás. A teszteléshez a forrás- és célregisztrálók ugyanabban vagy egy másik Azure-előfizetésben, Active Directory vagy felhőben is lehetnek. 

   Ha létre kell hoznia egy regisztrációs adatbázist, lásd: Rövid útmutató: Privát tároló-beállításjegyzék létrehozása [az Azure CLI használatával.](container-registry-get-started-azure-cli.md) 
* **Tárfiókok** – Forrás- és céltárfiókokat hozhat létre egy ön által választott előfizetésben és helyen. Tesztelési célokra használhatja ugyanazt az előfizetést vagy előfizetéseket, mint a forrás- és célregisztrálók. A több felhőre vonatkozó forgatókönyvek esetében általában külön tárfiókot kell létrehozni az egyes felhőkben. 

  Ha szükséges, hozza létre a tárfiókokat az [Azure CLI vagy](../storage/common/storage-account-create.md?tabs=azure-cli) más eszközök használatával. 

  Hozzon létre egy blobtárolót az összetevők átviteléhez az egyes fiókokban. Hozzon létre például egy transfer nevű *tárolót.* Két vagy több átviteli folyamat osztozhat ugyanazon a tárfiókon, de különböző tárolók hatókörét kell használnia.
* **Kulcstartók** – Kulcstartókra van szükség a forrás- és céltárfiókok eléréséhez használt SAS-jogkivonatok titkos kulcsának tárolásához. Hozza létre a forrás- és célkulcstartókat ugyanabban az Azure-előfizetésben vagy -előfizetésben, mint a forrás- és célregisztrálókat. Bemutatási célból a cikkben használt sablonok és parancsok azt is feltételezik, hogy a forrás- és célkulcstartók ugyanabban az erőforráscsoportban találhatók, mint a forrás- és célregisztrálók. A gyakori erőforráscsoportok használata nem szükséges, de leegyszerűsíti a cikkben használt sablonokat és parancsokat.

   Ha szükséges, hozzon létre kulcstartókat az [Azure CLI vagy](../key-vault/secrets/quick-create-cli.md) más eszközök használatával.

* **Környezeti változók** – A cikkben például a parancsokhoz állítsa be a következő környezeti változókat a forrás- és célkörnyezetekhez. Minden példa a Bash-rendszerhéjhoz van formázva.
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>A forgatókönyv áttekintése

A következő három folyamaterőforrást hozza létre a regisztrációs jegyzékek közötti rendszerképátvitelhez. Mindegyik PUT-műveletekkel jön létre. Ezek az erőforrások a forrás- *és* célregisztrálókon és a tárfiókon működnek.  

A tárolóhitelesítés SAS-jogkivonatokat használ, amelyek titkos kulcsként vannak kezeltek a kulcstartókban. A folyamatok felügyelt identitások használatával olvassák a tárolókban tárolt titkos kulcsokat.

* **[ExportPipeline –](#create-exportpipeline-with-resource-manager)** Tartós erőforrás, amely magas szintű  információkat tartalmaz a forrás beállításjegyzékről és a tárfiókról. Ezek az információk tartalmazzák a forrástároló blobtároló URI-ját és a forrás SAS-jogkivonatot kezelő kulcstartót. 
* **[ImportPipeline –](#create-importpipeline-with-resource-manager)** Tartós erőforrás, amely magas szintű  információkat tartalmaz a cél beállításjegyzékről és a tárfiókról. Ezek az információk tartalmazzák a céltároló blobtároló URI-ját és a cél SAS-jogkivonatot kezelő kulcstartót. Az importálási eseményindító alapértelmezés szerint engedélyezve van, így a folyamat automatikusan lefut, amikor egy összetevőblob a céltárolóba kerül. 
* **[PipelineRun](#create-pipelinerun-for-export-with-resource-manager)** – Az ExportPipeline vagy az ImportPipeline erőforrás meghívására használt erőforrás.  
  * Az ExportPipeline manuális futtatásához hozzon létre egy PipelineRun erőforrást, és adja meg az exportálni kívánt munkatermékeket.  
  * Ha egy importálási eseményindító engedélyezve van, az ImportPipeline automatikusan lefut. Manuálisan is futtatható a PipelineRun használatával. 
  * Jelenleg legfeljebb **50** összetevő továbbítható az egyes PipelineRun értékekkel.

### <a name="things-to-know"></a>Tudnivalók
* Az ExportPipeline és az ImportPipeline jellemzően különböző Active Directory a forrás- és célfelhőkhöz társított bérlőkben. Ehhez a forgatókönyvhöz külön felügyelt identitások és kulcstartókra van szükség az erőforrások exportálásához és importálásához. Tesztelési célból ezek az erőforrások elhelyezhetőek ugyanabban a felhőben, és megoszthatják az identitásokat.
* Alapértelmezés szerint az ExportPipeline és az ImportPipeline sablonok lehetővé teszik, hogy a rendszer által hozzárendelt felügyelt identitás hozzáférjen a Key Vault titkos kulcsához. Az ExportPipeline és az ImportPipeline sablon az Ön által megadott, felhasználó által hozzárendelt identitást is támogatja. 

## <a name="create-and-store-sas-keys"></a>SAS-kulcsok létrehozása és tárolása

Az átvitel közös hozzáférésű jogosultság jogosultsági (SAS) jogkivonatokat használ a forrás- és célkörnyezetekben a tárfiókok eléréséhez. Hozza létre és tárolja a jogkivonatokat a következő szakaszokban leírtak szerint.  

### <a name="generate-sas-token-for-export"></a>SAS-jogkivonat létrehozása exportáláshoz

Futtassa [az az storage container generate-sas][az-storage-container-generate-sas] parancsot egy SAS-jogkivonat létrehozásához a forrás tárfiókban található tárolóhoz, amely az összetevő exportálására használható.

*Ajánlott jogkivonat-engedélyek:* Olvasás, Írás, Lista, Hozzáadás. 

A következő példában a parancs kimenete a EXPORT_SAS környezeti változóhoz van rendelve, a "?" karakter előtaggal. Frissítse `--expiry` a környezet értékét:

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>SAS-jogkivonat tárolása exportáláshoz

Tárolja az SAS-jogkivonatot a forrás Azure Key Vaultban [az az keyvault secret set használatával:][az-keyvault-secret-set]

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>SAS-jogkivonat létrehozása importáláshoz

Futtassa [az az storage container generate-sas][az-storage-container-generate-sas] parancsot egy SAS-jogkivonat létrehozásához a cél tárfiókban található tárolóhoz, amely az összetevő importálására használható.

*Ajánlott jogkivonat-engedélyek:* Olvasás, Törlés, Lista

A következő példában a parancs kimenete a IMPORT_SAS környezeti változóhoz van rendelve, a "?" karakter előtaggal. Frissítse `--expiry` a környezet értékét:

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>SAS-jogkivonat tárolása importáláshoz

Tárolja az SAS-jogkivonatot a cél Azure Key Vaultban [az az keyvault secret set használatával:][az-keyvault-secret-set]

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>ExportPipeline létrehozása Resource Manager

Hozzon létre egy ExportPipeline erőforrást a forrásként szolgáló tárolójegyzékhez a Azure Resource Manager használatával.

Másolja az ExportPipeline Resource Manager [sablonfájlokat](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines) egy helyi mappába.

Adja meg a következő paraméterértékeket a `azuredeploy.parameters.json` fájlban:

|Paraméter  |Érték  |
|---------|---------|
|registryName (beállításjegyzék neve)     | A forrás tárolójegyzék neve      |
|exportPipelineName     |  Az exportálási folyamathoz választott név       |
|targetUri     |  A forráskörnyezetben (az exportálási folyamat célja) található Storage-tároló URI-ja.<br/>Például: `https://sourcestorage.blob.core.windows.net/transfer`       |
|keyVaultName     |  A forráskulcs-tároló neve  |
|sasTokenSecretName  | Az SAS-jogkivonat titkos kulcsának neve a forráskulcs-tárolóban <br/>Például: acrexportsas

### <a name="export-options"></a>Exportálási beállítások

Az `options` exportálási folyamatok tulajdonsága támogatja a választható logikai értékeket. A következő értékek ajánlottak:

|Paraméter  |Érték  |
|---------|---------|
|Lehetőségek | OverwriteBlobs – Meglévő célblobok felülírása<br/>ContinueOnErrors – Folytathatja a fennmaradó összetevők exportálását a forrásjegyzékben, ha egy összetevő exportálása meghiúsul.

### <a name="create-the-resource"></a>Az erőforrás létrehozása

Futtassa [az az deployment group create parancsokat][az-deployment-group-create] egy *exportPipeline* nevű erőforrás létrehozásához az alábbi példákban látható módon. Alapértelmezés szerint a példasablon az első lehetőséggel engedélyezi a rendszer által hozzárendelt identitást az ExportPipeline erőforrásban. 

A második lehetőséggel meg lehet adni az erőforrásnak egy felhasználó által hozzárendelt identitást. (A felhasználó által hozzárendelt identitás létrehozása nem látható.)

Bármelyik lehetőséget is választja, a sablon úgy konfigurálja az identitást, hogy hozzáférjen az SAS-jogkivonathoz az exportálási kulcstartóban. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>1. lehetőség: Erőforrás létrehozása és a rendszer által hozzárendelt identitás engedélyezése

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>2. lehetőség: Erőforrás létrehozása és felhasználó által hozzárendelt identitás megszabadása

Ebben a parancsban további paraméterként adja meg a felhasználó által hozzárendelt identitás erőforrás-azonosítóját.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

A parancs kimenetében jegyezze fel a folyamat erőforrás-azonosítóját ( `id` ). Ezt az értéket tárolhatja egy környezeti változóban későbbi használatra az [az deployment group show futtatásával.][az-deployment-group-show] Például:

```azurecli
EXPORT_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>ImportPipeline létrehozása Resource Manager 

Hozzon létre egy ImportPipeline erőforrást a céltároló-beállításjegyzékben a Azure Resource Manager üzembe helyezési folyamatával. Alapértelmezés szerint a folyamat automatikusan importálható, ha a célkörnyezet tárfiókja rendelkezik összetevőblobokkal.

Másolja az ImportPipeline Resource Manager [sablonfájlokat](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines) egy helyi mappába.

Adja meg a következő paraméterértékeket a `azuredeploy.parameters.json` fájlban:

Paraméter  |Érték  |
|---------|---------|
|registryName (beállításjegyzék neve)     | A céltároló-beállításjegyzék neve      |
|importPipelineName     |  Az importálási folyamathoz választott név       |
|sourceUri     |  A tároló URI-ja a célkörnyezetben (az importálási folyamat forrása).<br/>Például: `https://targetstorage.blob.core.windows.net/transfer/`|
|keyVaultName     |  A célkulcstartó neve |
|sasTokenSecretName     |  Az SAS-jogkivonat titkos kulcsának neve a célkulcstartóban<br/>Példa: acr importsas |

### <a name="import-options"></a>Importálási lehetőségek

Az `options` importálási folyamat tulajdonsága támogatja a választható logikai értékeket. A következő értékek ajánlottak:

|Paraméter  |Érték  |
|---------|---------|
|Lehetőségek | OverwriteTags – Meglévő célcímkék felülírása<br/>DeleteSourceBlobOnSuccess – A forrástároló blob törlése a céljegyzékbe való sikeres importálás után<br/>ContinueOnErrors – Ha egy összetevő importálása meghiúsul, folytassa a fennmaradó összetevők importálását a céljegyzékben.

### <a name="create-the-resource"></a>Az erőforrás létrehozása

Az [az deployment group create parancs][az-deployment-group-create] futtatásával hozzon létre egy *importPipeline* nevű erőforrást az alábbi példákban látható módon. Alapértelmezés szerint a példasablon az első lehetőséggel engedélyezi a rendszer által hozzárendelt identitást az ImportPipeline erőforrásban. 

A második lehetőséggel meg lehet adni az erőforrásnak egy felhasználó által hozzárendelt identitást. (A felhasználó által hozzárendelt identitás létrehozása nem látható.)

Bármelyik lehetőséget is választja, a sablon konfigurálja az identitást, hogy hozzáférjen az SAS-jogkivonathoz az importálási kulcstartóban. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>1. lehetőség: Erőforrás létrehozása és a rendszer által hozzárendelt identitás engedélyezése

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json 
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>2. lehetőség: Erőforrás létrehozása és felhasználó által hozzárendelt identitás megszabadása

Ebben a parancsban további paraméterként adja meg a felhasználó által hozzárendelt identitás erőforrás-azonosítóját.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Ha manuálisan tervezi futtatni az importálást, jegyezze fel a folyamat erőforrás-azonosítóját ( `id` ). Ezt az értéket egy környezeti változóban tárolhatja későbbi használatra az [az deployment group show parancs futtatásával.][az-deployment-group-show] Például:

```azurecli
IMPORT_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>Create PipelineRun for export with Resource Manager 

Hozzon létre egy PipelineRun erőforrást a forrásként szolgáló tárolójegyzékhez a Azure Resource Manager üzembe helyezési folyamatával. Ez az erőforrás futtatja a korábban létrehozott ExportPipeline erőforrást, és blobként exportálja a megadott munkatermékeket a tároló-beállításjegyzékből a forrás tárfiókba.

Másolja a PipelineRun Resource Manager [sablonfájlokat](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export) egy helyi mappába.

Adja meg a következő paraméterértékeket a `azuredeploy.parameters.json` fájlban:

|Paraméter  |Érték  |
|---------|---------|
|registryName (beállításjegyzék neve)     | A forrásként szolgáló tárolójegyzék neve      |
|pipelineRunName     |  A futtatáshoz választott név       |
|pipelineResourceId     |  Az exportálási folyamat erőforrás-azonosítója.<br/>Például: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|targetName (célnév)     |  A forrás tárfiókba exportált artifacts blob választott neve, például *myblob*
|Leletek | Az átvitelre cél forrás-összetevők tömbje címkékként vagy jegyzékkivonatokként<br/>Például: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

Ha a PipelineRun erőforrást azonos tulajdonságokkal újraalkotja, a [forceUpdateTag](#redeploy-pipelinerun-resource) tulajdonságot is használnia kell.

Futtassa [az az deployment group create futtatását][az-deployment-group-create] a PipelineRun erőforrás létrehozásához. Az alábbi példa az *exportPipelineRun* nevet tartalmazza.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

Későbbi használatra tárolja a folyamat futásának erőforrás-azonosítóját egy környezeti változóban:

```azurecli
EXPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Az összetevők exportálása több percig is eltarthat. Ha az üzembe helyezés sikeresen befejeződött, ellenőrizze az összetevők  exportálását a forrás tárfiók átviteli tárolóban található exportált blob listázásával. Futtassa például az [az storage blob list parancsot:][az-storage-blob-list]

```azurecli
az storage blob list \
  --account-name $SOURCE_SA \
  --container transfer \
  --output table
```

## <a name="transfer-blob-optional"></a>Blob átvitele (nem kötelező) 

Az AzCopy eszközzel vagy más módszerekkel [blobadatokat](../storage/common/storage-use-azcopy-v10.md#transfer-data) továbbít a forrás tárfiókból a cél tárfiókba.

A következő parancs például átmásolja a myblob adatokat a forrásfiókban található átviteli tárolóból a [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md) célfiókban található *átviteli* tárolóba.  Ha a blob létezik a célfiókban, a blob felül lesz írva. A hitelesítés a forrás- és céltárolókhoz megfelelő engedélyekkel rendelkező SAS-jogkivonatokat használ. (A jogkivonatok létrehozási lépései nem jelennek meg.)

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>ImportPipeline erőforrás aktiválása

Ha engedélyezte az ImportPipeline paramétert (ez az alapértelmezett érték), a folyamat azután aktiválódik, hogy a blobot a cél `sourceTriggerStatus` tárfiókba másolta. Az összetevők importálása több percig is eltarthat. Ha az importálás sikeresen befejeződött, ellenőrizze az összetevők importálását a céltároló-beállításjegyzék adattárának listázásával. Futtassa például az [az acr repository list et:][az-acr-repository-list]

```azurecli
az acr repository list --name <target-registry-name>
```

Ha nem engedélyezték az importálási folyamat paraméterét, futtassa manuálisan az ImportPipeline erőforrást az alábbi `sourceTriggerStatus` szakaszban látható módon. 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>Create PipelineRun for import with Resource Manager (nem kötelező) 
 
A PipelineRun erőforrás használatával is aktiválhat egy ImportPipeline indítót a cél tárolójegyzékbe történő összetevő-importáláshoz.

Másolja a PipelineRun Resource Manager [sablonfájlokat](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import) egy helyi mappába.

Adja meg a következő paraméterértékeket a `azuredeploy.parameters.json` fájlban:

|Paraméter  |Érték  |
|---------|---------|
|registryName (beállításjegyzék neve)     | A céltároló regisztrációs adatbázisának neve      |
|pipelineRunName     |  A futtatáshoz választott név       |
|pipelineResourceId     |  Az importálási folyamat erőforrás-azonosítója.<br/>Például: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|sourceName (forrásnév)     |  A tárfiókban exportált összetevők (például *myblob)* meglévő blobneve

Ha a PipelineRun erőforrást azonos tulajdonságokkal újraalkotja, a [forceUpdateTag](#redeploy-pipelinerun-resource) tulajdonságot is használnia kell.

Futtassa [az az deployment group create futtatását][az-deployment-group-create] az erőforrás futtatásához.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

Későbbi használatra tárolja a folyamat futásának erőforrás-azonosítóját egy környezeti változóban:

```azurecli
IMPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Ha az üzembe helyezés sikeresen befejeződött, ellenőrizze az összetevők importálását a céltároló-beállításjegyzék adattárának listázásával. Futtassa például az [az acr repository list et:][az-acr-repository-list]

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="redeploy-pipelinerun-resource"></a>Folyamat ismételt üzembeásaErőforrás újratelepítése

Ha a PipelineRun erőforrást azonos tulajdonságokkal újraalkotja, akkor a **forceUpdateTag** tulajdonságot kell használnia. Ez a tulajdonság azt jelzi, hogy a PipelineRun erőforrást akkor is újra létre kell hozni, ha a konfiguráció nem változott. Győződjön meg arról, hogy a forceUpdateTag minden alkalommal eltérő, amikor újra üzembe lép a PipelineRun erőforrás. Az alábbi példa újra létrehoz egy PipelineRun for export adatokat. Az aktuális dátum/idő érték a forceUpdateTag beállítására használható, ezzel biztosítva, hogy ez a tulajdonság mindig egyedi legyen.

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

## <a name="delete-pipeline-resources"></a>Folyamaterőforrások törlése

Az alábbi példaparancsok az [az resource delete][az-resource-delete] parancsot használják a cikkben létrehozott folyamaterőforrások törléséhez. Az erőforrás-adatok korábban környezeti változókban voltak tárolva.

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

* **Template deployment vagy hibák**
  * Ha egy folyamat futtatása sikertelen, nézze meg `pipelineRunErrorMessage` a futtatás erőforrásának tulajdonságát.
  * A sablonok üzembe helyezési hibáival kapcsolatos gyakori hibákért lásd: [ARM-sablonok üzembe helyezésének hibaelhárítása](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
* **A tároló elérésével kapcsolatos problémák**<a name="problems-accessing-storage"></a>
  * Ha hibát lát a `403 Forbidden` tárolóban, valószínűleg problémája van az SAS-jogkivonattal.
  * Előfordulhat, hogy az SAS-jogkivonat jelenleg nem érvényes. Előfordulhat, hogy az SAS-jogkivonat lejárt, vagy a tárfiók kulcsai megváltoztak az SAS-jogkivonat létrehozása óta. Ellenőrizze, hogy az SAS-jogkivonat érvényes-e. Próbálja meg használni az SAS-jogkivonatot a tárfiók tárolóhoz való hozzáféréshez való hitelesítéshez. Például helyezzen egy meglévő blobvégpontot, majd az SAS-jogkivonatot egy új Microsoft Edge InPrivate-ablak címsorába, vagy töltsön fel egy blobot a tárolóba az SAS-jogkivonattal a `az storage blob upload` használatával.
  * Előfordulhat, hogy az SAS-jogkivonat nem rendelkezik elegendő engedélyezett erőforrástípussal. Ellenőrizze, hogy az SAS-jogkivonat rendelkezik-e engedélyekkel a szolgáltatáshoz, a tárolóhoz és az objektumhoz az Engedélyezett erőforrástípusok alatt ( a `srt=sco` SAS-jogkivonatban).
  * Előfordulhat, hogy az SAS-jogkivonat nem rendelkezik megfelelő engedélyekkel. Az exportálási folyamatokhoz a szükséges SAS-jogkivonat-engedélyek a következőek: Olvasás, Írás, Lista és Hozzáadás. Importálási folyamatok esetén a szükséges SAS-jogkivonat-engedélyek a következőek: Olvasás, Törlés és Lista. (A Törlés engedélyre csak akkor van szükség, ha az importálási folyamaton `DeleteSourceBlobOnSuccess` engedélyezve van a beállítás.)
  * Előfordulhat, hogy az SAS-jogkivonat nem csak HTTPS használatára van konfigurálva. Ellenőrizze, hogy az SAS-jogkivonat csak HTTPS használatára van-e konfigurálva ( `spr=https` a SAS-jogkivonatban).
* **Tárolási blobok exportálásával vagy importálásával kapcsolatos problémák**
  * Előfordulhat, hogy az SAS-jogkivonat érvénytelen, vagy nem rendelkezik megfelelő engedélyekkel a megadott exportálási vagy importálási futtatáshoz. Lásd: [Storage-hozzáféréssel kapcsolatos problémák.](#problems-accessing-storage)
  * Előfordulhat, hogy a forrás tárfiók meglévő storage blobja nem lesz felülírva több exportálási futtatás során. Győződjön meg arról, hogy az OverwriteBlob beállítás be van állítva az exportálási futtatásban, és hogy az SAS-jogkivonat megfelelő engedélyekkel rendelkezik.
  * Előfordulhat, hogy a cél tárfiókban lévő Storage-blob nem törlődik a sikeres importálási futtatás után. Ellenőrizze, hogy a DeleteBlobOnSuccess beállítás be van-e állítva az importálási futtatásban, és hogy az SAS-jogkivonat megfelelő engedélyekkel rendelkezik-e.
  * A Storage-blob nincs létrehozva vagy törölve. Győződjön meg arról, hogy az exportálási vagy importálási futtatás során megadott tároló létezik, vagy a megadott tárolóblob létezik manuális importálási futtatáshoz. 
* **AzCopyval kapcsolatos problémák**
  * Lásd: [AzCopy-problémák elhárítása.](../storage/common/storage-use-azcopy-configure.md)  
* **Összetevők átvitelével kapcsolatos problémák**
  * Nem minden összetevő lesz átadva, vagy egyik sem. Erősítse meg az összetevők helyesírását az exportálási futtatás során, valamint a blob nevét az exportálási és importálási futtatás során. Győződjön meg arról, hogy legfeljebb 50 összetevő átvitelét kezdeményezi.
  * Előfordulhat, hogy a folyamat futtatása nem fejeződött be. Az exportálási vagy importálási futtatás egy kis időt is vegyen át. 
  * Egyéb folyamatokkal kapcsolatos problémák [](../azure-resource-manager/templates/deployment-history.md) esetén adja meg az exportálási futtatás vagy az importálási futtatás üzembe helyezési korrelációs azonosítóját a Azure Container Registry csapatnak.
* **Problémák a kép fizikai elkülönített környezetben való lekért lehúzása esetén**
  * Ha külső rétegekkel kapcsolatos hibákat lát, vagy megkísérli feloldani a mcr.microsoft.com amikor fizikailag elkülönített környezetben kísérel meg le egy képet, a rendszerkép jegyzékfájlja valószínűleg nem terjeszthető rétegekkel rendelkezik. A fizikailag elkülönített környezet természetéből adódóan ezek a lemezképek gyakran nem lesznek lekértek. Erről meggyőződhet, ha ellenőrzi a rendszerkép jegyzékfájlját, hogy vannak-e külső regisztrációs adatokra mutató hivatkozások. Ebben az esetben le kell helyeznie a nem terjeszthető rétegeket a nyilvános felhőbeli ACR-be, mielőtt üzembe helyez egy exportálási folyamatfuttatást a rendszerképhez. Ehhez a nem terjeszthető rétegek beállításjegyzékbe való leküldését Hogyan tekintse [meg.](./container-registry-faq.md#how-do-i-push-non-distributable-layers-to-a-registry)

## <a name="next-steps"></a>Következő lépések

Ha egy tárolólemezképet egy nyilvános regisztrációs adatbázisból vagy egy másik privát beállításjegyzékből importál egy Azure Container Registrybe, tekintse meg az [az acr import][az-acr-import] parancsreferenciát.

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
