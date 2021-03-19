---
title: Diagnosztikai naplók figyelése Azure Monitor használatával
description: Ez a cikk bemutatja, hogyan irányíthatja át és tekintheti meg a diagnosztikai naplókat Azure Monitor használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0719fede4147943e76ddfea1c5e77388c7c5cc9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609321"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Media Services diagnosztikai naplók figyelése

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

[Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview.md) lehetővé teszi a metrikák és diagnosztikai naplók figyelését, amelyek segítenek megérteni az alkalmazások teljesítményét. A funkció részletes ismertetését és a Azure Media Services metrikák és diagnosztikai naplók használatának okát lásd: [Media Services metrikák és diagnosztikai naplók figyelése](monitor-media-services.md).

Ebből a cikkből megtudhatja, hogyan irányíthatja az adatútvonalat a Storage-fiókba, majd megtekintheti azokat.

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy Media Services fiókot](../create-account-howto.md).
- Tekintse át a  [figyelő Media Services](monitor-media-services.md).

## <a name="route-data-to-the-storage-account-using-the-portal"></a>Az adatirányítás a Storage-fiókba a portál használatával

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.
1. Navigáljon a Media Services fiókjához, és kattintson a **figyelés** lehetőségre a **diagnosztikai beállítások** elemre. Itt láthatja az előfizetésben lévő összes olyan erőforrást, amely monitorozási adatokat készít az Azure Monitoron keresztül.

    ![Képernyőfelvétel: a figyelés szakasz diagnosztikai beállításainak kiemelése.](../media/media-services-diagnostic-logs/logs01.png)

1. Kattintson a **diagnosztikai beállítás hozzáadása** elemre.

   Az erőforrások diagnosztikai beállítása annak definíciója, hogy *milyen* monitorozási adatokat kell átirányítani egy adott erőforrásból, és *hová* kell kerülniük ezeknek a monitorozási adatoknak.

1. A megjelenő szakaszban adjon egy **nevet** a beállításnak, és jelölje be az **Archiválás tárfiókba** jelölőnégyzetet.

    Válassza ki azt a Storage-fiókot, amelyhez naplókat szeretne küldeni, majd nyomja meg **az OK** gombot.
1. Jelölje be a **Napló** és a **Metrika** alatti jelölőnégyzeteket. Az erőforrás típusától függően előfordulhat, hogy csak az egyik lehetőség jelenik meg. Ezek a jelölőnégyzetek szabályozzák, hogy a napló- és metrikaadatok milyen, az erőforrástípushoz elérhető kategóriái kerülnek a kiválasztott célra, amely ebben az esetben a tárfiók.

   ![Diagnosztikai beállítások szakasz](../media/media-services-diagnostic-logs/logs02.png)
1. Állítsa a **Megőrzés (nap)** csúszkát a 30 értékre. Ez a csúszka beállítja a monitorozási adatok tárfiókban való megőrzésének időtartamát napokban. Az Azure Monitor automatikusan törli a megadott számú napnál régebbi adatokat. A nulla értékű megőrzési időszak határozatlan ideig tárolja az adatokat.
1. Kattintson a **Mentés** gombra.

Az erőforrás monitorozási adatai mostantól a tárfiókba kerülnek.

## <a name="route-data-to-the-storage-account-using-the-azure-cli"></a>Az Azure CLI használatával továbbíthatja az adataikat a Storage-fiókba

A diagnosztikai naplók tárolási fiókban való tárolásának engedélyezéséhez futtassa a következő `az monitor diagnostic-settings` Azure CLI-parancsot:

```azurecli-interactive
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

Például:

```azurecli-interactive
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>A Storage-fiókban tárolt adatmegjelenítés a portál használatával

Ha követte az előző lépéseket, az adatok elkezdtek a tárfiókba érkezni.

Előfordulhat, hogy akár öt percet is várnia kell, mielőtt az esemény megjelenik a tárfiókban.

1. A portálban navigáljon a bal oldali navigációs sávon található **Tárfiókok** szakaszhoz.
1. Azonosítsa az előző szakaszban létrehozott tárfiókot, és kattintson rá.
1. Kattintson a **Blobok** elemre, majd a tároló címkézett elemzések **– naplók – keydeliveryrequests** elemre. Ez az a tároló, amelyben a naplók szerepelnek. A figyelési adatok az erőforrás-azonosító alapján, majd dátum és idő szerint vannak kiosztva a tárolókban.
1. Az erőforrás-azonosító, dátum és idő tárolóiba kattintva keresse meg a PT1H.json fájlt. Kattintson a PT1H.json fájlra, majd a **Letöltés** gombra.

 Most megtekintheti a tárfiókban tárolt JSON-eseményt.

### <a name="examples-of-pt1hjson"></a>Példák a PT1H.jsbekapcsolására

#### <a name="clear-key-delivery-log"></a>Kulcs kézbesítési naplójának törlése

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>Widevine titkosított kulcs kézbesítési naplója

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="see-also"></a>Lásd még

* [Azure Monitor-metrikák](https://docs.microsoft.com/azure/azure-monitor/data-platform.md)
* [Diagnosztikai naplók Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/essentials/platform-logs-overview.md)
* [Az Azure-erőforrások naplózási adatainak gyűjtése és felhasználása](https://docs.microsoft.com/azure/azure-monitor/essentials/platform-logs-overview.md)

## <a name="next-steps"></a>Következő lépések

[Megfigyelési mérőszámok](media-services-metrics-howto.md)
