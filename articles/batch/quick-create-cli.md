---
title: Rövid útmutató – Az első Batch-feladat futtatása az Azure CLI használatával
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Batch-fiókot, és hogyan futtathat Batch-feladatot az Azure CLI-val.
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8d3005233320a7ba0d00f186944a0a8c0c456647
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765304"
---
# <a name="quickstart-run-your-first-batch-job-with-the-azure-cli"></a>Rövid útmutató: Az első Batch-feladat futtatása az Azure CLI használatával

Az Azure Batch használatának első lépésekhez használja az Azure CLI-t Batch-fiók, számítási csomópontok (virtuális gépek) készletének létrehozásához, valamint egy feladatot, amely tevékenységeket futtat a készleten. Minden egyes mintatevékenység egy alapvető parancsot futtat a készlet egyik csomópontján.

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. A rövid útmutatóból megismerheti a Batch szolgáltatás fő fogalmait, és készen áll majd a Batch szolgáltatás használatára realisztikusabb számítási feladatokkal, nagyobb léptékben.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a rövid útmutatóhoz az Azure CLI 2.0.20-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Az alábbi példa egy *QuickstartBatch-rg* nevű erőforráscsoportot hoz létre az *eastus2 helyen.*

```azurecli-interactive
az group create \
    --name QuickstartBatch-rg \
    --location eastus2
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A Batch-fiókot összekapcsolhatja egy Azure Storage-fiókkal. Bár a rövid útmutatóhoz nem kötelező, a Storage-fiók hasznos az alkalmazások üzembe helyezéséhez, valamint a legtöbb valós számítási feladat be- és kimeneti adatainak tárolásához. Az [az storage account create](/cli/azure/storage/account#az_storage_account_create) paranccsal hozzon létre egy Storage-fiókot az erőforráscsoportban.

```azurecli-interactive
az storage account create \
    --resource-group QuickstartBatch-rg \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

## <a name="create-a-batch-account"></a>Batch-fiók létrehozása

Az [az batch account create](/cli/azure/batch/account#az_batch_account_create) paranccsal hozzon létre egy Batch-fiókot. Számítási erőforrások (számítási csomópontok készletei) és Batch-feladatok létrehozásához szükség van egy fiókra.

Az alábbi példa létrehoz egy *mybatchaccount* nevű *Batch-fiókot a QuickstartBatch-rg parancsban,* és összeköti a létrehozott tárfiókot.  

```azurecli-interactive
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group QuickstartBatch-rg \
    --location eastus2
```

A számítási készletek és feladatok létrehozásához és kezeléséhez hitelesítést kell végeznie a Batchben. Jelentkezzen be a fiókba az [az batch account login](/cli/azure/batch/account#az_batch_account_login) paranccsal. A bejelentkezés után az `az batch` parancsok ezt a fiókkörnyezetet használják.

```azurecli-interactive
az batch account login \
    --name mybatchaccount \
    --resource-group QuickstartBatch-rg \
    --shared-key-auth
```

## <a name="create-a-pool-of-compute-nodes"></a>Számításicsomópont-készlet létrehozása

Most, hogy már rendelkezik Batch-fiókkal, hozzon létre egy linuxos számítási csomópontokból álló mintakészletet az [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) paranccsal. A következő példában egy *mypool* nevű, 2 *Standard_A1_v2* méretű, Ubuntu 16.04 LTS rendszert futtató csomópontot tartalmazó készletet hozunk létre. A javasolt csomópontméret jó teljesítmény/költség arányt kínál a jelen rövid példában.
 
```azurecli-interactive
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:16.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

A Batch azonnal létrehozza a készletet, de a számítási csomópontok lefoglalása és elindítása igénybe vesz néhány percet. Eközben a készlet `resizing` állapotban van. A készlet állapotának megtekintéséhez futtassa az [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) parancsot. Ez a parancs a készlet összes tulajdonságát megjeleníti, és lehetővé teszi konkrét tulajdonságok lekérdezését. A következő parancs a készlet lefoglalási állapotát kérdezi le:

```azurecli-interactive
az batch pool show --pool-id mypool \
    --query "allocationState"
```

Miközben a készlet állapotának változására vár, folytassa az alábbi lépésekkel, és hozzon létre egy feladatot és tevékenységeket. A készlet akkor áll készen tevékenységek futtatására, amikor a lefoglalási állapota `steady` értékre vált, és minden csomópont fut.

## <a name="create-a-job"></a>Feladat létrehozása

Most, hogy már rendelkezik készlettel, hozzon létre egy feladatot, amelyet azon futtat. A Batch-feladat egy vagy több tevékenység logikai csoportja. Egy Batch-feladat magában foglalja a tevékenységek közös beállításait, mint a prioritást, illetve a készletet, amelyeken a tevékenységeket futtatni szeretné. Az [az batch job create](/cli/azure/batch/job#az_batch_job_create) paranccsal hozzon létre egy Batch-feladatot. Az alábbi példa egy *myjob* nevű feladatot hoz létre a *mypool* készleten. A feladat kezdetben nem tartalmaz tevékenységeket.

```azurecli-interactive
az batch job create \
    --id myjob \
    --pool-id mypool
```

## <a name="create-tasks"></a>Tevékenységek létrehozása

Most pedig az [az batch task create](/cli/azure/batch/task#az_batch_task_create) paranccsal hozzon létre a feladatban futtatni kívánt tevékenységeket. Ebben a példában négy azonos tevékenységet hozunk létre. Minden tevékenység egy `command-line` futtatásával megjeleníti a Batch környezeti változókat a számítási csomóponton, majd vár 90 másodpercet. Batch használata esetén ebben a parancssorban adhatja meg az alkalmazást vagy a szkriptet. A Batch számos módszert kínál az alkalmazások és szkriptek számítási csomópontokon történő üzembe helyezésére.

Az alábbi Bash-szkript 4 párhuzamos tevékenységet hoz létre (*mytask1*–*mytask4*).

```azurecli-interactive
for i in {1..4}
do
   az batch task create \
    --task-id mytask$i \
    --job-id myjob \
    --command-line "/bin/bash -c 'printenv | grep AZ_BATCH; sleep 90s'"
done
```

A parancskimenet megjeleníti az egyes tevékenységek beállításait. A Batch kiosztja a feladatokat a számítási csomópontoknak.

## <a name="view-task-status"></a>Tevékenységek állapotának megtekintése

Tevékenység létrehozása után a Batch várólistára helyezi azt a készleten való futtatáshoz. Amint egy csomópont készen áll a futtatásra, a rendszer futtatja a tevékenységet.

Az [az batch task show](/cli/azure/batch/task#az_batch_task_show) paranccsal tekintheti meg a Batch-tevékenységek állapotát. A következő példa a készlet egyik csomópontján futó *mytask1* tevékenység részleteit jeleníti meg.

```azurecli-interactive
az batch task show \
    --job-id myjob \
    --task-id mytask1
```

A parancs kimenete számos részletet tartalmaz. Tekintse meg a tevékenység parancssorának `exitCode` elemét, illetve a `nodeId` értékét. Ha az `exitCode` értéke 0, akkor a tevékenység parancssora sikeresen befejeződött. A `nodeId` a készlet azon csomópontjának azonosítóját jelöli, amelyen a tevékenység futott.

## <a name="view-task-output"></a>A tevékenység kimenetének megtekintése

A tevékenységek által egy számítási csomóponton létrehozott fájlok felsorolásához használja az [az batch task file list](/cli/azure/batch/task) parancsot. A következő parancs felsorolja a *mytask1* tevékenység által létrehozott fájlokat:

```azurecli-interactive
az batch task file list \
    --job-id myjob \
    --task-id mytask1 \
    --output table
```

A kimenet a következőkhöz hasonló:

```
Name        URL                                                                                         Is Directory      Content Length
----------  ------------------------------------------------------------------------------------------  --------------  ----------------
stdout.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stdout.txt  False                  695
certs       https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/certs       True
wd          https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/wd          True
stderr.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stderr.txt  False                     0

```

A kimeneti fájlok az [az batch task file download](/cli/azure/batch/task) paranccsal tölthetők le helyi könyvtárba. Ebben a példában a tevékenység kimenete a `stdout.txt` fájlban található.

```azurecli-interactive
az batch task file download \
    --job-id myjob \
    --task-id mytask1 \
    --file-path stdout.txt \
    --destination ./stdout.txt
```

A `stdout.txt` fájl tartalmát egy szövegszerkesztőben tekintheti meg. A tartalom a csomóponton beállított Azure Batch környezeti változókat jeleníti meg. Saját Batch-feladatok létrehozása során hivatkozhat ezekre a környezeti változókra a tevékenységek parancssorában, illetve a parancssorok által futtatott alkalmazásokban és szkriptekben. Például:

```
AZ_BATCH_TASK_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1
AZ_BATCH_NODE_STARTUP_DIR=/mnt/batch/tasks/startup
AZ_BATCH_CERTIFICATES_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/certs
AZ_BATCH_ACCOUNT_URL=https://mybatchaccount.eastus2.batch.azure.com/
AZ_BATCH_TASK_WORKING_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/wd
AZ_BATCH_NODE_SHARED_DIR=/mnt/batch/tasks/shared
AZ_BATCH_TASK_USER=_azbatch
AZ_BATCH_NODE_ROOT_DIR=/mnt/batch/tasks
AZ_BATCH_JOB_ID=myjobl
AZ_BATCH_NODE_IS_DEDICATED=true
AZ_BATCH_NODE_ID=tvm-257509324_2-20180703t215033z
AZ_BATCH_POOL_ID=mypool
AZ_BATCH_TASK_ID=mytask1
AZ_BATCH_ACCOUNT_NAME=mybatchaccount
AZ_BATCH_TASK_USER_IDENTITY=PoolNonAdmin
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatni szeretné a Batch-oktatóanyagok és -minták használatát, használja az ebben a rövid útmutatóban létrehozott Batch-fiókot és az ahhoz kapcsolt Storage-fiókot. A Batch-fiók használata díjmentes.

A készletekért díjat számítunk fel, amíg a csomópontok futnak, még akkor is, ha nincsenek feladatok ütemezve. Ha már nincs szüksége a készletre, törölje azt az [az batch pool delete](/cli/azure/batch/pool#az_batch_pool_delete) paranccsal. A készlet törlésekor a rendszer a csomópont összes tevékenységének kimenetét is törli.

```azurecli-interactive
az batch pool delete --pool-id mypool
```

Ha már nincs szükség rájuk, az [az group delete](/cli/azure/group#az_group_delete) paranccsal eltávolíthatja az erőforráscsoportot, a Batch-fiókot, a készleteket és az összes kapcsolódó erőforrást. Az erőforrásokat a következőképpen törölheti:

```azurecli-interactive
az group delete --name QuickstartBatch-rg
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy Batch-fiókot, egy Batch-készletet és egy Batch-feladatot hozott létre. A feladat mintatevékenységeket futtatott, Ön pedig megtekintette az egyik csomóponton létrejött kimenetet. Most, hogy megismerkedett a Batch szolgáltatás fő fogalmaival, készen áll a Batch szolgáltatás realisztikusabb számítási feladatokkal, nagyobb léptékben történő kipróbálására. Az Azure Batchről további információt az Azure Batch-oktatóanyagokban találhat.

> [!div class="nextstepaction"]
> [Azure Batch-oktatóanyagok](./tutorial-parallel-dotnet.md)
