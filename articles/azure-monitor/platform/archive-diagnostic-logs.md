---
title: Az Azure diagnosztikai naplóinak archiválása
description: Ismerje meg, a hosszú távú adatmegőrzés storage-fiókban lévő Azure diagnosztikai naplóinak archiválása.
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: 8ab8a0bcf0c2c00515e46f3e2bbdb55b42ff7a2a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071537"
---
# <a name="archive-azure-diagnostic-logs"></a>Az Azure diagnosztikai naplóinak archiválása

Ebben a cikkben bemutatjuk a használatáról az Azure portal, PowerShell-parancsmagok, CLI vagy REST API-val archiválása a [Azure diagnosztikai naplók](diagnostic-logs-overview.md) a storage-fiókban. Ez a beállítás akkor hasznos, ha szeretné megőrizni a diagnosztikai naplók egy opcionális adatmegőrzési szabályzat naplózási, elemzési statikus vagy biztonsági mentés. A storage-fiók nem rendelkezik és a naplókat kibocsátó mindaddig, amíg a beállítást konfiguráló felhasználónak megfelelő hozzáférése RBAC mindkét előfizetéshez erőforrásnak ugyanabban az előfizetésben lennie.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, kell [hozzon létre egy tárfiókot](../../storage/common/storage-quickstart-create-account.md) , amelyhez a diagnosztikai naplókat archiválhatja. Kifejezetten ajánljuk, hogy nem használja egy meglévő tárfiókot, amely a benne tárolt, így jobban szabályozhatja a hozzáférést a figyelési adatok más, nem figyelési adatokat tartalmaz. Azonban ha is archiválni a tevékenységnapló és a egy tárfiókot a diagnosztikai metrikák, célszerű felhasználójának, hogy az összes monitorozási adat egy központi helyen, hogy a tárfiókot a diagnosztikai naplók használata.

## <a name="diagnostic-settings"></a>Diagnosztikai beállítások

Az alábbi módszerek bármelyikével a diagnosztikai naplók archiválása, beállíthat egy **diagnosztikai beállítás** egy adott erőforráshoz. Egy erőforrás diagnosztikai beállításának meghatározza a naplók és metrikaadatok küldeni a célhelyre (storage-fiók, az Event Hubs-névtér vagy Log Analytics-munkaterület). Események az egyes naplókategória és a mérőszám, a storage-fiókban tárolt adatok az adatmegőrzési (Ha a napok száma) is meghatározza. Adatmegőrzési értéke nulla, a napló kategória események tárolása történik határozatlan idejű (azaz, végtelen). Adatmegőrzési más módon lehet minden olyan 1 és 365 közötti napok számát. [További információ itt a diagnosztikai beállítások](../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings). Adatmegőrzési házirendek, az alkalmazott napi, hogy naponta (UTC), naplók, amely mostantól a megőrzési ideje meghaladja a nap végén a rendszer törli a szabályzatot. Például ha egy nap adatmegőrzési, ma a nap kezdetén az a napja előtt tegnap naplóinak törlődnének. A törlési folyamat kezdődik UTC szerint éjfélig, de vegye figyelembe, hogy a naplók a tárfiókból a törlendő akár 24 órát is igénybe vehet. 

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Az Eseményközpont "Bejövő üzenetek" metrikája fel és ábrázolható egy üzenetsoronként. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
>

## <a name="archive-diagnostic-logs-using-the-portal"></a>Archív diagnosztikai naplók a portál használatával

1. A portálon lépjen az Azure Monitor, és kattintson a **diagnosztikai beállítások**

    ![Az Azure Monitor figyelési szakasza](media/archive-diagnostic-logs/diagnostic-settings-blade.png)

2. Igény szerint szűrje a listát, erőforráscsoport vagy erőforrás típusa, majd kattintson az a erőforrás, amelynek szeretné egy diagnosztikai beállítás.

3. Ha a beállítások nem létezik az erőforráson kiválasztott, kéri létre beállítást. Kattintson a "Engedélyezze a diagnosztikát."

   ![Diagnosztikai beállítás - beállítások nélkül hozzáadása](media/archive-diagnostic-logs/diagnostic-settings-none.png)

   Ha meglévő beállításokat az erőforráson, látni fogja a beállítások már konfigurálva van a ehhez az erőforráshoz. Kattintson a "Diagnosztikai beállítás hozzáadása."

   ![Diagnosztikai beállítás - meglévő beállítások hozzáadása](media/archive-diagnostic-logs/diagnostic-settings-multiple.png)

3. Adja meg a beállítás a neve, és jelölje be a **exportálás Tárfiókba**, majd válassza ki a tárfiókot. Beállíthatja a megőrzi ezeket a naplókat a napok számát a **megőrzés (nap)** csúszkák. Egy nulla napnyi adatmegőrzéshez határozatlan ideig tárolja a naplókat.

   ![Diagnosztikai beállítás - meglévő beállítások hozzáadása](media/archive-diagnostic-logs/diagnostic-settings-configure.png)

4. Kattintson a **Save** (Mentés) gombra.

Néhány pillanat múlva az új beállítás jelenik meg az ehhez az erőforráshoz beállítások listáját, és a diagnosztikai naplók archivált tárfiók, amint új esemény adat keletkezik.

## <a name="archive-diagnostic-logs-via-azure-powershell"></a>Archív diagnosztikai naplók az Azure PowerShell-lel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Category networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| ResourceId |Igen |Erőforrás-Azonosítóját, amelyen a diagnosztikai beállítás kívánt erőforrás. |
| StorageAccountId |Nem |Erőforrás-Azonosítóját a Tárfiókot a diagnosztikai naplók mentéséhez. |
| Category |Nem |Naplókategóriák engedélyezéséhez vesszővel tagolt listája. |
| Enabled |Igen |Logikai érték, amely azt jelzi, hogy diagnosztika engedélyezve van vagy le van tiltva az adott erőforráshoz. |
| RetentionEnabled |Nem |Logikai érték, amely azt jelzi, hogy engedélyezve vannak-e adatmegőrzési az erőforráson. |
| RetentionInDays |Nem |Amely eseményeket kell megtartani 1 és 365 közötti napok számát. A nulla érték határozatlan ideig tárolja a naplókat. |

## <a name="archive-diagnostic-logs-via-the-azure-cli"></a>Archív diagnosztikai naplók az Azure CLI-n keresztül

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Adhat hozzá további kategóriát a diagnosztikai napló szótárak ad hozzá a JSON-tömböt adhatók be a `--logs` paraméter.

A `--resource-group` argumentum csak akkor kötelező, ha `--storage-account` nem egy objektumot. Diagnosztikai naplók tárolási archiválási teljes dokumentációjáért lásd: a [CLI parancsdokumentációja](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Archív diagnosztikai naplók a REST API-n keresztül

[Ez a dokumentum megtekintéséhez](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) olvashat, hogyan beállíthat egy diagnosztikai beállítás, az Azure Monitor REST API használatával.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Diagnosztikai naplók a storage-fiókban lévő séma

Beállítása archiválási, miután egy storage-tárolót a storage-fiókban jön létre, amint az esemény következik be, a napló kategóriák engedélyezte. A tárolóban lévő blobok ugyanazt az elnevezési konvenciót tevékenységeket tartalmazó naplók és a diagnosztikai naplók, hajtsa végre az alábbiak szerint:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Például a blobnév lehet:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Mindegyik PT1H.json blob tartalmazza a blob URL-jében meghatározott órában (például h=12) bekövetkezett események JSON-blobját. Az aktuális órában az események az előfordulásukkor lesznek a PT1H.json fájlhoz fűzve. A perc értéke (m = 00) mindig 00, mert a diagnosztikai naplóesemények óránként külön blobokba vannak osztva.

A PT1H.json fájlt belül minden egyes esemény tárolja a "rekord" tömb, a következő formátumban:

``` JSON
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Elem neve | Leírás |
| --- | --- |
| time |Időbélyeg, ha az esemény jött létre az Azure-szolgáltatás a megfelelő esemény kérelem feldolgozása. |
| resourceId |Erőforrás-azonosító az érintett erőforrás. |
| operationName |A művelet neve. |
| category |Az esemény kategóriája napló. |
| properties |Állítsa be a `<Key, Value>` párok (azaz szótár), az esemény részleteit leíró. |

> [!NOTE]
> A tulajdonságokat, és azokat a tulajdonságokat a használat függvényében az erőforrás.

## <a name="next-steps"></a>További lépések

* [Tölthet le blobokat elemzéshez](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Stream-diagnosztikai naplók az Event Hubs-névtér](../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)
* [Az Azure Monitor szolgáltatással az Azure Active Directory naplóinak archiválása](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
* [További információ a diagnosztikai naplók](../../azure-monitor/platform/diagnostic-logs-overview.md)

