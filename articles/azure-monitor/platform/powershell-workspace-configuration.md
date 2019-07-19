---
title: A powershellel létrehozása és konfigurálása a Log Analytics-munkaterület |} A Microsoft Docs
description: Log Analytics munkaterületek Azure Monitor a helyszíni vagy Felhőbeli infrastruktúrában található kiszolgálók adatait tárolják. Ha az Azure diagnostics által létrehozott Azure storage-ból is összegyűjtheti a számítógépadatok.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.devlang: powershell
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.openlocfilehash: 4faa58536d6458b01adbb7dab60bfd10be18275b
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234800"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Log Analytics munkaterület kezelése a Azure Monitor a PowerShell használatával

A [log Analytics PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/) -parancsmagokkal különböző függvényeket hajthat végre a Azure Monitor egy log Analytics munkaterületen, a parancssorból vagy egy parancsfájl részeként.  A PowerShell használatával is elvégezheti a feladatok közé:

* Munkaterület létrehozása
* Adja hozzá, vagy eltávolíthat egy megoldást
* Importálás és exportálás mentett keresések
* Számítógépcsoport létrehozása
* Telepített Windows-ügynökkel rendelkező számítógépek IIS-naplók gyűjtésének engedélyezéséhez
* A Linux és Windows-számítógépekről teljesítményszámlálók gyűjtése
* Syslog események gyűjtésére a Linux rendszerű számítógépek
* Windows-Eseménynapló eseményeinek gyűjtése
* Egyéni események naplóinak összegyűjtése
* A log analytics-ügynök hozzáadása egy Azure virtuális gépen
* Konfigurálja a log analytics index adatokat gyűjteni, az Azure diagnostics használatával

Ez a cikk két kódmintákért a függvények a Powershellből hajthat végre.  Olvassa el a [Log Analytics PowerShell-parancsmagok leírása](https://docs.microsoft.com/powershell/module/az.operationalinsights/) más függvények.

> [!NOTE]
> A log Analytics korábbi neve Operational Insights, ezért a parancsmagok a név legyen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek
Ezek a példák az az. OperationalInsights modulhoz tartozó 1.0.0 vagy újabb verzióval működnek.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Létrehozása és konfigurálása a Log Analytics-munkaterület
Az alábbi parancsfájl a példa bemutatja, hogyan lehet:

1. Munkaterület létrehozása
2. Az elérhető megoldások listája
3. Megoldások hozzáadása a munkaterülethez
4. Importálás mentett keresések
5. Exportálás mentett keresések
6. Számítógépcsoport létrehozása
7. Telepített Windows-ügynökkel rendelkező számítógépek IIS-naplók gyűjtésének engedélyezéséhez
8. Logikai lemez teljesítményszámlálók gyűjtése Linux rendszerű számítógépek (% Inode-OK; Szabad hely MB-ban; Foglalt hely; % Lemez átvitel/mp-ben; Lemezolvasások/mp; Lemezírások/mp)
9. Syslog-események gyűjtésére a Linux rendszerű számítógépek
10. Az alkalmazások eseménynaplójában a Windows-számítógépek hiba és figyelmeztetés eseményeinek gyűjtése
11. Windows-számítógépekről memória rendelkezésre álló memória (MB) teljesítményszámláló gyűjtése.
12. Egyéni napló gyűjtése

```powershell

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Perf | where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and InstanceName == "C:",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1",
    "description": "Example custom log datasource",
    "inputs": [
        {
            "location": {
            "fileSystemLocations": {
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                "linuxFileTypeLogPaths": [ "/var/logs" ]
                }
            },
        "recordDelimiter": {
            "regexDelimiter": {
                "pattern": "\\n",
                "matchIndex": 0,
                "matchIndexSpecified": true,
                "numberedGroup": null
                }
            }
        }
    ],
    "extractions": [
        {
            "extractionName": "TimeGenerated",
            "extractionType": "DateTime",
            "extractionProperties": {
                "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                    }
                }
            }
        ]
    }
"@

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxPerformanceCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```
A fenti példában a regexDelimiter "\\n"-ként lett definiálva a sortöréshez. A naplózási határolójel is lehet időbélyeg.  A támogatott formátumok a következők:

| Formátum | A JSON-regex formátuma kettőt \\ használ a standard regexben, így ha egy regex alkalmazásban a teszt csökkenti \\ a \ | | |
| --- | --- | --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` | | |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` | | |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM  d HH:mm:ss` <br> két szóköz az MMM után | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> ahol a + + vagy a- <br> zzzz idő eltolása | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> A T egy literál betű, a t | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Log Analytics konfigurálása az Azure Diagnostics elküldéséhez
Az ügynök nélküli figyelés az Azure-erőforrások, az erőforrásokat az Azure diagnostics engedélyezni és konfigurálni a Log Analytics-munkaterület írni rendelkeznie kell. Ez a módszer közvetlenül a munkaterületre küld adatokat, és nem igényli, hogy az adatokat egy Storage-fiókba írja. Támogatott erőforrások közé tartoznak:

| Erőforrás típusa | Logs | Mérőszámok |
| --- | --- | --- |
| Application Gateway-átjárók    | Igen | Igen |
| Automation-fiókok     | Igen | |
| Batch-fiókok          | Igen | Igen |
| A Data Lake analytics     | Igen | |
| A Data Lake store         | Igen | |
| Rugalmas SQL-készlet        |     | Igen |
| Event Hubs-névtér     |     | Igen |
| IoT Hubok                |     | Igen |
| Key Vault               | Igen | |
| Terheléselosztók          | Igen | |
| Logic Apps              | Igen | Igen |
| Network Security Groups (Hálózati biztonsági csoportok) | Igen | |
| Azure Cache for Redis             |     | Igen |
| Szolgáltatások keresése         | Igen | Igen |
| Service Bus-névtér   |     | Igen |
| SQL (v12)               |     | Igen |
| Webhelyek               |     | Igen |
| Webkiszolgálófarmok        |     | Igen |

A rendelkezésre álló metrikák részleteit, tekintse meg a [az Azure monitorban támogatott mérőszámok](../../azure-monitor/platform/metrics-supported.md).

A részletes naplók, tekintse meg [szolgáltatások és a séma támogatja a diagnosztikai naplók](../../azure-monitor/platform/diagnostic-logs-schema.md).

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

A fenti parancsmag használatával gyűjtsön naplókat azokról erőforrások különböző előfizetésekhez tartoznak. A parancsmag az előfizetések között tud működni, mivel az erőforrás-létrehozási naplókat és a naplókat küldő munkaterületet is megadja.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Log Analytics munkaterület konfigurálása az Azure Diagnostics tárolóból való összegyűjtéséhez
Klasszikus a felhőszolgáltatás és a egy service fabric-fürtben futó példányát belül származó naplóadatokat gyűjthet, először az Azure storage-szeretne adatokat írni kell. Ezután egy Log Analytics munkaterület lesz konfigurálva a naplók a Storage-fiókból való összegyűjtéséhez. Támogatott erőforrások közé tartoznak:

* Klasszikus cloud services (webes és feldolgozói szerepkörök)
* Service fabric-fürtök

A következő példa bemutatja, hogyan:

1. Felsorolja a meglévő Storage-fiókokat és-helyeket, amelyeket a munkaterület az adatok indexeléséhez fog indexelni
2. Hozzon létre egy storage-fiókból olvassa el a konfigurációt
3. Frissítse az újonnan létrehozott konfigurációt index adatokat további helyekről
4. Az újonnan létrehozott konfigurációjának törlése

```powershell
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable"
$workspace = (Get-AzOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want the workspace to index
$storageId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight"

```

Az előző parancsfájlt használhatja gyűjteni a tárfiókok eltérő előfizetésekben is. A szkript az előfizetések között tud működni, mivel a Storage-fiók erőforrás-AZONOSÍTÓját és egy hozzá tartozó hozzáférési kulcsot biztosít. Amikor módosítja a hozzáférési kulcsot, az új kulcsot, a storage insight frissíteni szeretné.


## <a name="next-steps"></a>További lépések
* [Tekintse át a Log Analytics PowerShell-parancsmagok](https://docs.microsoft.com/powershell/module/az.operationalinsights/) további információk a PowerShell használatával a Log Analytics-konfigurációhoz.

