---
title: Azure Event Grid található esemény-tartományok
description: Ez a cikk azt ismerteti, hogyan használhatók az események a különböző üzleti szervezetekhez, ügyfelekhez vagy alkalmazásokhoz tartozó egyéni események forgalmának kezeléséhez.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 46a50a8ecc50bd1b80efcba41228564df1c36c9f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198659"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Az Event Grid témakörök kezeléséhez kapcsolódó események tartományának ismertetése

Ez a cikk azt ismerteti, hogyan használhatók az események a különböző üzleti szervezetekhez, ügyfelekhez vagy alkalmazásokhoz tartozó egyéni események forgalmának kezeléséhez. Esemény-tartományok használata a következőhöz:

* Több-bérlős esemény-architektúrák kezelése nagy léptékben.
* Az engedélyezés és a hitelesítés kezelése.
* Particionálja a témaköröket külön kezelése nélkül.
* Kerülje az egyes témakör-végpontok egyéni közzétételét.

## <a name="event-domain-overview"></a>Az Event domain áttekintése

Az Event domain egy felügyeleti eszköz nagy számú Event Grid témakörhöz, amely ugyanahhoz az alkalmazáshoz kapcsolódik. Úgy képzelheti el, mint egy olyan meta-témakört, amely több ezer egyéni témával is rendelkezhet.

Az esemény-tartományok az Azure-szolgáltatások (például a Storage és a IoT Hub) által használt architektúrát teszik elérhetővé az események közzétételéhez. Lehetővé teszik az események több ezer témakörbe való közzétételét. A tartományok lehetővé teszik az egyes témakörök engedélyezési és hitelesítési szabályozását is, így a bérlők particionálása is megtörténik.

## <a name="example-use-case"></a>Példa használati esetre
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="access-management"></a>Hozzáférés-kezelés

A tartományokban az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) révén részletes gabona-engedélyezési és hitelesítési vezérlést kap az egyes témakörökben. Ezekkel a szerepkörökkel korlátozhatja, hogy az alkalmazás minden bérlője csak azokra a témakörökre korlátozódjon, amelyeknek hozzáférést kíván biztosítani számukra.

Az Azure-beli RBAC ugyanúgy működnek, mint a [felügyelt hozzáférés-vezérlés](security-authorization.md) a többi Event Grid és az Azure-ban. Az Azure RBAC segítségével egyéni szerepkör-definíciókat hozhat létre és alkalmazhat az esemény-tartományokban.

### <a name="built-in-roles"></a>Beépített szerepkörök

A Event Grid két beépített szerepkör-definícióval rendelkezik, amelyek megkönnyítik az Azure-RBAC használatát az esemény-tartományokkal való munkavégzéshez. Ezek a szerepkörök a **EventGrid EventSubscription közreműködő (előzetes verzió)** és a **EventGrid EventSubscription Reader (előzetes verzió)**. Ezeket a szerepköröket olyan felhasználókhoz rendeli, akiknek elő kell fizetniük az esemény tartományában lévő témakörökre. A szerepkör-hozzárendelést csak arra a témakörre kell hozzárendelni, amelyre a felhasználóknak elő kell fizetniük.

További információ ezekről a szerepkörökről: [Event Grid beépített szerepkörei](security-authorization.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Feliratkozás a témakörökre

Egy esemény tartományon belüli eseményekre való feliratkozás megegyeznek az [esemény-előfizetések egyéni témakörben való létrehozásával](./custom-event-quickstart.md) , vagy egy Azure-szolgáltatás eseményeire való feliratkozással.

### <a name="domain-scope-subscriptions"></a>Tartományi hatókör-előfizetések

Az esemény-tartományok lehetővé teszik a tartományhoz tartozó előfizetések használatát is. Egy esemény-tartomány esemény-előfizetése megkapja a tartományba elküldett összes eseményt, függetlenül attól, hogy az események milyen témakörben lesznek elküldve. A tartományra vonatkozó előfizetések kezelési és naplózási célokra is hasznosak lehetnek.

## <a name="publishing-to-an-event-domain"></a>Közzététel esemény-tartományba

Amikor létrehoz egy eseményt, egy közzétételi végpontot kap, amely ahhoz hasonló, ha létrehozott egy témakört a Event Gridban. 

Az események egy adott esemény tartomány bármely témakörében való közzétételéhez küldje el az eseményeket a tartomány végpontjának [ugyanúgy, mint egy egyéni témakört](./post-to-custom-topic.md). Az egyetlen különbség, hogy meg kell adnia azt a témakört, amelyre az eseményt el szeretné küldeni.

Tegyük fel például, hogy az alábbi tömbben szereplő események az eseményt a témakörben küldi el, `"id": "1111"` `foo` miközben az eseményt a következő `"id": "2222"` témakörben küldi el `bar` :

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Az esemény-tartományok kezelik a témakörökre való közzétételt. Ahelyett, hogy minden egyes, egyenként felügyelt témakörhöz eseményeket tegyen közzé, az összes eseményt közzéteheti a tartomány végpontján. Event Grid gondoskodik arról, hogy az egyes események a megfelelő témakörbe legyenek küldve.

## <a name="limits-and-quotas"></a>Korlátok és kvóták
Az esemény-tartományokhoz kapcsolódó korlátok és kvóták:

- 100 000 témakör/esemény tartománya 
- 100 esemény-tartomány Azure-előfizetéssel 
- 500 esemény-előfizetések egy adott esemény tartományában
- 50 tartományhoz tartozó előfizetések 
- 5 000 esemény/másodperc betöltési arány (tartományba)

Ha ezek a korlátozások nem felelnek meg Önnek, a terméktámogatási jegy megnyitásával vagy e-mailben történő elküldésével elérheti a termék csapatát [askgrid@microsoft.com](mailto:askgrid@microsoft.com) . 

## <a name="pricing"></a>Díjszabás
Az esemény-tartományok ugyanazokat az [üzemeltetési díjszabást](https://azure.microsoft.com/pricing/details/event-grid/) használják, mint amelyeket a Event Grid összes többi funkciója használ.

A műveletek ugyanúgy működnek az esemény-tartományokban, mint az egyéni témakörökben. Az események egy adott esemény tartományba való beáramlása egy művelet, és az eseményekhez tartozó minden kézbesítési kísérlet egy művelet.



## <a name="next-steps"></a>Következő lépések

* Az események tartományának beállításával, a témakörök létrehozásával, az esemény-előfizetések létrehozásával és az események közzétételével kapcsolatos további tudnivalókért lásd: események [tartományának kezelése](./how-to-event-domains.md).
