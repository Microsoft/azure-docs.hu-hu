---
title: Forrás-és Origin-csoport az Azure bejárati ajtó standard/prémium
description: Ez a cikk azt ismerteti, hogy a forrás-és a származási csoport egy Azure-beli bejárati ajtó konfigurációjában van
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 42a9a0ea23faa481140a46ec52b2214431dd723e
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100084"
---
# <a name="origin-and-origin-group-in-azure-front-door-standardpremium-preview"></a>Forrás-és származási csoport az Azure-beli előtérben standard/prémium (előzetes verzió)

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Ez a cikk bemutatja, hogyan működik a webalkalmazás-telepítés az Azure bejárati ajtó standard/Premium szolgáltatásával. Arról is tájékozódhat, hogy a *forrás* és a *származási csoport* hogyan szerepel az Azure bejárati ajtó standard/prémium konfigurációjában.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="origin"></a>Forrás

Az Azure bejárati ajtó standard/Premium forrása az ügyfél kéréseinek kiszolgálására szolgáló állomásnévre vagy nyilvános IP-címére vonatkozik. Az Azure bejárati ajtó standard/Premium verziója az Azure-t és a nem Azure-beli erőforrásokat is támogatja a Origin csoportban. Az alkalmazás a helyszíni adatközpontban vagy egy másik felhőalapú szolgáltatón is üzemeltethető. A forrás nem tévesztendő össze az adatbázis-szintjével vagy a tárolási szintjével. A forrást az alkalmazás-háttér nyilvános végpontjának kell tekinteni. Amikor felvesz egy forrást egy Azure-beli előtérben lévő standard/prémium eredetű csoportba, a következő adatokat is hozzá kell adnia:

* **Forrás típusa:** A hozzáadni kívánt erőforrás típusa. A bejárati ajtó támogatja az alkalmazási hátterek automatikus észlelését App Service, Cloud Service vagy Storage szolgáltatásból. Ha egy másik erőforrást szeretne használni az Azure-ban, vagy akár egy nem Azure-beli hátteret is, válassza az **Egyéni gazdagép** lehetőséget.

    >[!IMPORTANT]
    >A konfiguráció során az API-k nem ellenőrzik, hogy a forrás nem érhető-e el az előtérben lévő környezetből. Győződjön meg arról, hogy a bejárati ajtó elérheti a forrást.

* **Előfizetés és forrás állomásneve:** Ha nem választotta ki az **Egyéni gazdagép** elemet a háttérrendszer-típushoz, válassza ki a megfelelő előfizetést, és adja meg a háttérbeli állomásnév megfelelő nevét.

* **Forrás gazdagépének fejléce:** A gazdagép fejlécének értéke minden kérelem esetében a háttérbe kerül. További információ: [Origin Host header](#hostheader).

* **Prioritás:** Rendeljen prioritásokat a különböző forrásokhoz, ha elsődleges forrást szeretne használni az összes forgalomhoz. Adja meg a biztonsági mentést is, ha az elsődleges vagy a biztonsági mentés eredete nem érhető el. További információ: Priority ( [prioritás](#priority)).

* **Súlyozás:** A különböző forrásokhoz tartozó súlyok kiosztásával terjesztheti a forgalmat egy készleten belül, akár egyenletesen, akár súlyozási együtthatók alapján. További információ: [súlyok](#weighted).

### <a name="origin-host-header"></a><a name = "hostheader"></a>Forrás állomásfejléce

Az Azure bejárati ajtó standard/Premium által a forrásnak továbbított kérések egy állomásfejléc-mezőt is tartalmaznak, amelyet a forrás használ a célként megadott erőforrás lekéréséhez. A mező értéke általában a állomásfejléc és a portot tartalmazó forrás URI azonosítóból származik.

A (z) rendszerre vonatkozó kérelem például `www.contoso.com` a gazdagép fejlécét fogja tartalmazni `www.contoso.com` . Ha a forrást a Azure Portal használatával konfigurálja, a mező alapértelmezett értéke a háttér állomásneve. Ha a forrás a Azure Portal, akkor a `contoso-westus.azurewebsites.net` forrásként szolgáló gazdagép fejlécének automatikusan feltöltött értéke lesz `contoso-westus.azurewebsites.net` . Ha azonban Azure Resource Manager sablonokat vagy egy másik módszert használ a mező explicit beállítása nélkül, akkor a bejárati ajtó a gazdagép fejlécének értékeként elküldi a bejövő állomásnév nevét. Ha a kérést a (z) alkalmazáshoz kapta, `www.contoso.com` és a forrásának `contoso-westus.azurewebsites.net` üres a fejléce, akkor a bejárati ajtó a következőképpen állítja be a gazdagép fejlécét: `www.contoso.com` .

A legtöbb alkalmazás-háttér (az Azure Web Apps, a blob Storage és a Cloud Services) megköveteli, hogy a gazdagép fejléce megfeleljen a háttér tartományának. Azonban a háttérrendszer által használt előtér-gazdagép egy másik állomásnevet fog használni, például: `www.contoso.net` .

Ha a forrás megköveteli, hogy a gazdagép fejléce megfeleljen a háttérbeli állomásnévnek, győződjön meg arról, hogy a háttérbeli állomásfejléc tartalmazza a háttér állomásnevét.

#### <a name="configuring-the-origin-host-header-for-the-origin"></a>A forrásként szolgáló állomásfejléc konfigurálása a forráshoz

A forrásként szolgáló **gazdagép fejlécének** beállítása a forrás csoport szakaszban található forráshoz:

1. Nyissa meg az előtérben lévő erőforrást, és válassza ki a kezdőpontot a konfigurálandó forrással.

2. Ha még nem tette meg, vegyen fel egy forrást, vagy szerkesszen egy meglévőt.

3. Állítsa a forrásként szolgáló állomásfejléc mezőt egyéni értékre, vagy hagyja üresen. A rendszer a bejövő kérelem állomásneveként fogja használni a gazdagép fejlécének értékét.

## <a name="origin-group"></a>Forrás csoport

Az Azure-beli bejárati ajtó standard/prémium szintű csoportja az olyan származási csoportra utal, amely hasonló adatforgalmat fogad az alkalmazáshoz. Más szóval az alkalmazás példányainak logikai csoportosítása a világon, amely ugyanazt a forgalmat kapja, és a várt működésre reagál. Ezek az eredetek különböző régiókban vagy ugyanazon a régión belül helyezhetők üzembe. Az összes eredet lehet aktív/aktív üzembe helyezési módban, vagy az aktív/passzív konfigurációként definiált érték.

A származási csoport határozza meg, hogy az eredetek hogyan legyenek kiértékelve az állapot-mintavételek használatával. Azt is meghatározza, hogyan történik a terheléselosztás a közöttük.

### <a name="health-probes"></a>Állapotminták

Az Azure bejárati ajtó standard/Premium időszakos HTTP/HTTPS mintavételi kérelmeket küld az egyes konfigurált eredetekhez. A mintavételi kérelmek meghatározzák az egyes forrásokhoz tartozó közelséget és állapotot a végfelhasználói kérelmek elosztása érdekében. A származási csoport állapot-mintavételi beállításai határozzák meg, hogy az alkalmazás-háttérrendszer állapotának lekérdezése hogyan történjen meg. A terheléselosztási konfigurációhoz a következő beállítások érhetők el:

* **Elérési út**: a forrás csoport összes kezdőpontjának mintavételi kérelmeit használó URL-cím. Ha például az egyik eredete, `contoso-westus.azurewebsites.net` és az elérési út a/Probe/test.aspx értékre van állítva, akkor a bejövő környezetek, feltéve, hogy a protokoll a http, az állapot-mintavételi kérelmeket küldi a rendszernek `http://contoso-westus.azurewebsites.net/probe/test.aspx` .

* **Protokoll**: meghatározza, hogy a rendszer elküldje-e az állapot-mintavételi kérelmeket a bejárati ajtóról a http vagy a HTTPS protokoll használatával.

* **Metódus**: az állapotadatok küldéséhez használandó http-metódus. A lehetőségek közé tartozik a GET vagy a HEAD (alapértelmezett).
    > [!NOTE]
    > A háttérbeli terhelések és a költségek csökkentése érdekében a bejárati ajtó javasolja az állapot-mintavételek HEAD kérelmének használatát.

* **Időköz (másodperc)**: meghatározza az állapot-mintavételek gyakoriságát az eredetek számára, illetve azt az intervallumot, amelyben az egyes bejárati környezetek mintavételt küldenek.

    >[!NOTE]
    >A gyorsabb feladatátvétel érdekében állítsa az intervallumot alacsonyabb értékre. Minél alacsonyabb az érték, annál nagyobb az állapot mintavételi mennyisége, amelyet a hátterek kapnak. Ha például az intervallum 30 másodpercre van állítva, azaz a 100-es bejárati ajtó globálisan jelenik meg, az egyes háttérrendszer percenként körülbelül 200 mintavételi kérést fog kapni.

További információ: [Health](concept-health-probes.md)mintavétel.

### <a name="load-balancing-settings"></a>Terheléselosztási beállítások

A Origin csoport terheléselosztási beállításai határozzák meg, hogyan értékeljük ki az állapotadatok kiértékelését. Ezek a beállítások határozzák meg, hogy a forrás kifogástalan vagy sérült. Azt is bemutatják, hogyan kell terheléselosztást kiosztani a különböző eredetű források között a forrás csoportban. A terheléselosztási konfigurációhoz a következő beállítások érhetők el:

* **Minta mérete:** Meghatározza, hogy hány mintát kell figyelembe venni a származási állapot kiértékeléséhez.

* **Sikeres minta mérete:** Meghatározza a korábban említett minta méretét, a forrás kifogástalan meghívásához szükséges sikeres minták számát. Tegyük fel például, hogy a bejárati ajtó állapotának mintavételi intervalluma 30 másodperc, a minta mérete 5, a sikeres minta mérete pedig 3. Minden alkalommal, amikor kiértékeljük a forráshoz tartozó állapot-mintavételt, megvizsgáljuk az utolsó öt mintát 150 másodpercnél (5 x 30). Legalább három sikeres mintavételre van szükség ahhoz, hogy a forrást Kifogástalan állapotba lehessen nyilvánítani.

* **Késési érzékenység (extra késleltetés):** Meghatározza, hogy szeretné-e, hogy az Azure Door standard/Premium a kérést elküldje a forrásnak a késési mérési érzékenységi tartományon belül, vagy továbbítsa a kérést a legközelebbi háttérbe.

További információ: a [minimális késésen alapuló útválasztási módszer](#latency).

## <a name="routing-methods"></a>Útválasztási módszerek

Az Azure Door standard/Premium a különböző típusú forgalom-útválasztási módszerek segítségével határozza meg, hogyan irányítsa át a HTTP/HTTPS-forgalmat a különböző szolgáltatási végpontokra. Amikor az ügyfél a bejárati ajtót kéri, a konfigurált útválasztási módszert alkalmazza a rendszer, hogy a kérések továbbítása a legjobb háttér-példányra történjen. 

Négy forgalom-útválasztási módszer érhető el az Azure bejárati ajtó standard/Premium:

* **[Késés](#latency):** A késleltetés-alapú útválasztás biztosítja, hogy a rendszer a kérelmeket az érzékenységi tartományon belül elfogadható legalacsonyabb késési szintekre küldje. Alapvetően a felhasználói kéréseket a rendszer a hálózat késése szempontjából a "legközelebbi" háttérrendszer-készletre továbbítja.
* **[Prioritás](#priority):** Ha az összes forgalom kiszolgálásához elsődleges hátteret szeretne konfigurálni, akkor prioritásokat rendelhet a háttérekhez. A másodlagos háttérrendszer biztonsági mentése lehet, ha az elsődleges háttér elérhetetlenné válik.
* **[Súlyozott](#weighted):** A munkakörnyezetekhez súlyok is hozzárendelhetők, ha a forgalmat különböző háttérrendszer-készleteken szeretné terjeszteni. Akár egyenletesen, akár a súlyozási együtthatók alapján kívánja terjeszteni.

Az Azure bejárati ajtó standard/prémium konfigurációi közé tartozik a háttér-állapot és az automatikus azonnali globális feladatátvétel figyelése. További információ: háttér- [figyelés](concept-health-probes.md). A bejárati ajtó csak egyetlen útválasztási módszer alapján működhet. Az alkalmazás igényeitől függően azonban több útválasztási módszer is használható az optimális útválasztási topológia létrehozásához.

### <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Legalacsonyabb késések alapú forgalom – Útválasztás

A háttérrendszer két vagy több helyen történő üzembe helyezése javíthatja az alkalmazások rugalmasságát azáltal, hogy átirányítja a forgalmat a végfelhasználók számára "legközelebb" a célhelyre. Az alapértelmezett forgalom – a bejárati ajtó konfigurációjának útválasztási módszere a végfelhasználótól érkező kéréseket továbbítja a kérést befogadó ajtós környezet legközelebbi hátteréhez. Ez a megközelítés biztosítja, hogy a végfelhasználók a helyük alapján személyre szabott maximális teljesítményt kapjanak.

A "legközelebb" háttér nem feltétlenül a legközelebbi földrajzi távolság alapján mérve. Ehelyett a bejárati ajtó határozza meg a legközelebbi háttereket a hálózati késés mérésével.

Alább látható a teljes döntési folyamat:

| Elérhető háttérrendszer | Prioritás | Késési jel (állapot-mintavétel alapján) | Súlyozással |
|-------------| ----------- | ----------- | ----------- |
| Először válassza ki az összes engedélyezett és kifogástalan állapotú (200 OK) háttérrendszer állapotát. Ha hat háttér A, B, C, D, E és F, és ezek közül C nem kifogástalan, és az E le van tiltva. A rendelkezésre álló hátterek listája a, B, D és F.  | Ezt követően a legfontosabb prioritási háttér van kiválasztva a rendelkezésre állók közül. Ha az A, B és D háttérrendszer 1-es prioritással rendelkezik, és az F prioritása 2. Ezt követően a kiválasztott háttérrendszer A, B és D lesz.| Válassza ki a késési tartománnyal rendelkező háttereket (a megadott ms-os késleltetési idő & késési érzékenysége). Ha a háttérrendszer 15 MS, a B 30 MS, a D 60 pedig az a bejárati ajtós környezettől, ahol a kérelem bekerül, és a késési érzékenység 30 MS, akkor a legalacsonyabb késési készlet az A és B háttérből áll, mivel a D az a legközelebbi háttértől számítva 30 MS-ra esik. | Végül a bejárati ajtó a kiválasztott súlyok arányában a háttérrendszer utolsó kijelölt készlete közötti adatforgalomra kerekíti a forgalmat. Tegyük fel, hogy ha az A backend 5 és a "B" háttérbeli súlyozása 8, akkor a forgalom a 5:8 arányban lesz elosztva az A és A B háttérrendszer között. |

>[!NOTE]
> Alapértelmezés szerint a késleltetési érzékenység tulajdonság értéke 0 MS, azaz mindig továbbítja a kérést a leggyorsabb rendelkezésre állási háttérnek.

### <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Prioritáson alapuló forgalom – Útválasztás

A vállalatok gyakran szeretnék magas rendelkezésre állást biztosítani szolgáltatásaik számára azáltal, hogy egynél több biztonsági mentési szolgáltatást helyeznek üzembe, ha az elsődleges leállású. Az iparágban ezt a topológiát aktív/készenléti vagy aktív/passzív telepítési topológiának is nevezzük. A "prioritás" forgalom – az útválasztási módszer lehetővé teszi, hogy az Azure-ügyfelek könnyedén implementálják ezt a feladatátvételi mintát.

Az alapértelmezett bejárati ajtó a háttérrendszer azonos prioritású listáját tartalmazza. Alapértelmezés szerint a bevezető ajtó csak a legfontosabb prioritási háttérrendszer (a prioritás legalacsonyabb értéke) esetében küld forgalmat, azaz a háttérrendszer elsődleges készletét. Ha az elsődleges háttér nem érhető el, a bevezető ajtó a háttérrendszer másodlagos készletére irányítja át a forgalmat (a prioritás második legalacsonyabb értéke). Ha az elsődleges és a másodlagos háttérrendszer nem érhető el, a forgalom a harmadikra kerül, és így tovább. A háttérrendszer rendelkezésre állása a beállított állapoton (engedélyezve vagy letiltva), valamint az állapotú Szondák által meghatározott aktuális háttérbeli állapoton alapul.

#### <a name="configuring-priority-for-backends"></a>A háttérrendszer prioritásának konfigurálása

A bejárati ajtó konfigurációjában az összes háttérrendszer a "priority" nevű tulajdonsággal rendelkezik, amely 1 és 5 közötti szám lehet. Az Azure bejárati ajtót használva az egyes háttérrendszer esetében explicit módon konfigurálja a háttér prioritását. Ez a tulajdonság 1 és 5 közötti érték. Az alacsonyabb értékek magasabb prioritást jelentenek. A hátterek megoszthatják a prioritási értékeket.

### <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Súlyozott forgalom – útválasztási módszer
A "súlyozott" forgalom-útválasztási módszer lehetővé teszi, hogy egyenletesen ossza el a forgalmat, vagy használjon előre definiált súlyozást.

A súlyozott forgalom – útválasztási módszernél a háttérbeli készlet előtérben lévő konfigurációjában minden egyes háttérhez hozzá kell rendelni a súlyozást. A súlyok 1 és 1000 közötti egész számok. Ez a paraméter az alapértelmezett "50" súlyozást használja.

Az elfogadható késési érzékenységgel rendelkező rendelkezésre álló hátterek listájával a forgalom a megadott súlyok arányának megfelelő ciklikus multiplexelés használatával kerül elosztásra. Ha a késési érzékenység 0 ezredmásodpercre van beállítva, akkor ez a tulajdonság nem lép érvénybe, kivéve, ha két háttérrel rendelkezik ugyanazzal a hálózati késéssel. 

A súlyozott módszer néhány hasznos forgatókönyvet is lehetővé tesz:

* Az **alkalmazások fokozatos frissítése**: az új háttér felé irányuló adatforgalom százalékos arányát adja meg, és fokozatosan növelheti a forgalmat az idő múlásával, hogy a többi háttérbe kerüljön.
* **Alkalmazások áttelepítése az Azure**-ba: hozzon létre egy háttér-készletet az Azure-ban és a külső háttérrendszer használatával. Állítsa be a háttérrendszer súlyozását úgy, hogy az új háttérrendszer legyen előnyben részesíteni. Fokozatosan beállíthatja, hogy az új háttérrendszer le legyen tiltva, majd hozzárendeli a legalacsonyabb súlyozást, és lassan növelje azokat a szinteket, ahol a legtöbb forgalmat megkezdik. Végül tiltsa le a kevésbé preferált háttereket, és távolítsa el őket a készletből.  
* **Felhőbeli felskálázás a további kapacitáshoz**: gyorsan kiterjesztheti a helyszíni üzembe helyezést a felhőbe azáltal, hogy az első ajtót helyezi el. Ha további kapacitásra van szüksége a felhőben, több háttér hozzáadására és engedélyezésére is lehetősége van, és megadhatja, hogy a forgalom milyen hányada kerüljön az egyes háttérbe.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [hozhat létre standard/prémium szintű bejárati ajtót](create-front-door-portal.md)
