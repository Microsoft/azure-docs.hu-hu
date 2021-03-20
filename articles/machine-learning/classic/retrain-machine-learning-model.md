---
title: 'ML Studio (klasszikus): webszolgáltatás újratanítása – Azure'
description: Ismerje meg, hogyan frissíthet egy webszolgáltatást egy újonnan betanított gépi tanulási modell használatára Azure Machine Learning Studio (klasszikus).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-csharp
ms.date: 02/14/2019
ms.openlocfilehash: a4fe9e54e5e03a8dbf2a727b22f784c36d6c65f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517586"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Gépi tanulási modell újratanítása és üzembe helyezése

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Az átképzés az egyik módszer, amellyel biztosítható, hogy a gépi tanulási modellek pontosak maradjanak, és a rendelkezésre álló legfontosabb információk alapján. Ez a cikk bemutatja, hogyan lehet újratanítani és üzembe helyezni a Machine learning-modelleket új webszolgáltatásként a Studio (klasszikus) szolgáltatásban. Ha egy klasszikus webszolgáltatást szeretné áttanítani, [tekintse meg ezt a útmutató cikket.](retrain-classic-web-service.md)

Ez a cikk azt feltételezi, hogy már van üzembe helyezett prediktív webszolgáltatás. Ha még nem rendelkezik prediktív webszolgáltatással, [megtudhatja, hogyan helyezhet üzembe egy Studio (klasszikus) webszolgáltatást itt.](deploy-a-machine-learning-web-service.md)

A Machine learning új webszolgáltatás újratanításához és üzembe helyezéséhez kövesse az alábbi lépéseket:

1. **Átképzési webszolgáltatás** üzembe helyezése
1. Új modell betanítása az **átképzési webszolgáltatás** használatával
1. Meglévő **prediktív kísérlet** frissítése az új modell használatára

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>Az átképzési webszolgáltatás üzembe helyezése

Az átképzési webszolgáltatás lehetővé teszi, hogy a modellt új paraméterekkel (például új adatokkal) adja át, és később mentse. Amikor egy **webszolgáltatás kimenetét**  egy **vonat-modellhez** kapcsolja, a betanítási kísérlet egy új modellt fog használni.

A következő lépésekkel telepítheti az átképzési webszolgáltatásokat:

1. Csatlakoztasson egy **webszolgáltatáshoz tartozó bemeneti** modult az adatok bemenetéhez. Általában biztosítani szeretné, hogy a bemeneti adatok ugyanúgy legyenek feldolgozva, mint az eredeti betanítási adatok.
1. **Webszolgáltatások kimeneti** moduljának összekötése a **betanítási modell** kimenetével.
1. Ha a **modell kiértékelése** modult használ, a **webszolgáltatások kimeneti** modulját összekapcsolhatjuk a kiértékelés eredményeinek kinyomtatásához.
1. Futtassa a kísérletet.

    A kísérlet futtatása után az eredményül kapott munkafolyamatnak az alábbi képhez hasonlónak kell lennie:

    ![Eredményül kapott munkafolyamat](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Most üzembe helyezi a betanítási kísérletet egy átképzési webszolgáltatásként, amely egy betanított modellt és a modell kiértékelésének eredményét jeleníti meg.

1. A kísérlet vászon alján kattintson a **webszolgáltatás beállítása** elemre.
1. Válassza a **webszolgáltatás üzembe helyezése [új]** lehetőséget. Megnyílik a Azure Machine Learning webszolgáltatások portál a **webszolgáltatás üzembe helyezése** oldalon.
1. Adja meg a webszolgáltatás nevét, és válassza ki a fizetési tervet.
1. Válassza az **Üzembe helyezés** lehetőséget.

## <a name="retrain-the-model"></a>A modell újratanítása

Ebben a példában a C# nyelvet használjuk az átképzési alkalmazás létrehozásához. A feladat elvégzéséhez Python vagy R mintakód is használható.

A következő lépések végrehajtásával hívhatja meg az átképzési API-kat:

1. C# Console-alkalmazás létrehozása a Visual Studióban: **új**  >  **Project**  >  **Visual C#**  >  **Windows klasszikus asztali**  >  **konzol alkalmazás (.NET-keretrendszer)**.
1. Jelentkezzen be a Machine Learning Web Services portálra.
1. Kattintson arra a webszolgáltatásra, amelyen dolgozik.
1. Kattintson **a** felhasználás gombra.
1. **A felhasználás lap** alján, a **mintakód** szakaszban kattintson a **Batch** elemre.
1. Másolja a minta C#-kódot a Batch-végrehajtáshoz, és illessze be a program. cs fájlba. Győződjön meg arról, hogy a névtér érintetlen marad.

Adja hozzá a Microsoft. AspNet. WebApi. Client NuGet-csomagot a megjegyzésekben megadott módon. A Microsoft.WindowsAzure.Storage.dllre mutató hivatkozás hozzáadásához előfordulhat, hogy telepítenie kell az [Azure Storage-szolgáltatáshoz tartozó ügyféloldali kódtárat](https://www.nuget.org/packages/WindowsAzure.Storage).

Az alábbi képernyőfelvételen a Azure Machine Learning webszolgáltatások portál **felhasználás lapja látható** .

![Felhasznált oldal](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>A apikey-deklaráció frissítése

Keresse meg a **apikey** deklarációját:

```csharp
const string apiKey = "abc123"; // Replace this with the API key for the web service
```

A **Felhasználás lap alapszintű** használat adatai  szakaszában keresse meg az elsődleges kulcsot, és másolja a **apikey** deklarációba.

### <a name="update-the-azure-storage-information"></a>Az Azure Storage-adatok frissítése

A BES mintakód feltölt egy fájlt egy helyi meghajtóról (például "C:\temp\CensusInput.csv") az Azure Storage-ba, feldolgozza azt, és visszaírja az eredményeket az Azure Storage-ba.

1. Bejelentkezés az Azure Portalra
1. A bal oldali navigációs oszlopban kattintson a **További szolgáltatások** elemre, keressen rá a **Storage-fiókok** elemre, és jelölje ki.
1. A Storage-fiókok listájából válassza ki az egyiket az átképzésen áthelyezett modell tárolásához.
1. A bal oldali navigációs oszlopban kattintson a **hozzáférési kulcsok** elemre.
1. Másolja és mentse az **elsődleges hozzáférési kulcsot**.
1. A bal oldali navigációs oszlopban kattintson a **Blobok** elemre.
1. Válasszon ki egy meglévő tárolót, vagy hozzon létre egy újat, és mentse a nevet.

Keresse meg a *StorageAccountName*, a *StorageAccountKey* és a *StorageContainerName* deklarációt, és frissítse a portálról mentett értékeket.

```csharp
const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name
```

Azt is biztosítania kell, hogy a bemeneti fájl elérhető legyen a kódban megadott helyen.

### <a name="specify-the-output-location"></a>Adja meg a kimeneti helyet

Amikor megadja a kimeneti helyet a kérelem adattartalmában, a *RelativeLocation* megadott fájl kiterjesztését meg kell adni `ilearner` .

```csharp
Outputs = new Dictionary<string, AzureBlobDataReference>() {
    {
        "output1",
        new AzureBlobDataReference()
        {
            ConnectionString = storageConnectionString,
            RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
        }
    },
```

Íme egy példa a kimenet átképzésére:

![Kimenet átképzése](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Az újraképzés eredményeinek kiértékelése

Az alkalmazás futtatásakor a kimenet tartalmazza a kiértékelési eredmények eléréséhez szükséges URL-címet és közös hozzáférésű aláírási jogkivonatot.

A visszatanított modell teljesítménybeli eredményeit a *BaseLocation*, a *RelativeLocation* és a *SasBlobToken* a *output2* kimeneti eredményeiből való összekapcsolásával, valamint a teljes URL-cím a böngésző címsorába való beillesztésével tekintheti meg.

Vizsgálja meg az eredményeket annak megállapításához, hogy az újonnan betanított modell jobban teljesít-e, mint a meglévő.

Mentse a *BaseLocation*, a *RelativeLocation* és a *SasBlobToken* a kimeneti eredményekből.

## <a name="update-the-predictive-experiment"></a>A prediktív kísérlet frissítése

### <a name="sign-in-to-azure-resource-manager"></a>Bejelentkezés Azure Resource Manager

Először jelentkezzen be az Azure-fiókjába a PowerShell-környezetből a [Kapcsolódás-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával.

### <a name="get-the-web-service-definition-object"></a>Webszolgáltatás-definíciós objektum beszerzése

Ezután szerezze be a webszolgáltatás-definíciós objektumot a [Get-AzMlWebService](/powershell/module/az.machinelearning/get-azmlwebservice) parancsmag meghívásával.

```azurepowershell
$wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

Egy meglévő webszolgáltatás erőforráscsoport-nevének meghatározásához futtassa az Get-AzMlWebService parancsmagot paraméterek nélkül az előfizetésben lévő webszolgáltatások megjelenítéséhez. Keresse meg a webszolgáltatást, és tekintse meg a webszolgáltatás AZONOSÍTÓját. Az erőforráscsoport neve az azonosító negyedik eleme, közvetlenül a *resourceGroups* elem után. A következő példában az erőforráscsoport neve Default-MachineLearning-SouthCentralUS.

```azurepowershell
Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
Name : RetrainSamplePre.2016.8.17.0.3.51.237
Location : South Central US
Type : Microsoft.MachineLearning/webServices
Tags : {}
```

Másik lehetőségként egy meglévő webszolgáltatás erőforráscsoport-nevének meghatározásához jelentkezzen be a Azure Machine Learning webszolgáltatások portálra. Válassza ki a webszolgáltatást. Az erőforráscsoport neve a webszolgáltatás URL-címének ötödik eleme, közvetlenül a *resourceGroups* elem után. A következő példában az erőforráscsoport neve Default-MachineLearning-SouthCentralUS.

`https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237`

### <a name="export-the-web-service-definition-object-as-json"></a>Webszolgáltatás-definíciós objektum exportálása JSON-ként

Ha módosítani szeretné a betanított modell definícióját az újonnan betanított modell használatára, először az [export-AzMlWebService](/powershell/module/az.machinelearning/export-azmlwebservice) parancsmagot kell használnia, hogy egy JSON formátumú fájlba exportálja azt.

```azurepowershell
Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-reference-to-the-ilearner-blob"></a>A ilearner-blobra mutató hivatkozás frissítése

Az eszközök területen keresse meg a [betanított modell] elemet, frissítse a *locationInfo* csomópont *URI* értékét a ilearner blob URI-ja szerint. Az URI-t a *BaseLocation* és a *RelativeLocation* egyesítésével hozza létre a BES átképzési hívás kimenetéről.

```json
"asset3": {
    "name": "Retrain Sample [trained model]",
    "type": "Resource",
    "locationInfo": {
        "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
    },
    "outputPorts": {
        "Results dataset": {
            "type": "Dataset"
        }
    }
},
```

### <a name="import-the-json-into-a-web-service-definition-object"></a>A JSON importálása webszolgáltatás-definíciós objektumba

Az [import-AzMlWebService](/powershell/module/az.machinelearning/import-azmlwebservice) parancsmaggal alakítsa vissza a módosított JSON-fájlt egy webszolgáltatás-definíciós objektumba, amelyet a predicative-kísérlet frissítéséhez használhat.

```azurepowershell
$wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-web-service"></a>Webszolgáltatás frissítése

Végül az [Update-AzMlWebService](/powershell/module/az.machinelearning/update-azmlwebservice) parancsmag használatával frissítse a prediktív kísérletet.

```azurepowershell
Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a webszolgáltatások kezeléséről vagy több kísérlet futtatásának nyomon követéséről, tekintse meg a következő cikkeket:

* [Ismerkedés a webszolgáltatások portálján](manage-new-webservice.md)
* [Kísérletismétlések kezelése](manage-experiment-iterations.md)