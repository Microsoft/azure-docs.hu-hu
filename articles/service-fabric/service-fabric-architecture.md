---
title: Az Azure Service Fabric architektúrája
description: Ez a cikk ismerteti a Service Fabric architektúráját, amely egy elosztott rendszerplatform, amely méretezhető, megbízható és könnyen felügyelt alkalmazások létrehozására szolgál a felhőben.
services: service-fabric
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 972700dded1841994de9252b4aa4bbc8eaefeaf8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "76024713"
---
# <a name="service-fabric-architecture"></a>A Service Fabric-architektúra

A Service Fabric rétegzett alrendszerekkel készült. Ezek az alrendszerek lehetővé teszik a következő alkalmazások írására:

* Magas rendelkezésre állású
* Méretezhető
* Kezelhető
* Tesztelhető

Az alábbi ábrán a Service Fabric fő alrendszerei láthatók.

![Service Fabric architektúra ábrája](media/service-fabric-architecture/service-fabric-architecture.png)

Az elosztott rendszerekben elengedhetetlen a fürt csomópontjai közötti biztonságos kommunikáció. A verem alapja a átviteli alrendszer, amely biztonságos kommunikációt biztosít a csomópontok között. A szállítási alrendszer felett az összevonási alrendszer található, amely a különböző csomópontokat egyetlen entitásba (fürtökbe) helyezi, így a Service Fabric képes észlelni a hibákat, vezető választást végezni, és konzisztens útválasztást biztosítani. A megbízhatósági alrendszer, amely az összevonási alrendszer felett van, az a feladata, hogy Service Fabric szolgáltatások megbízhatóságát a replikálás, az erőforrás-kezelés és a feladatátvétel mechanizmusa segítségével hajtsa végre. Az összevonási alrendszer az üzemeltetési és aktiválási alrendszert is tartalmazza, amely egyetlen csomóponton kezeli az alkalmazások életciklusát. A felügyeleti alrendszer kezeli az alkalmazások és szolgáltatások életciklusát. A tesztelési alrendszer segíti az alkalmazások fejlesztői számára, hogy az alkalmazások és szolgáltatások éles környezetekben való üzembe helyezése előtt és után szimulált hibákon tesztelik szolgáltatásaikat. Service Fabric lehetővé teszi a szolgáltatási helyeknek a kommunikációs alrendszeren keresztüli feloldását. A fejlesztők számára elérhető programozási modellek ezeken az alrendszereken, valamint az alkalmazás modelljén is elérhetők, amelyek lehetővé teszik az eszközök bekapcsolását.

## <a name="transport-subsystem"></a>Szállítási alrendszer

A szállítási alrendszer egy pont-pont típusú datagram kommunikációs csatornát valósít meg. Ez a csatorna a Service Fabric-fürtökön belüli kommunikációhoz, valamint a Service Fabric-fürt és az ügyfelek közötti kommunikációhoz használatos. Támogatja az egyirányú és a kérés-válasz kommunikációs mintákat, amelyek a szórás és a csoportos küldés megvalósításának alapját képezik az összevonási rétegben. Az átviteli alrendszer X509-tanúsítványok vagy Windows-Biztonság használatával biztosítja a kommunikációt. Ezt az alrendszert a Service Fabric belsőleg használja, és nem érhető el közvetlenül a fejlesztők számára az alkalmazások programozásához.

## <a name="federation-subsystem"></a>Összevonási alrendszer

Az elosztott rendszer csomópontjaira vonatkozó okok miatt a rendszernek konzisztens nézettel kell rendelkeznie. Az összevonási alrendszer a szállítási alrendszer által biztosított kommunikációs primitíveket használja, és a különböző csomópontokat egyetlen egységesített fürthöz rendeli, amely az oka lehet. Ez biztosítja a más alrendszerek – a hibák észlelése, a vezető választása és a konzisztens útválasztás – által igényelt elosztott rendszerprimitíveket. Az összevonási alrendszer a 128 bites jogkivonat-hellyel rendelkező elosztott kivonatoló táblákra épül. Az alrendszer létrehoz egy gyűrűs topológiát a csomópontokon, és a gyűrű minden csomópontja kiosztotta a jogkivonatok területének egy részhalmazát a tulajdonos számára. A hiba észlelése érdekében a réteg a szívverés és a választottbírósági eljárás alapján a lízing mechanizmust használja. Az összevonási alrendszer a bonyolult csatlakozási és elindulási protokollokon keresztül is garantálja, hogy a jogkivonat csak egyetlen tulajdonosa létezik. Ez biztosítja a vezető választási és konzisztens útválasztási garanciákat.

## <a name="reliability-subsystem"></a>Megbízhatósági alrendszer

A megbízhatósági alrendszer biztosítja azt a mechanizmust, amely egy Service Fabric szolgáltatás állapotát a *replikátor*, a *feladatátvételi felügyelő* és az *erőforrás-Balancer* használatával igen elérhetővé teszi.

* A replikátor biztosítja, hogy az elsődleges szolgáltatási replika állapotának változásai automatikusan replikálódnak a másodlagos replikára, és megtartja a szolgáltatás replikájának elsődleges és másodlagos replikái közötti konzisztenciát. A replikátor a kópiakészlet replikái közötti kvórum-felügyeletért felelős. Együttműködik a feladatátvételi egységgel a replikálni kívánt műveletek listájának beolvasásához, az újrakonfigurálási ügynök pedig a replikakészlet konfigurációját biztosítja. Ez a konfiguráció jelzi, hogy mely replikákat kell replikálni a műveletekhez. Service Fabric a Fabric replikátor nevű alapértelmezett replikátort biztosít, amelyet a programozási modell API-val használhat a szolgáltatás állapotának nagyfokú rendelkezésre állása és megbízhatósága érdekében.
* A Feladatátvételi felügyelő biztosítja, hogy a csomópontok a fürthöz való hozzáadásakor vagy eltávolításakor a rendszer automatikusan újraosztja a terhelést az elérhető csomópontok között. Ha a fürt egyik csomópontja meghibásodik, a fürt automatikusan újrakonfigurálja a szolgáltatás replikáit a rendelkezésre állás fenntartása érdekében.
* A Resource Manager a fürtben található meghibásodási tartományokon keresztül replikálja a szolgáltatás replikáit, és gondoskodik arról, hogy minden feladatátvételi egység működőképes legyen. A Resource Manager emellett a fürtcsomópontok mögöttes megosztott készletében is kiegyenlíti a szolgáltatási erőforrásokat az optimális egységes terheléselosztás érdekében.

## <a name="management-subsystem"></a>Felügyeleti alrendszer

A felügyeleti alrendszer végpontok közötti szolgáltatást és az alkalmazások életciklusának felügyeletét biztosítja. A PowerShell-parancsmagok és a felügyeleti API-k lehetővé teszik az alkalmazások kiépítését, üzembe helyezését, javítását, frissítését és kiépítését a rendelkezésre állás elvesztése nélkül. A felügyeleti alrendszer a következő szolgáltatásokon keresztül hajtja végre ezt a szolgáltatást.

* **Fürtfelügyelő**: ez az elsődleges szolgáltatás, amely együttműködik a megbízhatósági feladatátvételi felügyelő az alkalmazások csomópontokon történő elhelyezéséhez a szolgáltatás elhelyezési megkötései alapján. A feladatátvételi alrendszer erőforrás-kezelője biztosítja, hogy a megkötések soha ne legyenek megszakítva. A Fürtfelügyelő felügyeli az alkalmazások életciklusát a kiépítéstől a kiépítés. Integrálva van az állapotfigyelő szolgáltatással, így biztosíthatja, hogy az alkalmazás rendelkezésre állása ne vesszen el a szemantikai állapot szempontjából a frissítések során.
* **Health Manager**: Ez a szolgáltatás lehetővé teszi az alkalmazások, szolgáltatások és fürtözött entitások állapotának figyelését. A fürtcsomópontok (például a csomópontok, a szolgáltatási partíciók és a replikák) jelenthetik az állapotadatok adatait, amelyek ezután összesítve vannak a központi Health Store-ba. Ez az állapotadatok átfogó időpontot biztosítanak a fürt több csomópontja között elosztott szolgáltatásokról és csomópontokról, lehetővé téve a szükséges javítási műveletek elvégzését. Az állapot-lekérdezési API-k lehetővé teszik az állapot-alrendszer számára jelentett állapotú események lekérdezését. Az állapotfigyelő lekérdezési API-k az állapotfigyelő tárolóban tárolt nyers egészségügyi adatokat vagy egy adott fürt entitásának összesített, értelmezett állapotát adják vissza.
* **Lemezképtároló**: Ez a szolgáltatás biztosítja az alkalmazás bináris fájljainak tárolását és terjesztését. Ez a szolgáltatás egy egyszerű elosztott adattárt biztosít, amelyen az alkalmazások fel lesznek töltve és letöltődnek.

## <a name="hosting-subsystem"></a>Üzemeltetési alrendszer

A Fürtfelügyelő tájékoztatja az üzemeltetési alrendszert (amely minden csomóponton fut), mely szolgáltatásokat kell felügyelni egy adott csomópont esetében. Az üzemeltetési alrendszer ezután kezeli az alkalmazás életciklusát ezen a csomóponton. Együttműködik a megbízhatóság és az állapot összetevőivel, így biztosítva, hogy a replikák megfelelően legyenek elhelyezve és kifogástalan állapotban legyenek.

## <a name="communication-subsystem"></a>Kommunikációs alrendszer

Ez az alrendszer megbízható üzenetküldést biztosít a fürtön belül, és a szolgáltatások felderítését az elnevezési szolgáltatáson keresztül. Az elnevezési szolgáltatás feloldja a szolgáltatások nevét a fürt egyik helyére, és lehetővé teszi a felhasználók számára a szolgáltatások nevének és tulajdonságainak kezelését. Az elnevezési szolgáltatás használatával az ügyfelek biztonságosan kommunikálhatnak a fürt bármely csomópontjához a szolgáltatás nevének feloldásához és a szolgáltatás metaadatainak lekéréséhez. Az egyszerű elnevezési ügyfél API-k használatával a Service Fabric-felhasználók olyan szolgáltatásokat és ügyfeleket hozhatnak létre, amelyek képesek az aktuális hálózati hely feloldására a csomóponti dinamizmus vagy a fürt átméretezése ellenére.

## <a name="testability-subsystem"></a>Tesztelési alrendszer

A tesztelési eszközök kifejezetten a Service Fabric-ra épülő szolgáltatások tesztelésére szolgálnak. Az eszközök lehetővé teszik a fejlesztők számára az értelmes hibák kiváltását és a tesztelési forgatókönyvek futtatását, hogy a számos államot és a szolgáltatás teljes élettartama alatt a szolgáltatások milyen mértékben tapasztalják, és mindezt ellenőrzött és biztonságos módon. A tesztelés egy olyan mechanizmust is biztosít, amely a rendelkezésre állás elvesztése nélkül képes megismételni a különböző lehetséges hibákat. Ez egy tesztelési célú környezettel rendelkezik.
