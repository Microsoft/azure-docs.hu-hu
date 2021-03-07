---
title: Azure App Service Event Grid forrásként
description: Ez a cikk azt ismerteti, hogyan használható a Azure App Service Event Grid eseményforrásként. Ez biztosítja a sémát és az oktatóanyagra és útmutatókra mutató hivatkozásokat.
author: jasonfreeberg
ms.topic: conceptual
ms.date: 03/06/2021
ms.author: jafreebe
ms.openlocfilehash: 404fac634a628da49dee72b10b52785820fe1bf6
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443777"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Azure App Service Event Grid forrásként

Ez a cikk a Azure App Service eseményeinek tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md). Emellett a gyors indulások és oktatóanyagok listáját is tartalmazza, amelyekkel Azure App Service lehet használni az esemény forrásaként.

## <a name="available-event-types"></a>Elérhető események típusai

A Azure App Service a következő típusú eseményeket bocsátja ki

|    Eseménytípus                                             |    Description                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft. Web/Sites. BackupOperationStarted             |    A biztonsági mentés megkezdése után aktiválódik                             |
|    Microsoft. Web/Sites. BackupOperationCompleted           |    A biztonsági mentés befejezésekor aktiválódik                           |
|    Microsoft. Web/Sites. BackupOperationFailed              |    A biztonsági mentés meghiúsulása esetén aktiválódik                              |
|    Microsoft. Web/Sites. RestoreOperationStarted            |    A biztonsági másolatból történő visszaállítás indításakor aktiválódik        |
|    Microsoft. Web/Sites. RestoreOperationCompleted          |    Akkor aktiválódik, ha egy biztonsági másolatból való visszaállítás befejeződött      |
|    Microsoft. Web/Sites. RestoreOperationFailed             |    Akkor aktiválódik, ha egy biztonsági másolatból való visszaállítás meghiúsult         |
|    Microsoft. Web/Sites. SlotSwapStarted                    |    A tárolóhely-csere megkezdése után aktiválódik                          |
|    Microsoft. Web/Sites. SlotSwapCompleted                  |    A tárolóhelyek felcserélése után aktiválódik                      |
|    Microsoft. Web/Sites. SlotSwapFailed                     |    A tárolóhely-csere meghiúsulása esetén aktiválódik                           |
|    Microsoft. Web/Sites. SlotSwapWithPreviewStarted         |    Elindítva, ha az előzetes verzió megkezdése után megkezdődött a tárolóhely cseréje           |
|    Microsoft. Web/Sites. SlotSwapWithPreviewCancelled       |    Akkor aktiválódik, ha az előzetes verzióra való felcserélés megszakadt    |
|    Microsoft. Web/Sites. AppUpdated. újraindítás               |    A hely újraindításakor aktiválódik                      |
|    Microsoft. Web/Sites. AppUpdated. leállítva                 |    A hely leállításakor aktiválódik                          |
|    Microsoft. Web/Sites. AppUpdated.ChangedAppSettings      |    A hely Alkalmazásbeállítások megváltozásakor aktiválódik             |
|    Microsoft. Web/kiszolgálófarmok. AppServicePlanUpdated        |    App Service terv frissítésekor aktiválódik                 |

## <a name="properties-common-to-all-events"></a>Az összes eseményhez közös tulajdonságok

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)
Egy esemény indításakor a Event Grid szolgáltatás adatokat küld az eseményről a végpontra való feliratkozáshoz.
Ez a szakasz egy példát mutat be, hogy az egyes események milyen módon néznek ki. Minden esemény a következő legfelső szintű adattal rendelkezik:

|     Tulajdonság          |     Típus     |     Description                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `topic`              |    sztring    |    Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg.                                      |
|    `subject`            |    sztring    |    Közzétevő által megadott elérési út az esemény tárgya számára.                                                                                              |
|    `eventType`          |    sztring    |    Az eseményforrás egyik regisztrált eseménytípus.                                                                                  |
|    `eventTime`          |    sztring    |    Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor.                                                                         |
|    `id`                 |    sztring    |    Az esemény egyedi azonosítója.                                                                                                            |
|    `data`               |    object    |    BLOB Storage-események                                                                                                                    |
|    `dataVersion`        |    sztring    |    Az adatobjektum séma-verziója. A sémaverziót a közzétevő határozza meg.                                                          |
|    `metadataVersion`    |    sztring    |    Az esemény metaadatainak séma-verziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Event Grid megadja ezt az értéket.    |

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

Egy esemény indításakor a Event Grid szolgáltatás adatokat küld az eseményről a végpontra való feliratkozáshoz.
Ez a szakasz egy példát mutat be, hogy az egyes események milyen módon néznek ki. Minden esemény a következő legfelső szintű adattal rendelkezik:

|     Tulajdonság          |     Típus     |     Description                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `source`              |    sztring    |    Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg.                                      |
|    `subject`            |    sztring    |    Közzétevő által megadott elérési út az esemény tárgya számára.                                                                                              |
|    `type`          |    sztring    |    Az eseményforrás egyik regisztrált eseménytípus.                                                                                  |
|    `time`          |    sztring    |    Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor.                                                                         |
|    `id`                 |    sztring    |    Az esemény egyedi azonosítója.                                                                                                            |
|    `data`               |    object    |    BLOB Storage-események                                                                                                                    |
| `specversion` | sztring | A CloudEvents séma specifikációjának verziója. |

---

## <a name="example-events"></a>Példa események

### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.BackupOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```
# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.BackupOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "specversion": "1.0"
}
```

---

Az adatobjektum a következő tulajdonságokat tartalmazza:

|    Tulajdonság                |    Típus      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Az alkalmazás műveletének részletei                                                                                       |
|    `action`                  |    sztring    |    A művelet műveletének típusa                                                                                   |
|    `name`                    |    sztring    |    az eseményt tartalmazó webhely neve                                                                          |
|    `clientRequestId`         |    sztring    |    Az eseményt kiváltó hely API-művelethez az App Service által generált ügyfél-kérelem azonosítója         |
|    `correlationRequestId`    |    sztring    |    Az eseményt kiváltó hely API-művelethez tartozó app Service által generált korrelációs kérelem azonosítója    |
|    `requestId`               |    sztring    |    Az eseményt kiváltó hely API-művelethez tartozó app Service által generált kérelem azonosítója                |
|    `address`                 |    sztring    |    A művelet HTTP-kérelmének URL-címe                                                                                |
|    `verb`                    |    sztring    |    A művelet HTTP-művelete                                                                                       |

### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted, RestoreOperationCompleted, RestoreOperationFailed

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.RestoreOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.RestoreOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

Az adatobjektum a következő tulajdonságokat tartalmazza:

|    Tulajdonság                |    Típus      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Az alkalmazás műveletének részletei                                                                                       |
|    `action`                  |    sztring    |    A művelet műveletének típusa                                                                                   |
|    `name`                    |    sztring    |    az eseményt tartalmazó webhely neve                                                                          |
|    `clientRequestId`         |    sztring    |    Az eseményt kiváltó hely API-művelethez az App Service által generált ügyfél-kérelem azonosítója         |
|    `correlationRequestId`    |    sztring    |    Az eseményt kiváltó hely API-művelethez tartozó app Service által generált korrelációs kérelem azonosítója    |
|    `requestId`               |    sztring    |    Az eseményt kiváltó hely API-művelethez tartozó app Service által generált kérelem azonosítója                |
|    `address`                 |    sztring    |    A művelet HTTP-kérelmének URL-címe                                                                                |
|    `verb`                    |    sztring    |    A művelet HTTP-művelete                                                                                       |

### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

Az adatobjektum a következő tulajdonságokat tartalmazza:

|    Tulajdonság                |    Típus      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Az alkalmazás műveletének részletei                                                                                       |
|    `action`                 |    sztring    |    A művelet műveletének típusa                                                                                   |
|    `name`                    |    sztring    |    az eseményt tartalmazó webhely neve                                                                          |
|    `clientRequestId`         |    sztring    |    Az eseményt kiváltó hely API-művelethez az App Service által generált ügyfél-kérelem azonosítója         |
|    `correlationRequestId`    |    sztring    |    Az eseményt kiváltó hely API-művelethez tartozó app Service által generált korrelációs kérelem azonosítója    |
|   `requestId`               |    sztring    |    Az eseményt kiváltó hely API-művelethez tartozó app Service által generált kérelem azonosítója                |
|    `address`                 |    sztring    |    A művelet HTTP-kérelmének URL-címe                                                                                |
|    `verb`                    |    sztring    |    A művelet HTTP-művelete                                                                                       |
|    `sourceSlot`              |    sztring    |    A swap forrásának tárolóhelye                                                                                       |

### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted, SlotSwapWithPreviewCancelled

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

Az adatobjektum a következő tulajdonságokat tartalmazza:

|    Tulajdonság                |    Típus      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Az alkalmazás műveletének részletei                                                                                       |
|    `action`                 |    sztring    |    A művelet műveletének típusa                                                                                   |
|    `name`                    |    sztring    |    az eseményt tartalmazó webhely neve                                                                          |
|    `clientRequestId`         |    sztring    |    Az eseményt kiváltó hely API-művelethez az App Service által generált ügyfél-kérelem azonosítója         |
|    `correlationRequestId`    |    sztring    |    Az eseményt kiváltó hely API-művelethez tartozó app Service által generált korrelációs kérelem azonosítója    |
|    `requestId`               |    sztring    |    Az eseményt kiváltó hely API-művelethez tartozó app Service által generált kérelem azonosítója                |
|    `address`                 |    sztring    |    A művelet HTTP-kérelmének URL-címe                                                                                |
|    `verb`                    |    sztring    |    A művelet HTTP-művelete                                                                                       |

### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated. restarted, AppUpdated. leállítva, AppUpdated. ChangedAppSettings

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "topic": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.AppUpdated",
    "eventTime": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "name": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "dataVersion": "1'",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "source": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.AppUpdated",
    "time": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "name": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

Az adatobjektum a következő tulajdonságokkal rendelkezik:

|    Tulajdonság                |    Típus      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Az alkalmazás műveletének részletei                                                                                       |
|    `action`                  |    sztring    |    A művelet műveletének típusa                                                                                   |
|    `name`                    |    sztring    |    az eseményt tartalmazó webhely neve                                                                          |
|    `clientRequestId`         |    sztring    |    Az eseményt kiváltó hely API-művelethez az App Service által generált ügyfél-kérelem azonosítója         |
|    `correlationRequestId`    |    sztring    |    Az eseményt kiváltó hely API-művelethez tartozó app Service által generált korrelációs kérelem azonosítója    |
|    `requestId`               |    sztring    |    Az eseményt kiváltó hely API-művelethez tartozó app Service által generált kérelem azonosítója                |
|    `address`                 |    sztring    |    A művelet HTTP-kérelmének URL-címe                                                                                |
|    `verb`                    |    sztring    |    A művelet HTTP-művelete                                                                                       |

### <a name="serverfarmsappserviceplanupdated"></a>Kiszolgálófarmok. AppServicePlanUpdated

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "eventType": "Microsoft.Web.AppServicePlanUpdated",
    "eventTime": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "type": "Microsoft.Web.AppServicePlanUpdated",
    "time": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "specversion": "1.0"
}
```

---

Az adatobjektum a következő tulajdonságokkal rendelkezik:

|    Tulajdonság                         |    Típus      |    Description                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appServicePlanEventTypeDetail`    |    object    |    Az App Service-csomag műveletének részletei                                                                          |
|    `stampKind`                        |    sztring    |    Olyan környezet, amelyben az App Service-csomag                                                                     |
|    `action`                           |    sztring    |    Az App Service-csomag műveletének típusa                                                                            |
|    `status`                           |    sztring    |    Az App Service-csomag műveletének állapota                                                                   |
|    `sku`                              |    object    |    az App Service-csomag SKU-jának száma                                                                                       |
|    `name`                             |    sztring    |    az App Service-csomag neve                                                                                      |
|    `Tier`                             |    sztring    |    az App Service-csomag szintje                                                                                      |
|    `Size`                             |    sztring    |    az App Service-csomag mérete                                                                                      |
|    `Family`                           |    sztring    |    App Service-csomag családja                                                                                        |
|    `Capacity`                         |    sztring    |    az App Service-csomag kapacitása                                                                                      |
|    `action`                           |    sztring    |    A művelet műveletének típusa                                                                                   |
|    `name`                             |    sztring    |    az eseményt tartalmazó webhely neve                                                                          |
|    `clientRequestId`                  |    sztring    |    Az eseményt kiváltó hely API-művelethez az App Service által generált ügyfél-kérelem azonosítója         |
|    `correlationRequestId`             |    sztring    |    Az eseményt kiváltó hely API-művelethez tartozó app Service által generált korrelációs kérelem azonosítója    |
|    `requestId`                        |    sztring    |    Az eseményt kiváltó hely API-művelethez tartozó app Service által generált kérelem azonosítója                |
|    `address`                         |    sztring    |    A művelet HTTP-kérelmének URL-címe                                                                                |
|    `verb`                             |    sztring    |    A művelet HTTP-művelete                                                                                       |

## <a name="next-steps"></a>Következő lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetési séma](subscription-creation-schema.md)