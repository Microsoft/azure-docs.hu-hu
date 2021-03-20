---
title: Azure bejárati ajtó – forgalom-útválasztási módszerek | Microsoft Docs
description: Ez a cikk segít megismerni a bejárati ajtó által használt különböző forgalom-útválasztási módszereket
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 2bc056620ff964747dfd83e7525cb5bfd2eb8e52
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91449149"
---
# <a name="front-door-routing-methods"></a>A bejárati ajtó útválasztási módszerei

Az Azure bevezető ajtaja különböző típusú forgalom-útválasztási módszereket támogat a HTTP-/HTTPS-forgalom különböző szolgáltatási végpontokra való továbbításának meghatározásához. Amikor az ügyfél a bejárati ajtót kéri, a konfigurált útválasztási módszert alkalmazza a rendszer, hogy a kérések továbbítása a legjobb háttér-példányra történjen. 

A bejárati ajtón négy forgalom-útválasztási módszer érhető el:

* **[Késés](#latency):** A késleltetés-alapú útválasztás biztosítja, hogy a rendszer a kérelmeket az érzékenységi tartományon belül elfogadható legalacsonyabb késési szintekre küldje. Alapvetően a felhasználói kéréseket a rendszer a hálózat késése szempontjából a "legközelebbi" háttérrendszer-készletre továbbítja.
* **[Prioritás](#priority):** Ha az összes forgalom kiszolgálásához elsődleges hátteret szeretne konfigurálni, akkor prioritásokat rendelhet a háttérekhez. A másodlagos háttérrendszer biztonsági mentése lehet, ha az elsődleges háttér elérhetetlenné válik.
* **[Súlyozott](#weighted):** A munkakörnyezetekhez súlyok is hozzárendelhetők, ha a forgalmat különböző háttérrendszer-készleteken szeretné terjeszteni. Akár egyenletesen, akár a súlyozási együtthatók alapján kívánja terjeszteni.
* **[Munkamenet-affinitás](#affinity):** Megadhatja a munkamenet-affinitást a előtér-gazdagépekhez vagy-tartományokhoz, így biztosítva, hogy az azonos végfelhasználótól érkező kérések ugyanarra a háttérre legyenek küldve.

A Front Door összes konfigurációja tartalmazza a háttérrendszer állapotának monitorozását és az automatizált azonnali globális feladatátvételt. További információ: az [első ajtó háttér-figyelése](front-door-health-probes.md). A bejárati ajtó csak egyetlen útválasztási módszer alapján működhet. Az alkalmazás igényeitől függően azonban több útválasztási módszer is használható az optimális útválasztási topológia létrehozásához.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Legalacsonyabb késések alapú forgalom – Útválasztás

A háttérrendszer két vagy több helyen történő üzembe helyezése javíthatja az alkalmazások rugalmasságát azáltal, hogy átirányítja a forgalmat a végfelhasználók számára "legközelebb" a célhelyre. Az alapértelmezett forgalom – a bejárati ajtó konfigurációjának útválasztási módszere a végfelhasználótól érkező kéréseket továbbítja a kérést befogadó ajtós környezet legközelebbi hátteréhez. Ez a megközelítés biztosítja, hogy a végfelhasználók a helyük alapján személyre szabott maximális teljesítményt kapjanak.

A "legközelebb" háttér nem feltétlenül a legközelebbi földrajzi távolság alapján mérve. Ehelyett a bejárati ajtó határozza meg a legközelebbi háttereket a hálózati késés mérésével. További információ a [bejárati ajtó útválasztási architektúráján](front-door-routing-architecture.md). 

Alább látható a teljes döntési folyamat:

| Elérhető háttérrendszer | Prioritás | Késési jel (állapot-mintavétel alapján) | Súlyozással |
|-------------| ----------- | ----------- | ----------- |
| Először válassza ki az összes engedélyezett és kifogástalan állapotú (200 OK) háttérrendszer állapotát. Ha hat háttér A, B, C, D, E és F, és ezek közül C nem kifogástalan, és az E le van tiltva. A rendelkezésre álló hátterek listája a, B, D és F.  | Ezt követően a legfontosabb prioritási háttér van kiválasztva a rendelkezésre állók közül. Ha az A, B és D háttérrendszer 1-es prioritással rendelkezik, és az F prioritása 2. Ezt követően a kiválasztott háttérrendszer A, B és D lesz.| Válassza ki a késési tartománnyal rendelkező háttereket (a megadott ms-os késleltetési idő & késési érzékenysége). Ha a háttérrendszer 15 MS, a B 30 MS, a D 60 pedig az a bejárati ajtós környezettől, ahol a kérelem bekerül, és a késési érzékenység 30 MS, akkor a legalacsonyabb késési készlet az A és B háttérből áll, mivel a D az a legközelebbi háttértől számítva 30 MS-ra esik. | Végül a bejárati ajtó a kiválasztott súlyok arányában a háttérrendszer utolsó kijelölt készlete közötti adatforgalomra kerekíti a forgalmat. Tegyük fel, hogy ha az A backend 5 és a "B" háttérbeli súlyozása 8, akkor a forgalom a 5:8 arányban lesz elosztva az A és A B háttérrendszer között. |

>[!NOTE]
> Alapértelmezés szerint a késleltetési érzékenység tulajdonság értéke 0 MS, azaz mindig továbbítja a kérést a leggyorsabb rendelkezésre állási háttérnek.

## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Prioritáson alapuló forgalom – Útválasztás

A vállalatok gyakran szeretnék magas rendelkezésre állást biztosítani szolgáltatásaik számára azáltal, hogy egynél több biztonsági mentési szolgáltatást helyeznek üzembe, ha az elsődleges leállású. Az iparágban ezt a topológiát aktív/készenléti vagy aktív/passzív telepítési topológiának is nevezzük. A "prioritás" forgalom – az útválasztási módszer lehetővé teszi, hogy az Azure-ügyfelek könnyedén implementálják ezt a feladatátvételi mintát.

Az alapértelmezett bejárati ajtó a háttérrendszer azonos prioritású listáját tartalmazza. Alapértelmezés szerint a bevezető ajtó csak a legfontosabb prioritási háttérrendszer (a prioritás legalacsonyabb értéke) esetében küld forgalmat, azaz a háttérrendszer elsődleges készletét. Ha az elsődleges háttér nem érhető el, a bevezető ajtó a háttérrendszer másodlagos készletére irányítja át a forgalmat (a prioritás második legalacsonyabb értéke). Ha az elsődleges és a másodlagos háttérrendszer nem érhető el, a forgalom a harmadikra kerül, és így tovább. A háttérrendszer rendelkezésre állása a beállított állapoton (engedélyezve vagy letiltva), valamint az állapotú Szondák által meghatározott aktuális háttérbeli állapoton alapul.

### <a name="configuring-priority-for-backends"></a>A háttérrendszer prioritásának konfigurálása

A bejárati ajtó konfigurációjában az összes háttérrendszer a "priority" nevű tulajdonsággal rendelkezik, amely 1 és 5 közötti szám lehet. Az Azure bejárati ajtót használva az egyes háttérrendszer esetében explicit módon konfigurálja a háttér prioritását. Ez a tulajdonság 1 és 5 közötti érték. Az alacsonyabb értékek magasabb prioritást jelentenek. A hátterek megoszthatják a prioritási értékeket.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Súlyozott forgalom – útválasztási módszer
A "súlyozott" forgalom-útválasztási módszer lehetővé teszi, hogy egyenletesen ossza el a forgalmat, vagy használjon előre definiált súlyozást.

A súlyozott forgalom – útválasztási módszernél a háttérbeli készlet előtérben lévő konfigurációjában minden egyes háttérhez hozzá kell rendelni a súlyozást. A súlyok 1 és 1000 közötti egész számok. Ez a paraméter az alapértelmezett "50" súlyozást használja.

Az elfogadható késési érzékenységgel rendelkező rendelkezésre álló hátterek listájával a forgalom a megadott súlyok arányának megfelelő ciklikus multiplexelés használatával kerül elosztásra. Ha a késési érzékenység 0 ezredmásodpercre van beállítva, akkor ez a tulajdonság nem lép érvénybe, kivéve, ha két háttérrel rendelkezik ugyanazzal a hálózati késéssel. 

A súlyozott módszer néhány hasznos forgatókönyvet is lehetővé tesz:

* Az **alkalmazások fokozatos frissítése**: az új háttér felé irányuló adatforgalom százalékos arányát adja meg, és fokozatosan növelheti a forgalmat az idő múlásával, hogy a többi háttérbe kerüljön.
* **Alkalmazások áttelepítése az Azure**-ba: hozzon létre egy háttér-készletet az Azure-ban és a külső háttérrendszer használatával. Állítsa be a háttérrendszer súlyozását úgy, hogy az új háttérrendszer legyen előnyben részesíteni. Fokozatosan beállíthatja, hogy az új háttérrendszer le legyen tiltva, majd hozzárendeli a legalacsonyabb súlyozást, és lassan növelje azokat a szinteket, ahol a legtöbb forgalmat megkezdik. Végül tiltsa le a kevésbé preferált háttereket, és távolítsa el őket a készletből.  
* **Felhőbeli felskálázás a további kapacitáshoz**: gyorsan kiterjesztheti a helyszíni üzembe helyezést a felhőbe azáltal, hogy az első ajtót helyezi el. Ha további kapacitásra van szüksége a felhőben, több háttér hozzáadására és engedélyezésére is lehetősége van, és megadhatja, hogy a forgalom milyen hányada kerüljön az egyes háttérbe.

## <a name="session-affinity"></a><a name = "affinity"></a>Munkamenet-affinitás
Alapértelmezés szerint a munkamenet-affinitás nélkül a bejárati ajtó továbbítja az azonos ügyféltől származó kérelmeket a különböző háttérrendszer számára. Bizonyos állapot-nyilvántartó alkalmazások vagy bizonyos forgatókönyvek, amelyek egy adott felhasználótól érkező kéréseket eredményeznek, ugyanazt a hátteret részesítik előnyben, mint a kezdeti kérés feldolgozása. A cookie-alapú munkamenet-affinitás akkor hasznos, ha ugyanazon a háttérrendszeren szeretne tartani egy felhasználói munkamenetet. A felügyelt cookie-k használatával az Azure bejárati ajtaja közvetlenül a felhasználói munkamenetből egy, a feldolgozásra szolgáló adatforgalomra irányíthatja át a forgalmat.

A munkamenet-affinitást az előtérbeli gazdagép szintjén engedélyezheti minden konfigurált tartomány (vagy altartomány) számára. Az engedélyezés után a Front Door hozzáad egy cookie-t a felhasználói munkamenethez. A cookie-alapú munkamenet-affinitás lehetővé teszi a Front Door számára, hogy azonos IP-cím esetén is azonosítsa a különböző felhasználókat, amely egyenletesebb forgalomelosztást tesz lehetővé a különböző háttérrendszerek között.

A cookie élettartama megegyezik a felhasználói munkamenet élettartamával, mivel a Front Door jelenleg csak a munkameneti cookie-kat támogatja. 

> [!NOTE]
> A nyilvános proxyk megakadályozhatják a munkamenet-affinitást. Ennek az az oka, hogy a munkamenet létrehozásához a bejárati ajtót kell hozzáadni egy munkamenet-affinitási cookie-nak a válaszhoz való hozzáadásához, ami nem végezhető el, ha a válasz gyorsítótárazható, mert az adott erőforrást kérő más ügyfelek cookie-jait megszakítja. Ennek elleni védelem érdekében a munkamenet-affinitás **nem** lesz létrehozva, ha a háttér-kezelő gyorsítótárazható választ küld a kísérlet során. Ha a munkamenet már létrejött, nem számít, hogy a háttérbeli válasz gyorsítótárazható-e.
> A munkamenet-affinitás a következő esetekben lesz létrehozva, **kivéve, ha** a válasz http 304 állapotkódot tartalmaz:
> - A válaszhoz megadott értékek vannak beállítva a ```Cache-Control``` fejléchez, amely megakadályozza a gyorsítótárazást, például "Private" vagy "Store".
> - A válasz olyan ```Authorization``` fejlécet tartalmaz, amely nem járt le.
> - A válasz HTTP 302 állapotkódot tartalmaz.

## <a name="next-steps"></a>Következő lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
