---
title: Oktatóanyag – jelenet megjelenítése a felhőben
description: Ismerje meg, hogyan teheti meg az Autodesk 3ds Max jelenetet az Arnold használatával a Batch rendering Service és az Azure Command-Line Interface segítségével
ms.topic: tutorial
ms.date: 12/30/2020
ms.custom: mvc, devx-track-azurecli
ROBOTS: NOINDEX
ms.openlocfilehash: 5165e5feb566a4b9081f40b681b92aafa143869f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491741"
---
# <a name="tutorial-render-a-scene-with-azure-batch"></a>Oktatóanyag: Jelenetek renderelése az Azure Batch segítségével

Az Azure Batch felhőméretű renderelési képességeket biztosít használatalapú fizetéssel. Az Azure Batch támogatja renderelési alkalmazások, például az Autodesk Maya, a 3ds Max, az Arnold és a V-Ray használatát. Ez az oktatóanyag azt mutatja be, hogy milyen lépésekkel renderelhet kisebb jeleneteket a Batch és az Azure parancssori felület használatával. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> - jelenet feltöltése az Azure-tárolóba;
> - Batch-készlet létrehozása rendereléshez;
> - egyetlen képkockából álló jelenet renderelése;
> - a készlet méretezése, majd egy több képkockából álló jelenet renderelése;
> - a renderelt kimenet letöltése.

Az oktatóanyagban egy 3ds Max-jelenetet fog renderelni a Batch és az [Arnold](https://www.autodesk.com/products/arnold/overview) sugárkövetéses renderelő használatával. A Batch-készletet egy előre telepített, használatalapú fizetési licenccel bíró grafikával és renderelő alkalmazásokkal rendelkező Azure Marketplace-képet használ.

## <a name="prerequisites"></a>Előfeltételek

- Ha használatalapú fizetést szeretne alkalmazni a Batch renderelő alkalmazásaira, használatalapú előfizetésre vagy egy egyéb Azure vásárlási opcióra van szüksége. **A használatalapú fizetés licencelése nem támogatott, ha olyan ingyenes Azure-ajánlatot használ, amely elkölthető kreditet biztosít.**

- Az oktatóanyagban szereplő 3ds Max-mintajelenet, valamint a Bash-mintaszkript és a JSON konfigurációs fájlok elérhetők a [GitHubon](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/batch/render-scene). A 3ds Max-jelenet az [Autodesk 3ds Max-mintafájlok](https://download.autodesk.com/us/support/files/3dsmax_sample_files/2017/Autodesk_3ds_Max_2017_English_Win_Samples_Files.exe) közül származik. (Az Autodesk 3ds Max-mintafájlok az Attribution-NonCommercial-Share Alike Creative Commons-licenc szerint érhetők el. Copyright &copy; Autodesk, Inc.)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Az oktatóanyaghoz az Azure CLI 2.0.20 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

> [!TIP]
> A Azure Batch Extension templates GitHub-tárházban megtekintheti az [Arnold-feladatok sablonjait](https://github.com/Azure/batch-extension-templates/tree/master/templates/arnold/render-windows-frames) .

## <a name="create-a-batch-account"></a>Batch-fiók létrehozása

Ha eddig még nem tette meg, hozzon létre egy erőforráscsoportot, egy Batch-fiókot és egy kapcsolt tárfiókot az előfizetésében.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus2* helyen.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus2
```

Az [az storage account create](/cli/azure/storage/account#az-storage-account-create) paranccsal hozzon létre egy Azure Storage-fiókot az erőforráscsoportban. Ebben az oktatóanyagban a tárfiókot fogja használni a bemeneti 3ds Max-jelenet és a renderelt kimenet tárolására.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

Az [az batch account create](/cli/azure/batch/account#az-batch-account-create) paranccsal hozzon létre egy Batch-fiókot. Az alábbi példa egy *mybatchaccount* nevű Batch-fiókot hoz létre a *myResourceGroup* erőforráscsoportban, és összekapcsolja a létrehozott tárfiókkal.  

```azurecli-interactive
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

A számítási készletek és feladatok létrehozásához és kezeléséhez hitelesítést kell végeznie a Batchben. Jelentkezzen be a fiókba az [az batch account login](/cli/azure/batch/account#az-batch-account-login) paranccsal. A bejelentkezés után az `az batch` parancsok ezt a fiókkörnyezetet használják. Az alábbi példa megosztott kulcsos hitelesítést használ a Batch-fiók neve és kulcsa alapján. A Batch az [Azure Active Directory](batch-aad-auth.md) segítségével történő hitelesítést is támogatja az egyéni felhasználók vagy a felügyelet nélküli alkalmazások hitelesítéséhez.

```azurecli-interactive
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```

## <a name="upload-a-scene-to-storage"></a>Jelenet feltöltése a tárolóba

A bemeneti jelenet tárolóba való feltöltéséhez először be kell lépnie a tárfiókba, és létre kell hoznia egy céltárolót a blobok számára. Az Azure Storage-fiók eléréséhez exportálja az `AZURE_STORAGE_KEY` és az `AZURE_STORAGE_ACCOUNT` környezeti változót. Az első Bash-felületparancs az [az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list) parancs használatával lekéri az első fiókkulcsot. A környezeti változók megadása után a tárolóparancsok ezt a fiókkörnyezetet használják.

```azurecli-interactive
export AZURE_STORAGE_KEY=$(az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup -o tsv --query [0].value)

export AZURE_STORAGE_ACCOUNT=mystorageaccount
```

Most hozzon létre egy blobtárolót a tárfiókban a jelenetfájlok számára. Az alábbi példa az [az storage container create](/cli/azure/storage/container#az-storage-container-create) parancs használatával létrehoz egy *scenefiles* nevű blobtárolót nyilvános olvasási hozzáféréssel.

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name scenefiles
```

Töltse le a `MotionBlur-Dragon-Flying.max` jelenetet a [GitHubról](https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max) egy helyi munkakönyvtárba. Például:

```azurecli-interactive
wget -O MotionBlur-DragonFlying.max https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max
```

Töltse fel a jelenetfájlt a helyi munkakönyvtárból a blobtárolóba. Az alábbi példa az [az storage blob upload-batch](/cli/azure/storage/blob#az-storage-blob-upload-batch) parancsot használja, amellyel egyszerre több fájl tölthető fel:

```azurecli-interactive
az storage blob upload-batch \
    --destination scenefiles \
    --source ./
```

## <a name="create-a-rendering-pool"></a>Renderelési készlet létrehozása

Hozzon létre egy Batch-készletet a rendereléshez az [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) paranccsal. Ebben a példában a készlet beállításait egy JSON-fájlban fogja megadni. Az aktuális felületen belül hozzon létre egy fájlt *mypool.json* néven, majd másolja és illessze be az alábbi tartalmat. Ügyeljen arra, hogy a teljes szöveget megfelelően átmásolja. (A fájlt letöltheti a [GitHubról](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/mypool.json).)


```json
{
  "id": "myrenderpool",
  "vmSize": "standard_d2_v2",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "batch",
      "offer": "rendering-windows2016",
      "sku": "rendering",
      "version": "1.3.8"
    },
    "nodeAgentSKUId": "batch.node.windows amd64"
  },
  "targetDedicatedNodes": 0,
  "targetLowPriorityNodes": 1,
  "enableAutoScale": false,
  "applicationLicenses":[
         "3dsmax",
         "arnold"
      ],
  "enableInterNodeCommunication": false 
}
```

A Batch támogatja a dedikált csomópontokat és az [alacsony prioritású](batch-low-pri-vms.md) csomópontokat is, és a készletekben használhatja mindkét fajtát akár egyszerre is. A dedikált csomópontok a készlet számára vannak fenntartva. Az alacsony prioritású csomópontok kedvezményes áron érhetők el az Azure többlet VM-kapacitásából. Ha az Azure nem rendelkezik elegendő kapacitással, az alacsony prioritású csomópontok elérhetetlenné válnak.

A megadott készlet egyetlen alacsony prioritású csomópontot tartalmaz, amely egy Windows Server-rendszerképet futtat a Batch renderelő szolgáltatáshoz szükséges szoftverekkel. A készlet a 3ds Max és az Arnold használatával való renderelésre van licencelve. Egy későbbi lépésben majd felskálázza a készletet nagyobb mennyiségű csomópont használatára.

Ha még nem jelentkezett be a Batch-fiókjába, használja az az [Batch Account login](/cli/azure/batch/account#az-batch-account-login) parancsot. Ezután hozza létre a készletet a JSON-fájlnak a parancsba való átadásával `az batch pool create` :

```azurecli-interactive
az batch pool create \
    --json-file mypool.json
```

A készlet kiépítése néhány percig tart. A készlet állapotának megtekintéséhez futtassa az [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) parancsot. A következő parancs a készlet lefoglalási állapotát kérdezi le:

```azurecli-interactive
az batch pool show \
    --pool-id myrenderpool \
    --query "allocationState"
```

Miközben a készlet állapotának változására vár, folytassa az alábbi lépésekkel, és hozzon létre egy feladatot és tevékenységeket. A készlet akkor épült ki teljesen, ha a lefoglalási állapota `steady` értékre vált, és a csomópontok futnak.  

## <a name="create-a-blob-container-for-output"></a>Blobtároló létrehozása a kimenet számára

Ennek az oktatóanyagnak a példáiban a renderelési feladat alá tartozó mindegyik tevékenység létrehoz egy kimeneti fájlt. A feladat ütemezése előtt hozzon létre egy blobtárolót a tárfiókjában a kimeneti fájlok célhelyeként. Az alábbi példa az [az storage container create](/cli/azure/storage/container#az-storage-container-create) parancs használatával létrehozza a *job-myrenderjob* tárolót nyilvános olvasási hozzáféréssel.

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name job-myrenderjob
```

A kimeneti fájlok a tárolóba való írásához a Batchnek a közös hozzáférésű jogosultságkód- (SAS-) jogkivonatot kell használnia. A jogkivonatot az [az storage account generate-sas](/cli/azure/storage/account#az-storage-account-generate-sas) paranccsal hozhatja létre. Ez a példa létrehoz egy jogkivonatot, amely a fiókban található bármelyik blob-tárolóba ír, a jogkivonat pedig 2021 november 15-én lejár:

```azurecli-interactive
az storage account generate-sas \
    --permissions w \
    --resource-types co \
    --services b \
    --expiry 2021-11-15
```

Jegyezze fel a parancs által visszaadott, a következőhöz hasonló jogkivonatot. Ezt a tokent egy későbbi lépésben fogja használni.

`se=2021-11-15&sp=rw&sv=2019-09-24&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

## <a name="render-a-single-frame-scene"></a>egyetlen képkockából álló jelenet renderelése;

### <a name="create-a-job"></a>Feladat létrehozása

Az [az batch job create](/cli/azure/batch/job#az-batch-job-create) paranccsal hozzon létre egy renderelési feladatot a készleten való futtatáshoz. Kezdetben a feladat nem rendelkezik feladatokkal.

```azurecli-interactive
az batch job create \
    --id myrenderjob \
    --pool-id myrenderpool
```

### <a name="create-a-task"></a>Tevékenység létrehozása

Az [az batch task create](/cli/azure/batch/task#az-batch-task-create) paranccsal hozzon létre egy renderelési tevékenységet a feladatban. Ebben a példában a tevékenység beállításait egy JSON-fájlban adjuk meg. Az aktuális rendszerhéjon belül hozzon létre egy fájlt *myrendertask.json* néven, majd másolja és illessze be az alábbiakat. Ügyeljen arra, hogy a teljes szöveget megfelelően átmásolja. (A fájlt letöltheti a [GitHubról](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask.json).)

A tevékenység egy 3ds Max-parancsot határoz meg a *MotionBlur-DragonFlying.max* jelenet egyetlen képkockájának rendereléséhez.

Módosítsa a JSON-fájl `blobSource` és `containerURL` elemeit, hogy tartalmazzák a tárfiók nevét és az SAS-jogkivonatot. 

> [!TIP]
> A `containerURL` végpontot az SAS-jogkivonattal ér véget, és a következőhöz hasonló: `https://mystorageaccount.blob.core.windows.net/job-myrenderjob/$TaskOutput?se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

```json
{
  "id": "myrendertask",
  "commandLine": "cmd /c \"%3DSMAX_2018%3dsmaxcmdio.exe -secure off -v:5 -rfw:0 -start:1 -end:1 -outputName:\"dragon.jpg\" -w 400 -h 300 MotionBlur-DragonFlying.max\"",
  "resourceFiles": [
    {
        "httpUrl": "https://mystorageaccount.blob.core.windows.net/scenefiles/MotionBlur-DragonFlying.max",
        "filePath": "MotionBlur-DragonFlying.max"
    }
  ],
    "outputFiles": [
        {
            "filePattern": "dragon*.jpg",
            "destination": {
                "container": {
                    "containerUrl": "https://mystorageaccount.blob.core.windows.net/job-myrenderjob/myrendertask/$TaskOutput?Add_Your_SAS_Token_Here"
                }
            },
            "uploadOptions": {
                "uploadCondition": "TaskSuccess"
            }
        }
    ],
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonAdmin"
    }
  }
}
```

Adja hozzá a tevékenységet a feladathoz a következő paranccsal:

```azurecli-interactive
az batch task create \
    --job-id myrenderjob \
    --json-file myrendertask.json
```

A Batch ütemezi a tevékenységet, és azonnal futtatja is, amint a fürt egy csomópontja elérhető.

### <a name="view-task-output"></a>A tevékenység kimenetének megtekintése

A tevékenység futtatása néhány percet vesz igénybe. Az [az batch task show](/cli/azure/batch/task#az-batch-task-show) paranccsal tekintheti meg a tevékenység részleteit.

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id myrendertask
```

A tevékenység létrehozza a *dragon0001.jpg* képet a számítási csomóponton, és feltölti a *job-myrenderjob* tárolóba a tárfiókon. A kimenet megtekintéséhez töltse le a fájlt a tárolóból a helyi számítógépre az [az storage blob download](/cli/azure/storage/blob#az-storage-blob-download) paranccsal.

```azurecli-interactive
az storage blob download \
    --container-name job-myrenderjob \
    --file dragon.jpg \
    --name dragon0001.jpg

```

Nyissa meg a *dragon.jpg* képet a számítógépen. A renderelt kép a következőhöz hasonló:

![Renderelt sárkány, 1. képkocka](./media/tutorial-rendering-cli/dragon-frame.png) 

## <a name="scale-the-pool"></a>A készlet méretezése

Most módosítsa a készletet, hogy felkészítse egy nagyobb, több képkockát tartalmazó renderelési feladatra. A Batch számos módszert biztosít a számítási erőforrások méretezésére, beleértve az [automatikus méretezést](batch-automatic-scaling.md), amely a tevékenységek igényei alapján ad hozzá vagy távolít el csomópontokat. Ebben az egyszerű példában az [az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize) paranccsal növelje a készlet alacsony prioritású csomópontjainak számát *6-ra*:

```azurecli-interactive
az batch pool resize --pool-id myrenderpool --target-dedicated-nodes 0 --target-low-priority-nodes 6
```

A készlet átméretezése néhány percet vesz igénybe. Miközben a folyamat végbemegy, állítsa be a meglévő renderelési feladatban futtatandó következő tevékenységeket.

## <a name="render-a-multiframe-scene"></a>Több képkockából álló jelenet renderelése

Az egy képkockás példához hasonlóan most is az [az batch task create](/cli/azure/batch/task#az-batch-task-create) paranccsal hozza létre a renderelési tevékenységeket a *myrenderjob* nevű feladatban. Itt a tevékenység beállításait a *myrendertask_multi.json* nevű JSON-fájlban adja meg. (A fájlt letöltheti a [githubról](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask_multi.json).) A hat feladat mindegyike egy Arnold parancssort határoz meg, amely a 3ds Max Scene *MotionBlur-DragonFlying. max* jelenetének egy keretét jeleníti meg.

Hozzon létre egy fájlt az aktuális felületen *myrendertask_multi.json* néven, majd másolja és illessze be a letöltött fájl tartalmát. Módosítsa a JSON-fájl `blobSource` és `containerURL` elemeit, hogy tartalmazzák a tárfiók nevét és az SAS-jogkivonatot. Ne feledje mind a hat tevékenység beállításait módosítani. Mentse a fájlt, majd a következő parancs futtatásával küldje a várólistára a tevékenységeket:

```azurecli-interactive
az batch task create --job-id myrenderjob --json-file myrendertask_multi.json
```

### <a name="view-task-output"></a>A tevékenység kimenetének megtekintése

A tevékenység futtatása néhány percet vesz igénybe. Az [az batch task list](/cli/azure/batch/task#az-batch-task-list) paranccsal tekintheti meg a tevékenységek állapotát. Például:

```azurecli-interactive
az batch task list \
    --job-id myrenderjob \
    --output table
```

Az [az batch task show](/cli/azure/batch/task#az-batch-task-show) paranccsal tekintheti meg az egyes tevékenységek részleteit. Például:

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id mymultitask1
```

A feladatok a *dragon0002.jpg* dragon0007.jpgnevű kimeneti fájlokat hoznak  -   a számítási csomópontokon, és feltöltik őket a Storage *-fiók myrenderjob-* tárolójába. A kimenet megtekintéséhez töltse le a fájlokat a helyi számítógép egyik mappájába az [az storage blob download-batch](/cli/azure/storage/blob) paranccsal. Például:

```azurecli-interactive
az storage blob download-batch \
    --source job-myrenderjob \
    --destination .
```

Nyissa meg az egyik fájlt a számítógépen. A 6. renderelt kép a következőhöz hasonló:

![Renderelt sárkány, 6. képkocka](./media/tutorial-rendering-cli/dragon-frame6.png) 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rájuk, az [az group delete](/cli/azure/group#az-group-delete) paranccsal eltávolíthatja az erőforráscsoportot, a Batch-fiókot, a készleteket és az összes kapcsolódó erőforrást. Az erőforrásokat a következőképpen törölheti:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következőket sajátította el:

> [!div class="checklist"]
> - jelenetek feltöltése Azure-tárolóba;
> - Batch-készlet létrehozása rendereléshez;
> - egyetlen képkockából álló jelenet renderelése az Arnolddal;
> - a készlet méretezése, majd egy több képkockából álló jelenet renderelése;
> - a renderelt kimenet letöltése.

További információ a Felhőbeli méretezésről: batch rendering dokumentáció.

> [!div class="nextstepaction"]
> [Batch-renderelési szolgáltatás](batch-rendering-service.md)
