---
title: Az Azure Service Bus-témakör szűrők |} A Microsoft Docs
description: Az Azure Service Bus-üzenettémakörök szűrése
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: 41af53dbfbb5c863007a332445a2f184fcbcbf81
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332227"
---
# <a name="topic-filters-and-actions"></a>Témakörszűrők és -műveletek

Az előfizetők meghatározhatják, hogy mely üzeneteket szeretnék megkapni egy témakörön belül. Ezeket az üzeneteket egy vagy több előfizetés nevesített szabály formájában vannak megadva. Minden egyes szabály egy feltételt, amely kiválasztja az adott üzenetek és a egy műveletet, amely jelzi a kijelölt üzenet áll. Az előfizetés megfelelő szabályfeltételenként létrehoz egy üzenetmásolatot, amelyek azután megfelelő szabályonként más és más jelöléssel láthatók el.

Minden egyes újonnan létrehozott témakör-előfizetésben rendelkezik egy kezdeti alapértelmezett előfizetés szabályt. Ha nem Ön kifejezetten megad egy szűrési feltételt a szabályhoz, van-e az alkalmazott szűrőt a **igaz** szűrőt, amely lehetővé teszi, hogy ki kell választani az előfizetést az összes üzenetet. Az alapértelmezett szabály nem társított jegyzet művelet rendelkezik.

Service Bus három szűrési feltételt támogat:

-   *Logikai szűrők* – a **TrueFilter** és **FalseFilter** vagy okoz az összes beérkező üzeneteket (**igaz**), vagy a beérkező üzeneteket (**hamis**) ki kell jelölni az előfizetéshez.

-   *Az SQL-szűrők* – **SqlFilter** tárol egy SQL-szerű feltételes kifejezés, amely a közvetítő a beérkező üzeneteket felhasználói és a rendszer tulajdonságai alapján értékeli ki. Összes rendszertulajdonsághoz előtaggal kell ellátni `sys.` a feltételes kifejezésben. A [SQL-nyelv részét a szűrési feltételek](service-bus-messaging-sql-filter.md) tesztek tulajdonságok létezik-e a (`EXISTS`), valamint null-értéket (`IS NULL`), nem/és/vagy logikai, relációs operátorokat, egyszerű numerikus számítási feladatokra, és egyszerű szöveges minta egyezéseit `LIKE`.

-   *A korrelációs szűrők* – **korrelációs szűrő** feltételeket, amelyek egy vagy több felhasználó és a rendszer tulajdonságai egy beérkező üzenetet összeveti készletét tartalmazza. Egy közös használata az egyeztetéshez a **CorrelationId** tulajdonság, de az alkalmazás is beállíthatja az egyeztetéshez **ContentType**, **címke**,  **Üzenetazonosító**, **ReplyTo**, **ReplyToSessionId**, **SessionId**, **való**, és minden olyan felhasználó által definiált tulajdonságok. Ha egy beérkező üzenetet egy tulajdonság értéke megegyezik a megadott korrelációs szűrő értéke van egyezés. A karakterlánc-kifejezés az összehasonlítás eredménye kis-és nagybetűket. Több egyezés tulajdonság megadása esetén a szűrőt, logikai és feltétel, ami azt jelenti, a megfelelő szűrő egyesíti azokat, minden feltétel egyeznie kell.

Az összes szűrő kiértékelése üzenet tulajdonságai. Szűrők az üzenet törzse nem értékelhető ki.

Összetett szűrési szabályokat a feldolgozási kapacitás szükséges. SQL Állapotszűrő szabályok használata különösen alacsonyabb teljes üzeneteinek átviteli sebessége az előfizetést, az üzenettémakör és a névtér szintjén eredményez. Amikor csak lehetséges, alkalmazások kell választania, a korrelációs szűrők SQL-szerű szűrők, mivel jóval hatékonyabb, a feldolgozás, és így kell kisebb hatással lesznek az átviteli sebesség.

## <a name="actions"></a>Műveletek

SQL-szűrő feltételekkel meghatározhatja egy műveletet, amely képes jegyzettel láthatja el az üzenet hozzáadása, eltávolítása vagy cseréje a tulajdonságok és azok értékei alapján. A művelet [használ egy SQL-szerű](service-bus-messaging-sql-filter.md) , amely lazán leans a frissítés az SQL-utasítás szintaxisa a. Miután egyeztetett azt, és mielőtt az üzenetet, az előfizetés van kiválasztva, az üzenet végrehajtott művelet. Az üzenet tulajdonságainak módosítását az üzenet átmásolhatja az előfizetés csak a.

## <a name="usage-patterns"></a>Használati minták

A témakör a legegyszerűbb használati forgatókönyvtől függ az, hogy minden előfizetés beolvasása, amely lehetővé teszi egy szórási minta témakörbe küldött minden üzenet másolata.

Szűrők és a műveletek lehetővé teszik a minták két további csoportok: a particionálás és az Útválasztás.

A particionálás használt szűrők üzenetek szét több meglévő előfizetések a kiszámítható és kölcsönösen kizárják egymást. A particionálási minta használatos, amikor a rendszer horizontálisan funkcionálisan azonos veremből, hogy minden egyes tartsa az összesített adatok egy részét a számos különböző környezeteket kezeli például a felhasználói profil adatait. A particionálás, közzétevő elküldi az üzenetet egy témakörbe particionálási minta ismerete nélkül. Az üzenet majd került, amelyről, majd lekérheti a partíció üzenetkezelőt a megfelelő előfizetés.

Üzenetek szét üzenettémakör-előfizetéseket, kiszámítható módon szűrők útválasztást használ, de nem feltétlenül kizárólagos. Együtt a [automatikus továbbítását](service-bus-auto-forwarding.md) funkció, a témakör a szűrők segítségével hozzon létre összetett útválasztást grafikonokon belül üzenet terjesztési Azure régiókon belül a Service Bus-névtér. Az Azure Functions vagy Azure Logic Apps között az Azure Service Bus-névterek hídként működik, az összetett globális üzleti alkalmazásokba való közvetlen integráció topológiák is létrehozhat.

## <a name="next-steps"></a>További lépések

További információ a Service Bus-üzenetkezelés, tekintse meg a következő témaköröket:

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [SQLFilter szintaxis](service-bus-messaging-sql-filter.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)