---
title: Azure Service Bus üzenet-szekvenálás és időbélyegek | Microsoft Docs
description: Ez a cikk bemutatja, hogyan őrizze meg az üzenetek sorrendbe Azure Service Bus sorrendjét (időbélyegekkel).
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: 3d5300568232afae1238445113d60eda8cdb2f1b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497097"
---
# <a name="message-sequencing-and-timestamps"></a>Üzenetek előkészítése és időbélyegek

Az alkalmazás-alkalmazás és az időbélyegző két olyan funkció, amely mindig engedélyezve van az összes Service Bus entitáson, és végigveszi a felületet a fogadott vagy a tallózott üzenetek és tulajdonságai `SequenceNumber` `EnqueuedTimeUtc` között.

Olyan esetekben, amikor az üzenetek abszolút sorrendje jelentős, és/vagy amikor a fogyasztónak megbízható egyedi azonosítóra van szüksége az üzenetekhez, a közvetítő hézagmentesként, az üzenetsorhoz vagy témakörhez képest növekvő sorszámmal bélyegezi az üzeneteket. Particionált entitások esetében a sorszám a partícióhoz viszonyítva lesz kibocsátva.

A **SequenceNumber** érték egy üzenethez rendelt egyedi, 64 bites egész szám, amelyet a közvetítő elfogad, és belső azonosítóként tárol. Particionált entitások esetében a legfelső 16 bit a partícióazonosítót tükrözi. Ha a 48/64 bites tartomány kimerül, a szekvenciaszámok nullára esnek.

A sorozatszám egyedi azonosítóként is megbízható, mivel azt egy központi és semleges hatóság rendeli hozzá, nem az ügyfelek. Emellett a pontos érkezési sorrendet is jelzi, és pontosabb, mint az időbélyeg mint rendelési feltétel, mivel az időbélyegek nem biztos, hogy elég magas felbontással vannak feltéve a szélsőséges üzenetváltások esetén, és óraelrendeződéseket okozhatnak olyan helyzetekben, amikor a közvetítő tulajdonjoga átvált a csomópontok között.

Az abszolút érkezési sorrend fontos, például olyan üzleti forgatókönyvekben, amelyekben korlátozott számú felajánlott terméket szolgálnak ki érkezési sorrendben, amíg a készletek utolsók; A "concert ticket"-értékesítés jó példa erre.

Az időbélyegző képesség semleges és megbízható hatóságként működik, amely pontosan rögzíti egy üzenet UTC-érkezési idejét, amit az **EnqueuedTimeUtc** tulajdonság tükröz. Ez az érték akkor hasznos, ha egy üzleti forgatókönyv a határidőktől függ, például attól, hogy egy munkaelem egy bizonyos napon éjfél előtt lett-e elküldve, de a feldolgozás jóval az üzenetsor hátraléka mögött van.

## <a name="scheduled-messages"></a>Ütemezett üzenetek

Az üzeneteket késleltetett feldolgozásra is beadhatja az üzenetsorba vagy témakörbe például azért, hogy egy ütemezett feladattal az üzenetek elérhetővé váljanak egy másik rendszer általi feldolgozásra egy bizonyos időpontban. Ez a képesség egy megbízható elosztott, időalapú ütemezőt képes megvalósítani.

Az ütemezett üzenetek nem materializálódnak az üzenetsorban, amíg a megadott sorba nem kerül. Ezt megelőzően az ütemezett üzenetek megszakíthatóak. A megszakítás törli az üzenetet.

Az üzeneteket kétféleképpen ütemezheti bármelyik ügyféllel:
- Használja a normál küldési API-t, de küldés előtt állítsa be az üzenet `ScheduledEnqueueTimeUtc` tulajdonságát.
- Használja a schedule message API-t, és adja át a normál üzenetet és az ütemezett időt is. Ez visszaadja az ütemezett üzenet **SequenceNumber** számát, amellyel később szükség esetén megszakíthatja az ütemezett üzenetet. 

Az ütemezett üzenetek és a sorszámaik az üzenetböngészővel [is felderítve vannak.](message-browsing.md)

Az ütemezett üzenetek **SequenceNumber** száma csak akkor érvényes, ha az üzenet ebben az állapotban van. Ahogy az üzenet aktív állapotba kerül, a rendszer úgy fűzi hozzá az üzenetet az üzenetsorhoz, mintha az aktuális pillanatban sorba lett volna küldődve, beleértve egy új **SequenceNumber hozzárendelését.**

Mivel a funkció az egyes üzenetekhez van horgonyozva, és az üzenetek csak egyszer Service Bus nem támogatja az üzenetek ismétlődő ütemezését.

## <a name="next-steps"></a>Következő lépések

Az üzenetkezelésről Service Bus alábbi témakörökben olvashat bővebben:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
