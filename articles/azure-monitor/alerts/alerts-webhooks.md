---
title: Webhook meghívása klasszikus metrikai riasztással Azure Monitor
description: Ismerje meg, hogyan irányíthatja át az Azure metrikus riasztásokat más, nem Azure rendszerekre.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: 7ab719560320882a3a6569bff5df7d2e2cf0b747
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037557"
---
# <a name="call-a-webhook-with-a-classic-metric-alert-in-azure-monitor"></a>Webhook meghívása klasszikus metrikai riasztással Azure Monitor

> [!WARNING]
> Ez a cikk a régebbi klasszikus metrikai riasztások használatát ismerteti. Azure Monitor mostantól támogatja [az újabb, közel valós idejű metrikai riasztásokat és új riasztási élményt](./alerts-overview.md). A klasszikus riasztások [kimaradnak](./monitoring-classic-retirement.md) a nyilvános Felhőbeli felhasználók számára, bár a használat továbbra is korlátozott a **2021**-ig. A Azure Government Cloud és az Azure China 21Vianet klasszikus riasztásai a **2024. február 29**-én törlődnek.
>

A webhookok használatával átirányíthat egy Azure-riasztási értesítést más rendszerekre a feldolgozás utáni vagy egyéni műveletekhez. A riasztások segítségével egy webhook használatával átirányíthatja azt a szolgáltatásba, amely SMS-üzeneteket küld, a hibák naplózása, a csapat csevegési vagy üzenetküldési szolgáltatásokon keresztüli értesítése, illetve egyéb műveletek esetén. 

Ez a cikk azt ismerteti, hogyan állítható be egy webhook egy Azure metrikai riasztásban. Emellett azt is bemutatja, hogy a HTTP-közzététel milyen hasznos adatokat keres egy webhookban. További információ az Azure-beli tevékenységekre vonatkozó riasztások beállításáról és sémáról (riasztás az eseményekről): [webhook meghívása Azure-beli tevékenység naplójának riasztására](../alerts/alerts-log-webhook.md).

Az Azure-riasztások a HTTP POST paranccsal küldik el a riasztás tartalmát JSON formátumban a riasztás létrehozásakor megadott webhook URI-hoz. A séma a cikk későbbi részében van meghatározva. Az URI azonosítónak érvényes HTTP-vagy HTTPS-végpontnak kell lennie. Az Azure a riasztások aktiválása után egy bejegyzést kér be.

## <a name="configure-webhooks-via-the-azure-portal"></a>Webhookok konfigurálása a Azure Portal használatával
A webhook URI-azonosítójának hozzáadásához vagy frissítéséhez lépjen a [Azure Portal](https://portal.azure.com/)a **riasztások létrehozása/frissítése** elemre.

![Riasztási szabály hozzáadása panel](./media/alerts-webhooks/Alertwebhook.png)

Az [Azure PowerShell-parancsmagok](../powershell-samples.md#create-metric-alerts), [platformfüggetlen parancssori](../cli-samples.md#work-with-alerts) [felület vagy Azure monitor REST API](/rest/api/monitor/alertrules)-k használatával riasztást is beállíthat a webhook URI-jának közzétételéhez.

## <a name="authenticate-the-webhook"></a>A webhook hitelesítése
A webhook a jogkivonat-alapú hitelesítés használatával tud hitelesítést végezni. A webhook URI-ja a jogkivonat-AZONOSÍTÓval lett mentve. Például: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Hasznos adatok sémája
A POST művelet a következő JSON-adattartalmat és sémát tartalmazza az összes metrika-alapú riasztáshoz:

```JSON
{
    "status": "Activated",
    "context": {
        "timestamp": "2015-08-14T22:26:41.9975398Z",
        "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
        "name": "ruleName1",
        "description": "some description",
        "conditionType": "Metric",
        "condition": {
            "metricName": "Requests",
            "metricUnit": "Count",
            "metricValue": "10",
            "threshold": "10",
            "windowSize": "15",
            "timeAggregation": "Average",
            "operator": "GreaterThanOrEqual"
        },
        "subscriptionId": "s1",
        "resourceGroupName": "useast",
        "resourceName": "mysite1",
        "resourceType": "microsoft.foo/sites",
        "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
        "resourceRegion": "centralus",
        "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
    },
    "properties": {
        "key1": "value1",
        "key2": "value2"
    }
}
```


| Mező | Kötelező | Rögzített értékek halmaza | Jegyzetek |
|:--- |:--- |:--- |:--- |
| status |Y |Aktiválva, megoldva |A riasztás állapota a beállított feltételek alapján. |
| összefüggésben |Y | |A riasztás kontextusa. |
| időbélyeg |Y | |A riasztás aktiválásának időpontja. |
| id |Y | |Minden riasztási szabály egyedi AZONOSÍTÓval rendelkezik. |
| name |Y | |A riasztás neve. |
| leírás |Y | |A riasztás leírása. |
| conditionType |Y |Metrika, esemény |Két típusú riasztás támogatott: metrika és esemény. A metrikai riasztások metrikai feltételen alapulnak. Az események riasztásai a tevékenység naplójában lévő eseményen alapulnak. Ezzel az értékkel ellenőrizhető, hogy a riasztás metrikán vagy eseményen alapul-e. |
| feltétel |Y | |A **conditionType** érték alapján ellenőrizhető konkrét mezők. |
| metricName |Metrikus riasztások esetén | |Annak a mérőszámnak a neve, amely meghatározza, hogy mi a szabály figyeli. |
| metricUnit |Metrikus riasztások esetén |Bájtok, BytesPerSecond, Darabszám, CountPerSecond, százalék, másodperc |A mérőszámban engedélyezett egység. Lásd az [engedélyezett értékeket](/previous-versions/azure/reference/dn802430(v=azure.100)). |
| metricValue |Metrikus riasztások esetén | |A riasztást kiváltó metrika tényleges értéke. |
| küszöb |Metrikus riasztások esetén | |Az a küszöbérték, amelyen a riasztás aktiválva van. |
| windowSize |Metrikus riasztások esetén | |A riasztási tevékenység küszöbérték alapján történő figyeléséhez használt időtartam. Az értéknek 5 perc és 1 nap közé kell esnie. Az értéknek ISO 8601 időtartam formátumúnak kell lennie. |
| timeAggregation |Metrikus riasztások esetén |Átlag, utolsó, maximum, minimum, none, összesen |Az összegyűjtött adatok időbeli összevonása. Az alapértelmezett érték az átlag. Lásd az [engedélyezett értékeket](/previous-versions/azure/reference/dn802410(v=azure.100)). |
| operátor |Metrikus riasztások esetén | |Az aktuális metrikai adatok meghatározott küszöbértékhez való összehasonlításához használt operátor. |
| subscriptionId |Y | |Az Azure-előfizetés azonosítója. |
| resourceGroupName |Y | |Az érintett erőforráshoz tartozó erőforráscsoport neve. |
| resourceName |Y | |Az érintett erőforrás neve. |
| resourceType |Y | |Az érintett erőforrás erőforrástípus. |
| resourceId |Y | |Az érintett erőforrás erőforrás-azonosítója. |
| resourceRegion |Y | |Az érintett erőforrás régiója vagy helye. |
| portalLink |Y | |Közvetlen hivatkozás a portál erőforrás-összefoglalás lapjára. |
| properties |N |Választható |Az esemény részleteit tartalmazó kulcs/érték párok halmaza. Például: `Dictionary<String, String>`. A Properties (Tulajdonságok) mező nem kötelező. Egyéni felhasználói felületen vagy logikai alkalmazáson alapuló munkafolyamatban a felhasználók megadhatják azokat a kulcs/érték párokat, amelyek átadhatók a hasznos adatokon keresztül. Az egyéni tulajdonságok a webhookba való visszaállításának másik módja a webhook URI-ja (lekérdezési paraméterek). |

> [!NOTE]
> A **Properties (Tulajdonságok** ) mezőt csak [Azure monitor REST API](/rest/api/monitor/alertrules)-k használatával állíthatja be.
>
>

## <a name="next-steps"></a>Következő lépések
* További információ az Azure-beli riasztásokról és webhookokról a videóban az [Azure-riasztások integrálása a PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080)-mel.
* Megtudhatja, hogyan [hajthat végre Azure Automation szkripteket (runbookok) az Azure-riasztásokon](https://go.microsoft.com/fwlink/?LinkId=627081).
* Ismerje meg, hogyan [KÜLDHET SMS-üzenetet a logikai alkalmazással egy Azure-riasztásból a Twilio-on keresztül](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Megtudhatja, hogyan lehet [logikai alkalmazás használatával Slack-üzenetet küldeni egy Azure-riasztásból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Megtudhatja, hogyan [küldhet egy Azure-riasztást egy logikai alkalmazással](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)egy Azure-üzenetsor számára.