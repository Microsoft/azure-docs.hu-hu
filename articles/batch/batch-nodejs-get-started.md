---
title: A Node.js Azure Batch ügyféloldali kódtár használata
description: Megismerheti az Azure Batch alapvető fogalmait, és létrehozhat egy egyszerű megoldást a Node.js használatával.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: a2d86582c94804a20a3a9e952bc64d60414e25a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656022"
---
# <a name="get-started-with-batch-sdk-for-nodejs"></a>Ismerkedés a Node.js-hez készült Batch SDK-val

Ebben a cikkben megismerheti a Batch-ügyfelek kiépítésének alapjait Node.js-ben, az [Azure Batch Node.js SDK](/javascript/api/overview/azure/batch) használatával. Részletes, lépésekre osztott módon ismerkedhet meg a Batch-alkalmazásokhoz tartozó forgatókönyvekkel és azok a Node.js-ügyfél használatával történő beállításával.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk a Node.js és a Linux gyakorlati ismeretét feltételezi. Azt is feltételezi, hogy rendelkezik egy, a Batch- és Storage-szolgáltatások létrehozásához szükséges hozzáférési jogosultságokkal ellátott Azure-fiókkal.

A jelen cikk lépéseinek végrehajtása előtt javasoljuk, hogy olvassa el az [Azure Batch technikai áttekintését](batch-technical-overview.md).

## <a name="understand-the-scenario"></a>A forgatókönyv megismerése

Itt van egy Pythonban írt egyszerű szkript, amely az Azure Blob Storage-tárolóból származó összes CSV-fájlt letölti, és a JSON-re konvertálja őket. Több tárfióktároló párhuzamos feldolgozásához Azure Batch-feladatként helyezheti üzembe a szkriptet.

## <a name="azure-batch-architecture"></a>Azure Batch architektúra

Az alábbi ábra bemutatja, hogyan történik a Python-szkript méretezése az Azure Batch és egy Node.js-ügyfél használatával.

![A forgatókönyv architektúráját bemutató ábra.](./media/batch-nodejs-get-started/BatchScenario.png)

A Node.js-ügyfél üzembe helyez egy előkészítő feladattal ellátott Batch-feladatot (részletesen lásd később), valamint – a tárfiókban lévő tárolók számától függően – egy feladatkészletet. A szkripteket letöltheti a GitHub-adattárból.

- [Node.js-ügyfél](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/sample.js)
- [Előkészítési tevékenységhez kapcsolódó héjszkriptek](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/startup_prereq.sh)
- [Python CSV – JSON feldolgozó](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/processcsv.py)

> [!TIP]
> A megadott hivatkozásban szereplő Node.js-ügyfél nem tartalmaz Azure-függvényalkalmazásként üzembe helyezendő kódot. Az alábbi hivatkozások azon útmutatásokra mutatnak, amelyek segítségével létrehozhatja azt.
> - [Függvényalkalmazás létrehozása](../azure-functions/functions-get-started.md)
> - [Időzítő által aktivált függvény létrehozása](../azure-functions/functions-bindings-timer.md)

## <a name="build-the-application"></a>Az alkalmazás létrehozása

Az alábbiakban lépésenként követjük a Node.js-ügyfél felépítésének folyamatát:

### <a name="step-1-install-azure-batch-sdk"></a>1. lépés: Az Azure Batch SDK telepítése

A Node.js-hez készült Azure Batch SDK telepítése az npm install paranccsal történik.

`npm install azure-batch`

Ezzel a paranccsal telepítheti az Azure Batch Node SDK legújabb verzióját.

>[!Tip]
> Egy Azure-függvényalkalmazásban az npm install parancs futtatása az alkalmazás Beállítások lapjának „Kudu konzol” felületéről történik. Ebben az esetben a Node.js-hez készült Azure Batch SDK-t telepíti.

### <a name="step-2-create-an-azure-batch-account"></a>2. lépés: Azure Batch-fiók létrehozása

A [Azure Portal](batch-account-create-portal.md) vagy a parancssorból ([PowerShell](batch-powershell-cmdlets-get-started.md)  / [Azure CLI](/cli/azure)) hozható létre.

Az alábbi parancsokkal az Azure CLI-n keresztül hozhat létre egy fiókot.

Hozzon létre egy erőforráscsoportot (hagyja ki ezt a lépést, ha a Batch-fiók létrehozásának helyén már található ilyen):

`az group create -n "<resource-group-name>" -l "<location>"`

A következő lépés az Azure Batch-fiók létrehozása.

`az batch account create -l "<location>"  -g "<resource-group-name>" -n "<batch-account-name>"`

Minden egyes Batch-fiók megfelelő hozzáférési kulcsokkal rendelkezik. Ezekre a kulcsokra szükség van további erőforrások az Azure Batch-fiókban történő létrehozásához. Éles környezetben a kulcsok tárolására tanácsos az Azure Key Vaultot használni. Ezután létrehozhatja az alkalmazáshoz tartozó egyszerű szolgáltatást. Ezen egyszerű szolgáltatás használatával az alkalmazás képes OAuth-token létrehozására a Key Vaultban tárolt kulcsokhoz történő hozzáféréshez.

`az batch account keys list -g "<resource-group-name>" -n "<batch-account-name>"`

Másolja és mentse a következő lépésekben használni kívánt kulcsot.

### <a name="step-3-create-an-azure-batch-service-client"></a>3. lépés: Azure Batch-szolgáltatásügyfél létrehozása

Az alábbi kódrészlet először importálja az Azure Batch Node.js modult, majd létrehoz egy Batch-szolgáltatásügyfelet. Először létre kell hoznia egy SharedKeyCredentials objektumot az előző lépésből átmásolt Batch-fiókkulccsal.

```nodejs
// Initializing Azure Batch variables

var batch = require('azure-batch');

var accountName = '<azure-batch-account-name>';

var accountKey = '<account-key-downloaded>';

var accountUrl = '<account-url>'

// Create Batch credentials object using account name and account key

var credentials = new batch.SharedKeyCredentials(accountName,accountKey);

// Create Batch service client

var batch_client = new batch.ServiceClient(credentials,accountUrl);

```

Az Azure Batch URI az Azure Portal Áttekintés lapján található. Ennek formátuma:

`https://accountname.location.batch.azure.com`

Tekintse meg a következő képernyőképet:

![Azure Batch URI](./media/batch-nodejs-get-started/azurebatchuri.png)

### <a name="step-4-create-an-azure-batch-pool"></a>4. lépés: Azure Batch-készlet létrehozása

Az Azure Batch-készlet több virtuális gépből áll (ezek Batch-csomópontokként is ismertek). Az Azure Batch-szolgáltatás elvégzi a feladatok központi telepítését ezeken a csomópontokon, és kezeli azokat. A készlet esetében az alábbi konfigurációs paramétereket adhatja meg.

- A virtuális gép rendszerképének típusa
- A virtuálisgép-csomópontok métere
- A virtuálisgép-csomópontok száma

> [!TIP]
> A virtuálisgép-csomópontok mérete és száma nagymértékben a párhuzamosan futtatni kívánt feladatok számától és magától a feladattól függ. Javasoljuk, hogy teszteléssel határozza meg az ideális számot és méretet.

Az alábbi kódrészlet a konfigurációsparaméter-objektumokat hozza létre.

```nodejs
// Creating Image reference configuration for Ubuntu Linux VM
var imgRef = {publisher:"Canonical",offer:"UbuntuServer",sku:"14.04.2-LTS",version:"latest"}

// Creating the VM configuration object with the SKUID
var vmconfig = {imageReference:imgRef,nodeAgentSKUId:"batch.node.ubuntu 14.04"}

// Setting the VM size to Standard F4
var vmSize = "STANDARD_F4"

//Setting number of VMs in the pool to 4
var numVMs = 4
```

> [!TIP]
> Az Azure Batch számára elérhető linuxos virtuális gépek rendszerképét és azok termékváltozat-azonosítóinak listáját a [virtuálisgép-rendszerképek listája](batch-linux-nodes.md#list-of-virtual-machine-images) tartalmazza.

A készletkonfiguráció megadását követően létrehozhatja az Azure Batch-készletet. A Batch-készlet parancs Azure-beli virtuálisgép-csomópontokat hoz létre, és felkészíti azokat a végrehajtandó feladatok fogadására. Mindegyik készletnek egyedi azonosítóval kell rendelkeznie a következő lépésekben történő hivatkozáshoz.

Az alábbi kódrészlet egy Azure Batch-készletet hoz létre.

```nodejs
// Create a unique Azure Batch pool ID
var poolid = "pool" + customerDetails.customerid;
var poolConfig = {id:poolid, displayName:poolid,vmSize:vmSize,virtualMachineConfiguration:vmconfig,targetDedicatedComputeNodes:numVms,enableAutoScale:false };
// Creating the Pool for the specific customer
var pool = batch_client.pool.add(poolConfig,function(error,result){
    if(error!=null){console.log(error.response)};
});
```

Ellenőrizheti a létrehozott készlet állapotát, és a készletbe történő feladatbeküldés előtt meggyőződhet arról, hogy a készlet „aktív” állapotban van.

```nodejs
var cloudPool = batch_client.pool.get(poolid,function(error,result,request,response){
        if(error == null)
        {

            if(result.state == "active")
            {
                console.log("Pool is active");
            }
        }
        else
        {
            if(error.statusCode==404)
            {
                console.log("Pool not found yet returned 404...");

            }
            else
            {
                console.log("Error occurred while retrieving pool data");
            }
        }
        });
```

Az alábbiakban a pool.get függvény által visszaadott objektumra láthat példát.

```
{ id: 'processcsv_201721152',
  displayName: 'processcsv_201721152',
  url: 'https://<batch-account-name>.centralus.batch.azure.com/pools/processcsv_201721152',
  eTag: '<eTag>',
  lastModified: 2017-03-27T10:28:02.398Z,
  creationTime: 2017-03-27T10:28:02.398Z,
  state: 'active',
  stateTransitionTime: 2017-03-27T10:28:02.398Z,
  allocationState: 'resizing',
  allocationStateTransitionTime: 2017-03-27T10:28:02.398Z,
  vmSize: 'standard_a1',
  virtualMachineConfiguration:
   { imageReference:
      { publisher: 'Canonical',
        offer: 'UbuntuServer',
        sku: '14.04.2-LTS',
        version: 'latest' },
     nodeAgentSKUId: 'batch.node.ubuntu 14.04' },
  resizeTimeout:
   { [Number: 900000]
     _milliseconds: 900000,
     _days: 0,
     _months: 0,
     _data:
      { milliseconds: 0,
        seconds: 0,
        minutes: 15,
        hours: 0,
        days: 0,
        months: 0,
        years: 0 },
     _locale:
      Locale {
        _calendar: [Object],
        _longDateFormat: [Object],
        _invalidDate: 'Invalid date',
        ordinal: [Function: ordinal],
        _ordinalParse: /\d{1,2}(th|st|nd|rd)/,
        _relativeTime: [Object],
        _months: [Object],
        _monthsShort: [Object],
        _week: [Object],
        _weekdays: [Object],
        _weekdaysMin: [Object],
        _weekdaysShort: [Object],
        _meridiemParse: /[ap]\.?m?\.?/i,
        _abbr: 'en',
        _config: [Object],
        _ordinalParseLenient: /\d{1,2}(th|st|nd|rd)|\d{1,2}/ } },
  currentDedicated: 0,
  targetDedicated: 4,
  enableAutoScale: false,
  enableInterNodeCommunication: false,
  taskSlotsPerNode: 1,
  taskSchedulingPolicy: { nodeFillType: 'Spread' } }
```

### <a name="step-4-submit-an-azure-batch-job"></a>4. lépés: Azure Batch-feladat elküldése

Az Azure Batch-feladatok hasonló feladatok logikai csoportjai. Ebben az esetben ez a következő: „CSV – JSON feldolgozás”. Minden egyes itt szereplő feladat képes az Azure Storage-tárolókban lévő CSV-fájlok feldolgozására.

E feladatok párhuzamosan futtathatók és több csomóponton üzembe helyezhetők, a vezénylésüket az Azure Batch-szolgáltatás végzi.

> [!TIP]
> A [taskSlotsPerNode](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) tulajdonság segítségével megadhatja, hogy legfeljebb hány feladat futhat egyszerre egyetlen csomóponton.

#### <a name="preparation-task"></a>Előkészítő feladat

A virtuálisgép-csomópontok üres Ubuntu-csomópontokat hoztak létre. Gyakori eset, hogy előfeltételként néhány programot kell telepíteni.
Linux-csomópontok esetében általában rendelkezhet olyan héjszkripttel, amely az aktuális feladatok futtatása előtt telepíti az előfeltételeket. Ez azonban bármilyen programozható végrehajtható fájl lehet.

Az e példában szereplő [héjszkript](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/startup_prereq.sh) telepíti a Python-PIP-et és a Pythonhoz készült Azure Storage SDK-t.

Feltöltheti a szkriptet egy Azure Storage-fiókba, és létrehozhat egy SAS URI-t a szkripthez történő hozzáféréshez. Az Azure Storage Node.js SDK használatával ez a folyamat automatizálható.

> [!TIP]
> Az adott feladathoz tartozó előkészítő feladat csak azokon a virtuálisgép-csomópontokon fut, ahol az adott feladatnak futnia kell. Ha azt szeretné, hogy az előfeltételek az összes csomóponton telepítve legyenek (függetlenül az azokon futó feladatoktól), készlet hozzáadásakor használhatja a [startTask](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) tulajdonságot. Referenciaként az alábbi előkészítőfeladat-meghatározás használható.

Az Azure Batch-feladat beküldése során megtörténik egy előkészítő feladat meghatározása. Az előkészítő feladat konfigurációs paraméterei a következők:

- **ID**: Az előkészítő feladat egyedi azonosítója.
- **commandLine**: A feladat végrehajtható fájljának végrehajtására szolgáló parancssor.
- **resourceFiles**: A jelen feladat futtatásához letöltendő fájlok részletes adatait biztosító objektumtömbök.  Ennek beállítási lehetőségei a következők:
  - blobSource: A fájl SAS URI-ja.
  - filePath: A fájl helyi letöltési és mentési elérési útja.
  - fileMode: Csak Linux-csomópontokra vonatkozik, a fileMode formátuma oktális, alapértelmezett értéke 0770.
- **waitForSuccess**: Ha a beállítása igaz, a feladat nem fut előkészítőtevékenység-hibák esetében.
- **runElevated**: Állítsa igaz értékre, ha a feladat futtatásához emelt szintű jogosultságokra van szükség.

Az alábbi kódrészleten az előkészítő feladat egy szkriptkonfigurációs mintáját láthatjuk:

```nodejs
var job_prep_task_config = {id:"installprereq",commandLine:"sudo sh startup_prereq.sh > startup.log",resourceFiles:[{'blobSource':'Blob SAS URI','filePath':'startup_prereq.sh'}],waitForSuccess:true,runElevated:true}
```

Ha a feladatok futtatásához nem szükséges előfeltételeket telepíteni, kihagyhatja az előkészítő tevékenységeket. Az alábbi kód „CSV-fájlok feldolgozása” megjelenített névvel ellátott feladatot hoz létre.

 ```nodejs
 // Setting up Batch pool configuration
 var pool_config = {poolId:poolid}
 // Setting up Job configuration along with preparation task
 var jobId = "processcsvjob"
 var job_config = {id:jobId,displayName:"process csv files",jobPreparationTask:job_prep_task_config,poolInfo:pool_config}
 // Adding Azure batch job to the pool
 var job = batch_client.job.add(job_config,function(error,result){
     if(error != null)
     {
         console.log("Error submitting job : " + error.response);
     }});
```

### <a name="step-5-submit-azure-batch-tasks-for-a-job"></a>5. lépés: Azure Batch-feladatok elküldése egy feladathoz

A CSV-feldolgozási feladat létrehozását követően hozzunk létre tevékenységeket ehhez a feladathoz. Feltételezve, hogy négy tárolóval rendelkezünk, négy tevékenységet hozunk létre – minden tárolóhoz egyet.

A [Python-szkript](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/processcsv.py) két paramétert fogad el:

- container name: Az a Storage-tároló, amelyből a fájlokat letöltjük.
- pattern: A fájlnév-minta választható paramétere.

Feltételezve, hogy négy tárolóval („con1”, „con2”, „con3”, „con4”) rendelkezünk, az alábbi kód a korábban létrehozott „CSV-feldolgozás” nevű Azure Batch-feladat számára történő négy tevékenység beküldését mutatja be.

```nodejs
// storing container names in an array
var container_list = ["con1","con2","con3","con4"]
    container_list.forEach(function(val,index){

           var container_name = val;
           var taskID = container_name + "_process";
           var task_config = {id:taskID,displayName:'process csv in ' + container_name,commandLine:'python processcsv.py --container ' + container_name,resourceFiles:[{'blobSource':'<blob SAS URI>','filePath':'processcsv.py'}]}
           var task = batch_client.task.add(poolid,task_config,function(error,result){
                if(error != null)
                {
                    console.log(error.response);
                }
                else
                {
                    console.log("Task for container : " + container_name + "submitted successfully");
                }



           });

    });
```

A kód több tevékenységet ad hozzá a készlethez. Minden egyes tevékenység a létrehozott virtuális gépek készletének egyik csomópontján lesz végrehajtva. Ha a feladatok száma meghaladja a készletben lévő virtuális gépek számát vagy a taskSlotsPerNode tulajdonságot, a feladatok megvárnak, amíg elérhetővé nem válik egy csomópont. Ennek vezénylését az Azure Batch automatikusan elvégzi.

A portálon részletesen megtekinthetők a tevékenységek és a feladatok állapotai. Használhatja az Azure Node SDK listázási és lekérési funkcióit is. Részletes információkat a dokumentáció [hivatkozását](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Job.html) megnyitva talál.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [Batch szolgáltatás munkafolyamatát és az elsődleges erőforrásokat](batch-service-workflow-features.md) , például a készleteket, a csomópontokat, a feladatokat és a feladatokat.
- A Batch API megismeréséhez lásd a [Batch – Node.js-referenciát](/javascript/api/overview/azure/batch).