---
title: Eseménytartományok a Azure Event Grid
description: Ez a cikk azt ismerteti, hogyan használhatja az eseménytartományokat az egyéni események különböző üzleti szervezetekhez, ügyfelekhez vagy alkalmazásokhoz való áramlásának kezeléséhez.
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 32c06ac55f667ec9807c7952127c2cf0f0384024
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374709"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>A témakörök kezeléséhez Event Grid eseménytartományok

Ez a cikk azt ismerteti, hogyan használhatja az eseménytartományokat az egyéni események különböző üzleti szervezetekhez, ügyfelekhez vagy alkalmazásokhoz való áramlásának kezeléséhez. Eseménytartományok használata a következőre:

* Több-bérlős eseményarchitektúrák kezelése nagy méretekben.
* Az engedélyezés és a hitelesítés kezelése.
* Particionálja a témaköröket anélkül, hogy mindegyiket külön-külön kezeli.
* Kerülje az egyes témakörvégpontokkal való egyenkénti közzétételt.

## <a name="event-domain-overview"></a>Eseménytartomány áttekintése

Az eseménytartomány egy felügyeleti eszköz, amely számos Event Grid, amely ugyanannak az alkalmazásnak a témaköréhez kapcsolódik. Ez egy metatéma, amely több ezer különálló témakört is tartalmaz.

Az eseménytartományok ugyanazt az architektúrát biztosítják, amelyet az Olyan Azure-szolgáltatások, mint a Storage IoT Hub az események közzétételéhez. Lehetővé teszik események közzétételét több ezer témakörben. A tartományok az egyes témakörök engedélyezési és hitelesítési vezérlését is biztosítják, így particionálhatja a bérlőket.

## <a name="example-use-case"></a>Példahasználati eset
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="access-management"></a>Hozzáférés-kezelés

A tartományokkal az Azure szerepköralapú hozzáférés-vezérlése (Azure RBAC) segítségével finomhangolhatja az egyes témakörök hitelesítését és hitelesítését. Ezekkel a szerepkörökkel az alkalmazás minden bérlőjéhez csak azokat a témaköröket korlátozhatja, amelyekhez hozzáférést szeretne számukra.

Az Azure RBAC az eseménytartományok esetén ugyanúgy működik, mint a felügyelt hozzáférés-vezérlés a többi Event Grid Azure-ban. [](security-authorization.md) Az Azure RBAC használatával egyéni szerepkör-definíciókat hozhat létre és kényszerít ki az eseménytartományban.

### <a name="built-in-roles"></a>Beépített szerepkörök

Event Grid két beépített szerepkör-definícióval rendelkezik, amelyek megkönnyítik az Azure RBAC-t az eseménytartományokkal való munkavégzésben. Ezek a szerepkörök az **EventGrid EventSubscription Közreműködő (előzetes verzió)** és az **EventGrid EventSubscription Reader (előzetes verzió)**. Ezeket a szerepköröket olyan felhasználókhoz rendelheti hozzá, akiknek elő kell fizetniük az eseménytartomány témaköreire. A szerepkör-hozzárendelés hatóköre csak arra a témakörre terjed ki, amelyre a felhasználóknak elő kell fizetniük.

További információ ezekről a szerepkörökről: Beépített szerepkörök [a Event Grid.](security-authorization.md#built-in-roles)

## <a name="subscribing-to-topics"></a>Feliratkozás témakörökre

Az eseménytartományon belüli témakörök eseményeire való feliratkozás [](./custom-event-quickstart.md) ugyanaz, mintha egy esemény-előfizetést hoz létre egy egyéni témakörben, vagy feliratkozik egy eseményre egy Azure-szolgáltatásból.

> [!IMPORTANT]
> A tartománytémaktémát a rendszer automatikusan **felügyelt erőforrásnak** Event Grid. Esemény-előfizetést a tartományi témakör hatókörében a tartományi témakör létrehozása nélkül hozhat létre. Ebben az esetben a Event Grid automatikusan létrehozza a tartományi témakört az Ön nevében. Természetesen továbbra is dönthet úgy, hogy manuálisan hozza létre a tartományi témakört. Ez a viselkedés lehetővé teszi, hogy kevesebb erőforrással kell törődni, amikor számos tartományi témakört kell foglalkoznunk. A tartományi témakör utolsó előfizetésének törlésekor a tartományi témakör is törlődik, függetlenül attól, hogy a tartományi témakör manuálisan lett-e létrehozva vagy automatikusan lett-e létrehozva. 

### <a name="domain-scope-subscriptions"></a>Tartományhatókör-előfizetések

Az eseménytartományok a tartományra vonatkozó előfizetéseket is lehetővé teszik. Egy eseménytartomány esemény-előfizetése a tartománynak küldött összes eseményt megkapja, függetlenül attól, hogy milyen témakörbe küldi a rendszer az eseményeket. A tartományhatókör-előfizetések felügyeleti és naplózási célokra lehetnek hasznosak.

## <a name="publishing-to-an-event-domain"></a>Közzététel eseménytartományban

Eseménytartomány létrehozásakor egy ahhoz hasonló közzétételi végpontot kap, mint ha egy témakört hozott létre a Event Grid. 

Ha eseményeket szeretne közzétenni egy eseménytartomány bármely témakörében, az eseményeket ugyanúgy kell lekuldatni a tartomány végpontjára, mint egy egyéni [témakör esetén.](./post-to-custom-topic.md) Az egyetlen különbség az, hogy meg kell adnia azt a témakört, amelybe el szeretné kézbesíteni az eseményt.

A következő eseménytömb közzététele például a következő eseményekkel küld eseményt a témakörbe, míg az esemény a témakörbe `"id": "1111"` `foo` lesz `"id": "2222"` `bar` elküldve:

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

Az eseménytartományok kezelik a témakörökben való közzétételt. Ahelyett, hogy az eseményeket külön-külön felügyeli az egyes témakörökben, közzéteheti az összes eseményt a tartomány végpontjára. Event Grid arról, hogy minden esemény a megfelelő témakörbe legyen elküldve.

## <a name="limits-and-quotas"></a>Korlátok és kvóták
Az eseménytartományokkal kapcsolatos korlátok és kvóták a következőek:

- Eseménytartományonként 100 000 témakör 
- Azure-előfizetésenként 100 eseménytartomány 
- Témakörenként 500 esemény-előfizetés egy eseménytartományban
- 50 tartományhatókör-előfizetés 
- Másodpercenként 5000 esemény be- és beúsítási sebessége (egy tartományba)

Ha ezek a korlátok nem megfelelők, nyisson meg egy támogatási jegyet, vagy küldjön e-mailt a [askgrid@microsoft.com](mailto:askgrid@microsoft.com) címre. 

## <a name="pricing"></a>Díjszabás
Az eseménytartományok ugyanazt a [műveleti díjszabást](https://azure.microsoft.com/pricing/details/event-grid/) használják, mint a Event Grid szolgáltatásai.

A műveletek ugyanúgy működnek az eseménytartományban, mint az egyéni témakörökben. Egy esemény minden eseménytartományba való bejövő forgalom egy művelet, egy esemény minden kézbesítési kísérlete pedig művelet.



## <a name="next-steps"></a>Következő lépések

* Az eseménytartományok beállításával, a témakörök létrehozásával, az esemény-előfizetések létrehozásával és az események közzétételével kapcsolatos információkért lásd: [Eseménytartományok kezelése.](./how-to-event-domains.md)
