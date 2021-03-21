---
title: Szerepkörök, engedélyek és biztonság a Azure Monitorban
description: Megtudhatja, hogyan használhatja a Azure Monitor beépített szerepköreit és engedélyeit a figyelési erőforrásokhoz való hozzáférés korlátozására.
author: johnkemnetz
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: johnkem
ms.openlocfilehash: d0877059dca01bf01df7a4f2aeaa5114278ecc97
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040277"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Szerepkörök, engedélyek és biztonság a Azure Monitorban

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Számos csapatnak szigorúan szabályoznia kell a figyelési és a beállítási funkciókhoz való hozzáférést. Ha például vannak olyan csapattagok, akik kizárólag a figyelés (támogatási mérnökök, DevOps mérnökök) esetében működnek, vagy ha felügyelt szolgáltatót használ, érdemes hozzáférést biztosítani számukra, hogy csak a figyelési adatmennyiséget használják, miközben korlátozza az erőforrások létrehozását, módosítását és törlését. Ez a cikk bemutatja, hogyan alkalmazhat gyorsan egy beépített monitorozási Azure-szerepkört egy Azure-beli felhasználóra, vagy létrehozhat egy saját egyéni szerepkört egy korlátozott figyelési engedélyekkel rendelkező felhasználó számára. Ezután a Azure Monitor kapcsolódó erőforrásaival kapcsolatos biztonsági szempontokat tárgyalja, valamint arról, hogyan korlátozhatja a hozzáférését a bennük található adathoz.

## <a name="built-in-monitoring-roles"></a>Beépített monitorozási szerepkörök
A Azure Monitor beépített szerepkörei úgy vannak kialakítva, hogy segítsenek korlátozni az előfizetésben lévő erőforrásokhoz való hozzáférést, miközben továbbra is lehetővé teszik az infrastruktúra figyelését a szükséges információk beszerzéséhez és konfigurálásához. Azure Monitor két beépített szerepkört biztosít: egy figyelési olvasót és egy figyelő közreműködőt.

### <a name="monitoring-reader"></a>Figyelő olvasó
A figyelési olvasó szerepkörrel rendelkező személyek megtekinthetik az előfizetések összes figyelési adatát, de nem módosíthatják az erőforrásokat, és nem módosíthatják a figyelési erőforrásokkal kapcsolatos beállításokat. Ez a szerepkör olyan szervezet felhasználói számára megfelelő, mint például a support vagy az Operations Engineers, akiknek a következőket kell tudniuk:

* Megtekintheti a portálon a figyelési irányítópultokat, és saját privát figyelési irányítópultokat hozhat létre.
* Az [Azure-riasztásokban](alerts/alerts-overview.md) definiált riasztási szabályok megtekintése
* A metrikák lekérdezése a [Azure Monitor REST API](/rest/api/monitor/metrics), a [PowerShell-parancsmagok](powershell-samples.md)vagy a [platformfüggetlen CLI](cli-samples.md)használatával.
* A műveletnapló lekérdezése a portál, a Azure Monitor REST API, a PowerShell-parancsmagok vagy a platformfüggetlen CLI használatával.
* Egy erőforrás [diagnosztikai beállításainak](essentials/diagnostic-settings.md) megtekintése.
* Az előfizetéshez tartozó [napló profiljának](essentials/activity-log.md#legacy-collection-methods) megtekintése.
* Az autoskálázási beállítások megtekintése.
* Riasztási tevékenység és beállítások megtekintése.
* Hozzáférés Application Insights az adatvédelemhez és az adatmegjelenítés az AI Analyticsben.
* Keressen Log Analytics munkaterület adatait, beleértve a munkaterület használati adatait.
* Log Analytics felügyeleti csoportok megtekintése.
* A keresési séma lekérése Log Analytics munkaterületen.
* Figyelési csomagok listázása Log Analytics munkaterületen.
* Mentett keresések lekérése és végrehajtása Log Analytics munkaterületen.
* A Log Analytics munkaterület tárolási konfigurációjának beolvasása.

> [!NOTE]
> Ez a szerepkör nem ad olvasási hozzáférést az Event hub-ba továbbított vagy egy Storage-fiókban tárolt adatnaplózási adathoz. Az ehhez az erőforrásokhoz való hozzáférés konfigurálásával kapcsolatban [lásd alább](#security-considerations-for-monitoring-data) .
> 
> 

### <a name="monitoring-contributor"></a>Közreműködő figyelése
A figyelő közreműködő szerepkörrel rendelkező személyek megtekinthetik az előfizetések összes figyelési adatát, és létrehozhatják vagy módosíthatják a figyelési beállításokat, de nem módosíthatják más erőforrásokat. Ez a szerepkör a figyelési olvasó szerepkör egy kibővített változata, amely a szervezet figyelési csapatának vagy olyan felügyelt szolgáltatóknak a tagjai számára megfelelő, akik a fenti engedélyeken kívül a következőkre is szükségük van:

* Figyelési irányítópultok közzététele megosztott irányítópultként.
* Erőforrás [diagnosztikai beállításainak](essentials/diagnostic-settings.md) megadása.\*
* Adja meg egy előfizetés [naplózási profilját](essentials/activity-log.md#legacy-collection-methods) .\*
* Riasztási szabályok tevékenységének és beállításainak megadása az [Azure-riasztások](alerts/alerts-overview.md)használatával.
* Application Insights webes tesztek és összetevők létrehozása.
* Log Analytics munkaterület megosztott kulcsainak listázása.
* A figyelési csomagok engedélyezése vagy letiltása Log Analytics munkaterületen.
* Mentett keresések létrehozása és törlése és végrehajtása Log Analytics munkaterületen.
* Hozza létre és törölje a Log Analytics munkaterület tárolási konfigurációját.

\*a felhasználónak külön kell megadnia a Listkeys műveletének beolvasása engedélyt a cél erőforráson (Storage-fiók vagy Event hub-névtér) a naplózási profil vagy a diagnosztikai beállítás megadásához.

> [!NOTE]
> Ez a szerepkör nem ad olvasási hozzáférést az Event hub-ba továbbított vagy egy Storage-fiókban tárolt adatnaplózási adathoz. Az ehhez az erőforrásokhoz való hozzáférés konfigurálásával kapcsolatban [lásd alább](#security-considerations-for-monitoring-data) .
> 
> 

## <a name="monitoring-permissions-and-azure-custom-roles"></a>Figyelési engedélyek és egyéni Azure-szerepkörök
Ha a fenti beépített szerepkörök nem felelnek meg a csoport pontos igényeinek, létrehozhat egy részletesebb engedélyekkel rendelkező Azure-beli [Egyéni szerepkört](../role-based-access-control/custom-roles.md) is. Alább láthatók a közös Azure RBAC-műveletek, amelyekkel a leírásuk Azure Monitor.

| Művelet | Description |
| --- | --- |
| Microsoft. ininsights/ActionGroups/[olvasás, írás, törlés] |A műveleti csoportok olvasása/írása/törlése. |
| Microsoft. ininsights/ActivityLogAlerts/[olvasás, írás, törlés] |Olvasási/írási/törlési műveletnapló riasztásai. |
| Microsoft. ininsights/AlertRules/[olvasás, írás, törlés] |Riasztási szabályok olvasása/írása/törlése (a klasszikus riasztásokból). |
| Microsoft. ininsights/AlertRules/incidensek/olvasás |Az incidensek listázása (riasztási szabály előzményei) a riasztási szabályokhoz. Ez csak a portálra vonatkozik. |
| Microsoft. ininsights/AutoscaleSettings/[olvasás, írás, törlés] |Az autoskálázási beállítások olvasása/írása/törlése. |
| Microsoft. ininsights/DiagnosticSettings/[olvasás, írás, törlés] |Diagnosztikai beállítások olvasása/írása/törlése. |
| Microsoft. bepillantások/EventCategories/olvasás |A tevékenység naplójában lehetséges összes kategória enumerálása. A Azure Portal használja. |
| Microsoft. bepillantások/eventtypes/digestevents/olvasás |Ez az engedély olyan felhasználók számára szükséges, akiknek a portálon keresztül kell hozzáférnie a tevékenység naplóihoz. |
| Microsoft. bepillantások/eventtypes/értékek/olvasás |Az előfizetésben szereplő tevékenység-naplózási események (kezelési események) listázása. Ez az engedély mind a programozási, mind a portálhoz való hozzáférésre alkalmazható a tevékenység naplójában. |
| Microsoft. ininsights/ExtendedDiagnosticSettings/[olvasás, írás, törlés] | Hálózati folyamatok naplóihoz tartozó diagnosztikai beállítások olvasása/írása/törlése. |
| Microsoft. bepillantások/LogDefinitions/olvasás |Ez az engedély olyan felhasználók számára szükséges, akiknek a portálon keresztül kell hozzáférnie a tevékenység naplóihoz. |
| Microsoft. ininsights/LogProfiles/[olvasás, írás, törlés] |Olvasási/írási/törlési napló profiljai (adatfolyam-tevékenységek naplója az Event hub vagy a Storage-fiók számára). |
| Microsoft. ininsights/MetricAlerts/[olvasás, írás, törlés] |Olvasási/írási/törlési közel valós idejű metrikai riasztások |
| Microsoft. bepillantások/MetricDefinitions/olvasás |A metrikai definíciók (az adott erőforráshoz elérhető metrikai típusok listája) olvasása. |
| Microsoft. bepillantások/mérőszámok/olvasás |Erőforrás metrikáinak olvasása. |
| Microsoft. elemzések/regisztráció/művelet |Regisztrálja a Azure Monitor erőforrás-szolgáltatót. |
| Microsoft. ininsights/ScheduledQueryRules/[olvasás, írás, törlés] |Olvasási/írási és törlési naplózási riasztások a Azure Monitorban. |



> [!NOTE]
> Az erőforrások riasztásokhoz, diagnosztikai beállításokhoz és metrikához való hozzáféréséhez a felhasználónak olvasási hozzáférésre van szüksége az erőforrás típusához és hatóköréhez. A Storage-fiókhoz vagy az esemény-hubokhoz tartozó streamekhez archivált diagnosztikai beállítások vagy log-profilok létrehozásakor a felhasználónak Listkeys műveletének beolvasása engedéllyel is rendelkeznie kell a cél erőforráson.
> 
> 

A fenti táblázat használatával például létrehozhat egy Azure-beli egyéni szerepkört a "Tevékenységnaplók olvasója" számára, a következőhöz hasonló módon:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Biztonsági szempontok az adatok monitorozásához
A figyelési adatok – különösen a naplófájlok – bizalmas adatokat (például IP-címeket vagy felhasználóneveket) tartalmazhatnak. Az Azure-beli monitorozási adatok három alapvető formában jelennek meg:

1. A tevékenység naplója, amely az Azure-előfizetésen belüli összes vezérlési sík műveletet ismerteti.
2. erőforrás-naplók, amelyek egy erőforrás által kibocsátott naplók.
3. Az erőforrások által kibocsátott mérőszámok.

Mindhárom adattípust tárolhatja egy Storage-fiókban, vagy továbbíthatja az Event hub-ba, mindkettő általános célú Azure-erőforrás. Mivel ezek az általános célú erőforrások, a létrehozás, a törlés és az azokhoz való hozzáférés egy rendszergazda számára fenntartott privilegizált művelet. Javasoljuk, hogy a visszaélések megelőzése érdekében kövesse az alábbi eljárásokat a figyeléssel kapcsolatos erőforrásokhoz:

* Egyetlen dedikált Storage-fiókot használjon az adatfigyeléshez. Ha több Storage-fiókba kell elkülönítenie a figyelési adatokat, soha ne ossza meg a Storage-fiók használatát a figyelési és a nem megfigyelési adatok között, mivel ez véletlenül megadhatja, hogy csak a figyelési adatokhoz (például egy harmadik féltől származó SIEM-hez) való hozzáférésre van szükség a nem megfigyelési adatokhoz.
* Egyetlen, dedikált Service Bus vagy Event hub-névteret használhat az összes diagnosztikai beállításban, a fentiekhez hasonló okból.
* Korlátozza a hozzáférést a figyeléshez kapcsolódó Storage-fiókokhoz vagy az Event hubokhoz azáltal, hogy egy külön erőforráscsoporthoz tartja őket, és a figyelési szerepkörök [hatókörének használatával](../role-based-access-control/overview.md#scope) korlátozza a hozzáférést csak az adott erőforráscsoporthoz.
* Soha ne adja meg a Listkeys műveletének beolvasása engedélyt a Storage-fiókok vagy az Event hubok számára az előfizetés hatókörében, ha a felhasználónak csak a figyelési adathoz kell hozzáférést biztosítania. Ehelyett adja meg ezeket az engedélyeket a felhasználónak egy erőforrás vagy erőforráscsoport számára (ha van egy dedikált figyelési erőforráscsoport) hatóköre.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>A figyeléssel kapcsolatos Storage-fiókokhoz való hozzáférés korlátozása
Ha egy felhasználónak vagy alkalmazásnak hozzá kell férnie egy Storage-fiókban lévő figyelési információhoz, olyan [fiókot kell előállítania](/rest/api/storageservices/create-account-sas) a Storage-fiókhoz, amely a blob Storage-hoz csak olvasási hozzáféréssel rendelkező figyelési adathozzáférést tartalmaz. A PowerShellben ez a következőhöz hasonló lehet:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Ezután megadhatja a jogkivonatot az adott Storage-fiókból beolvasni kívánt entitásnak, valamint listázhatja és beolvashatja az adott Storage-fiókban lévő összes blobot.

Ha ezt az engedélyt az Azure RBAC-mel kell vezérelni, az adott tárolási fiókra vonatkozóan a Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/Action engedélyt is megadhatja. Ez olyan felhasználók számára szükséges, akiknek be kell tudniuk állítani a diagnosztikai beállításokat vagy a naplózási profilt egy Storage-fiókba való archiváláshoz. Létrehozhat például egy olyan felhasználóhoz vagy alkalmazáshoz a következő egyéni Azure-szerepkört, amelynek csak egy Storage-fiókból kell beolvasnia:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzRoleDefinition -Role $role 
```

> [!WARNING]
> A Listkeys műveletének beolvasása engedély lehetővé teszi a felhasználó számára az elsődleges és másodlagos Storage-fiókok kulcsának listázását. Ezek a kulcsok biztosítják a felhasználónak az összes aláírt engedélyt (olvasás, írás, blobok, blobok törlése stb.) minden aláírt szolgáltatásban (blob, üzenetsor, tábla, fájl) az adott Storage-fiókban. Azt javasoljuk, hogy ha lehetséges, használjon a fent ismertetett fiók SAS-t.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>A figyeléssel kapcsolatos Event hubok hozzáférésének korlátozása
Egy hasonló minta követhető az Event hubokban, de először létre kell hoznia egy dedikált figyelési engedélyezési szabályt. Ha meg szeretne adni egy olyan alkalmazáshoz való hozzáférést, amelynek csak figyelnie kell a figyeléssel kapcsolatos esemény-hubhoz, tegye a következőket:

1. Hozzon létre egy megosztott hozzáférési szabályzatot azon az Event hub (ok) ban, amely a figyelési adattovábbításra lett létrehozva, csak a figyelési jogcímeket. Ezt a portálon teheti meg. Előfordulhat például, hogy "monitoringReadOnly"-t hív meg. Ha lehetséges, közvetlenül a fogyasztónak kell megadnia a kulcsot, és ki kell hagynia a következő lépést.
2. Ha a fogyasztónak képesnek kell lennie az ad hoc kulcs lekérésére, a felhasználónak a Listkeys műveletének beolvasása műveletet kell megadnia az adott Event hub számára. Ez olyan felhasználók számára is szükséges, akiknek be kell tudniuk állítani a diagnosztikai beállításokat vagy a naplózási profilt az Event hubokba való továbbításhoz. Létrehozhat például egy Azure RBAC-szabályt:
   
   ```powershell
   $role = Get-AzRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>Monitorozás biztonságos virtuális hálózaton belül

Azure Monitor hozzáférést kell biztosítania az Azure-erőforrásokhoz az Ön által engedélyezett szolgáltatások biztosításához. Ha szeretné figyelni az Azure-erőforrásokat, miközben továbbra is biztonságossá teszi őket a nyilvános internethez való hozzáféréstől, akkor a következő beállításokat engedélyezheti.

### <a name="secured-storage-accounts"></a>Biztonságos Storage-fiókok 

A figyelési adatgyűjtést gyakran egy Storage-fiókba kell írni. Előfordulhat, hogy meg kell győződnie arról, hogy a Storage-fiókba másolt adatfájlok jogosulatlan felhasználók számára nem érhetők el. A további biztonság érdekében zárolhatja a hálózati hozzáférést, hogy csak a jogosult erőforrások és a megbízható Microsoft-szolgáltatások férhessenek hozzá egy Storage-fiókhoz, ha a Storage-fiókot a "kiválasztott hálózatok" használatára korlátozza.
![Az Azure Storage-beállítások párbeszédpanel ](./media/roles-permissions-security/secured-storage-example.png) Azure monitor a "megbízható Microsoft-szolgáltatások" közé tartozik, ha engedélyezi a megbízható Microsoft-szolgáltatások számára a biztonságos tároló elérését, az Azure monitor hozzáférhet a biztonságos Storage-fiókhoz, amely lehetővé teszi a Azure monitor erőforrás-naplók, a műveletnapló és a metrikák írását a Storage-fiókba a következő védett feltételek szerint. Ez azt is lehetővé teszi, hogy a Log Analytics beolvassák a biztonságos tárolóból származó naplókat.   


További információ: [hálózati biztonság és Azure Storage](../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Következő lépések
* [További információ az Azure RBAC és a Resource Manager engedélyeiről](../role-based-access-control/overview.md)
* [A monitorozás áttekintése az Azure-ban](overview.md)

