---
title: Fogalmak – Azure Event Grid IoT Edge | Microsoft Docs
description: A IoT Edge Event Grid kapcsolatos fogalmak.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 8314447e7d5d282eb428ec9316c4eef6844a7423
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98682379"
---
# <a name="event-grid-concepts"></a>Az Event Griddel kapcsolatos fogalmak

Ez a cikk a Azure Event Grid főbb fogalmait ismerteti.

## <a name="events"></a>Események

Az esemény az a legkisebb mennyiségű információ, amely teljes mértékben leírja a rendszeren történteket. Minden eseményhez olyan általános információk tartoznak, mint például: az esemény forrása, az esemény időpontja és egyedi azonosító. Minden eseményhez konkrét információ is tartozik, amely csak az adott típusú eseményre vonatkozik. A legfeljebb 1 MB méretű esemény támogatása jelenleg előzetes verzióban érhető el.

Az események által tartalmazott tulajdonságokért lásd: [Azure Event Grid Event Schema](event-schemas.md).

## <a name="publishers"></a>Kiadók

A közzétevő az a felhasználó vagy szervezet, aki úgy dönt, hogy elküldi az eseményeket Event Gridba. A saját alkalmazásából is tehet közzé eseményeket.

## <a name="event-sources"></a>Eseményforrások

Az esemény forrása az esemény. Minden eseményforráshoz egy vagy több eseménytípus tartozik. Például a blob által létrehozott események tekintetében az Azure Storage az eseményforrás. A saját megadott egyéni eseményei tekintetében a saját alkalmazása az eseményforrás. Az eseményforrások feladata, hogy eseményeket küldjenek az Event Grid számára.

## <a name="topics"></a>Témakörök

Az Event Grid-témakör olyan végpontot biztosít, amelyben a forrás eseményeket küld. A közzétevő létrehozza az Event Grid-témakört, és eldönti, hogy egy adott eseményforrás egy vagy több témakörre van-e szüksége. A témakörök a kapcsolódó események gyűjteményéhez használatosak. Bizonyos típusú események megválaszolásához az előfizetők eldönthetik, hogy mely témakörökre kell előfizetni.

Az alkalmazás tervezésekor rugalmasan dönthet arról, hogy hány témakört kell létrehoznia. Nagyméretű megoldások esetében hozzon létre egy egyéni témakört a kapcsolódó események egyes kategóriáira. Tekintsünk például egy olyan alkalmazást, amelyik a felhasználói fiókok módosításához és a megrendelések feldolgozásához kapcsolódóan küld eseményeket. Nem valószínű, hogy valamely eseménykezelőnek mind a két esemény kategória kellene. Hozzon létre két külön témakört, és az eseménykezelők hadd iratkozzanak fel arra, amelyik érdekli őket. Kis megoldások esetében érdemes lehet az összes eseményt egyetlen témakörbe elküldeni. Az esemény-előfizetők szűrhetik a kívánt típusú eseményeket.

A Event Grid témaköreinek kezeléséről [REST API dokumentációban](api.md) talál további információt.

## <a name="event-subscriptions"></a>Esemény-előfizetések

Az előfizetés azt mutatja be, Event Grid hogy mely események érdeklik egy adott témakörben. Az előfizetés létrehozásakor meg kell adnia egy végpontot az esemény kezelésére. A végpontnak elküldett eseményeket szűrheti. 

A Event Grid előfizetések kezelésével kapcsolatban [REST API dokumentációban](api.md) talál további információt.

## <a name="event-handlers"></a>Eseménykezelők

Egy Event Grid perspektívából az eseménykezelő az a hely, ahol az eseményt elküldjék. A kezelő további műveletet hajt végre az esemény feldolgozásához. Event Grid számos kezelő típust támogat. Használhat egy támogatott Azure-szolgáltatást vagy a saját webhookot kezelőként. A kezelő típusától függően a Event Grid különböző mechanizmusokat követ az esemény kézbesítésének garantálása érdekében. Ha a cél eseménykezelő egy HTTP-webhook, akkor a rendszer újrapróbálkozik az eseménysel, amíg a kezelő nem adja vissza a következő állapotkódot: `200 – OK` . Az Edge hub esetében, ha az esemény kivétel nélkül kerül kézbesítésre, akkor azt a rendszer sikeresnek tekinti.

## <a name="security"></a>Biztonság

Event Grid biztosít biztonságot a témakörökre való feliratkozáshoz és a témakörök közzétételéhez. További információ: [Event Grid biztonság és hitelesítés](security-authentication.md).

## <a name="event-delivery"></a>Eseménykézbesítés

Ha Event Grid nem tudja megerősíteni, hogy az előfizető végpontja megkapta-e az eseményt, az visszaadja az eseményt. További információ: [Event Grid üzenet kézbesítése, és próbálkozzon újra](delivery-retry.md).

## <a name="batching"></a>Kötegelés

Egyéni témakör használatakor az eseményeket mindig közzé kell tenni egy tömbben. Alacsony átviteli sebesség esetén a tömbnek csak egy értéke lesz. Nagy mennyiségű használati eset esetén javasoljuk, hogy a nagyobb hatékonyság érdekében a közzététel során egyszerre több eseményt is hozzon létre. A kötegek legfeljebb 1 MB méretűek lehetnek. Minden eseménynek még nem lehet 1 MB-nál nagyobbnak lennie (előzetes verzió).