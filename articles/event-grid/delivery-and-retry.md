---
title: Azure Event Grid kézbesítés és újrapróbálkozás
description: Leírja, hogy Azure Event Grid hogyan kézbesíti az eseményeket, és hogyan kezeli a kézbesítetlen üzeneteket.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: e7fa627464ddb85ebded3ae99229b7fe8dd3fde3
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629274"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid üzenet kézbesítése és újrapróbálkozás

Ez a cikk azt ismerteti, hogyan kezeli a Azure Event Grid az eseményeket, amikor a kézbesítés nem ismerhető fel.

Event Grid tartós kézbesítést biztosít. Minden egyes előfizetéshez **legalább egyszer** kézbesít minden üzenetet. Az eseményeket azonnal elküldi az egyes előfizetések regisztrált végpontjának. Ha egy végpont nem igazolja egy esemény fogadását, Event Grid újrapróbálkozik az esemény kézbesítésével.

> [!NOTE]
> A Event Grid nem garantálja az események kézbesítésének sorrendjét, így az előfizető a rendeléstől függetlenül is megkaphatja őket. 

## <a name="batched-event-delivery"></a>Kötegelt esemény kézbesítése

Event Grid alapértelmezett értéke az egyes események küldése az előfizetőknek. Az előfizető egyetlen eseménnyel rendelkező tömböt kap. A nagy átviteli sebességű forgatókönyvek esetében beállíthatja, hogy a rendszer a kötegelt eseményekre Event Grid a HTTP-teljesítmény javítására.

A kötegelt kézbesítésnek két beállítása van:

* **Események másodpercenkénti** maximális száma – a rendszer a kötegben Event Grid által kézbesített események maximális számát adja meg. A rendszer soha nem lépi túl ezt a számot, azonban kevesebb esemény jelenhet meg, ha a közzétételkor nem áll rendelkezésre más esemény. Event Grid nem késlelteti az eseményeket egy köteg létrehozásához, ha kevesebb esemény áll rendelkezésre. 1 és 5 000 közöttinek kell lennie.
* Az **előnyben részesített köteg mérete (kilobájtban** ) – a Batch méretének felső határa kilobájtban. A maximális eseményekhez hasonlóan a köteg mérete is kisebb lehet, ha a közzététel időpontjában további események nem érhetők el. Lehetséges, hogy egy köteg nagyobb, mint az előnyben részesített köteg mérete, *Ha* egyetlen esemény nagyobb az előnyben részesített méretnél. Ha például az előnyben részesített méret 4 KB, a 10 KB-os eseményt pedig Event Grid küldi el a rendszer, akkor a 10 KB-os esemény továbbra is a saját kötegében fog megjelenni az eldobása helyett.

A kötegelt kézbesítés a portálon, a CLI-n, a PowerShellen vagy az SDK-n keresztül, az esemény-előfizetések alapján konfigurálva van.

### <a name="azure-portal"></a>Azure Portal: 
![Batch-kézbesítési beállítások](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
Esemény-előfizetés létrehozásakor használja a következő paramétereket: 

- **Max-Events-per-batch** -a kötegben lévő események maximális száma. 1 és 5000 közötti számnak kell lennie.
- **preferált-batch-size-in-kilobájtban** – előnyben részesített köteg mérete (kilobájtban). 1 és 1024 közötti számnak kell lennie.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Az Azure CLI és a Event Grid használatával kapcsolatos további információkért lásd: [tárolási események továbbítása webes végponthoz az Azure CLI-vel](../storage/blobs/storage-blob-event-quickstart.md).

## <a name="retry-schedule-and-duration"></a>Újrapróbálkozási ütemterv és időtartam

Ha a EventGrid hibaüzenetet kap egy esemény kézbesítési kísérlete során, a EventGrid eldönti, hogy újra kell-e próbálkoznia a kézbesítéssel vagy a kézbesítetlen levelekkel, vagy el kell dobnia az eseményt a hiba típusa alapján. 

Ha az előfizetett végpont által visszaadott hiba olyan konfigurációval kapcsolatos hiba, amely nem oldható fel az újrapróbálkozásokkal (például ha a végpont törölve van), a EventGrid elküldheti az eseményt, vagy eldobja az eseményt, ha a kézbesítetlen levél nincs konfigurálva.

A következő típusú végpontok típusai nem történnek újra:

| Végpont típusa | Hibakódok |
| --------------| -----------|
| Azure-erőforrások | 400 hibás kérelem, 413 kérelem entitása túl nagy, 403 Tiltott | 
| Webhook | 400 hibás kérelem, 413 kérelem entitása túl nagy, 403 Tiltott, 404 nem található, 401 jogosulatlan |
 
> [!NOTE]
> Ha a Dead-Letter nincs konfigurálva a végponthoz, az események a fenti hibák miatt el lesznek dobva. Ha nem szeretné, hogy az ilyen típusú események el legyenek dobva, érdemes lehet a kézbesítetlen levelek konfigurálása.

Ha az előfizetett végpont által visszaadott hiba nem szerepel a fenti listában, a EventGrid a lent ismertetett szabályzatok használatával hajtja végre az újrapróbálkozást:

Az üzenet kézbesítése után a Event Grid 30 másodpercet vár a válaszra. 30 másodperc elteltével, ha a végpont nem válaszolt, az üzenet várólistára kerül az újrapróbálkozáshoz. Event Grid exponenciális leállítási újrapróbálkozási házirendet használ az események kézbesítéséhez. Event Grid a következő ütemterv szerint újrapróbálkozik a kézbesítéssel az ajánlott eljárás alapján:

- 10 másodperc
- 30 másodperc
- 1 perc
- 5 perc
- 10 perc
- 30 perc
- 1 óra
- 3 óra
- 6 óra
- 12 óránként akár 24 óráig


Ha a végpont 3 percen belül válaszol, Event Grid megpróbálja eltávolítani az eseményt az újrapróbálkozási sorból a legjobb erőfeszítés alapján, de a duplikált elemek továbbra is fogadhatók.

Event Grid egy kis véletlenszerűség az összes újrapróbálkozási lépéshez, és bizonyos újrapróbálkozások kihagyása, ha egy végpont nem kifogástalan állapotú, hosszú ideig leáll, vagy úgy tűnik, hogy túlterhelt.

A determinisztikus működéséhez állítsa az esemény időpontját élő és maximális kézbesítési kísérletekre az [előfizetés-újrapróbálkozási házirendekben](manage-event-delivery.md).

Alapértelmezés szerint a Event Grid minden olyan eseményt lejár, amely 24 órán belül nem érkezik meg. [Az újrapróbálkozási szabályzatot az](manage-event-delivery.md) esemény-előfizetés létrehozásakor is testreszabhatja. Megadja a kézbesítési kísérletek maximális számát (az alapértelmezett érték 30) és az esemény élettartamát (az alapértelmezett érték 1440 perc).

## <a name="delayed-delivery"></a>Késleltetett kézbesítés

A végpontok kézbesítési meghibásodások esetén a Event Grid megkezdi a kézbesítést, és az események ismételt megismétlését az adott végpontra. Ha például egy végponton közzétett első 10 esemény meghiúsul, Event Grid feltételezi, hogy a végpont problémákba ütközik, és az összes további újrapróbálkozást *és új* kézbesítést késlelteti, néhány esetben akár több óráig is eltarthat.

A késleltetett kézbesítés funkcionális célja a nem megfelelő állapotú végpontok és a Event Grid rendszer megóvása. A nem kifogástalan állapotú végpontokra történő kézbesítés nélkül, Event Grid az újrapróbálkozási szabályzat és a mennyiségi képességek könnyedén elérhetik a rendszereket.

## <a name="dead-letter-events"></a>Kézbesítetlen levelek eseményei
Ha Event Grid egy adott időszakon belül nem tud eseményt kézbesíteni, vagy ha az eseményt bizonyos számú alkalommal próbálta kézbesíteni, akkor a kézbesítetlen eseményt egy Storage-fiókba küldheti. Ezt a folyamatot **Kézbesítetlen levélnek** nevezzük. **A következő feltételek valamelyikének** teljesülése esetén Event Grid a kézbesítetlen leveleket. 

- Az esemény nem az adott időszakon belül **kerül** kézbesítésre. 
- Az esemény kézbesítésére tett **kísérletek száma** túllépte a korlátot.

Ha a feltételek bármelyike teljesül, az esemény eldobása vagy elutasítása nem történik meg.  Alapértelmezés szerint a Event Grid nem kapcsolja be a kézbesítetlen betűket. Az engedélyezéshez meg kell adnia egy Storage-fiókot, amely az esemény-előfizetés létrehozásakor nem kézbesítési eseményeket tart fenn. A kézbesítések feloldásához le kell kérnie az eseményeket ebből a Storage-fiókból.

Event Grid küld egy eseményt a kézbesítetlen levelek helyére, amikor megpróbálta az összes újrapróbálkozási kísérletet. Ha a Event Grid 400 (hibás kérés) vagy 413 (kérelem entitás túl nagy) választ kap, a rendszer azonnal a kézbesítetlen levelekre vonatkozó eseményt ütemezhet. Ezek a hibakódok jelzik, hogy az esemény kézbesítése soha nem fog sikerülni.

Az élettartam lejáratát csak a következő ütemezett kézbesítési kísérletnél ellenőrzi a rendszer. Tehát akkor is, ha a következő ütemezett kézbesítési kísérlet előtt lejár az esemény, akkor a lejárati idő csak a következő kézbesítéskor van bejelölve, majd ezt követően kézbesítve lesz. 

Az utolsó kísérlet az esemény kézbesítése és a kézbesítetlen levél helyére való továbbítása között öt perc késéssel jár. Ez a késleltetés a blob Storage-műveletek számának csökkentésére szolgál. Ha a kézbesítetlen levelek helye négy órán keresztül nem érhető el, a rendszer elveti az eseményt.

A kézbesítetlen levelek helyének beállítása előtt egy tárolóval rendelkező Storage-fiókkal kell rendelkeznie. Az esemény-előfizetés létrehozásakor adja meg a tároló végpontját. A végpont formátuma a (z): `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Előfordulhat, hogy értesítést szeretne kapni, ha egy eseményt elküldtek a kézbesítetlen levél helyére. Ha Event Gridt szeretne használni a nem kézbesített eseményekre való válaszadásra, [hozzon létre egy esemény-előfizetést](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) a kézbesítetlen levél blob Storage-hoz. Minden alkalommal, amikor a kézbesítetlen levelek blob-tárolója egy nem kézbesített eseményt kap, Event Grid értesíti a kezelőt. A kezelő olyan műveletekkel válaszol, amelyeket el kíván végezni a nem beérkező események egyeztetéséhez. A kézbesítetlen levelek helyének beállításához és az újrapróbálkozási szabályzatok létrehozásához lásd: [kézbesítetlen levelek és újrapróbálkozási szabályzatok](manage-event-delivery.md).

## <a name="delivery-event-formats"></a>Kézbesítési események formátuma
Ez a szakasz példákat mutat be a különböző kézbesítési sémák formátumában (Event Grid sémában, CloudEvents 1,0 sémában és egyéni sémában) található eseményekről és elhalt levelekről. További információ ezekről a formátumokról: [Event Grid Schema](event-schema.md) and [Cloud Events 1,0 Schema](cloud-event-schema.md) article. 

### <a name="event-grid-schema"></a>Event Grid-séma

#### <a name="event"></a>Esemény 
```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/000000000-0000-0000-0000-00000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    }
}
```

#### <a name="dead-letter-event"></a>Kézbesítetlen levelek eseménye

```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    },

    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T17:18:14.0265758Z",
    "lastDeliveryAttemptTime": "2020-08-13T17:18:14.0465788Z" 
}
```

### <a name="cloudevents-10-schema"></a>CloudEvents 1,0 séma

#### <a name="event"></a>Esemény

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    }
}
```

#### <a name="dead-letter-event"></a>Kézbesítetlen levelek eseménye

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    },

    "deadletterreason": "MaxDeliveryAttemptsExceeded",
    "deliveryattempts": 1,
    "lastdeliveryoutcome": "NotFound",
    "publishtime": "2020-08-13T21:21:36.4018726Z",
}
```

### <a name="custom-schema"></a>Egyéni séma

#### <a name="event"></a>Esemény

```json
{
    "prop1": "my property",
    "prop2": 5,
    "myEventType": "fooEventType"
}

```

#### <a name="dead-letter-event"></a>Kézbesítetlen levelek eseménye
```json
{
    "id": "8bc07e6f-0885-4729-90e4-7c3f052bd754",
    "eventTime": "2020-08-13T18:11:29.4121391Z",
    "eventType": "myEventType",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/00000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.EventGrid/topics/myCustomSchemaTopic",
    "subject": "subjectDefault",
  
    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T18:11:29.4121391Z",
    "lastDeliveryAttemptTime": "2020-08-13T18:11:29.4277644Z",
  
    "data": {
        "prop1": "my property",
        "prop2": 5,
        "myEventType": "fooEventType"
    }
}
```


## <a name="message-delivery-status"></a>Üzenet kézbesítési állapota

A Event Grid HTTP-válasz kódokat használ az események fogadásának visszaigazolására. 

### <a name="success-codes"></a>Sikerességi kódok

A Event Grid **csak** a következő http-válaszokat veszi figyelembe sikeres kézbesítésként. Az összes többi állapotkód sikertelen kézbesítésnek minősül, és szükség szerint újra próbálkozik vagy deadlettered. A sikeres állapotkód fogadása után Event Grid úgy véli, hogy a kézbesítés befejeződött.

- 200 OK
- 201 létrehozva
- 202 elfogadva
- 203 nem mérvadó információ
- 204 nincs tartalom

### <a name="failure-codes"></a>Hibakódok

Az összes többi, a fenti készletben nem szereplő kód (200-204) hibáknak minősül, és a rendszer újrapróbálkozik (ha szükséges). Némelyikhez az alább vázolt konkrét újrapróbálkozási szabályzatok tartoznak, az összes többi pedig a standard exponenciális visszalépési modellt követi. Fontos szem előtt tartani, hogy a Event Grid architektúrájának nagy mértékben párhuzamos jellege miatt az újrapróbálkozási viselkedés nem determinisztikus. 

| Állapotkód | Újrapróbálkozási viselkedés |
| ------------|----------------|
| 400 Hibás kérés | Nincs újrapróbálva |
| 401 Nem engedélyezett | 5 perc múlva próbálkozzon újra az Azure-erőforrások végpontjai esetében |
| 403 – Tiltott | Nincs újrapróbálva |
| 404 Nem található | 5 perc múlva próbálkozzon újra az Azure-erőforrások végpontjai esetében |
| 408 Kérés időtúllépése | Próbálkozzon újra 2 perc múlva |
| 413 kérelem entitása túl nagy | Nincs újrapróbálva |
| 503 A szolgáltatás nem érhető el | Újrapróbálkozás 30 másodperc vagy több után |
| Minden más | Újrapróbálkozás 10 másodperc vagy több után |

## <a name="delivery-with-custom-headers"></a>Kézbesítés egyéni fejlécekkel
Az esemény-előfizetések lehetővé teszik a kézbesített események részét képező HTTP-fejlécek beállítását. Ez a funkció lehetővé teszi, hogy egyéni fejléceket állítson be, amelyekre a célhelynek szüksége van. Esemény-előfizetés létrehozásakor akár 10 fejlécet is beállíthat. Minden fejléc értéke nem lehet nagyobb, mint 4 096 (4K) bájt. Egyéni fejléceket állíthat be a következő célhelyekre küldött eseményeken:

- Webhookok
- Témakörök és várólisták Azure Service Bus
- Azure Event Hubs
- Továbbító Hibrid kapcsolatok

További információ: [kézbesítés egyéni fejlécekkel](delivery-properties.md). 

## <a name="next-steps"></a>Következő lépések

* Az események kézbesítési állapotának megtekintéséhez lásd: [Event Grid üzenet kézbesítésének figyelése](monitor-event-delivery.md).
* Az esemény-kézbesítési beállítások testreszabásával kapcsolatban lásd: [kézbesítetlen levelek és újrapróbálkozási szabályzatok](manage-event-delivery.md).
