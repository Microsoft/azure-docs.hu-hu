---
title: 'ML Studio (klasszikus): üzembe helyezés és felhasználás – Azure'
description: A gépi tanulási munkafolyamatok és modellek webszolgáltatásként való üzembe helyezéséhez Azure Machine Learning Studio (klasszikus) használható. Ezek a webszolgáltatások a gépi tanulási modellek interneten keresztüli meghívására használhatók az előrejelzések valós idejű vagy kötegelt módban történő elvégzéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 57c5462cd6eccab1d9c42e674e10ea34db3612b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520357"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatások: üzembe helyezés és felhasználás

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

A gépi tanulási munkafolyamatok és modellek webszolgáltatásként való üzembe helyezéséhez Azure Machine Learning Studio (klasszikus) használható. Ezek a webszolgáltatások a gépi tanulási modellek interneten keresztüli meghívására használhatók az előrejelzések valós idejű vagy kötegelt módban történő elvégzéséhez. Mivel a webszolgáltatások REST-alapúak, különböző programozási nyelveken és platformokon, például a .NET-ben és a Java-ban, illetve az alkalmazásokból, például az Excelből is meghívhatók.

A következő szakaszokban a bemutatókat, a kódokat és a dokumentációt ismertető hivatkozásokat talál, amelyek segítenek az első lépések megtételében.

## <a name="deploy-a-web-service"></a>Webszolgáltatás üzembe helyezése

### <a name="with-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klasszikus)

A Studio (klasszikus) portál és a Microsoft Azure Machine Learning Web Services portál segítségével programkód írása nélkül telepítheti és kezelheti a webszolgáltatásokat.

Az alábbi hivatkozások általános információkat nyújtanak az új webszolgáltatás üzembe helyezéséről:

* A Azure Resource Manageron alapuló új webszolgáltatás üzembe helyezéséről az [új webszolgáltatás üzembe helyezését](deploy-a-machine-learning-web-service.md)ismertető cikkben olvashat.
* A webszolgáltatások üzembe helyezésével kapcsolatos útmutatóért lásd: [Azure Machine learning webszolgáltatás üzembe](deploy-a-machine-learning-web-service.md)helyezése.
* A webszolgáltatások létrehozásával és üzembe helyezésével kapcsolatos teljes útmutatóért először az [1. Oktatóanyag: a hitelkockázat előrejelzése](tutorial-part1-credit-risk.md)című témakörben olvashat.
* Webszolgáltatások üzembe helyezésére szolgáló konkrét Példákért lásd:

  * [3. Oktatóanyag: hitelkockázat-modell üzembe helyezése](tutorial-part3-credit-risk-deploy.md)
  * [Webszolgáltatás üzembe helyezése több régióban](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Webszolgáltatások erőforrás-szolgáltatói API-jai (Azure Resource Manager API-k)

A webszolgáltatások Azure Machine Learning Studio (klasszikus) erőforrás-szolgáltatója lehetővé teszi a webszolgáltatások üzembe helyezését és kezelését REST API-hívások használatával. További információ: [Machine learning webszolgáltatás (REST)](/rest/api/machinelearning/index) referenciája.

<!-- [Machine Learning Web Service (REST)](/rest/api/machinelearning/webservices) reference. -->

### <a name="with-powershell-cmdlets"></a>PowerShell-parancsmagokkal

A webes szolgáltatások Azure Machine Learning Studio (klasszikus) erőforrás-szolgáltatója lehetővé teszi a webszolgáltatások üzembe helyezését és kezelését a PowerShell-parancsmagok használatával.

A parancsmagok használatához először be kell jelentkeznie az Azure-fiókjába a PowerShell-környezetből a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával. Ha nem tudja, hogyan hívhatja meg a Resource Manageren alapuló PowerShell-parancsokat, tekintse meg a [Azure PowerShell használata a Azure Resource Manager használatával](../../azure-resource-manager/management/manage-resources-powershell.md)című témakört.

A prediktív kísérlet exportálásához használja [ezt a mintakód-kódot](https://github.com/ritwik20/AzureML-WebServices). Miután létrehozta az. exe fájlt a kódból, beírhatja a következőt:

```azurepowershell
C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>
```

Az alkalmazás futtatása webszolgáltatási JSON-sablont hoz létre. Ha a sablont egy webszolgáltatás üzembe helyezéséhez szeretné használni, a következő információkat kell felvennie:

* Storage-fiók neve és kulcsa

    A Storage-fiók nevét és kulcsát a [Azure Portal](https://portal.azure.com/)kérheti le.
* Kötelezettségvállalási terv azonosítója

    A díjcsomag beszerzéséhez jelentkezzen be a [Azure Machine learning Web Services](https://services.azureml.net) portálról, és kattintson a csomag nevére.

Adja hozzá őket a JSON-sablonhoz a *Tulajdonságok* csomópont gyermekeiként a *MachineLearningWorkspace* csomóponttal megegyező szinten.

Bemutatunk egy példát:

```json
"StorageAccount": {
        "name": "YourStorageAccountName",
        "key": "YourStorageAccountKey"
},
"CommitmentPlan": {
    "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
}
```

További részletekért tekintse meg a következő cikkeket és mintakód-kódot:

* [Azure Machine learning Studio (klasszikus) parancsmagok](/powershell/module/az.machinelearning) referenciája az MSDN-ben

## <a name="consume-the-web-services"></a>A webszolgáltatások felhasználása

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>A Azure Machine Learning Web Services felhasználói felületén (tesztelés)

A webszolgáltatást a Azure Machine Learning webszolgáltatások portálján ellenőrizheti. Ez magában foglalja a Request-Response Service (RR) és a Batch végrehajtási szolgáltatás (BES) felületének tesztelését.

* [Új webszolgáltatás üzembe helyezése](deploy-a-machine-learning-web-service.md)
* [Azure Machine Learning webszolgáltatás üzembe helyezése](deploy-a-machine-learning-web-service.md)
* [3. Oktatóanyag: hitelkockázat-modell üzembe helyezése](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Excelből

Letölthető egy Excel-sablon, amely a webszolgáltatást használja:

* [Azure Machine Learning webszolgáltatás kihasználása az Excelből](consuming-from-excel.md)
* [Azure Machine Learning webszolgáltatások Excel-bővítménye](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>REST-alapú ügyfélről

Azure Machine Learning webszolgáltatások REST-alapú API-k. Ezeket az API-kat különböző platformokról, például a .NET, a Python, az R, a Java stb. használatával lehet felhasználni. A  webszolgáltatások felhasználható lapja a [Microsoft Azure Machine learning webszolgáltatások portálján](https://services.azureml.net) olyan mintakód található, amely segítséget nyújt az első lépésekhez. További információ: [Az Azure Machine Learning webszolgáltatásainak használata](consume-web-services.md).