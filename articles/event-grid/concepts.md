---
title: Azure Event Grid fogalmak
description: Az Azure Event Gridet és a vele kapcsolatos fogalmakat ismerteti. A Event Grid számos kulcsfontosságú összetevőjét határozza meg.
ms.topic: conceptual
ms.date: 01/21/2021
ms.openlocfilehash: 6edc8a3980bfea15f28cfb7114bb9f8350a47a3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685703"
---
# <a name="concepts-in-azure-event-grid"></a>Fogalmak a Azure Event Grid

Ez a cikk a Azure Event Grid főbb fogalmait ismerteti.

## <a name="events"></a>Események

Az esemény az a legkisebb mennyiségű információ, amely teljes mértékben leírja a rendszeren történteket. Minden eseményhez olyan általános információk tartoznak, mint például: az esemény forrása, az esemény időpontja és egyedi azonosító. Minden eseményhez konkrét információ is tartozik, amely csak az adott típusú eseményre vonatkozik. Például az Azure Storage-ban létrehozott új fájlról szóló esemény a fájlról tartalmaz információkat, például a `lastTimeModified` értéket. Egy Event Hubs esemény pedig a Capture fájl URL-jét tartalmazza. 

Egy esemény maximálisan megengedett mérete 1 MB. Az 64 KB-nál nagyobb számú esemény díja 64 KB. Az eseményekben eljuttatott tulajdonságokért lásd: [Azure Event Grid Event Schema](event-schema.md).

## <a name="publishers"></a>Kiadók

A közzétevő az a felhasználó vagy szervezet, aki úgy dönt, hogy elküldi az eseményeket Event Gridba. A Microsoft számos Azure-szolgáltatáshoz tesz közzé eseményeket. A saját alkalmazásából is tehet közzé eseményeket. Az Azure-on kívül szolgáltatásokat üzemeltető szervezetek tehetnek közzé eseményeket Event Griden keresztül.

## <a name="event-sources"></a>Eseményforrások

Az esemény forrása az esemény. Minden eseményforráshoz egy vagy több eseménytípus tartozik. Például a blob által létrehozott események tekintetében az Azure Storage az eseményforrás. Az eszközök által létrehozott események tekintetében az IoT Hub az eseményforrás. A saját megadott egyéni eseményei tekintetében a saját alkalmazása az eseményforrás. Az eseményforrások feladata, hogy eseményeket küldjenek az Event Grid számára.

A támogatott Event Grid források megvalósításával kapcsolatos további információkért lásd: [eseményforrás a Azure Event Grid](overview.md#event-sources).

## <a name="topics"></a>Témakörök

Az Event Grid-témakör olyan végpontot biztosít, amelyben a forrás eseményeket küld. A közzétevő létrehozza az Event Grid-témakört, és eldönti, hogy egy adott eseményforrás egy vagy több témakörre van-e szüksége. A témakörök a kapcsolódó események gyűjteményéhez használatosak. Bizonyos típusú események megválaszolásához az előfizetők eldönthetik, hogy mely témakörökre kell előfizetni.

A **rendszertémakörök** az Azure-szolgáltatások, például az Azure Storage, az Azure Event Hubs és a Azure Service Bus által nyújtott beépített témakörök. Létrehozhat rendszertémaköröket az Azure-előfizetésében, és feliratkozhat rájuk. További információ: [a rendszertémakörök áttekintése](system-topics.md). 

Az **Egyéni témakörök** az alkalmazások és a harmadik féltől származó témakörök. Amikor létrehoz vagy hozzáférést kap egy egyéni témakörhöz, akkor az megjelenik a saját előfizetésében. További információ: [Egyéni témakörök](custom-topics.md). Az alkalmazás tervezésekor rugalmasságot biztosít, amikor eldönti, hány témakört kell létrehoznia. Nagyméretű megoldások esetében hozzon létre egy egyéni témakört a kapcsolódó események egyes kategóriáira. Tekintsünk például egy olyan alkalmazást, amelyik a felhasználói fiókok módosításához és a megrendelések feldolgozásához kapcsolódóan küld eseményeket. Nem valószínű, hogy valamely eseménykezelőnek mind a két esemény kategória kellene. Hozzon létre két külön témakört, és az eseménykezelők hadd iratkozzanak fel arra, amelyik érdekli őket. Kis megoldások esetében érdemes lehet az összes eseményt egyetlen témakörbe elküldeni. Az esemény-előfizetők szűrhetik a kívánt típusú eseményeket.

Van egy másik típusú témakör: **partner témakör**. A [partner Events](partner-events-overview.md) szolgáltatás lehetővé teszi, hogy egy harmadik féltől származó SaaS-szolgáltató eseményeket tegyen közzé a szolgáltatásaiból, hogy azok elérhetők legyenek az adott eseményekre előfizethető felhasználók számára. Az SaaS-szolgáltató egy témakör típusát, egy **partneri témakört** tesz elérhetővé, amelyet az előfizetők az események felhasználására használnak. Emellett egy tiszta pub-sub modellt is kínál, amely elválasztja az esemény-közzétevők és az előfizetők által használt erőforrások tulajdonjogát.

## <a name="event-subscriptions"></a>Esemény-előfizetések

Az előfizetés azt mutatja be, Event Grid hogy mely események érdeklik egy adott témakörben. Az előfizetés létrehozásakor meg kell adnia egy végpontot az esemény kezelésére. A végpontnak elküldett eseményeket szűrheti. A szűrést eseménytípus szerint vagy a tárgy mintája alapján végezheti el. További információ: [Event Grid előfizetés sémája](subscription-creation-schema.md).

Az előfizetések létrehozására példákat a következő témakörben talál:

* [Azure CLI-minták az Event Gridhez](cli-samples.md)
* [Azure PowerShell-minták az Event Gridhez](powershell-samples.md)
* [Azure Resource Manager-sablonok az Event Gridhez](template-samples.md)

További információ az aktuális Event Grid-előfizetések beszerzéséről: [Event Grid-előfizetések lekérdezése](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Esemény-előfizetés lejárata
Az esemény-előfizetés a megadott dátumot követően automatikusan lejár. Olyan esemény-előfizetések elévülésének beállítása, amelyek csak korlátozott ideig szükségesek, és nem szeretne aggódni az előfizetések tisztítása során. Ha például esemény-előfizetést hoz létre egy forgatókönyv teszteléséhez, érdemes lehet lejáratot beállítani. 

A lejárati idő beállítására példa: [előfizetés speciális szűrőkkel](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Eseménykezelők

Egy Event Grid perspektívából az eseménykezelő az a hely, ahol az eseményt elküldjék. A kezelő további műveletet hajt végre az esemény feldolgozásához. Event Grid számos kezelő típust támogat. A kezelőként támogatott Azure-szolgáltatást vagy saját webhookot is használhat. A kezelő típusától függően a Event Grid különböző mechanizmusokat követ az esemény kézbesítésének garantálása érdekében. A HTTP webhook-eseménykezelők esetében az eseményt a rendszer újra megkísérli, amíg a kezelő nem ad vissza állapotkódot `200 – OK` . Az Azure Storage-üzenetsor esetében az eseményeket a rendszer újra megkísérli, amíg a Queue szolgáltatás sikeresen feldolgozza az üzenetet a várólistába.

A támogatott Event Grid kezelők megvalósításával kapcsolatos további információkért lásd: [eseménykezelők a Azure Event Gridban](event-handlers.md).

## <a name="security"></a>Biztonság

Event Grid biztosít biztonságot a témakörökre való feliratkozáshoz és a témakörök közzétételéhez. Feliratkozáskor megfelelő engedélyekkel kell rendelkeznie az erőforrás vagy az Event Grid témakörben. Közzétételkor SAS-jogkivonattal vagy kulcs-hitelesítéssel kell rendelkeznie a témakörhöz. További információ: [Event Grid biztonság és hitelesítés](security-authentication.md).

## <a name="event-delivery"></a>Eseménykézbesítés

Ha Event Grid nem tudja megerősíteni, hogy az előfizető végpontja megkapta-e az eseményt, az visszaadja az eseményt. További információ: [Event Grid üzenet kézbesítése, és próbálkozzon újra](delivery-and-retry.md).

## <a name="batching"></a>Kötegelés

Egyéni témakör használatakor az eseményeket mindig közzé kell tenni egy tömbben. Ez lehet egy köteg az alacsony átviteli sebességű forgatókönyvek esetében, azonban a nagy mennyiségű használati esetek esetében ajánlott egyszerre több eseményt felvenni egy közzétételsel, hogy nagyobb hatékonyságot érjenek el. A kötegek legfeljebb 1 MB méretűek lehetnek, az események maximális mérete pedig 1 MB. 

## <a name="next-steps"></a>Következő lépések

* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Grid használatának gyors megkezdéséhez tekintse meg [az egyéni események létrehozása és irányítása Azure Event Grid](custom-event-quickstart.md)használatával című témakört.
