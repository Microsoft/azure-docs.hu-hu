---
title: Felkészülés a formátum módosítására Azure Monitor erőforrás-naplókra
description: Az Azure-erőforrás-naplók a hozzáfűzési Blobok használatával lettek áthelyezve a 2018. november 1-jén.
author: johnkemnetz
services: monitoring
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 001dfbc78c0027249143e933684523d47af383d1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79096779"
---
# <a name="prepare-for-format-change-to-azure-monitor-platform-logs-archived-to-a-storage-account"></a>Felkészülés a formátum módosítására Azure Monitor a Storage-fiókba archivált platform-naplókra

> [!WARNING]
> Ha [Azure-erőforrás-naplókat vagy-metrikákat küld egy Storage-fiókba a diagnosztikai beállítások](resource-logs-collect-storage.md) vagy a [tevékenység naplóinak használatával a Storage-fiókba](resource-logs-collect-storage.md), a Storage-fiókban lévő adatok formátuma a következő: 2018. november 1.,. Az alábbi utasítások ismertetik a hatását, és azt, hogy miként lehet frissíteni az eszközöket az új formátum kezelésére.
>

## <a name="what-changed"></a>Mi változott

A Azure Monitor olyan képességgel rendelkezik, amely lehetővé teszi az erőforrás-naplók és a tevékenységek naplóinak küldését egy Azure Storage-fiókba, Event Hubs névtérbe vagy egy Log Analytics munkaterületbe Azure Monitor. A rendszer teljesítményével kapcsolatos probléma megoldásához **november 1-jén, 2018-kor, 12:00 éjfélkor** a naplózási adat a blob Storage-ba való küldésének formátuma módosult. Ha olyan eszközzel rendelkezik, amely a blob Storage-ból olvas be adatolvasást, frissítenie kell az eszközt az új adatformátum megismeréséhez.

* Csütörtökön, november 1., 2018., 12:00 éjfélkor UTC, a blob formátuma [JSON-sorként](http://jsonlines.org/)lett módosítva. Ez azt jelenti, hogy az egyes rekordok egy sortöréssel lesznek elválasztva, a külső rekordok tömbje és a JSON-rekordok közötti vesszők nélkül.
* A blob formátuma az összes előfizetés összes diagnosztikai beállítására vonatkozóan egyszerre módosult. A november 1-től kibocsátott fájl első PT1H.jsezt az új formátumot használta. A blob és a tároló nevei változatlanok maradnak.
* Ha egy diagnosztikai beállítást november 1. előtt állít be, továbbra is a jelenlegi formátumban bocsátja ki az adatkibocsátást november 1-ig.
* Ez a változás egyszerre fordult elő az összes nyilvános felhő-régióban. A módosítás nem következik be Microsoft Azure 21Vianet, Azure Germany vagy Azure Government felhők által üzemeltetett.
* Ez a változás a következő adattípusokra van hatással:
  * [Azure-erőforrás-naplók](archive-diagnostic-logs.md) ([itt találja az erőforrások listáját](diagnostic-logs-schema.md))
  * [A diagnosztikai beállítások által exportált Azure-erőforrás-metrikák](diagnostic-settings.md)
  * [A log-profilok által exportált Azure-tevékenység naplófájljai](activity-log-collect.md)
* Ez a változás nem befolyásolja a következőket:
  * Hálózati folyamatok naplói
  * Az Azure-szolgáltatási naplók még nem érhetők el Azure Monitoron keresztül (például Azure App Service erőforrás-naplók, Storage Analytics-naplók)
  * Az Azure erőforrás-naplók és a tevékenységek naplófájljainak továbbítása más célhelyekre (Event Hubs, Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Hogyan lehet megtekinteni, hogy hatással van-e

Ezt a változást csak akkor befolyásolja, ha:
1. Naplózási adatokat küld egy Azure Storage-fiókba diagnosztikai beállítás használatával, és
2. A tárolóban található naplók JSON-struktúrájától függnek az eszközök.
 
Annak megállapításához, hogy rendelkezik-e olyan diagnosztikai beállításokkal, amelyek adatokat küldenek egy Azure Storage-fiókba, navigáljon a **figyelés** szakaszhoz a portálon, kattintson a **diagnosztikai beállítások**elemre, és azonosítsa az összes olyan erőforrást, amelyeken **engedélyezve**van a **diagnosztikai állapot** :

![Azure Monitor diagnosztikai beállítások panel](media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Ha a diagnosztikai állapot engedélyezve értékre van állítva, akkor az adott erőforráson aktív diagnosztikai beállítás van érvényben. Az erőforrásra kattintva megtekintheti, hogy a diagnosztikai beállítások egy Storage-fiókba küldenek-e adatokat:

![Storage-fiók engedélyezve](media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Ha olyan erőforrásokkal rendelkezik, amelyek ezen erőforrás-diagnosztikai beállítások használatával küldenek adatokat egy Storage-fióknak, az adott tárolási fiókban lévő adatok formátuma hatással lesz a változásra. Ha nem rendelkezik olyan egyéni eszközzel, amely nem működik ezekből a Storage-fiókokból, a formátum módosítása nem befolyásolja Önt.

### <a name="details-of-the-format-change"></a>A formátum változásának részletei

Az Azure Blob Storage-ban található fájl PT1H.jsának jelenlegi formátuma a rekordok JSON-tömbjét használja. Íme egy példa egy kulcstartó-naplófájlra:

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

Az új formátum [JSON-vonalakat](http://jsonlines.org/)használ, ahol minden esemény egy sor, a sortörés karakter pedig új eseményt jelez. A fenti minta a következőhöz hasonlóan fog kinézni a fájl PT1H.jsa változás után:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Ez az új formátum lehetővé teszi, hogy Azure Monitor a naplófájlok leküldését a [hozzáfűzési Blobok](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)használatával, amelyek hatékonyabban használják az új események folyamatos hozzáfűzését.

## <a name="how-to-update"></a>Frissítés

Csak akkor kell frissítéseket létrehoznia, ha olyan egyéni eszközzel rendelkezik, amely további feldolgozás céljából betölti ezeket a naplófájlokat. Ha külső log Analytics vagy SIEM eszközt használ, javasoljuk, [hogy az Event hubok használatával végezze el ezeket az adatmennyiséget](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). Az Event hubok integrációja egyszerűbb a számos szolgáltatásból származó naplók feldolgozásához és egy adott naplóban található könyvjelzők helyéhez.

Az egyéni eszközöket úgy kell frissíteni, hogy az aktuális és a JSON-vonalak formátumát is kezelni lehessen a fent ismertetett módon. Ezzel biztosíthatja, hogy amikor az új formátumban kezdi meg az adatmegjelenítést, az eszközök nem törnek.

## <a name="next-steps"></a>További lépések

* Tudnivalók az [erőforrás-erőforrások naplófájljainak Storage-fiókba való archiválásáról](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* Tudnivalók a [műveletnapló adatainak a Storage-fiókba való archiválásáról](./../../azure-monitor/platform/archive-activity-log.md)

