---
title: Azure Service Bus – üzenet lejárata
description: Ez a cikk a Azure Service Bus üzenetek érvényességének lejáratát és időpontját ismerteti. Az ilyen határidő lejárta után az üzenet már nem érkezik meg.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: 74df8909633c2fa048c23c559ffdd315a8616e11
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042827"
---
# <a name="message-expiration-time-to-live"></a>Üzenetek lejárata (élettartama)
Az üzenetben szereplő hasznos adatok, illetve az üzenetek fogadóknak küldött utasításait vagy lekérdezéseit szinte mindig az alkalmazási szintű lejárati határidő valamilyen formája adja. Az ilyen határidő lejárta után a rendszer már nem továbbítja a tartalmat, vagy a kért művelet már nem lesz végrehajtva.

Olyan fejlesztési és tesztelési környezetekben, amelyekben gyakran használják a várólistákat és a témaköröket az alkalmazások és az alkalmazások részleges futtatásának kontextusában, azt is érdemes használni, hogy a rendszer az átállási teszteket automatikusan begyűjtse, hogy a következő teszt futtatása megtisztítsa.

Az egyes üzenetek elévülését az **élettartam** rendszer tulajdonság beállításával szabályozhatja, amely a relatív időtartamot határozza meg. Ha az üzenetet várólistán lévő az entitásba, a lejárati idő abszolút lesz. Ebben az időben a **lejárati idő (UTC)** tulajdonság az **várólistán lévő-UTC**-időpontok közötti időszakot veszi igénybe  +  . A közvetített üzenetekben az élettartam (TTL) beállítása nem érvényesül, ha nincsenek aktívan figyelt ügyfelek.

A **lejárati idő lejártakor** az üzenetek lekérésére nem jogosultak. A lejárat nem érinti a kézbesítéshez jelenleg zárolt üzeneteket. Ezeket az üzeneteket a rendszer továbbra is szabályosan kezeli. Ha a zárolás lejár, vagy az üzenet el lett hagyva, a lejárat azonnal érvénybe lép.

Amíg az üzenet zárolva van, előfordulhat, hogy az alkalmazás lejárt üzenettel rendelkezik. Azt jelzi, hogy az alkalmazás készen áll-e a feldolgozásra, vagy úgy dönt, hogy elhagyja az üzenetet a végrehajtónak.

A rendkívül alacsony élettartam az ezredmásodpercek és másodpercek sorrendjében az üzenetek lejáratát okozhatja, mielőtt a fogadó alkalmazások megkapják azt. Vegye figyelembe az alkalmazáshoz használható legmagasabb ÉLETTARTAMot.

## <a name="entity-level-expiration"></a>Entitások szintjének lejárata
A várólistába vagy témakörbe küldött összes üzenet az entitás szintjén beállított alapértelmezett lejárati idő alá esik. Később is beállítható a portálon a létrehozás és a módosítás során. Az alapértelmezett lejáratot a rendszer az entitásnak küldött összes üzenethez használja, ahol az élettartam nincs explicit módon beállítva. Az alapértelmezett lejárati idő felső határként is működik az élettartam értékeként. Azok az üzenetek, amelyek hosszabb élettartamú lejárattal rendelkeznek, mint az alapértelmezett érték, a várólistán lévő előtt csendben módosítva lesznek az üzenet alapértelmezett élettartama értékre.

> [!NOTE]
> A felügyelt üzenetekhez tartozó alapértelmezett élettartam értéke a 64 bites egész szám legnagyobb lehetséges értéke, ha nincs más megadva.
>
> Az üzenetküldési entitások (várólisták és témakörök) esetében az alapértelmezett lejárati idő a Service Bus standard és a prémium szint esetében is a legnagyobb lehetséges érték a besorolt 64 bites egész számhoz. Az alapszintű **csomag** esetében az alapértelmezett (maximális) lejárati idő **14 nap**.

A lejárt üzenetek nem helyezhetők át [kézbesíthető levelek várólistába](service-bus-dead-letter-queues.md). Ezt a beállítást programozott módon vagy a Azure Portal használatával is konfigurálhatja. Ha a beállítás le van tiltva, a rendszer elveti a lejárt üzeneteket. A kézbesítetlen levelek várólistára helyezett lejárt üzenetek megkülönböztetni más kézbesítetlen üzenetektől, ha kiértékeli a felhasználó tulajdonságai szakaszban a közvetítő által tárolt [kézbesítetlen levelek oka](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) tulajdonságot. 

Abban az esetben, amikor az üzenet védett a lejárati idő alatt a zárolás alatt, és ha a jelző be van állítva az entitásra, a rendszer áthelyezi az üzenetet a kézbesítetlen levelek várólistába, mivel a zárolás elhagyható vagy lejár. Azonban a rendszer nem helyezi át, ha az üzenet sikeres rendezése megtörtént, ami azt feltételezi, hogy az alkalmazás sikeresen kezelte azt a névleges lejárati idő ellenére.

Az élettartam és az automatikus (és tranzakciós) lejárati idő kombinációja olyan értékes eszköz, amellyel biztos lehet abban, hogy egy kezelőnek vagy kezelői csoportnak adott feladatot a határidő elérésekor lekéri a rendszer.

Tegyük fel például, hogy egy olyan webhelynek, amely megbízhatóan hajtja végre a feladatokat egy méretezéssel korlátozott háttérrendszer esetében, és amely esetenként a forgalmi csúcsokat tapasztalja, vagy az adott háttérrendszer rendelkezésre állási epizódjában szeretne szigetelni. A normál esetben az elküldött felhasználói adatok kiszolgálóoldali kezelője leküldi az adatokat egy várólistába, és később a tranzakció sikeres kezelését a válasz-várólistába fogadja. Ha van forgalmi tüske, és a háttér-kezelő nem tudja időben feldolgozni a várakozó elemeket, a rendszer a lejárt feladatokat a kézbesítetlen levelek várólistáján adja vissza. Az interaktív felhasználó értesítést kaphat arról, hogy a kért művelet a szokásosnál valamivel hosszabb ideig tart, és a kérést egy másik várólistára helyezheti a feldolgozási útvonalon, ahol a végleges feldolgozási eredményeket a rendszer e-mailben küldi el a felhasználónak. 


## <a name="temporary-entities"></a>Ideiglenes entitások

Service Bus várólisták, témakörök és előfizetések ideiglenes entitásként hozhatók létre, amelyeket a rendszer automatikusan eltávolít, ha nem használták a megadott ideig.
 
Az automatikus tisztítás olyan fejlesztési és tesztelési helyzetekben hasznos, amelyekben az entitások dinamikusan jönnek létre, és a használat után nem törlődnek, mert a teszt vagy a hibakeresés futtatása megszakad. Akkor is hasznos, ha egy alkalmazás dinamikus entitásokat (például egy válasz-várólistát) hoz létre a válaszok fogadásához egy webkiszolgálói folyamatba, vagy egy másik viszonylag rövid életű objektumba, ahol nehéz megbízhatóan megtisztítani ezeket az entitásokat, amikor az objektum példánya eltűnik.

A szolgáltatás engedélyezve van a névtér **automatikus törlés inaktív** tulajdonságának használatával. Ez a tulajdonság arra az időtartamra van beállítva, ameddig az entitásnak tétlennek kell lennie (használaton kívül), mielőtt automatikusan törölné. A tulajdonság minimális értéke 5.
 
## <a name="idleness"></a>Semmittevés

Az entitások (várólisták, témakörök és előfizetések) tétlensége az alábbiak szerint történik:

- Üzenetsorok
    - Nincs küldés  
    - Nincs fogadás  
    - Nincsenek frissítések a várólistához  
    - Nincsenek ütemezett üzenetek  
    - Nincs Tallózás/betekintés 
- Témakörök  
    - Nincs küldés  
    - Nincsenek frissítések a témakörhöz  
    - Nincsenek ütemezett üzenetek 
- Előfizetések
    - Nincs fogadás  
    - Nincs frissítés az előfizetéshez  
    - Nincs új szabály hozzáadva az előfizetéshez  
    - Nincs Tallózás/betekintés  
 

## <a name="next-steps"></a>Következő lépések

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
