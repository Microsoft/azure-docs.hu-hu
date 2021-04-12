---
title: Log Analytics konfigurálása a PowerShell-lel & létrehozása
description: Log Analytics munkaterületek Azure Monitor a helyszíni vagy Felhőbeli infrastruktúrában található kiszolgálók adatait tárolják. Az Azure-beli tárolóból származó gépi adatokat gyűjthet, ha azokat az Azure Diagnostics szolgáltatás hozza létre.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: a1abe79b4ab1caad04a088f659c0afceb2668eac
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106921"
---
# <a name="create-and-configure-a-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Log Analytics munkaterület létrehozása és konfigurálása Azure Monitorban a PowerShell használatával
Ez a cikk két olyan mintakód-mintát tartalmaz, amelyek bemutatják, hogyan hozhat létre és konfigurálhat egy Log Analytics munkaterületet Azure Monitor.  


> [!NOTE]
> Log Analytics korábban Operational Insights néven hívták, ezért a parancsmagokban használt név.


## <a name="prerequisites"></a>Előfeltételek
Ezek a példák az az. OperationalInsights modulhoz tartozó 1.0.0 vagy újabb verzióval működnek.

## <a name="create-workspace"></a>Munkaterület létrehozása
Az alábbi példa egy olyan munkaterületet hoz létre, amely nem rendelkezik adatforrás-konfigurációval. 

```powershell
$ResourceGroup = "my-resource-group"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique in resource group - Get-Random helps with this for the example code
$Location = "westeurope"

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
```

## <a name="create-workspace-and-configure-data-sources"></a>Munkaterület létrehozása és az adatforrások konfigurálása

Az alábbi minta parancsfájl egy munkaterületet hoz létre, és több adatforrást konfigurál. Ezek az adatforrások csak akkor szükségesek, ha a [log Analytics ügynök](../agents/log-analytics-agent.md)használatával figyeli a virtuális gépeket.

Ez a szkript a következő függvényeket hajtja végre:

1. Munkaterület létrehozása
2. Az elérhető megoldások listázása
3. Megoldások hozzáadása a munkaterülethez
4. Mentett keresések importálása
5. Mentett keresések exportálása
6. Számítógépcsoport létrehozása
7. IIS-naplók gyűjteményének engedélyezése a telepített Windows-ügynökkel rendelkező számítógépekről
8. Logikai lemezes teljesítmény-számlálók gyűjtése Linux rendszerű számítógépekről (felhasznált inode%-ban) Szabad megabájt; Felhasznált terület%-ban; Lemez átvitele/mp; Olvasási sebesség (lemez/mp) Írási idő/mp)
9. Syslog-események gyűjtése Linux rendszerű számítógépekről
10. Hiba-és figyelmeztetési események gyűjtése a Windows rendszerű számítógépekről származó alkalmazás-eseménynaplóból
11. Memória rendelkezésre álló memóriájának összegyűjtése (MB) teljesítményszámláló a Windows rendszerű számítógépekről
12. Egyéni napló gyűjtése

```powershell
$ResourceGroup = "my-resource-group"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique in resource group - Get-Random helps with this for the example code
$Location = "westeurope"

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

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
        "Query":  "Perf | where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and InstanceName == "C:"",
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
Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

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
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
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

> [!NOTE]
> Az egyéni naplók konfigurációját definiáló **CustomLogRawJson** paraméter formátuma összetett lehet. Egy meglévő egyéni napló konfigurációjának beolvasása a [Get-AzOperationalInsightsDataSource](/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource) használatával. A **Properties (Tulajdonságok** ) tulajdonság a **CustomLogRawJson** paraméterhez szükséges konfiguráció.

A fenti példában a regexDelimiter "n"-ként lett definiálva \\ a sortöréshez. A naplózási határolójel is lehet időbélyeg.  A támogatott formátumok a következők:

| Formátum | A JSON RegEx formátuma kettőt használ a `\\` `\` standard regexben, így ha a regex alkalmazásban a teszt csökkenti a következőt: `\\``\` |
| --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` |
| `MMM  d HH:mm:ss` <br> két szóköz az MMM után | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> ahol a + + vagy a- <br> zzzz idő eltolása | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` |
| `yyyy-MM-ddTHH:mm:ss` <br> A T egy literál betű, a t | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` |

## <a name="troubleshooting"></a>Hibaelhárítás
Ha olyan munkaterületet hoz létre, amely az elmúlt 14 napban törölve lett, és nem [törölhető állapotban](../logs/delete-workspace.md#soft-delete-behavior)van, a művelet a munkaterület konfigurációjától függően eltérő eredménnyel járhat:
1. Ha ugyanazt a munkaterület-nevet, erőforráscsoportot, előfizetést és régiót adja meg, mint a törölt munkaterületen, a munkaterület helyreállítva lesz, beleértve az adatait, a konfigurációját és a csatlakoztatott ügynököket is.
2. A munkaterület nevének egyedinek kell lennie erőforráscsoport szerint. Ha olyan munkaterület-nevet használ, amely már létezik, a rendszer a törlést is törli az Ön erőforráscsoportjában, akkor hibaüzenet jelenik meg, ha *a munkaterület neve nem egyedi*, vagy *ütközést okoz*. Az alábbi lépéseket követve felülbírálhatja a munkaterület helyreállítható törlését és végleges törlését, és létrehozhatja az új munkaterületet ugyanazzal a névvel, a munkaterület helyreállításához és az állandó törlés végrehajtásához hajtsa végre a következő lépéseket:
   * Munkaterület [helyreállítása](../logs/delete-workspace.md#recover-workspace)
   * Munkaterület [végleges törlése](../logs/delete-workspace.md#permanent-workspace-delete)
   * Új munkaterület létrehozása ugyanazzal a munkaterület-névvel


## <a name="next-steps"></a>Következő lépések
* [Tekintse át log Analytics PowerShell-parancsmagokat](/powershell/module/az.operationalinsights/) a log Analytics konfigurálásához a PowerShell használatával kapcsolatos további információkért.
