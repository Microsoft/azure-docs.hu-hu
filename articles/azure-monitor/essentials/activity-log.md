---
title: Azure-tevékenységnapló
description: Tekintse meg az Azure-tevékenység naplóját, és küldje el Azure Monitor naplókba, Azure Event Hubsba és Azure Storage-ba.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 200c4c536df4a3e32b59945ae4ad97d7b770f269
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613413"
---
# <a name="azure-activity-log"></a>Azure-tevékenységnapló
A Tevékenységnapló az Azure előfizetési szintű eseményekkel kapcsolatos megállapításokat biztosító [platformnaplója](../platform/platform-logs-overview.md). Olyan információkat tartalmaz, mint az erőforrások módosításának vagy a virtuális gépek indításának az időpontja. Megtekintheti a tevékenység naplóját a Azure Portal vagy beolvashatja a bejegyzéseket a PowerShell és a parancssori felület használatával. További funkciókért hozzon létre egy diagnosztikai beállítást, amely elküldi a tevékenység naplóját [Azure monitor naplókba](../platform/data-platform-logs.md), az Azure Event Hubs az Azure-on kívülre vagy az Azure Storage-ba az archiváláshoz. Ez a cikk részletesen ismerteti a tevékenység naplójának megtekintését és a különböző célhelyekre való küldését.

A diagnosztikai beállítások létrehozásával kapcsolatos részletekért tekintse meg a [diagnosztikai beállítások létrehozása a platform naplófájljainak és metrikáinak a különböző célhelyekre való küldéséhez](../platform/diagnostic-settings.md) című témakört.

> [!NOTE]
> A Tevékenységnapló bejegyzéseit a rendszer hozza létre, és nem módosíthatók vagy törölhetők.

## <a name="view-the-activity-log"></a>A műveletnapló megtekintése
A Tevékenységnaplót az Azure Portal legtöbb menüjéből elérheti. A megnyitásához használt menü határozza meg a napló kezdeti szűrőjét. Ha a **figyelés** menüből nyitja meg, akkor az egyetlen szűrő lesz az előfizetésben. Ha egy erőforrás menüjéből nyitja meg, akkor a szűrő erre az erőforrásra lesz beállítva. A szűrőt bármikor módosíthatja, ha az összes többi bejegyzést meg szeretné tekinteni. Kattintson a **szűrő hozzáadása** lehetőségre további tulajdonságok hozzáadásához a szűrőhöz.

![Műveletnapló megtekintése](./media/activity-log/view-activity-log.png)

A műveletnapló-kategóriák leírását lásd: az [Azure Activity log esemény sémája](activity-log-schema.md#categories).

### <a name="view-change-history"></a>Változási előzmények megtekintése

Egyes eseményeknél megtekintheti a változási előzményeket, amelyek azt mutatják, hogy milyen változások történtek az adott esemény ideje alatt. Válasszon ki egy olyan eseményt a tevékenységi naplóból, amelyet mélyebbre szeretne keresni. Az eseményhez kapcsolódó módosítások megtekintéséhez válassza a **change History (előzetes verzió)** lapot.

![Eseményhez tartozó előzmények listájának módosítása](media/activity-log/change-history-event.png)

Ha az eseményhez kapcsolódó változások vannak, akkor a kiválasztott módosítások listája látható. Ekkor megnyílik a **change History (előzetes verzió)** oldal. Ezen a lapon láthatja az erőforrás módosításait. A következő példában nem csak azt láthatja, hogy a virtuális gép mérete módosult, de az előző virtuális gép mérete korábbi volt a változás előtt, és hogy mi változott. További információ a változási előzményekről: [erőforrás-változások beolvasása](../../governance/resource-graph/how-to/get-resource-changes.md).

![Változások az előzmények között oldalon látható különbségek](media/activity-log/change-history-event-details.png)


### <a name="other-methods-to-retrieve-activity-log-events"></a>A műveletnapló eseményeinek beolvasására szolgáló egyéb metódusok
A tevékenység-naplózási eseményeket a következő módszerekkel érheti el.

- A [Get-AzLog](/powershell/module/az.monitor/get-azlog) parancsmaggal kérheti le a tevékenység naplóját a powershellből. Lásd: [Azure monitor PowerShell-minták](../samples/powershell-samples.md#retrieve-activity-log).
- Az az [monitor Activity-log](/cli/azure/monitor/activity-log) paranccsal kérheti le a tevékenység naplóját a parancssori felületről.  Lásd: [Azure monitor CLI-minták](../samples/cli-samples.md#view-activity-log).
- A [Azure Monitor REST API](/rest/api/monitor/) használatával kérheti le a tevékenység naplóját egy Rest-ügyfélről. 


## <a name="send-to-log-analytics-workspace"></a>Küldés a Log Analytics-munkaterületre
 Küldje el a tevékenység naplóját egy Log Analytics munkaterületre, és engedélyezze a [Azure monitor naplók](../platform/data-platform-logs.md) funkcióit, amelyek a következőket tartalmazzák:

- A tevékenység-naplózási adatok korrelációja Azure Monitor által gyűjtött egyéb megfigyelési adatokkal.
- Több Azure-előfizetésből és-bérlőből származó naplóbejegyzések konszolidálása egyetlen helyre az elemzéshez.
- A naplók használatával összetett elemzéseket végezhet, és részletes elemzéseket készíthet a tevékenységi naplók bejegyzéseiről.
- A naplózási riasztásokat olyan tevékenységi bejegyzésekkel használhatja, amelyek összetettebb riasztási logikát tesznek lehetővé.
- 90 napnál hosszabb ideig tárolhatja a tevékenység naplójának bejegyzéseit.
- A Log Analytics munkaterületen tárolt műveletnapló-adatokra vonatkozóan nem történik adatfeldolgozási díj.
- Log Analytics munkaterületen tárolt műveletnapló-adatok esetében nem számítunk fel adatmegőrzési díjat, amíg 90 nap.

[Hozzon létre egy diagnosztikai beállítást](../platform/diagnostic-settings.md) a műveletnapló log Analytics munkaterületre való elküldéséhez. A tevékenység naplóját bármely előfizetésből akár öt munkaterületre is elküldheti. A több bérlőt érintő naplógyűjtéshez az [Azure Lighthouse](../../lighthouse/index.yml) szükséges.

A Log Analytics munkaterületen található műveletnapló-adattábla egy *AzureActivity* nevű táblázatban található, amelyet a [log Analytics](../log-query/log-analytics-tutorial.md) [napló lekérdezésével](../log-query/log-query-overview.md) lehet lekérni. A tábla szerkezete a [naplóbejegyzés kategóriájára](activity-log-schema.md)függően változik. A táblázat tulajdonságainak leírását a [Azure monitor adathivatkozás](/azure/azure-monitor/reference/tables/azureactivity)című részben tekintheti meg.

Ha például meg szeretné tekinteni az egyes kategóriákhoz tartozó műveletnapló-rekordok számát, használja a következő lekérdezést.

```kusto
AzureActivity
| summarize count() by CategoryValue
```

A felügyeleti kategória összes rekordjának lekéréséhez használja a következő lekérdezést.

```kusto
AzureActivity
| where CategoryValue == "Administrative"
```


## <a name="send-to-azure-event-hubs"></a>Küldés az Azure Event Hubsba
Küldje el a műveletnapló az Azure Event Hubsba az Azure-on kívüli bejegyzések küldéséhez, például egy harmadik féltől származó SIEM vagy más log Analytics-megoldáshoz. Az Event hubokból származó műveletnapló eseményei JSON formátumban vannak felhasználva, és `records` az egyes adattartalomban lévő rekordokat tartalmazó elemmel rendelkeznek. A séma a (z) kategóriától függ, és a [Storage-fiók és az Event hubok sémája](activity-log-schema.md)című témakörben található.

A következő példa kimeneti adatokat Event Hubs egy tevékenység naplójában:

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


## <a name="send-to--azure-storage"></a>Küldés az Azure Storage-ba
Küldje el a tevékenység naplóját egy Azure Storage-fiókba, ha a naplózási, statikus vagy biztonsági mentéshez 90 napnál hosszabb ideig szeretné megőrizni a napló adatait. Ha csak 90 napig vagy kevesebb ideig kell megőriznie az eseményeket, nincs szükség a Storage-fiók archiválására, mivel a tevékenység-naplózási események az Azure-platformon maradnak meg a 90 napig.

Amikor az Azure-ba küldi a tevékenység naplóját, a rendszer egy tárolót hoz létre a Storage-fiókban, amint az esemény bekövetkezik. A tárolóban lévő Blobok a következő elnevezési konvenciót használják:

```
insights-activity-logs/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Egy adott blob például a következőhöz hasonló névvel rendelkezhet:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/y=2020/m=06/d=08/h=18/m=00/PT1H.json
```

Mindegyik PT1H.json blob tartalmazza a blob URL-jében meghatározott órában (például h=12) bekövetkezett események JSON-blobját. Az aktuális órában az események az előfordulásukkor lesznek a PT1H.json fájlhoz fűzve. A perc értéke (m = 00) mindig 00, mivel az erőforrás-naplózási események óránként egyedi blobokra vannak bontva.

Az egyes eseményeket a rendszer a fájl PT1H.jstárolja, és a következő formátumot használja, amely közös legfelső szintű sémát használ, de egyébként egyedi az egyes kategóriákhoz a  [tevékenység naplójának sémája](activity-log-schema.md)szerint.

``` JSON
{ "time": "2020-06-12T13:07:46.766Z", "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MV-VM-01", "correlationId": "0f0cb6b4-804b-4129-b893-70aeeb63997e", "operationName": "Microsoft.Resourcehealth/healthevent/Updated/action", "level": "Information", "resultType": "Updated", "category": "ResourceHealth", "properties": {"eventCategory":"ResourceHealth","eventProperties":{"title":"This virtual machine is starting as requested by an authorized user or process. It will be online shortly.","details":"VirtualMachineStartInitiatedByControlPlane","currentHealthStatus":"Unknown","previousHealthStatus":"Unknown","type":"Downtime","cause":"UserInitiated"}}}
```


## <a name="legacy-collection-methods"></a>Örökölt gyűjtési módszerek
Ez a szakasz a diagnosztikai beállítások előtt használt műveletnapló összegyűjtésének régi módszereit ismerteti. Ha ezeket a módszereket használja, érdemes áttérnie a diagnosztikai beállításokra, amelyek jobb funkciókat és az erőforrás-naplók konzisztenciáját biztosítják.

### <a name="log-profiles"></a>Log-profilok
A log profilok a tevékenység naplójának az Azure Storage-ba vagy az Event hubokba való küldésére szolgáló örökölt módszer. A következő eljárással folytathatja a munkát a napló profiljával, vagy letilthatja azt a diagnosztikai beállításokra való Migrálás előkészítése során.

1. A Azure Portal **Azure monitor** menüjében válassza a **műveletnapló** elemet.
3. Kattintson a **Diagnosztikai beállítások** elemre.

   ![Diagnosztikai beállítások](media/activity-log/diagnostic-settings.png)

4. A régi élményhez kattintson a lila szalagcímre.

    ![Korábbi élmény](media/activity-log/legacy-experience.png)



### <a name="configure-log-profile-using-powershell"></a>A log profil konfigurálása a PowerShell használatával

Ha már létezik egy bejelentkezési profil, először el kell távolítania a meglévő log-profilt, majd létre kell hoznia egy újat.

1. `Get-AzLogProfile`A használatával azonosíthatja, hogy létezik-e egy log-profil.  Ha egy log-profil létezik, jegyezze fel a *Name (név* ) tulajdonságot.

1. A használatával `Remove-AzLogProfile` távolítsa el a napló profilt a Name ( *név* ) tulajdonság értéke alapján.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. `Add-AzLogProfile`Új naplózási profil létrehozásához használja a következőt:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Tulajdonság | Kötelező | Leírás |
    | --- | --- | --- |
    | Név |Yes |A napló profiljának neve. |
    | StorageAccountId |No |Azon Storage-fiók erőforrás-azonosítója, amelybe menteni kell a tevékenység naplóját. |
    | serviceBusRuleId |No |Service Bus a Service Bus névtérhez tartozó szabály AZONOSÍTÓját, amelybe az Event hub-t létre szeretné hozni. Ez a következő formátumú karakterlánc: `{service bus resource ID}/authorizationrules/{key name}` . |
    | Hely |Igen |Azoknak a régióknak a vesszővel tagolt listája, amelyeknek a tevékenység-naplózási eseményeket össze szeretné gyűjteni. |
    | RetentionInDays |Yes |Ennyi nap elteltével kell megőrizni az eseményeket a Storage-fiókban 1 és 365 között. A nulla érték határozatlan ideig tárolja a naplókat. |
    | Kategória |No |Az összegyűjteni kívánt események kategóriáinak vesszővel tagolt listája. A lehetséges értékek a következők: _írás_, _Törlés_ és _művelet_. |

### <a name="example-script"></a>Példaszkript
A következő példa egy PowerShell-szkriptet hoz létre egy olyan log-profil létrehozásához, amely a tevékenység naplóját a Storage-fiókra és az Event hub-ra írja.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -StorageAccountId  $storageAccountId -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>A log profil konfigurálása az Azure CLI használatával

Ha már létezik egy naplózási profil, először el kell távolítania a meglévő log-profilt, majd létre kell hoznia egy új napló-profilt.

1. `az monitor log-profiles list`A használatával azonosíthatja, hogy létezik-e egy log-profil.
2. A használatával `az monitor log-profiles delete --name "<log profile name>` távolítsa el a napló profilt a Name ( *név* ) tulajdonság értéke alapján.
3. `az monitor log-profiles create`Új naplózási profil létrehozásához használja a következőt:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Tulajdonság | Kötelező | Leírás |
    | --- | --- | --- |
    | name |Yes |A napló profiljának neve. |
    | Storage-Account-ID |Yes |Azon Storage-fiók erőforrás-azonosítója, amelybe menteni szeretné a tevékenység naplóit. |
    | helyek |Yes |Szóközzel tagolt lista azoknak a régióknak a listájához, amelyeknek a tevékenység-naplózási eseményeket össze szeretné gyűjteni. Az előfizetéshez tartozó összes régió listáját megtekintheti a használatával `az account list-locations --query [].name` . |
    | nap |Yes |Azon napok száma, amelyekhez meg kell őrizni az eseményeket 1 és 365 között. A nulla érték a naplókat határozatlan ideig (Forever) tárolja.  Ha nulla, akkor az engedélyezett paramétert false értékre kell állítani. |
    |engedélyezve | Yes |Igaz vagy hamis?  Az adatmegőrzési szabály engedélyezésére vagy letiltására szolgál.  Ha az értéke igaz, akkor a Days paraméternek 0-nál nagyobbnak kell lennie.
    | kategóriák |Yes |Az összegyűjteni kívánt események kategóriáinak szóközzel tagolt listája. A lehetséges értékek a következők: írás, törlés és művelet. |


### <a name="log-analytics-workspace"></a>Log Analytics-munkaterület
A tevékenység naplójának a Log Analytics munkaterületre való küldésének örökölt módja a munkaterület konfigurációjában található napló csatlakoztatása. 

1. A Azure Portal **log Analytics munkaterületek** menüjében válassza ki a munkaterületet a tevékenység naplójának összegyűjtéséhez.
1. A munkaterület menü **munkaterület adatforrásai** területén válassza az **Azure-tevékenység napló** elemet.
1. Kattintson arra az előfizetésre, amelyhez csatlakozni szeretne.

    ![A képernyőképen Log Analytics munkaterület van kiválasztva egy Azure-tevékenység naplójában.](media/activity-log/workspaces.png)

2. Kattintson a **Kapcsolódás** lehetőségre a tevékenység naplójának a kiválasztott munkaterülethez való összekapcsolásához. Ha az előfizetés már egy másik munkaterülethez van csatlakoztatva, kattintson az első **Leválasztás** elemre a leválasztáshoz.

    ![Munkaterületek összekapcsolása](media/activity-log/connect-workspace.png)


A beállítás letiltásához hajtsa végre ugyanezt az eljárást, és kattintson a **Leválasztás** elemre az előfizetés munkaterületről való eltávolításához.

### <a name="data-structure-changes"></a>Adatstruktúra-változások
A diagnosztikai beállítások ugyanazokat az adatfájlokat küldik el, mint a *AzureActivity* tábla struktúrájának módosításaival a tevékenység naplójának elküldéséhez használt örökölt metódus.

A következő táblázat oszlopai elavultak a frissített sémában. Továbbra is léteznek a *AzureActivity* -ben, de nem lesznek elérhetők. Ezeknek az oszlopoknak a pótlása nem új, de ugyanazokat az adattípusokat tartalmazzák, mint az elavult oszlop. Ezek eltérő formátumúak, ezért előfordulhat, hogy módosítania kell azokat a naplózási lekérdezéseket, amelyek használják azokat. 

| Elavult oszlop | Helyettesítő oszlop |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| Kategória          | CategoryValue          |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> Bizonyos esetekben az ezekben az oszlopokban lévő értékek csupa nagybetűvel jelenhetnek meg. Ha ezeket az oszlopokat tartalmazó lekérdezése van, használja az [=~ operátort](/azure/kusto/query/datatypes-string-operators) a kis- és nagybetűket nem megkülönböztető összehasonlításhoz.

A következő oszlop lett hozzáadva a *AzureActivity* -hez a frissített sémában:

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-analytics-monitoring-solution"></a>Activity Log Analytics figyelési megoldás
Az Azure Log Analytics-figyelési megoldás hamarosan elavult, és a Log Analytics munkaterület frissített sémájával helyettesíti a munkafüzetet. Továbbra is használhatja a megoldást, ha már engedélyezve van, de csak akkor használható, ha örökölt beállítások használatával gyűjti a tevékenység naplóját. 



### <a name="use-the-solution"></a>A megoldás használata
A figyelési megoldások a Azure Portal **figyelő** menüjében érhetők el. Válassza a **továbbiak** lehetőséget a **betekintési** szakaszban, hogy megnyissa az **Áttekintés** lapot a megoldás csempével. Az **Azure-tevékenység naplói** csempén a munkaterületen található **AzureActivity** -rekordok száma látható.

![Az Azure-tevékenység naplói csempéje](media/activity-log/azure-activity-logs-tile.png)


Kattintson az **Azure-tevékenységek naplói** csempére az **Azure-tevékenység naplói** nézetének megnyitásához. A nézet a következő táblázatban szereplő vizualizációs részeket tartalmazza. Mindegyik rész legfeljebb 10 olyan elemet sorol fel, amelyek megfelelnek a megadott időtartományra vonatkozó feltételeknek. Futtathat egy olyan naplózási lekérdezést, amely az összes egyező rekordot visszaadja, ha az **összes megtekintése** elemre kattint a rész alján.

![Azure Activity-naplók irányítópultja](media/activity-log/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>A megoldás engedélyezése új előfizetésekhez
Hamarosan többé nem fogja tudni felvenni a tevékenység naplóinak elemzési megoldását az előfizetésbe a Azure Portal használatával. A következő eljárással adhat hozzá egy Resource Manager-sablon használatával. 

1. Másolja a következő JSON-t egy *ActivityLogTemplate*. JSON nevű fájlba.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Telepítse a sablont a következő PowerShell-parancsok használatával:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```



## <a name="next-steps"></a>Következő lépések

* [A platform naplófájljainak áttekintése](../platform/platform-logs-overview.md)
* [A műveletnapló esemény sémájának áttekintése](activity-log-schema.md)
* [Diagnosztikai beállítás létrehozása a tevékenységek naplófájljainak más célhelyekre való küldéséhez](../platform/diagnostic-settings.md)
