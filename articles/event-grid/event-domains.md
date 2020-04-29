---
title: Azure Event Grid található esemény-tartományok
description: Ez a cikk azt ismerteti, hogyan használhatók az események a különböző üzleti szervezetekhez, ügyfelekhez vagy alkalmazásokhoz tartozó egyéni események forgalmának kezeléséhez.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: f6698f91d7659f9fc2c314a9291380301146f8ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78898862"
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

### <a name="example-use-case"></a>Példa használati esetre

Egy példa használatával a rendszer a legkönnyebben ismerteti az események tartományait. Tegyük fel, hogy a contoso építőipari gépeket futtatja, ahol a traktorokat, a kiásási berendezéseket és más nehéz gépeket gyárt. A vállalkozás működtetésének részeként valós idejű információkat küldhet az ügyfeleknek a berendezések karbantartásával, a rendszer állapotával és a szerződések frissítéseivel kapcsolatban. Mindezen információk különböző végpontokra mutatnak, beleértve az alkalmazást, az ügyfél-végpontokat és az ügyfelek által beállított egyéb infrastruktúrát.

Az esemény-tartományok lehetővé teszik a contoso építőipari gépek egyetlen eseményként való modellezését. Minden ügyfelünk a tartományon belüli témakörként jelenik meg. A hitelesítés és az engedélyezés Azure Active Directory használatával kezelhetők. Az ügyfelek előfizethetnek a témára, és megkapják a nekik küldött eseményeket. A felügyelt hozzáférés az esemény-tartományon keresztül biztosítja, hogy csak a témához férhessenek hozzá.

Emellett egyetlen végpontot is biztosít, amely az összes ügyfél-eseményt közzéteheti. Event Grid gondoskodni fog arról, hogy az egyes témakörök csak a bérlőre vonatkozó eseményekről tájékoztassanak.

![Contoso építési példa](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Hozzáférés-kezelés

A tartományokkal az Azure szerepköralapú hozzáférés-vezérlés (RBAC) révén részletes, gabona-engedélyezési és hitelesítési vezérlési lehetőségekkel látja el az egyes témaköröket. Ezekkel a szerepkörökkel korlátozhatja, hogy az alkalmazás minden bérlője csak azokra a témakörökre korlátozódjon, amelyeknek hozzáférést kíván biztosítani számukra.

Az RBAC ugyanúgy működik, ahogy a [felügyelt hozzáférés-vezérlés](security-authorization.md) a többi Event Grid és az Azure-ban is működik. Az RBAC használatával egyéni szerepkör-definíciókat hozhat létre és alkalmazhat az esemény-tartományokban.

### <a name="built-in-roles"></a>Beépített szerepkörök

A Event Grid két beépített szerepkör-definícióval rendelkezik, amelyek megkönnyítik az RBAC való munkavégzést. Ezek a szerepkörök a **EventGrid EventSubscription közreműködő (előzetes verzió)** és a **EventGrid EventSubscription Reader (előzetes verzió)**. Ezeket a szerepköröket olyan felhasználókhoz rendeli, akiknek elő kell fizetniük az esemény tartományában lévő témakörökre. A szerepkör-hozzárendelést csak arra a témakörre kell hozzárendelni, amelyre a felhasználóknak elő kell fizetniük.

További információ ezekről a szerepkörökről: [Event Grid beépített szerepkörei](security-authorization.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Feliratkozás a témakörökre

Egy esemény tartományon belüli eseményekre való feliratkozás megegyeznek az [esemény-előfizetések egyéni témakörben való létrehozásával](./custom-event-quickstart.md) , vagy egy Azure-szolgáltatás eseményeire való feliratkozással.

### <a name="domain-scope-subscriptions"></a>Tartományi hatókör-előfizetések

Az esemény-tartományok lehetővé teszik a tartományhoz tartozó előfizetések használatát is. Egy esemény-tartomány esemény-előfizetése megkapja a tartományba elküldett összes eseményt, függetlenül attól, hogy az események milyen témakörben lesznek elküldve. A tartományra vonatkozó előfizetések kezelési és naplózási célokra is hasznosak lehetnek.

## <a name="publishing-to-an-event-domain"></a>Közzététel esemény-tartományba

Amikor létrehoz egy eseményt, egy közzétételi végpontot kap, amely ahhoz hasonló, ha létrehozott egy témakört a Event Gridban. 

Az események egy adott esemény tartomány bármely témakörében való közzétételéhez küldje el az eseményeket a tartomány végpontjának [ugyanúgy, mint egy egyéni témakört](./post-to-custom-topic.md). Az egyetlen különbség, hogy meg kell adnia azt a témakört, amelyre az eseményt el szeretné küldeni.

Tegyük fel például, hogy az alábbi tömbben szereplő események az `"id": "1111"` eseményt a `foo` témakörben küldi `"id": "2222"` el, miközben az eseményt `bar`a következő témakörben küldi el:

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

Ha ezek a korlátozások nem felelnek meg Önnek, a terméktámogatási jegy megnyitásával vagy e-mailben történő [askgrid@microsoft.com](mailto:askgrid@microsoft.com)elküldésével elérheti a termék csapatát. 

## <a name="pricing"></a>Díjszabás
Az esemény-tartományok ugyanazokat az [üzemeltetési díjszabást](https://azure.microsoft.com/pricing/details/event-grid/) használják, mint amelyeket a Event Grid összes többi funkciója használ.

A műveletek ugyanúgy működnek az esemény-tartományokban, mint az egyéni témakörökben. Az események egy adott esemény tartományba való beáramlása egy művelet, és az eseményekhez tartozó minden kézbesítési kísérlet egy művelet.

## <a name="next-steps"></a>További lépések

* Az események tartományának beállításával, a témakörök létrehozásával, az esemény-előfizetések létrehozásával és az események közzétételével kapcsolatos további tudnivalókért lásd: események [tartományának kezelése](./how-to-event-domains.md).
