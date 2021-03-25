---
title: Alkalmazási forgatókönyvek és kialakítás
description: A felhőalapú alkalmazások kategóriáinak áttekintése Service Fabricban. Ismerteti az alkalmazás kialakítását, amely állapot-nyilvántartó és állapot nélküli szolgáltatásokat használ.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 7d9459e6e0168657cf8e9062331fec0237f58c70
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045756"
---
# <a name="service-fabric-application-scenarios"></a>Alkalmazás-forgatókönyvek Service Fabric

Az Azure Service Fabric megbízható és rugalmas platformot kínál, ahol számos különböző típusú üzleti alkalmazást és szolgáltatást írhat és futtathat. Ezek az alkalmazások és a szolgáltatások állapot nélküliek vagy állapot-nyilvántartók lehetnek, és a hatékonyság maximalizálása érdekében erőforrás-kiegyensúlyozottak a virtuális gépek között.

A Service Fabric egyedi architektúrája lehetővé teszi a közel valós idejű adatelemzést, a memórián belüli számításokat, a párhuzamos tranzakciókat és az alkalmazásokban végzett események feldolgozását. A változó erőforrás-követelményektől függően egyszerűen és akár ki is méretezheti az alkalmazásokat.

Az alkalmazások létrehozásával kapcsolatos tervezési útmutatásért olvassa el a [Services architektúra az Azure Service Fabric](/azure/architecture/reference-architectures/microservices/service-fabric) és [az alkalmazások tervezésére vonatkozó ajánlott eljárásokat Service Fabric használatával](service-fabric-best-practices-applications.md)című témakört.

Érdemes lehet a Service Fabric platformot használni a következő típusú alkalmazásokhoz:

* **Adatgyűjtés, feldolgozás és IoT**: a Service Fabric nagy léptéket kezel, és alacsony késéssel jár az állapot-nyilvántartó szolgáltatásain keresztül. Több millió eszközön képes feldolgozni az adatmennyiséget, ahol az eszközhöz és a számítási feladatsorhoz tartozó adatmennyiség található.

    Azok az ügyfelek, akik a Service Fabric használatával építették IoT-szolgáltatásokat, például a [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), a [PCL-konstrukció](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), a [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), a  [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), a [Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)és a [Mesh rendszerek](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Játékok és munkamenet-alapú interaktív alkalmazások**: Service Fabric akkor lehet hasznos, ha az alkalmazás kis késleltetésű olvasást és írást igényel, például online játékokban vagy azonnali üzenetküldésben. A Service Fabric lehetővé teszi az interaktív, állapot-nyilvántartó alkalmazások összeállítását anélkül, hogy külön tárolót vagy gyorsítótárat kellene létrehoznia. Tekintse meg az [Azure-játékok megoldásait](https://azure.microsoft.com/solutions/gaming/) , amelyekkel megtudhatja, hogyan [használhatja a Service Fabrict a játékokban](/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    A [következő játékokat](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) és [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)is tartalmazó ügyfeleinknek készültek. Azok az ügyfelek, akik beépített interaktív munkamenetekkel rendelkeznek [, a Honeywellt is tartalmazzák a Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens).

* **Adatelemzés és munkafolyamat-feldolgozás**: olyan alkalmazások, amelyeknek megbízhatóan kell feldolgozniuk az eseményeket vagy adatfolyamokat a Service Fabric optimalizált olvasási és írási feladatokból. A Service Fabric támogatja az alkalmazások feldolgozási folyamatait is, ahol az eredményeknek megbízhatónak kell lenniük, és a következő feldolgozási fázisba kell esniük veszteség nélkül. Ezek a folyamatok tranzakciós és pénzügyi rendszereket tartalmaznak, amelyekben az adatkonzisztencia és a számítási garanciák elengedhetetlenek.

    Azok az ügyfelek, akik üzleti munkafolyamat-szolgáltatásokat készítettek, például a [Zeiss Group](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), a [kvórum üzleti megoldásai](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)és a [Société General](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Adatszámítások**: Service Fabric lehetővé teszi az intenzív adatszámítást igénylő állapot-nyilvántartó alkalmazások kiépítését. Service Fabric lehetővé teszi a feldolgozás (számítás) és az alkalmazások alkalmazásokban való elhelyezését. 

   Ha az alkalmazás adatelérést igényel, a külső adatgyorsítótárhoz vagy a tárolási csomaghoz tartozó hálózati késés korlátozza a számítási időt. Az állapot-nyilvántartó Service Fabric szolgáltatások kizárja ezt a késést, és lehetővé teszi az optimalizált olvasási és írási műveleteket.

   Vegyünk például egy olyan alkalmazást, amely közel valós idejű ajánlásokat tesz lehetővé az ügyfelek számára, és egy időkorlátot 100 ezredmásodpercnél kevesebb időt vesz igénybe. Service Fabric szolgáltatások késése és teljesítménybeli jellemzői rugalmas élményt nyújtanak a felhasználónak, a standard implementációs modellel összehasonlítva, a szükséges adatok távoli tárterületről való beolvasásához. A rendszer sokkal rugalmasabb, mert a kiválasztási javaslat kiszámításának helye az adatmennyiség és a szabályok.

    A kiépített számítási szolgáltatásokat magában foglalja a [Solidsoft reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) és a [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Nagy rendelkezésre állású szolgáltatások**: Service Fabric gyors feladatátvételt biztosít több másodlagos szolgáltatás replikájának létrehozásával. Ha egy csomópont, folyamat vagy egyedi szolgáltatás hardver vagy más hiba miatt leáll, az egyik másodlagos replika egy elsődleges replikára kerül elő, amelynek a minimális elvesztése a szolgáltatás.

* **Skálázható szolgáltatások**: az egyes szolgáltatások particionálható, ami lehetővé teszi, hogy a rendszer kibővítse az állapotot a fürtön belül. Az egyes szolgáltatások menet közben is létrehozhatók és eltávolíthatók. Több példányon is kibővítheti a szolgáltatásokat néhány csomóponton, több ezer példányban számos csomóponton, majd szükség szerint méretezheti azokat újra. A Service Fabric használatával felépítheti ezeket a szolgáltatásokat, és kezelheti a teljes életciklusát.

## <a name="application-design-case-studies"></a>Alkalmazás-tervezési esettanulmányok

Esettanulmányok, amelyek bemutatják, hogyan történik a Service Fabric az alkalmazások megtervezésére az [Azure-](https://azure.microsoft.com/solutions/microservice-applications/) webhelyeken az [ügyfél történeteit](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc) és a szolgáltatásait.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Állapot nélküli és állapot-nyilvántartó szolgáltatásokból álló alkalmazások tervezése

Az Azure Cloud Services feldolgozói szerepkörökkel rendelkező alkalmazások kiépítése az állapot nélküli szolgáltatásokra mutat példát. Ezzel szemben az állapot-nyilvántartó szolgáltatások a kérésen és a válaszon túl megőrzik a mérvadó állapotukat. Ez a funkció magas rendelkezésre állást és konzisztenciát biztosít az állapotnak olyan egyszerű API-k segítségével, amelyek a replikáció által támogatott tranzakciós garanciákat biztosítanak.

A Service Fabric állapot-nyilvántartó szolgáltatásai magas rendelkezésre állást biztosítanak az összes típusú alkalmazáshoz, nem csak adatbázisokhoz és más adattárakhoz. Ez természetes folyamat. Az alkalmazások a NoSQL-adatbázisok magas rendelkezésre állása érdekében már tisztán összehasonlított adatbázisok használatával lettek áthelyezve. Az alkalmazások maguk is rendelkezhetnek a "forró" állapottal és a bennük kezelt adatokkal a teljesítmény növelése érdekében, a megbízhatóság, a konzisztencia vagy a rendelkezésre állás feláldozása nélkül.

Ha olyan alkalmazásokat fejleszt, amelyek a-szolgáltatásokból állnak, jellemzően az állapot nélküli webalkalmazások (például a ASP.NET és a Node.js) kombinációja jelent állapot nélküli és állapot-nyilvántartó üzleti középszintű szolgáltatásokat. Az alkalmazások és szolgáltatások mind ugyanabban a Service Fabric-fürtben vannak telepítve a Service Fabric telepítési parancsok használatával. Ezek a szolgáltatások a méretezés, a megbízhatóság és az erőforrás-használat tekintetében függetlenek egymástól. Ez a függetlenség javítja a fejlesztés és az életciklus-kezelés rugalmasságát és rugalmasságát.

Az állapot-nyilvántartó szolgáltatások leegyszerűsítik az alkalmazások kialakítását, mivel azok nem igénylik a kizárólag állapot nélküli alkalmazások rendelkezésre állási és késési követelményeinek kielégítéséhez szükséges további várólisták és gyorsítótárak szükségességét. Mivel az állapot-nyilvántartó szolgáltatások magas rendelkezésre állással és alacsony késéssel rendelkeznek, kevesebb részletet kell kezelni az alkalmazásban.

Az alábbi ábrák az állapot nélküli alkalmazások megtervezése és az egyik állapot közötti különbséget szemléltetik. A [Reliable Services](service-fabric-reliable-services-introduction.md) és [Reliable Actors](service-fabric-reliable-actors-introduction.md) programozási modellek előnyeit kihasználva az állapot-nyilvántartó szolgáltatások csökkentik az alkalmazások összetettségét a nagy átviteli sebesség és az alacsony késés elérése mellett.

Az alábbi példa az állapot nélküli szolgáltatásokat használó alkalmazást használja: ![ az állapot nélküli szolgáltatásokat használó alkalmazás][Image1]

Az alábbi példa egy állapot-nyilvántartó szolgáltatásokat használó alkalmazás: ![ állapot-nyilvántartó szolgáltatásokat használó alkalmazás][Image2]

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg az állapot nélküli és állapot-nyilvántartó szolgáltatásokkal a Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) és a [Reliable Actors](service-fabric-reliable-actors-get-started.md) programozási modellekkel.
* Látogasson el a Azure Architecture Centerra, és útmutatást nyújt az Azure-beli [szolgáltatások létrehozásához](/azure/architecture/microservices/).
* Az alkalmazások kialakítására vonatkozó útmutatásért keresse fel az [Azure Service Fabric alkalmazást és a fürt ajánlott eljárásait](./service-fabric-best-practices-security.md) .

* Lásd még:
  * [A mikroszolgáltatások ismertetése](service-fabric-overview-microservices.md)
  * [A szolgáltatás állapotának meghatározása és kezelése](service-fabric-concepts-state.md)
  * [A szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
  * [Szolgáltatások méretezése](service-fabric-concepts-scalability.md)
  * [Partition Services](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png