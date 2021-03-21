---
title: Azure Stream Analytics feladatok figyelése és kezelése a PowerShell-lel
description: Ez a cikk azt ismerteti, hogyan használhatók a Azure PowerShell és a parancsmagok a Azure Stream Analytics feladatok figyeléséhez és kezeléséhez.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/28/2017
ms.openlocfilehash: 6b404516c513dea0888974ffb4fa3d8d43db6c44
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015181"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Stream Analytics feladatok figyelése és kezelése Azure PowerShell-parancsmagokkal
Megtudhatja, hogyan figyelheti és kezelheti Stream Analytics erőforrásait Azure PowerShell parancsmagokkal és PowerShell-parancsfájlokkal, amelyek alapszintű Stream Analytics feladatokat hajtanak végre.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Azure PowerShell-parancsmagok futtatásának előfeltételei a Stream Analytics
* Hozzon létre egy Azure-erőforráscsoportot az előfizetésében. A következő egy minta Azure PowerShell szkript. Azure PowerShell információ: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/);  

Azure PowerShell 0.9.8:  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Azure PowerShell 1,0:

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription -SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> Stream Analytics a programozott módon létrehozott feladatok nem rendelkeznek alapértelmezett figyeléssel.  Az Azure Portalon manuálisan engedélyezheti a figyelést, ha megnyitja a feladat figyelője lapját, és az Engedélyezés gombra kattint, vagy ezt programozott módon is elvégezheti, ha a [Azure stream Analytics-Monitor stream Analytics-feladatokat programozott](stream-analytics-monitor-jobs.md)módon végzi el.
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>A Stream Analytics Azure PowerShell-parancsmagjai
A következő Azure PowerShell parancsmagok használhatók Azure Stream Analytics feladatok figyelésére és kezelésére. Vegye figyelembe, hogy Azure PowerShell különböző verziókkal rendelkezik. 
**Az első parancsban szereplő példákban Azure PowerShell 0.9.8, a második parancs a Azure PowerShell 1,0.** A Azure PowerShell 1,0 parancsok mindig az "az" parancsot fogják tartalmazni a parancsban.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Felsorolja az Azure-előfizetésben vagy a megadott erőforráscsoportben definiált összes Stream Analytics feladatot, vagy az erőforráscsoport egy adott feladatával kapcsolatos információkat kap.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob
```

Ez a PowerShell-parancs az Azure-előfizetésben található összes Stream Analytics feladat adatait adja vissza.

**2\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Ez a PowerShell-parancs az StreamAnalytics-default-Central-USA nevű erőforráscsoport összes Stream Analytics feladatával kapcsolatos információkat ad vissza.

**3\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Ez a PowerShell-parancs az StreamAnalytics-default-Central-USA StreamingJob található Stream Analytics feladatra vonatkozó adatokat adja vissza.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Felsorolja a megadott Stream Analytics feladatban definiált összes bemenetet, vagy lekéri egy adott bemenet adatait.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Ez a PowerShell-parancs a StreamingJob megadott összes bemenet adatait adja vissza.

**2\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Ez a PowerShell-parancs a StreamingJob-ben definiált EntryStream nevű bemenettel kapcsolatos információkat adja vissza.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Felsorolja a megadott Stream Analytics feladatban definiált összes kimenetet, vagy lekéri egy adott kimenet adatait.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Ez a PowerShell-parancs a StreamingJob megadott kimenetekkel kapcsolatos információkat adja vissza.

**2\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Ez a PowerShell-parancs a StreamingJob-ben definiált kimenetre vonatkozó adatokat adja vissza.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Beolvas egy adott régióban lévő folyamatos átviteli egységekre vonatkozó kvótát.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota -Location "Central US" 
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsQuota -Location "Central US" 
```

Ez a PowerShell-parancs az USA középső régiójában lévő folyamatos átviteli egységek kvótáját és használatát adja vissza.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
Információ beolvasása egy Stream Analytics feladatban definiált adott transzformációról.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Ez a PowerShell-parancs a StreamingJob nevű átalakítással kapcsolatos információkat ad vissza a (StreamingJob) feladatban.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
Új bemenetet hoz létre egy Stream Analytics feladaton belül, vagy frissít egy meglévő megadott bemenetet.

A bemenet neve megadható a. JSON fájlban vagy a parancssorban. Ha mindkettő meg van adva, a parancssorban szereplő névnek meg kell egyeznie a fájl egy fájljával.

Ha olyan bemenetet ad meg, amely már létezik, és nem adja meg a-Force paramétert, a parancsmag megkérdezi, hogy lecseréli-e a meglévő bemenetet.

Ha a-Force paramétert adja meg, és megad egy meglévő bemeneti nevet, a rendszer megerősítés nélkül lecseréli a bemenetet.

A JSON-fájl struktúrájával és tartalmával kapcsolatos részletes információkért tekintse meg a [stream Analytics Management REST API hivatkozási könyvtár][stream.analytics.rest.api.reference] [bemenetének létrehozása (Azure stream Analytics)][msdn-rest-api-create-stream-analytics-input] szakaszát.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Ez a PowerShell-parancs új bemenetet hoz létre a (z) Input.jsfájlból. Ha már meg van adva egy meglévő bemenet a bemeneti definíciós fájlban megadott névvel, akkor a parancsmag megkérdezi, hogy lecseréli-e a fájlt.

**2\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Ez a PowerShell-parancs létrehoz egy új bemenetet a EntryStream nevű feladatban. Ha már definiálva van ilyen nevű meglévő bemenet, a parancsmag megkérdezi, hogy lecseréli-e vagy sem.

**3\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Ez a PowerShell-parancs lecseréli a EntryStream nevű meglévő bemeneti forrás definícióját a fájl definíciója alapján.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
Új Stream Analytics-feladatot hoz létre a Microsoft Azureban, vagy egy meglévő megadott feladatok definícióját frissíti.

A feladatok neve megadható a. JSON fájlban vagy a parancssorban. Ha mindkettő meg van adva, a parancssorban szereplő névnek meg kell egyeznie a fájl egy fájljával.

Ha olyan feladatot ad meg, amely már létezik, és nem adja meg a-Force paramétert, a parancsmag megkérdezi, hogy lecseréli-e a meglévő feladatot.

Ha a-Force paramétert adja meg, és megad egy meglévő feladatot, a rendszer megerősítés nélkül lecseréli a feladatdefiníció értékét.

A JSON-fájl struktúrájával és tartalmával kapcsolatos részletes információkért tekintse meg az [stream Analytics Management REST API hivatkozási könyvtárának][stream.analytics.rest.api.reference] [stream Analytics feladatok létrehozása][msdn-rest-api-create-stream-analytics-job] című szakaszát.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Ez a PowerShell-parancs új feladatot hoz létre a definíciójában JobDefinition.json. Ha már meg van adva egy meglévő, a feladattípusban megadott nevű feladattípus, a parancsmag megkérdezi, hogy lecseréli-e a fájlt.

**2\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Ez a PowerShell-parancs a StreamingJob tartozó feladatdefiníció helyébe lép.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
Létrehoz egy új kimenetet egy Stream Analytics feladatokon belül, vagy frissít egy meglévő kimenetet.  

A kimenet neve megadható a. JSON fájlban vagy a parancssorban. Ha mindkettő meg van adva, a parancssorban szereplő névnek meg kell egyeznie a fájl egy fájljával.

Ha olyan kimenetet ad meg, amely már létezik, és nem adja meg a-Force paramétert, a parancsmag megkérdezi, hogy lecseréli-e a meglévő kimenetet.

Ha a-Force paramétert adja meg, és egy meglévő kimeneti nevet ad meg, a rendszer megerősítés nélkül lecseréli a kimenetet.

A JSON-fájl struktúrájával és tartalmával kapcsolatos részletes információkért tekintse meg a [stream Analytics Management REST API-függvénytár][stream.analytics.rest.api.reference] [kimenetének létrehozása (Azure stream Analytics)][msdn-rest-api-create-stream-analytics-output] szakaszát.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Ez a PowerShell-parancs egy "output" nevű új kimenetet hoz létre a (z) StreamingJob feladatokban. Ha már meg van adva egy ilyen nevű meglévő kimenet, a parancsmag megkérdezi, hogy lecseréli-e vagy sem.

**2\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Ez a PowerShell-parancs lecseréli a "output" definícióját a StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
Új átalakítást hoz létre egy Stream Analytics feladatokon belül, vagy frissíti a meglévő átalakítást.

Az átalakítás neve megadható a. JSON fájlban vagy a parancssorban. Ha mindkettő meg van adva, a parancssorban szereplő névnek meg kell egyeznie a fájl egy fájljával.

Ha olyan átalakítást ad meg, amely már létezik, és nem adja meg a-Force paramétert, a parancsmag megkérdezi, hogy lecseréli-e a meglévő átalakítást.

Ha a-Force paramétert adja meg, és megadta a meglévő átalakítás nevét, a rendszer megerősítés nélkül lecseréli az átalakítást.

A JSON-fájl struktúrájával és tartalmával kapcsolatos részletes információkért tekintse meg a [stream Analytics felügyeleti REST API hivatkozási könyvtár][stream.analytics.rest.api.reference] [átalakításának létrehozása (Azure stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] szakaszát.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Ez a PowerShell-parancs létrehoz egy új, StreamingJobTransform nevű átalakítást a (StreamingJob) feladatokban. Ha már létezik ilyen nevű átalakítás, a parancsmag megkérdezi, hogy lecseréli-e vagy sem.

**2\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

 Ez a PowerShell-parancs a StreamingJobTransform definícióját váltja fel a StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
Egy Stream Analytics feladatból aszinkron módon töröl egy adott bemenetet Microsoft Azure.  
Ha a-Force paramétert adja meg, a rendszer megerősítés nélkül törli a bemenetet.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Ez a PowerShell-parancs eltávolítja a bemeneti EventStream a feladathoz tartozó StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
Egy adott Stream Analytics-feladatot aszinkron módon töröl Microsoft Azure.  
Ha a-Force paramétert megadja, a rendszer megerősítés nélkül törli a feladatot.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Ez a PowerShell-parancs eltávolítja a StreamingJob feladatot.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
Aszinkron módon töröl egy adott kimenetet egy Stream Analytics feladatokból a Microsoft Azureban.  
Ha a-Force paramétert adja meg, a rendszer megerősítés nélkül törli a kimenetet.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Ez a PowerShell-parancs eltávolítja a kimeneti kimenetet a StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Aszinkron módon helyez üzembe és indít el egy Stream Analytics feladatot a Microsoft Azureban.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1,0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Ez a PowerShell-parancs elindítja a feladatot a StreamingJob egy egyéni kimeneti kezdési időponttal, amely 2012, 12:12:12 UTC értékre van állítva.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Aszinkron módon leállít egy Stream Analytics feladatot a Microsoft Azure-ben való futtatásból, és lefoglalja a használatban lévő erőforrásokat. A feladatdefiníció és a metaadatok a Azure Portal és a felügyeleti API-kon keresztül is elérhetők maradnak az előfizetésen belül, így a feladatok szerkeszthetők és újraindíthatók. Leállított állapotban lévő feladatokért nem számítunk fel díjat.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1,0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Ez a PowerShell-parancs leállítja a StreamingJob feladatot.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Ellenőrzi, hogy Stream Analytics tud-e csatlakozni egy megadott bemenethez.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Ez a PowerShell-parancs a StreamingJob bemeneti EntryStream a kapcsolatok állapotát teszteli.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Ellenőrzi, hogy Stream Analytics tud-e csatlakozni a megadott kimenethez.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1,0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Ez a PowerShell-parancs teszteli a kimeneti kimenet StreamingJob való állapotát.  

## <a name="get-support"></a>Támogatás kérése
További segítségért próbálja ki a [Microsoft Q&a Azure stream Analytics kérdéseit](/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)

[msdn-switch-azuremode]: /previous-versions/azure/dn722470(v=azure.100)
[powershell-install]: /powershell/azure/
[msdn-rest-api-create-stream-analytics-job]: ./stream-analytics-quick-create-portal.md
[msdn-rest-api-create-stream-analytics-input]: ./stream-analytics-define-inputs.md
[msdn-rest-api-create-stream-analytics-output]: ./stream-analytics-define-outputs.md
[msdn-rest-api-create-stream-analytics-transformation]: /cli/azure/ext/stream-analytics/stream-analytics/transformation

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/