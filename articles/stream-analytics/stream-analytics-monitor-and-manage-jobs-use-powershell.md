---
title: Monitor and manage Azure Stream Analytics jobs with PowerShell
description: Ez a cikk azt ismerteti, hogyan használhatók Azure PowerShell és parancsmagok a feladatok figyelése és Azure Stream Analytics kezelésére.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/28/2017
ms.openlocfilehash: 15b2e5ef5873ea48c6c3f2c790619392f622fb66
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870138"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Monitor and manage Stream Analytics jobs with Azure PowerShell parancsmagok
Megtudhatja, hogyan figyelheti és kezelheti Stream Analytics erőforrásokat olyan Azure PowerShell parancsmagokkal és PowerShell-parancsfájlokkal, amelyek alapszintű Stream Analytics hajthatnak végre.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>A Azure PowerShell parancsmagok futtatásának előfeltételei Stream Analytics
* Hozzon létre egy Azure-erőforráscsoportot az előfizetésében. Az alábbi példa egy Azure PowerShell szkriptet. További Azure PowerShell lásd: [Install and configure Azure PowerShell](/powershell/azure/);  

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

Azure PowerShell 1.0:

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
> Stream Analytics létrehozott feladatokhoz alapértelmezés szerint nincs engedélyezve a figyelés.  Manuálisan engedélyezheti a monitorozást az Azure Portalon. Ehhez navigálhat a feladat Monitorozás lapjára, és kattintson az Engedélyezés gombra, vagy programozott módon, a [Azure Stream Analytics - Monitor Stream Analytics Jobs programmatically](stream-analytics-monitor-jobs.md)( Figyelés a Stream Analytics-feladatokban) helyen található lépéseket követve.
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure PowerShell parancsmagok a Stream Analytics
A következő Azure PowerShell parancsmagok segítségével figyelheti és kezelheti a Azure Stream Analytics feladatokat. Vegye figyelembe, Azure PowerShell verziók eltérőek. 
**A felsorolt példákban az első parancs Azure PowerShell 0.9.8-as, a második pedig az 1.0-s Azure PowerShell parancs.** Az Azure PowerShell 1.0-s parancsban mindig az "Az" szó lesz a parancsban.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Felsorolja Stream Analytics Azure-előfizetésben vagy adott erőforráscsoportban meghatározott összes feladatot, vagy lekért egy adott feladatra vonatkozó feladatadatokat egy erőforráscsoporton belül.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob
```

Ez a PowerShell-parancs információt ad vissza Stream Analytics Azure-előfizetésben található összes feladatról.

**2\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Ez a PowerShell-parancs információt ad vissza Stream Analytics StreamAnalytics-Default-Central-US erőforráscsoportban található összes feladatról.

**3\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Ez a PowerShell-parancs a StreamAnalytics-Default-Central-US Stream Analytics StreamJob erőforráscsoportban található streamjob feladatról ad vissza adatokat.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Felsorolja az adott feladatban meghatározott összes bemenetet Stream Analytics vagy információt kap egy adott bemenetről.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Ez a PowerShell-parancs információt ad vissza a StreamingJob feladatban meghatározott összes bemenetről.

**2\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Ez a PowerShell-parancs a StreamingJob feladatban meghatározott EntryStream nevű bemenettel kapcsolatos információkat ad vissza.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Felsorolja az adott feladatban meghatározott összes kimenetet Stream Analytics, vagy információt kap egy adott kimenetről.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Ez a PowerShell-parancs a StreamingJob feladatban definiált kimenetekkel kapcsolatos információkat ad vissza.

**2\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Ez a PowerShell-parancs a StreamingJob feladatban meghatározott Kimenet nevű kimenettel kapcsolatos információkat ad vissza.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Lekért információk a megadott régióban található streamelési egységek kvótáiról.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota -Location "Central US" 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsQuota -Location "Central US" 
```

Ez a PowerShell-parancs az USA középső régiójában található streamelési egységek kvótáját és használatát adja vissza.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
Információkat kap egy adott átalakításról, amely egy Stream Analytics feladatban van meghatározva.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Ez a PowerShell-parancs a StreamingJob nevű átalakítással kapcsolatos információkat ad vissza a StreamingJob feladatban.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
Új bemenetet hoz létre egy Stream Analytics feladatban, vagy frissíti a meglévő megadott bemenetet.

A bemenet neve a .json fájlban vagy a parancssorban is megadva lehet. Ha mindkettő meg van adva, a parancssor nevének meg kell egynie a fájlban található névvel.

Ha olyan bemenetet ad meg, amely már létezik, és nem adja meg a -Force paramétert, a parancsmag megkérdezi, hogy lecseréli-e a meglévő bemenetet.

Ha megadja a -Force paramétert, és megad egy meglévő bemeneti nevet, a rendszer megerősítés nélkül cseréli le a bemenetet.

A JSON-fájl szerkezetével és tartalmával kapcsolatos részletes információkért tekintse meg a Stream Analytics Management REST API Reference Library (Bemenet létrehozása [(Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] [című szakaszát.][stream.analytics.rest.api.reference]

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Ez a PowerShell-parancs új bemenetet hoz létre a fájlból, Input.jsbe. Ha a bemeneti definíciós fájlban megadott nevű meglévő bemenet már definiálva van, a parancsmag megkérdezi, hogy lecseréli-e vagy sem.

**2\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Ez a PowerShell-parancs új bemenetet hoz létre az EntryStream nevű feladatban. Ha már definiálva van egy meglévő bemenet ezzel a névvel, a parancsmag rá fog kérdezni, hogy lecseréli-e vagy sem.

**3\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Ez a PowerShell-parancs lecseréli az EntryStream nevű meglévő bemeneti forrás definícióját a fájlból származó definícióra.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
Létrehoz egy új Stream Analytics feladatot a Microsoft Azure, vagy frissíti egy meglévő megadott feladat definícióját.

A feladat neve a .json fájlban vagy a parancssorban is megadva lehet. Ha mindkettő meg van adva, a parancssor nevének meg kell egynie a fájlban található névvel.

Ha olyan feladatnevet ad meg, amely már létezik, és nem adja meg a -Force paramétert, a parancsmag megkérdezi, hogy lecseréli-e a meglévő feladatot.

Ha megadja a -Force paramétert, és megad egy meglévő feladatnevet, a rendszer megerősítés nélkül lecseréli a feladatdefiníciót.

A JSON-fájl szerkezetével és tartalmával kapcsolatos részletes információkért tekintse meg a Stream Analytics Management REST API [Reference][msdn-rest-api-create-stream-analytics-job] Library (Stream Analytics-feladat létrehozása) című [szakaszát.][stream.analytics.rest.api.reference]

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Ez a PowerShell-parancs létrehoz egy új feladatot a JobDefinition.jsalatt. Ha a feladatdefiníciós fájlban megadott nevű meglévő feladat már definiálva van, a parancsmag megkérdezi, hogy le kell-e cserélni.

**2\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Ez a PowerShell-parancs lecseréli a StreamingJob feladatdefinícióját.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
Új kimenetet hoz létre egy Stream Analytics feladatban, vagy frissíti a meglévő kimenetet.  

A kimenet neve a .json fájlban vagy a parancssorban is megadva lehet. Ha mindkettő meg van adva, a parancssor nevének meg kell egynie a fájlban található névvel.

Ha olyan kimenetet ad meg, amely már létezik, és nem adja meg a -Force paramétert, a parancsmag megkérdezi, hogy lecseréli-e a meglévő kimenetet.

Ha megadja a -Force paramétert, és megad egy meglévő kimeneti nevet, a kimenet megerősítés nélkül lesz lecserélve.

A JSON-fájl szerkezetével és tartalmával kapcsolatos részletes információkért tekintse meg a Stream Analytics Management REST API Reference Library (Kimenet létrehozása [(Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] című [szakaszát.][stream.analytics.rest.api.reference]

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Ez a PowerShell-parancs létrehoz egy "output" (kimenet) nevű új kimenetet a StreamingJob feladatban. Ha már definiálva van egy meglévő kimenet ezzel a névvel, a parancsmag rá fog kérdezni, hogy le kell-e cserélni.

**2\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Ez a PowerShell-parancs lecseréli a StreamingJob feladat "output" definícióját.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
Új átalakítást hoz létre egy Stream Analytics feladatban, vagy frissíti a meglévő átalakítást.

Az átalakítás neve a .json fájlban vagy a parancssorban is megadva lehet. Ha mindkettő meg van adva, a parancssor nevének meg kell egynie a fájlban található névvel.

Ha olyan átalakítást ad meg, amely már létezik, és nem adja meg a -Force paramétert, a parancsmag megkérdezi, hogy lecseréli-e a meglévő átalakítást.

Ha megadja a -Force paramétert, és megad egy meglévő átalakítási nevet, a rendszer megerősítés nélkül lecseréli az átalakítást.

A JSON-fájl szerkezetével és tartalmával kapcsolatos részletes információkért tekintse meg a Stream Analytics Management REST API Reference Library (Átalakítás létrehozása [(Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] [című szakaszát.][stream.analytics.rest.api.reference]

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Ez a PowerShell-parancs létrehoz egy StreamingJobTransform nevű új átalakítást a StreamingJob feladatban. Ha egy meglévő átalakítás már definiálva van ezzel a névvel, a parancsmag megkérdezi, hogy lecseréli-e vagy sem.

**2\. példa**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

 Ez a PowerShell-parancs felváltja a StreamingJobTransform definícióját a StreamingJob feladatban.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
Aszinkron módon töröl egy adott bemenetet egy Stream Analytics feladatból a Microsoft Azure.  
Ha megadja a -Force paramétert, a bemenet megerősítés nélkül törlődik.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Ez a PowerShell-parancs eltávolítja a bemeneti EventStreamet a StreamingJob feladatból.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
Aszinkron módon töröl egy adott Stream Analytics feladatot a Microsoft Azure.  
Ha megadja a -Force paramétert, a feladat megerősítés nélkül törlődik.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Ez a PowerShell-parancs eltávolítja a StreamingJob feladatot.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
Aszinkron módon töröl egy adott kimenetet egy Stream Analytics feladatból a Microsoft Azure.  
Ha megadja a -Force paramétert, a kimenet megerősítés nélkül törlődik.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Ez a PowerShell-parancs eltávolítja a kimenetet a StreamingJob feladatból.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Aszinkron módon telepít és elindít egy Stream Analytics feladatot a Microsoft Azure.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1.0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Ez a PowerShell-parancs 2012. december 12., 12:12:12 (UTC) egyéni kimeneti kezdési idővel indítja el a StreamingJob feladatot.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Aszinkron módon leállítja Stream Analytics, hogy egy Microsoft Azure futtason, és le kell foglalnia a használt erőforrásokat. A feladatdefiníció és a metaadatok az előfizetésen belül a Azure Portal és a felügyeleti API-kon keresztül is elérhetők maradnak, hogy a feladat szerkeszthető és újraindítható legyen. A leállított feladatért nem számítunk fel díjat.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Ez a PowerShell-parancs leállítja a StreamingJob feladatot.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Teszteli a Stream Analytics adott bemenethez való csatlakozásának képességét.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Ez a PowerShell-parancs az input EntryStream kapcsolati állapotát teszteli a StreamingJobs-feladatban.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Teszteli a Stream Analytics adott kimenethez való csatlakozásának képességét.

**1\. példa**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Ez a PowerShell-parancs a StreamingJobs-feladat kimenetének kapcsolati állapotát teszteli.  

## <a name="get-support"></a>Támogatás kérése
További segítségért próbálja ki [a Microsoft Q&A kérdések oldalát a Azure Stream Analytics.](/answers/topics/azure-stream-analytics.html) 

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)

[msdn-switch-azuremode]: /previous-versions/azure/dn722470(v=azure.100)
[powershell-install]: /powershell/azure/
[msdn-rest-api-create-stream-analytics-job]: ./stream-analytics-quick-create-portal.md
[msdn-rest-api-create-stream-analytics-input]: ./stream-analytics-define-inputs.md
[msdn-rest-api-create-stream-analytics-output]: ./stream-analytics-define-outputs.md
[msdn-rest-api-create-stream-analytics-transformation]: /cli/azure/stream-analytics/transformation

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/