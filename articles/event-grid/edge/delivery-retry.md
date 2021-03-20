---
title: Kézbesítés és újrapróbálkozás – Azure Event Grid IoT Edge | Microsoft Docs
description: Kézbesítés és újrapróbálkozás Event Grid IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: aa0b3a05fb26f6be951b697145d7b22e03b7792d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171618"
---
# <a name="delivery-and-retry"></a>Teljesítés és újrapróbálkozás

Event Grid tartós kézbesítést biztosít. Minden egyes megegyező előfizetés esetében minden üzenetet legalább egyszer megpróbál kézbesíteni. Ha egy előfizető végpontja nem igazolja egy esemény fogadását, vagy ha hiba történt, Event Grid újrapróbálkozik a kézbesítéssel egy rögzített **Újrapróbálkozás-ütemterv** alapján, és **újrapróbálkozási házirendet**.  Alapértelmezés szerint a Event Grid modul egyszerre egy eseményt biztosít az előfizetőnek. A hasznos adatok azonban egy tömb, amely egyetlen eseménnyel rendelkezik. A modult egyszerre több esemény is elvégezheti, ha engedélyezi a kimeneti köteg funkciót. A szolgáltatással kapcsolatos további információkért lásd: a [kimenetek kötegelt feldolgozása](delivery-output-batching.md).  

> [!IMPORTANT]
>Az események nem rendelkeznek adatmegőrzési támogatással. Ez azt jelenti, hogy a Event Grid modul újbóli üzembe helyezése vagy újraindítása miatt a még nem szállított események elvesznek.

## <a name="retry-schedule"></a>Újrapróbálkozási ütemterv

Egy üzenet kézbesítése után a Event Grid legfeljebb 60 másodpercet vár a válaszra. Ha az előfizető végpontja nem küldi el a választ, akkor az üzenet a további újrapróbálkozások egyik várólistán lévő fog megjelenni.

Két előre konfigurált várólista van, amelyek meghatározzák azt az ütemezést, amelyen az újrapróbálkozási kísérlet történik. Ezek a következők:

| Ütemezés | Description |
| ---------| ------------ |
| 1 perc | Az itt bekövetkező üzenetek percenként próbálkoznak.
| 10 perc | Az itt megjelenő üzeneteket 10 percenként kísérli meg a rendszer.

### <a name="how-it-works"></a>Működés

1. Üzenet érkezik a Event Grid modulba. Kísérlet történt az azonnali kézbesítésre.
1. Ha a kézbesítés sikertelen, akkor az üzenet 1 percenként várólistán lévő, és egy perc múlva újra próbálkozik.
1. Ha a kézbesítés továbbra is sikertelen, a rendszer 10 percenként várólistán lévő az üzenetet, és 10 percenként újrapróbálkozik.
1. A rendszer a sikeres vagy újrapróbálkozási szabályzatok elérésekor megkísérli a kézbesítést.

## <a name="retry-policy-limits"></a>Újrapróbálkozási szabályzat korlátai

Az újrapróbálkozási szabályzatot két konfiguráció határozza meg. Ezek a következők:

* Kísérletek maximális száma
* Esemény élettartama (TTL)

Egy esemény el lesz dobva, ha az újrapróbálkozási szabályzat korlátai bármelyike eléri a határértéket. Az újrapróbálkozási ütemtervet maga az újrapróbálkozási ütemterv szakaszban ismertetjük. Ezen korlátok konfigurálása az összes előfizető vagy előfizetések alapján végezhető el. A következő szakasz ismerteti a további részleteket.

## <a name="configuring-defaults-for-all-subscribers"></a>Az összes előfizető alapértelmezett beállításainak konfigurálása

Két tulajdonság létezik: `brokers__defaultMaxDeliveryAttempts` és `broker__defaultEventTimeToLiveInSeconds` a Event Grid központi telepítés részeként konfigurálható, amely az összes előfizető újrapróbálkozási szabályzatának alapértelmezett értékeit szabályozza.

| Tulajdonság neve | Description |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Egy eseményt kézbesítő kísérletek maximális száma. Alapértelmezett érték: 30.
| `broker__defaultEventTimeToLiveInSeconds` | Az esemény ÉLETTARTAMa másodpercben, amely után az esemény el lesz dobva, ha nem érkezik meg. Alapértelmezett érték: **7200** másodperc

## <a name="configuring-defaults-per-subscriber"></a>Alapértelmezett beállítások konfigurálása előfizető számára

Az újrapróbálkozási szabályzat korlátozásait előfizetések alapján is megadhatja.
Tekintse meg az [API-dokumentációt](api.md) , amelyből megtudhatja, hogyan konfigurálhatja az alapértelmezett beállításokat előfizetőként. Az előfizetési szint alapértelmezett beállításai felülbírálják a modul szintjének konfigurációit.

## <a name="examples"></a>Példák

Az alábbi példa az újrapróbálkozási szabályzatot állítja be a Event Grid modulban a maxNumberOfAttempts = 3 és az Event TTL (30 perc) értékkel.

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

Az alábbi példa egy webhook-előfizetést állít be a maxNumberOfAttempts = 3 és az Event TTL (30 perc) értékkel.

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
