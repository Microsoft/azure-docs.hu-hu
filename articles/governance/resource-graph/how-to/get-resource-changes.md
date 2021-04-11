---
title: Erőforrás-módosítások lekérése
description: Ismerje meg, hogy miként lehet megkeresni egy erőforrás megváltozását, lekérni a módosított tulajdonságok listáját, és kiértékelni a különbségeket.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: 500a2d58c5fc9e1b63a544978c4b583eba60a63e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219076"
---
# <a name="get-resource-changes"></a>Erőforrás-módosítások lekérése

Az erőforrások a napi használat, az újrakonfigurálás és még az újratelepítés során változnak.
A módosítás egy egyedi vagy egy automatizált folyamat alapján is elvégezhető. A legtöbb változás a tervezés szerint történik, de néha nem. Az elmúlt 14 napban az Azure Resource Graph a következőket teszi lehetővé:

- A Azure Resource Manager tulajdonság változásainak észlelése
- Az egyes erőforrások változásairól lásd: tulajdonság változásának részletei
- Tekintse meg az erőforrás teljes összehasonlítását az észlelt módosítás előtt és után.

Az észlelés és a részletek változása az alábbi példák esetében hasznos:

- Az incidensek kezelése során a _potenciálisan_ kapcsolódó változások megismeréséhez. Egy adott időintervallumban megjelenő változási események lekérdezése és a módosítás részleteinek kiértékelése.
- A CMDB néven ismert Configuration Management-adatbázis naprakészen tartása. Ahelyett, hogy az összes erőforrást és a teljes tulajdonságokat egy ütemezett gyakoriságon frissítse, csak a megváltoztatott értéket adja meg.
- Annak megértése, hogy milyen egyéb tulajdonságok változtak, amikor egy erőforrás megváltoztatta a megfelelőségi állapotot. Ezeknek a további tulajdonságoknak a kiértékelése további, Azure Policy-definíción keresztül felügyelhető tulajdonságokkal is rendelkezhet.

Ez a cikk bemutatja, hogyan gyűjtheti ezeket az információkat az erőforrás-gráf SDK-n keresztül. Ha szeretné megtekinteni ezeket az információkat a Azure Portalban, tekintse meg a Azure Policy [változási előzményeit](../../policy/how-to/determine-non-compliance.md#change-history) vagy az Azure-tevékenység naplójának [módosításait](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log). Az alkalmazásoknak az infrastruktúra rétegből való változásairól az alkalmazások telepítésének módjáról az alkalmazás- [módosítási elemzés használata (előzetes verzió)](../../../azure-monitor/app/change-analysis.md) című témakörben olvashat bővebben Azure monitor.

> [!NOTE]
> Az erőforrás-diagram adatainak módosítása a Resource Manager-tulajdonságok. A virtuális gépek változásainak nyomon követéséhez tekintse meg a Azure Automation [változások követése](../../../automation/change-tracking/overview.md) vagy a Azure Policy a virtuális gépekhez [tartozó vendég konfigurációját](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Az Azure Resource Graph változási előzményei nyilvános előzetes verzióban érhetők el.

## <a name="find-detected-change-events-and-view-change-details"></a>Észlelt változási események keresése és a változás részleteinek megtekintése

Az erőforrás változásának első lépése, hogy megkeresse az adott erőforráshoz kapcsolódó módosítási eseményeket az adott időintervallumon belül. Az egyes változási események az erőforrás változásának részleteit is tartalmazzák. Ez a lépés a **resourceChanges** Rest-végponton keresztül történik.

A **resourceChanges** végpont a következő paramétereket fogadja el a kérelem törzsében:

- **resourceId** \[ kötelező \] : az Azure-erőforrás, amely a módosításokat keresi.
- **intervallum** \[ kötelező \] : egy olyan tulajdonság, amely _kezdő_ és _befejező_ dátummal rendelkezik, hogy mikor érdemes megkeresni egy változási eseményt a **Zulu Time Zone (Z)** használatával.
- **fetchPropertyChanges** (nem kötelező): logikai tulajdonság, amely meghatározza, hogy a válasz objektum tartalmazza-e a tulajdonságok változásait.

Példa a kérelem törzsére:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

A fenti kérelem törzsével a **resourceChanges** REST API URI-ja a következő:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

A válasz a következőhöz hasonlóan néz ki:

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

A **resourceId** minden észlelt változási eseménye a következő tulajdonságokkal rendelkezik:

- **changeId** – ez az érték egyedi az adott erőforráshoz. Habár a **changeId** karakterlánc esetenként más tulajdonságokat is tartalmazhat, csak egyedinek kell lennie.
- **beforeSnapshot** – annak az erőforrás-pillanatképnek a **snapshotId** és **időbélyegét** tartalmazza, amelyet a rendszer a változás észlelése előtt hozott.
- **afterSnapshot** – az erőforrás-pillanatkép **snapshotId** és **időbélyegét** tartalmazza, amelyet a rendszer a változás észlelése után hozott.
- **changeType** – a **BeforeSnapshot** és a **afterSnapshot** közötti teljes módosítási rekordra vonatkozóan észlelt változás típusát írja le. Az értékek a következők: _Létrehozás_, _frissítés_ és _Törlés_. A **propertyChanges** tulajdonság tömbje csak akkor szerepel, ha a **changeType** _frissítése_ történik.

  > [!IMPORTANT]
  > A _create_ csak a korábban létezett erőforrásokon érhető el, és az elmúlt 14 napban törölve lett.

- **propertyChanges** – a tulajdonságok ezen tömbje részletezi az összes olyan erőforrás-tulajdonságot, amely a **BeforeSnapshot** és a **afterSnapshot** között frissült:
  - **propertyName** – a megváltoztatott erőforrás-tulajdonság neve.
  - **changeCategory** – ismerteti, hogy mi történt a módosítással. Az értékek a következők: _rendszer_ és _felhasználó_.
  - **changeType** – az egyes erőforrás-tulajdonságokra vonatkozóan észlelt változás típusát írja le.
    Értékek: _Insert_, _Update_, _Remove_.
  - **beforeValue** – az erőforrás tulajdonság értéke a **beforeSnapshot**. A **ChangeType** _beszúrásakor_ nem jelenik meg.
  - **afterValue** – az erőforrás tulajdonság értéke a **afterSnapshot**. A **ChangeType** _eltávolításakor_ nem jelenik meg.

## <a name="compare-resource-changes"></a>Erőforrás-módosítások összehasonlítása

A **resourceChanges** végpont **changeId** a **resourceChangeDetails** Rest-végpontot használja a rendszer a módosított erőforrás előtti és utáni Pillanatképek lekéréséhez.

A **resourceChangeDetails** végpontjának két paramétert kell megadnia a kérelem törzsében:

- **resourceId**: az Azure-erőforrás, amellyel összehasonlíthatja a módosításokat.
- **changeId**: a **resourceChanges**-ből összegyűjtött **resourceId** egyedi változási eseménye.

Példa a kérelem törzsére:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

A fenti kérelem törzsével a **resourceChangeDetails** REST API URI-ja a következő:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

A válasz a következőhöz hasonlóan néz ki:

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

a **beforeSnapshot** és a **afterSnapshot** mindegyike megadja a pillanatkép készítésének időpontját és a tulajdonságokat. A módosítás néhány ponton történt a pillanatképek között. Az előző példát megtekintve láthatjuk, hogy a módosított tulajdonság **supportsHttpsTrafficOnly** volt.

Az eredmények összehasonlításához használja a **resourceChanges** **Changes** tulajdonságát, vagy értékelje ki a **resourceChangeDetails** egyes Pillanatképek **tartalom** részét a különbség megállapításához. Ha összehasonlítja a pillanatképeket, az **időbélyegző** mindig különbséget mutat a vártnál.

## <a name="next-steps"></a>Következő lépések

- Tekintse meg az [alapszintű lekérdezésekben](../samples/starter.md)használt nyelvet.
- Lásd: speciális alkalmazások a [speciális lekérdezésekben](../samples/advanced.md).
- További információ az [erőforrások feltárásáról](../concepts/explore-resources.md).
- A lekérdezések magas gyakorisággal történő kezelésével kapcsolatos útmutatásért lásd: [útmutató a szabályozott kérelmekhez](../concepts/guidance-for-throttled-requests.md).
