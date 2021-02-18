---
title: Log Analytics riasztás használata REST API
description: A Log Analytics riasztási REST API lehetővé teszi a riasztások létrehozását és kezelését Log Analyticsban, amely Log Analytics részét képezi.  Ez a cikk részletesen ismerteti az API-t és számos példát a különböző műveletek végrehajtásához.
ms.subservice: logs
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 6c347b7b8ddaac103019e15e32fb5c06219e0064
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620915"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Riasztási szabályok létrehozása és kezelése a Log Analyticsban REST API 

> [!IMPORTANT]
> Amint [bejelentettük](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), a *2019. június 1* . után létrehozott log Analytics-munkaterületek a riasztási szabályok kezelése a jelenlegi [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules/)-val. Javasoljuk, hogy a régebbi munkaterületeken található [aktuális API](./alerts-log-api-switch.md) -ra váltson Azure monitor scheduledQueryRules [előnyök](./alerts-log-api-switch.md#benefits)kihasználása érdekében. Ez a cikk a riasztási szabályok a régi API-val történő kezelését ismerteti.

A Log Analytics riasztási REST API lehetővé teszi a riasztások létrehozását és kezelését Log Analyticsban.  Ez a cikk részletesen ismerteti az API-t és számos példát a különböző műveletek végrehajtásához.

A Log Analytics keresési REST API REST-vel rendelkezik, és a Azure Resource Manager REST API keresztül érhető el. Ebben a dokumentumban olyan példákat talál, amelyekben az API egy PowerShell-parancssorból érhető el az  [ARMClient](https://github.com/projectkudu/ARMClient)használatával, amely leegyszerűsíti a Azure Resource Manager API meghívását. A ARMClient és a PowerShell használata számos lehetőség a Log Analytics Search API eléréséhez. Ezekkel az eszközökkel a REST-Azure Resource Manager API-val hívásokat indíthat Log Analytics munkaterületekre, és keresési parancsokat hajthat végre rajtuk. Az API JSON formátumban jeleníti meg a keresési eredményeket, így a keresési eredmények többféleképpen is használhatók.

## <a name="prerequisites"></a>Előfeltételek
Jelenleg a riasztásokat csak Log Analytics mentett kereséssel lehet létrehozni.  További információért tekintse meg a [naplóbeli keresés REST API](../log-query/log-query-overview.md) .

## <a name="schedules"></a>Ütemezések
Egy mentett kereséshez egy vagy több ütemterv is tartozhat. Az ütemterv meghatározza, hogy a keresés milyen gyakran fusson, valamint azt az időtartamot, ameddig a feltételek azonosíthatók.
Az ütemezett tulajdonságok a következő táblázatban láthatók.

| Tulajdonság | Leírás |
|:--- |:--- |
| Időköz |A keresés futtatásának gyakorisága. Percekben mérve. |
| QueryTimeSpan |Az az időtartam, ameddig a feltételek kiértékelése megtörténik. Az értéknek meg kell egyeznie az Intervallumtal vagy nagyobbnak kell lennie. Percekben mérve. |
| Verzió |A használt API-verzió.  Jelenleg ezt mindig 1-re kell állítani. |

Tegyük fel például, hogy egy 15 perces időközzel és egy 30 perces TimeSpan rendelkező esemény-lekérdezést. Ebben az esetben a lekérdezés 15 percenként fut, és a rendszer riasztást küld, ha a feltételek továbbra is az igaz értékre vannak állítva egy 30 perces tartományon belül.

### <a name="retrieving-schedules"></a>Ütemtervek beolvasása
A Get metódussal kérheti le a mentett keresések összes ütemtervét.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20
```

A Get metódus és egy ütemezett azonosító használatával egy adott ütemtervet kérhet le egy mentett kereséshez.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

Az alábbi példa egy ütemezett választ mutat be.

```json
{
   "value": [{
      "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/sampleRG/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
      "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
      "properties": {
         "Interval": 15,
         "QueryTimeSpan": 15,
         "Enabled": true,
      }
   }]
}
```

### <a name="creating-a-schedule"></a>Ütemterv létrehozása
Új ütemterv létrehozásához használja a Put metódust egy egyedi ütemterv-AZONOSÍTÓval.  Két ütemterv nem rendelkezhet ugyanazzal az AZONOSÍTÓval, még akkor sem, ha különböző mentett keresésekhez vannak társítva.  Ha a Log Analytics-konzolon hoz létre egy ütemtervet, a rendszer létrehoz egy GUID azonosítót az ütemezett azonosítóhoz.

> [!NOTE]
> A Log Analytics API-val létrehozott összes mentett keresés, ütemterv és művelet neve csak kisbetűs lehet.

```powershell
$scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="editing-a-schedule"></a>Ütemterv szerkesztése
A Put metódust meglévő ütemezett AZONOSÍTÓval használhatja ugyanazon mentett kereséshez az adott ütemterv módosításához; az alábbi példában az ütemterv le van tiltva. A kérelem törzsének tartalmaznia kell az ütemterv *ETAG* .

```powershell
$scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="deleting-schedules"></a>Ütemtervek törlése
Az ütemterv törléséhez használja a DELETE metódust az ütemterv-AZONOSÍTÓval.

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

## <a name="actions"></a>Műveletek
Egy ütemterv több művelettel is rendelkezhet. Egy művelet egy vagy több folyamatot is meghatározhat, például e-mailek küldését vagy runbook elindítását, vagy meghatározhat egy küszöbértéket, amely meghatározza, hogy egy adott keresési eredmény megfelel-e bizonyos feltételeknek.  Néhány művelet mindkét esetben meghatározza, hogy a rendszer a küszöbérték teljesülése esetén hajtsa végre a folyamatokat.

Az összes művelet a következő táblázatban található tulajdonságokkal rendelkezik.  A különböző típusú riasztások különböző további tulajdonságokkal rendelkeznek, amelyek leírása alább található.

| Tulajdonság | Leírás |
|:--- |:--- |
| `Type` |A művelet típusa.  A lehetséges értékek jelenleg a riasztások és a webhookok. |
| `Name` |A riasztás megjelenítendő neve. |
| `Version` |A használt API-verzió.  Jelenleg ezt mindig 1-re kell állítani. |

### <a name="retrieving-actions"></a>Műveletek beolvasása

A Get metódussal kérheti le az összes műveletet egy adott ütemtervhez.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20
```

Használja a Get metódust a műveleti AZONOSÍTÓval egy adott művelet ütemezett lekéréséhez.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20
```

### <a name="creating-or-editing-actions"></a>Műveletek létrehozása vagy szerkesztése
Egy új művelet létrehozásához használja a Put metódust egy olyan műveleti AZONOSÍTÓval, amely egyedi az ütemtervben.  Amikor létrehoz egy műveletet a Log Analytics-konzolon, a műveleti AZONOSÍTÓhoz egy GUID azonosító szükséges.

> [!NOTE]
> A Log Analytics API-val létrehozott összes mentett keresés, ütemterv és művelet neve csak kisbetűs lehet.

Használja az Put metódust egy meglévő műveleti AZONOSÍTÓval ugyanahhoz a mentett kereséshez az ütemterv módosításához.  A kérelem törzsének tartalmaznia kell az ütemterv ETAG.

Az új műveletek létrehozási kérelmi formátuma Művelettípus szerint változik, így ezek a példák az alábbi részekben találhatók.

### <a name="deleting-actions"></a>Műveletek törlése

Művelet törléséhez használja a DELETE metódust a műveleti AZONOSÍTÓval.

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20
```

### <a name="alert-actions"></a>Riasztási műveletek
Az ütemtervnek egy és csak egy riasztási művelettel kell rendelkeznie.  A riasztási műveletekhez a következő táblázat egy vagy több szakaszában található.  Az alábbiakban részletesebben is olvashat.

| Section | Description | Használat |
|:--- |:--- |:--- |
| Küszöbérték |A művelet futtatásának feltételei.| Minden riasztáshoz szükséges, az Azure-ra való kiterjesztés előtt vagy után. |
| Súlyosság |Az aktiváláskor a riasztás osztályozásához használt címke.| Minden riasztáshoz szükséges, az Azure-ra való kiterjesztés előtt vagy után. |
| Elnyomják |Az értesítések riasztásból való leállításának lehetősége. | Nem kötelező minden riasztáshoz, az Azure-ra való kiterjesztés előtt vagy után. |
| Műveletcsoportok |Az Azure ActionGroup azonosítói, ahol a szükséges műveletek, például az e-mailek, az SMS-hívások, a hanghívások, a webhookok, az Automation Runbookok, a ITSM-összekötők stb.| A riasztások az Azure-ra való kiterjesztése kötelező|
| Műveletek testreszabása|A Select műveletek standard kimenetének módosítása a ActionGroup| Minden riasztás esetében választható, ha a riasztások kiterjeszthetők Az Azure-ra. |

### <a name="thresholds"></a>Küszöbértékek
A riasztási műveletnek egy és csak egy küszöbértékkel kell rendelkeznie.  Ha a mentett keresés eredményei megfelelnek a kereséshez társított művelet küszöbértékének, akkor az adott műveletben szereplő többi folyamat is fut.  Egy művelet csak egy küszöbértéket tartalmazhat, hogy olyan más típusú műveletekkel is használható legyen, amelyek nem tartalmaznak küszöbértékeket.

A küszöbértékek tulajdonságai a következő táblázatban láthatók.

| Tulajdonság | Leírás |
|:--- |:--- |
| `Operator` |A küszöbérték-összehasonlítás operátora. <br> gt = nagyobb, mint <br> lt = kisebb, mint |
| `Value` |A küszöbérték értéke. |

Vegyünk például egy 15 perces intervallummal rendelkező Event lekérdezést, egy 30 perces TimeSpan és egy 10-nél nagyobb küszöbértéket. Ebben az esetben a lekérdezés 15 percenként fut, és a riasztás akkor aktiválódik, ha 10 olyan eseményt adott vissza, amely egy 30 perces időtartamon belül lett létrehozva.

A következő példa egy olyan műveletre adott választ, amely csak egy küszöbértéket mutat be.  

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Version": 1
}
```

A Put metódust egyedi műveleti AZONOSÍTÓval használva új küszöbértéket hozhat létre egy ütemezett művelethez.  

```powershell
$thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

Egy meglévő műveleti AZONOSÍTÓval rendelkező Put metódus használatával módosíthatja egy ütemezett küszöbérték-műveletet.  A kérelem törzsének tartalmaznia kell a művelet ETAG.

```powershell
$thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

#### <a name="severity"></a>Súlyosság
Log Analytics lehetővé teszi a riasztások kategóriákba osztályozását, így könnyebben kezelhető és osztályozható. A riasztás súlyossága definiálva: tájékoztatási, figyelmeztetési és kritikus. Ezek az Azure-riasztások normalizált súlyossági skálájának megfelelően vannak leképezve:

|Súlyossági szint Log Analytics  |Azure-riasztások súlyossági szintje  |
|---------|---------|
|`critical` |0. szint|
|`warning` |1. szint|
|`informational` | Sev 2|

A következő példa egy olyan műveletre adott választ, amely csak a küszöbértéket és a súlyosságot követi. 

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Severity": "critical",
   "Version": 1
}
```

A Put metódust egyedi műveleti AZONOSÍTÓval használva hozzon létre egy új műveletet egy súlyosságú ütemtervhez.  

```powershell
$thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

Egy meglévő műveleti AZONOSÍTÓval rendelkező Put metódus használatával módosíthatja egy adott ütemterv súlyossági műveletét.  A kérelem törzsének tartalmaznia kell a művelet ETAG.

```powershell
$thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

#### <a name="suppress"></a>Elnyomják
A Log Analytics-alapú lekérdezési riasztások minden alkalommal elérik vagy túllépik a küszöbértéket. A lekérdezésben szereplő logika alapján ez riasztást eredményezhet az intervallumok egy adott időpontjában, ezért az értesítések is folyamatosan lesznek elküldve. Az ilyen helyzetek megelőzése érdekében a felhasználó beállíthatja, hogy a letiltási lehetőség arra utasítja a Log Analyticsot, hogy várjon egy meghatározott időtartamra, mielőtt a riasztási szabály második alkalommal bekerül az értesítésbe. Így ha a Mellőzés 30 percre van beállítva, ezt követően a riasztás az első alkalommal fog megjelenni, és az értesítések küldésére van konfigurálva. A riasztási szabályra vonatkozó értesítés ismételt használata azonban 30 percet is igénybe vehet. Az átmeneti időszak alatt a riasztási szabály továbbra is csak a csak az értesítéseket fogja letiltani Log Analytics a megadott időtartamig, függetlenül attól, hogy a riasztási szabály hányszor lett elindítva ebben az időszakban.

Log Analytics riasztási szabály tulajdonságának letiltása a *szabályozási* érték és a letiltási időszak használatával a *DurationInMinutes* érték használatával.

A következő példa egy olyan műveletre adott válasz, amely csak a küszöbértéket, a súlyosságot és a letiltási tulajdonságot követi

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Throttling": {
   "DurationInMinutes": 30
   },
   "Severity": "critical",
   "Version": 1
}
```

A Put metódust egyedi műveleti AZONOSÍTÓval használva hozzon létre egy új műveletet egy súlyosságú ütemtervhez.  

```powershell
$AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

Egy meglévő műveleti AZONOSÍTÓval rendelkező Put metódus használatával módosíthatja egy adott ütemterv súlyossági műveletét.  A kérelem törzsének tartalmaznia kell a művelet ETAG.

```powershell
$AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

#### <a name="action-groups"></a>Műveletcsoportok
Minden riasztás az Azure-ban, a műveleti csoport használata a műveletek kezelésére szolgáló alapértelmezett mechanizmusként. A műveleti csoport segítségével egyszer adhatja meg a műveleteket, majd a műveleti csoportot több riasztáshoz társíthatja az Azure-on keresztül. Anélkül, hogy szükség lenne rá, hogy ismételten deklarálja ugyanazokat a műveleteket újra és újra. A műveleti csoportok több műveletet is támogatnak – például e-maileket, SMS-t, hanghívást, ITSM, Automation Runbook, webhook URI-t és egyebeket. 

Azok a felhasználók, akik kibővítették a riasztásokat az Azure-ba, a riasztás létrehozásához a küszöbértékkel együtt át kell adni a műveleti csoport részleteit. Az e-mailek adatait, a webhook URL-jeit, a Runbook Automation részleteit és az egyéb műveleteket a riasztás létrehozása előtt először a műveleti csoportba kell definiálni. Létrehozhat [műveleti csoportot Azure monitor](./action-groups.md) a portálon, vagy használhatja a [műveleti csoport API](/rest/api/monitor/actiongroups)-t.

Ha a műveleti csoport társítását szeretné hozzáadni egy riasztáshoz, adja meg a műveleti csoport egyedi Azure Resource Manager AZONOSÍTÓját a riasztás definíciójában. Alább látható egy minta illusztráció:

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ]
   },
   "Severity": "critical",
   "Version": 1
}
```

A Put metódust egy egyedi műveleti AZONOSÍTÓval társíthatja, hogy egy már meglévő műveleti csoportot rendeljen hozzá egy ütemtervhez.  A következő példa a használati ábrát szemlélteti.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

A Put metódust meglévő műveleti AZONOSÍTÓval használva módosíthatja az ütemtervhez társított műveleti csoportokat.  A kérelem törzsének tartalmaznia kell a művelet ETAG.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

#### <a name="customize-actions"></a>Műveletek testreszabása
Alapértelmezés szerint a műveletek standard sablont és formátumot követve értesítések küldéséhez. A felhasználó azonban testre szabhatja bizonyos műveleteket, még akkor is, ha azokat műveleti csoportok vezérlik. Jelenleg az e-mailek tárgya és a webhook adattartalma is testreszabható.

##### <a name="customize-e-mail-subject-for-action-group"></a>A műveleti csoport E-Mail tárgyának testreszabása
Alapértelmezés szerint az e-mail-tárgy a riasztások esetében a riasztási értesítés `<AlertName>` a következőhöz: `<WorkspaceName>` . Ez azonban testreszabható, így konkrét szavakat vagy címkéket is használhat, így egyszerűen alkalmazhatja a beérkezett fájlok szűrési szabályait. Az e-mail-fejléc testreszabása részleteit a ActionGroup részleteivel együtt kell elküldeni, ahogy az alábbi példában is látható.

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ],
      "CustomEmailSubject": "Azure Alert fired"
   },
   "Severity": "critical",
   "Version": 1
}
```

A Put metódust egy egyedi műveleti AZONOSÍTÓval társíthatja, hogy a már meglévő műveleti csoportot egy ütemezett testreszabással társítsa.  A következő példa a használati ábrát szemlélteti.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

A Put metódust meglévő műveleti AZONOSÍTÓval használva módosíthatja az ütemtervhez társított műveleti csoportokat.  A kérelem törzsének tartalmaznia kell a művelet ETAG.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

##### <a name="customize-webhook-payload-for-action-group"></a>Webhook-tartalom testreszabása a műveleti csoport számára
Alapértelmezés szerint a log Analytics műveleti csoportján keresztül továbbított webhook rögzített struktúrával rendelkezik. A JSON-adattartalom azonban az egyes támogatott változók használatával testreszabható, hogy megfeleljen a webhook-végpont követelményeinek. További információ: [webhook művelet a naplózási riasztási szabályokhoz](./alerts-log-webhook.md). 

A webhook testreszabása részleteit a ActionGroup részleteivel együtt kell elküldeni, és a rendszer a műveleti csoporton belül megadott összes webhook URI-ra alkalmazza. mint az alábbi példában.

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ],
   "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
   "CustomEmailSubject": "Azure Alert fired"
   },
   "Severity": "critical",
   "Version": 1
},
```

A Put metódust egy egyedi műveleti AZONOSÍTÓval társíthatja, hogy a már meglévő műveleti csoportot egy ütemezett testreszabással társítsa.  A következő példa a használati ábrát szemlélteti.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

A Put metódust meglévő műveleti AZONOSÍTÓval használva módosíthatja az ütemtervhez társított műveleti csoportokat.  A kérelem törzsének tartalmaznia kell a művelet ETAG.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

## <a name="next-steps"></a>Következő lépések

* A [REST API használatával hajthat végre naplóbeli keresést](../log-query/log-query-overview.md) a log Analytics.
* További tudnivalók a [naplózási riasztásokról az Azure monitorban](./alerts-unified-log.md)
* A [naplók riasztási szabályainak létrehozása, szerkesztése és kezelése az Azure monitorban](./alerts-log.md)

