---
title: További információ az Azure Service Fabric
description: Ismerje meg az Azure Service Fabric legfontosabb alapfogalmait és főbb területeit. A részletes áttekintést nyújt a Service Fabricről és a szolgáltatások létrehozásáról.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: c709abe1087a9cc69c9e6e23cd1ff344a3dbebd2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589042"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Szeretne többet megtudni a Service Fabricról?
Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások csomagolását, üzembe helyezését és kezelését.  Service Fabric nagy felülettel rendelkezik, és sokat tanulhat.  Ez a cikk áttekintést nyújt a Service Fabricekről, valamint ismerteti az alapvető fogalmakat, programozási modelleket, az alkalmazások életciklusát, a tesztelést, a fürtöket és az állapot-figyelést Olvassa el az [áttekintést](service-fabric-overview.md) , és [Mik azok a szolgáltatások?](service-fabric-overview-microservices.md) a bevezetéshez és a Service Fabric használhatók a-szolgáltatások létrehozásához. Ez a cikk nem tartalmaz átfogó listát, de a Service Fabric összes területének áttekintésére és az első lépéseket ismertető cikkekre mutató hivatkozást tartalmaz. 

## <a name="core-concepts"></a>Alapfogalmak
[Service Fabric terminológia](service-fabric-technical-overview.md), az [alkalmazás modellje](service-fabric-application-model.md)és a [támogatott programozási modellek](service-fabric-choose-framework.md) több fogalmat és leírást biztosítanak, de itt vannak az alapjai.

### <a name="design-time-service-type-service-package-and-manifest-application-type-application-package-and-manifest"></a>Tervezési idő: szolgáltatástípus, szervizcsomag és jegyzékfájl, alkalmazás típusa, alkalmazáscsomag és jegyzékfájl
A szolgáltatás típusa a szolgáltatás kódjához, adatcsomagjaihoz és konfigurációs csomagjaihoz rendelt név/verzió. Ez egy ServiceManifest.xml fájlban van definiálva. A szolgáltatás típusa a végrehajtható kód és a szolgáltatás konfigurációs beállításaiból tevődik össze, amelyek a futási időben tölthetők be, és a szolgáltatás által felhasznált statikus adatmennyiségek.

A szervizcsomag a szolgáltatás típusának ServiceManifest.xml fájlját tartalmazó lemez, amely a szolgáltatás típusának kódját, statikus adatait és konfigurációs csomagjaira hivatkozik. Egy szolgáltatáscsomag például hivatkozhat az adatbázis-szolgáltatást alkotó kód, statikus és konfigurációs csomagokra.

Az alkalmazás típusa a szolgáltatás típusú gyűjteményhez rendelt név/verzió. Ez egy ApplicationManifest.xml fájlban van definiálva.

![Service Fabric az alkalmazások típusai és a szolgáltatások típusai][cluster-imagestore-apptypes]

Az alkalmazáscsomag egy olyan lemez-könyvtár, amely tartalmazza az alkalmazás típusát ApplicationManifest.xml fájlt, amely az alkalmazás típusát alkotó egyes szolgáltatástípus szolgáltatási csomagjaira hivatkozik. Egy e-mail-alkalmazáshoz tartozó alkalmazáscsomag például tartalmazhat üzenetsor-szolgáltatási csomagra, egy előtér-szolgáltatási csomagra és egy adatbázis-szolgáltatási csomagra mutató hivatkozásokat.  

Az alkalmazáscsomag könyvtárában lévő fájlokat a rendszer a Service Fabric-fürt rendszerkép-tárolójába másolja. Ezután létrehozhat egy elnevezett alkalmazást ebből az alkalmazásból, amely ezután a fürtön belül fut. Elnevezett alkalmazás létrehozása után létrehozhat egy elnevezett szolgáltatást az alkalmazás egyik szolgáltatástípus-típusáról. 

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Futási idő: fürtök és csomópontok, megnevezett alkalmazások, nevesített szolgáltatások, partíciók és replikák
A [Service Fabric-fürt](service-fabric-deploy-anywhere.md) olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait. A fürtök több ezer gépre skálázhatók.

A fürtök részét képező gépeket vagy virtuális gépeket csomópontoknak nevezzük. Minden csomóponthoz hozzá van rendelve egy csomópontnév (egy sztring). A csomópontok jellemzőkkel, például elhelyezési tulajdonságokkal rendelkeznek. Minden számítógép vagy virtuális gép rendelkezik egy automatikus indítású Windows-szolgáltatással, `FabricHost.exe` amely elindítja a rendszert a rendszerindítás után, majd elindítja a két végrehajtható fájl: `Fabric.exe` és `FabricGateway.exe` . Ez a két végrehajtható fájl alkotja a csomópontot. Fejlesztési vagy tesztelési helyzetekben több csomópontot is tárolhat egyetlen gépen vagy virtuális gépen a és a több példányának futtatásával `Fabric.exe` `FabricGateway.exe` .

Az elnevezett alkalmazások olyan nevesített szolgáltatások gyűjteményei, amelyek egy bizonyos függvényt vagy függvényt hajtanak végre. A szolgáltatás teljes és önálló funkciót hajt végre (a többi szolgáltatástól függetlenül elindítható és futtatható), és kód-, konfiguráció-és adatmennyiségből áll. Az alkalmazáscsomag a rendszerkép-tárolóba való másolása után a fürtön belül hozza létre az alkalmazás egy példányát az alkalmazáscsomag alkalmazási típusának (a neve/verziója használatával) megadásával. Minden Application Type-példányhoz egy URI-név tartozik, amely a következőhöz hasonló *: Fabric:/MyNamedApp*. Egy fürtön belül több névvel ellátott alkalmazást is létrehozhat egyetlen alkalmazás típusból. Elnevezett alkalmazásokat is létrehozhat különböző típusú alkalmazásokból. Az elnevezett alkalmazások felügyelete és verziója egymástól függetlenül történik.

Elnevezett alkalmazás létrehozása után létrehozhat egy, a fürtön belüli egyik szolgáltatástípus (egy nevesített szolgáltatás) egy példányát a szolgáltatás típusának megadásával (a név/verzió használatával). Minden egyes szolgáltatástípus-példányhoz hozzá van rendelve egy URI-név, amely az elnevezett alkalmazás URI-ja alá tartozik. Ha például létrehoz egy "MyDatabase" nevű szolgáltatást egy "MyNamedApp" nevű alkalmazásban, az URI a következőképpen néz ki: *Fabric:/MyNamedApp/MyDatabase*. Egy elnevezett alkalmazásban létrehozhat egy vagy több elnevezett szolgáltatást. Minden elnevezett szolgáltatás rendelkezhet saját partíciós sémával és példány/replika számokkal. 

A szolgáltatások két típusa létezik: állapot nélküli és állapot-nyilvántartó. Az állapot nélküli szolgáltatások nem tárolják az állapotot a szolgáltatáson belül. Az állapot nélküli szolgáltatások nem rendelkeznek állandó tárterülettel, vagy nem tárolnak állandó állapotot egy külső tárolási szolgáltatásban (például Azure Storage, Azure SQL Database vagy Azure Cosmos DB). Az állapot-nyilvántartó szolgáltatás a szolgáltatáson belül tárolja az állapotot, és megbízható gyűjtemények vagy Reliable Actors programozási modellek használatával kezeli az állapotot. 

Elnevezett szolgáltatás létrehozásakor meg kell adnia egy partíciós sémát. A nagy mennyiségű állapotú szolgáltatások felosztják az adatpartíciók közötti megosztást. Az egyes partíciók a szolgáltatás teljes állapotának egy részének felelnek meg, amely a fürt csomópontjain oszlik el.  

Az alábbi ábrán az alkalmazások és a szolgáltatási példányok, a partíciók és a replikák közötti kapcsolat látható.

![Partíciók és replikák egy szolgáltatáson belül][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Particionálás, skálázás és rendelkezésre állás
A [particionálás](service-fabric-concepts-partitioning.md) nem egyedi a Service Fabric. A particionálás jól ismert formája az adatparticionálás vagy a horizontális skálázás. A nagy mennyiségű állapotú állapot-nyilvántartó szolgáltatások felosztják az adatpartíciók közötti felosztást. Az egyes partíciók a szolgáltatás teljes állapotának egy részéért felelősek. 

Az egyes partíciók replikái a fürt csomópontjain oszlanak el, ami lehetővé teszi, hogy az elnevezett szolgáltatás a [méretezést](service-fabric-concepts-scalability.md). Mivel az adatmennyiség növekedésre szorul, a partíciók növekednek, és Service Fabric a partíciókat a csomópontok között a hardveres erőforrások hatékony kihasználásához. Ha új csomópontokat ad hozzá a fürthöz, Service Fabric a csomópontok számának megnövekedésével újra kiegyenlíti a partíció replikáit. Az alkalmazások teljes teljesítményének növelése és a memória-hozzáférés csökkentése. Ha a fürt csomópontjait nem használják hatékonyan, csökkentheti a fürt csomópontjainak számát. Service Fabric ismét kiegyensúlyozza a partíció replikáit a csomópontok számának csökkenésével, hogy jobban használhassa a hardvert az egyes csomópontokon.

Egy partíción belül az állapot nélküli elnevezett szolgáltatások rendelkeznek példányokkal, míg az állapot-nyilvántartó nevű szolgáltatások replikákkal rendelkeznek. Általában az állapot nélküli nevesített szolgáltatások esetében csak egy partíció van, mivel nincsenek belső állapotuk, bár [kivételek](./service-fabric-concepts-partitioning.md#partition-service-fabric-stateless-services)vannak. A partíciós példányok [rendelkezésre állást](service-fabric-availability-services.md)biztosítanak. Ha az egyik példány meghibásodik, más példányok továbbra is szabályosan működnek, majd Service Fabric új példányt hoz létre. Az állapot-nyilvántartó elnevezett szolgáltatások megőrzik a replikán belüli állapotukat, és mindegyik partícióhoz saját replikakészlet tartozik. Az olvasási és írási műveletek az egyik replikán (elsődleges) vannak elvégezve. Az írási műveletek állapotának módosításait a rendszer több más replikára replikálja (az Active formátumú másodlagos zónák néven). Ha egy replika meghibásodik, Service Fabric új replikát hoz létre a meglévő replikák közül.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Állapot nélküli és állapotalapú mikroszolgáltatások a Service Fabrichez
A Service Fabric segítségével mikroszolgáltatásokból vagy tárolókból álló alkalmazásokat építhet. Az állapot nélküli mikroszolgáltatások (például a protokollátjárók és webproxyk) nem tartanak fenn változtatható állapotot a kéréseken és a szolgáltatástól ezekre kapott válaszokon kívül. Az Azure Cloud Services feldolgozói szerepkörei például állapotmentes szolgáltatások. Az állapotalapú mikroszolgáltatások (például felhasználói fiókok, adatbázisok, eszközök, kosarak és üzenetsorok) változtatható, mérvadó állapotot tartanak fenn a kéréseken és a válaszokon kívül is. A modern webes alkalmazások állapot nélküli és állapotalapú mikroszolgáltatások kombinációjából állnak. 

A Service Fabric kulcsfontosságú különbséget tesz az állapot-nyilvántartó szolgáltatások kiépítéséhez, vagy a [beépített programozási modellekkel](service-fabric-choose-framework.md) vagy a tároló állapot-nyilvántartó szolgáltatásokkal. Az [alkalmazás-forgatókönyvek](service-fabric-application-scenarios.md) az állapotalapú szolgáltatások használatát mutatják be.

Miért van az állapot-nyilvántartó szolgáltatásokkal együtt? A két fő oka:

* Nagy átviteli sebességű, kis késleltetésű, hibatűrő online tranzakció-feldolgozási (OLTP) szolgáltatásokat építhet ki a kód és az adatok ugyanazon a gépen való bezárásával. Ilyenek például az interaktív kirakatok, a keresési, a eszközök internetes hálózata (IoT) rendszerek, a kereskedelmi rendszerek, a hitelkártya-feldolgozási és a csalások észlelési rendszerei, valamint a személyes rekordok kezelése.
* Egyszerűbbé teheti az alkalmazások kialakítását. Az állapot-nyilvántartó szolgáltatások nem igénylik a további várólisták és gyorsítótárak szükségességét, amelyek hagyományosan a tisztán állapot nélküli alkalmazások rendelkezésre állási és késési követelményeinek kielégítéséhez szükségesek. Az állapot-nyilvántartó szolgáltatások természetesen magas rendelkezésre állást és kis késleltetést biztosítanak, ami csökkenti az alkalmazás egészére kiterjedő mozgó részek számát.

## <a name="supported-programming-models"></a>Támogatott programozási modellek
A Service Fabric többféle módszert kínál a szolgáltatások írására és kezelésére. A szolgáltatások a Service Fabric API-kkal teljes mértékben kihasználhatják a platform funkcióit és alkalmazási keretrendszereit. A szolgáltatások bármilyen nyelven írt és Service Fabric-fürtön tárolt lefordított végrehajtható programok is lehetnek. További információ: [támogatott programozási modellek](service-fabric-choose-framework.md).

### <a name="containers"></a>Tárolók
Alapértelmezés szerint a Service Fabric folyamatokként telepíti és aktiválja a szolgáltatásokat. A Service Fabric [tárolókban](service-fabric-containers-overview.md)is telepíthetnek szolgáltatásokat. Fontos, hogy az ugyanazon alkalmazásban lévő tárolókban található folyamatokban és szolgáltatásokban is összekeverheti a szolgáltatásokat. Service Fabric támogatja a Linux-tárolók és Windows-tárolók telepítését a Windows Server 2016-es verziója esetén. Meglévő alkalmazásokat, állapot nélküli szolgáltatásokat vagy állapot-nyilvántartó szolgáltatásokat helyezhet üzembe tárolókban. 

### <a name="reliable-services"></a>Reliable Services
A [Reliable Services](service-fabric-reliable-services-introduction.md) egy egyszerűsített keretrendszer a Service Fabric platformmal integrált szolgáltatások írásához, és a platform összes funkciójának kihasználása mellett. A Reliable Services állapot nélküliek lehetnek (a legtöbb szolgáltatási platformhoz hasonlóan, például webkiszolgálók vagy feldolgozói szerepkörök az Azure Cloud Servicesban), ahol az állapotot egy külső megoldás, például az Azure DB vagy az Azure Table Storage őrzi meg. A Reliable Services állapot-nyilvántartó is lehet, ha az állapotot a megbízható gyűjtemények használatával közvetlenül a szolgáltatásban őrzi meg. Az állapotot a rendszer a replikáláson keresztül, a [particionálással](service-fabric-concepts-partitioning.md)elosztottan, a Service Fabric által automatikusan felügyelt módon végzi [el](service-fabric-availability-services.md) .

### <a name="reliable-actors"></a>Reliable Actors
A Reliable Servicesra épülő, [megbízható színészi](service-fabric-reliable-actors-introduction.md) keretrendszer egy alkalmazási keretrendszer, amely a színész kialakítási mintája alapján implementálja a virtuális színész mintáját. A megbízható szereplők keretrendszere a számítási és az állapot-független egységeket használja az egyszálas végrehajtással. A megbízható Actors keretrendszer beépített kommunikációt biztosít a szereplők számára, és előre beállított állapot-megőrzési és kibővíthető konfigurációkat tartalmaz.

### <a name="aspnet-core"></a>ASP.NET Core
A Service Fabric a webes és API-alkalmazások létrehozásához első osztályú programozási modellként integrálódik a [ASP.net Coreba](service-fabric-reliable-services-communication-aspnetcore.md) .  A ASP.NET Core a Service Fabric kétféleképpen használható:

- Vendég végrehajtható fájlként futtatva. Ez elsősorban a meglévő ASP.NET Core alkalmazások futtatására használatos Service Fabric a kód módosítása nélkül.
- Megbízható szolgáltatáson belül fut. Ez jobb integrációt tesz lehetővé a Service Fabric futtatókörnyezettel, és lehetővé teszi az állapot-nyilvántartó ASP.NET Core szolgáltatások használatát.

### <a name="guest-executables"></a>Vendég végrehajtható fájlok
A [vendég végrehajtható fájl](service-fabric-guest-executables-introduction.md) egy Service Fabric-fürtön található, más szolgáltatásokkal együtt futtatott, tetszőleges végrehajtható fájl (bármilyen nyelven írt). A vendég végrehajtható fájlok nem integrálhatók közvetlenül Service Fabric API-kkal. A REST API-k meghívásával azonban továbbra is kihasználják a platform által kínált funkciók előnyeit, például az egyéni állapotot és a betöltési jelentéskészítést és a szolgáltatások felderíthető Emellett teljes körű alkalmazás-életciklus-támogatással rendelkeznek. 

## <a name="application-lifecycle"></a>Alkalmazás-életciklus
A többi platformhoz hasonlóan a Service Fabric alkalmazások általában a következő fázisokon haladnak át: tervezés, fejlesztés, tesztelés, üzembe helyezés, frissítés, karbantartás és eltávolítás. A Service Fabric első osztályú támogatást biztosít a felhőalapú alkalmazások teljes életciklusához, a fejlesztéstől az üzembe helyezés, a napi felügyelet és a végleges leszerelésig történő karbantartás révén. A szolgáltatási modell lehetővé teszi, hogy a különböző szerepkörök egymástól függetlenül vehessenek részt az alkalmazás életciklusában. [Service Fabric alkalmazás életciklusa](service-fabric-application-lifecycle.md) áttekintést nyújt az API-król, és azt, hogy a különböző szerepkörök hogyan használják őket a Service Fabric alkalmazás életciklusának fázisaiban. 

A teljes alkalmazás-életciklus a PowerShell- [parancsmagok](/powershell/module/servicefabric/?view=azureservicefabricps), a [CLI-parancsok](service-fabric-sfctl.md), a [C# API](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)-k, a [Java API](/java/api/overview/azure/servicefabric)-k és a [REST API](/rest/api/servicefabric/)-k használatával kezelhető. Folyamatos integrációs/folyamatos üzembe helyezési folyamatokat is beállíthat olyan eszközökkel, mint például az [Azure-folyamatok](./service-fabric-tutorial-deploy-app-with-cicd-vsts.md) vagy a [Jenkins](/azure/developer/jenkins/deploy-to-service-fabric-cluster).

## <a name="test-applications-and-services"></a>Alkalmazások és szolgáltatások tesztelése
A valóban felhőalapú szolgáltatások létrehozásához kritikus fontosságú annak ellenőrzése, hogy az alkalmazások és szolgáltatások elbírja-e a valós hibákat. A hiba-elemzési szolgáltatás a Service Fabric-ra épülő szolgáltatások tesztelésére szolgál. A [hiba-elemzési szolgáltatással](service-fabric-testability-overview.md)értelmes hibákat válthat ki, és teljes körű tesztelési forgatókönyveket futtathat az alkalmazásain. Ezek a hibák és forgatókönyvek a számos államot, valamint a szolgáltatás teljes élettartama során felmerülő, ellenőrzött, biztonságos és konzisztens módon működtetett állapotokat gyakorolják és érvényesítik.

A [műveletek](service-fabric-testability-actions.md) célja, hogy egy szolgáltatás az egyes hibákkal történő tesztelésre szolgáljon. A szolgáltatás-fejlesztő ezeket a építőelemeket összetett forgatókönyvek írásához használhatja. Példa szimulált hibákra:

* Indítsa újra a csomópontot, hogy tetszőleges számú helyzetet Szimuláljon, ha a gép vagy a virtuális gép újraindul.
* Helyezzen át egy replikát az állapot-nyilvántartó szolgáltatásból a terheléselosztás, a feladatátvétel vagy az alkalmazás frissítésének szimulálása érdekében.
* Állapot-nyilvántartó szolgáltatás kvórum elvesztésének meghívásával olyan helyzetet hozhat létre, amelyben az írási műveletek nem folytatódnak, mert nincs elég "biztonsági másolat" vagy "másodlagos" replika az új adatok elfogadásához.
* Egy állapot-nyilvántartó szolgáltatás adatvesztésének meghívásával olyan helyzetet hozhat létre, amelyben az összes memóriában tárolt állapot teljesen törlődik.

A [forgatókönyvek](service-fabric-testability-scenarios.md) egy vagy több műveletből álló összetett műveletek. A hiba-elemzési szolgáltatás két beépített teljes forgatókönyvet biztosít:

* [Chaos-forgatókönyv](service-fabric-controlled-chaos.md)– a folyamatos, összekapcsolt hibák (mind a kecses, mind a nem teljes) szimulálása a fürtön a hosszabb időtartamon keresztül.
* [Feladatátvételi forgatókönyv](service-fabric-testability-scenarios.md#failover-test)– a Chaos tesztelési forgatókönyv olyan verziója, amely egy adott szolgáltatási partíciót céloz meg, miközben más szolgáltatásokat nem érint.

## <a name="clusters"></a>Fürtök
A [Service Fabric-fürt](service-fabric-deploy-anywhere.md) olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait. A fürtök több ezer gépre skálázhatók. A fürt részét képező számítógépet vagy virtuális gépet fürtcsomópont-csomópontnak nevezzük. Minden csomóponthoz hozzá van rendelve egy csomópontnév (egy sztring). A csomópontok jellemzőkkel, például elhelyezési tulajdonságokkal rendelkeznek. Minden gépen vagy virtuális gépnek van egy automatikus indítási szolgáltatása, `FabricHost.exe` amely elindul a rendszerindítás után, majd elindítja a két végrehajtható fájl: Fabric.exe és FabricGateway.exe. Ez a két végrehajtható fájl alkotja a csomópontot. Tesztelési helyzetekben több csomópontot is tárolhat egyetlen gépen vagy virtuális gépen a és a több példányának futtatásával `Fabric.exe` `FabricGateway.exe` .

Service Fabric fürtöket Windows Server vagy Linux rendszerű virtuális vagy fizikai gépeken lehet létrehozni. Service Fabric alkalmazást bármely olyan környezetben telepítheti és futtathatja, ahol a Windows Server vagy a Linux rendszerű számítógépek összekapcsolhatók: helyszíni, Microsoft Azure vagy bármely felhőalapú szolgáltatón.

### <a name="clusters-on-azure"></a>Fürtök az Azure-on
A Service Fabric-fürtök Azure-ban való futtatása a többi Azure-szolgáltatással és-szolgáltatással való integrációt is lehetővé teszi, így a fürt működése és kezelése egyszerűbb és megbízhatóbb. A fürt egy Azure Resource Manager erőforrás, így a fürtöket, például az Azure más erőforrásait is modellezheti. A Resource Manager emellett a fürt által egyetlen egységként használt összes erőforrás egyszerű kezelését is lehetővé teszi. Az Azure-beli fürtök integrálva vannak az Azure Diagnostics szolgáltatással és Azure Monitor naplókkal. A fürtcsomópontok típusai [virtuálisgép-méretezési](../virtual-machine-scale-sets/index.yml)csoportok, így az automatikus skálázási funkciók beépítettek.

Létrehozhat egy fürtöt az Azure-ban a [Azure Portal](service-fabric-cluster-creation-via-portal.md), egy [sablonból](service-fabric-cluster-creation-via-arm.md)vagy a [Visual studióból](./service-fabric-cluster-creation-via-arm.md).

A Linux rendszeren elérhető Service Fabric lehetővé teszi, hogy a Windows rendszerhez hasonlóan olyan, a Linuxon jól méretezhető alkalmazásokat hozzon létre, telepítsen és kezeljen. A Service Fabric keretrendszerek (Reliable Services és Reliable Actors) Java Linux rendszeren, a C# (.NET Core) mellett is elérhetők. A [vendég által végrehajtható szolgáltatásokat](service-fabric-guest-executables-introduction.md) bármilyen nyelven vagy keretrendszerrel is létrehozhatja. A Docker-tárolók összehangolása is támogatott. A Docker-tárolók futtathatják a Service Fabric-keretrendszereket használó vendég végrehajtható fájlokat vagy natív Service Fabric szolgáltatásokat. További információért olvassa el a [Service Fabric Linuxon](service-fabric-deploy-anywhere.md)című témakört.

Vannak olyan szolgáltatások, amelyek támogatottak a Windowsban, de Linuxon nem. Ha többet szeretne megtudni, olvassa el [a Linux és a Windows Service Fabric közötti különbségeket](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Önálló fürtök
A Service Fabric egy olyan telepítőcsomagot biztosít, amellyel önálló Service Fabric-fürtöket hozhat létre helyszíni vagy bármely felhőalapú szolgáltatón. Az önálló fürtök lehetővé teszi, hogy a fürt tetszőlegesen üzemeltethető legyen. Ha az adatai megfelelőségi vagy szabályozási korlátozások alá esnek, vagy ha szeretné megtartani az adataikat a helyi gépen, saját fürtöt és alkalmazásokat is futtathat. Service Fabric alkalmazások több üzemeltetési környezetben is futhatnak, módosítás nélkül, így az alkalmazások kiépítésének ismerete az egyik üzemeltetési környezetből a másikba. 

[Az első önálló Service Fabric-fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)

A Linux önálló fürtök még nem támogatottak.

### <a name="cluster-security"></a>Fürtbiztonság
A fürtöket védeni kell annak megakadályozása érdekében, hogy jogosulatlan felhasználók csatlakozzanak a fürthöz, különösen akkor, ha az éles környezetben futó munkaterhelésekkel rendelkezik. Bár lehetséges, hogy nem biztonságos fürtöt hoz létre, így a névtelen felhasználók csatlakozhatnak hozzá, ha a felügyeleti végpontok elérhetők a nyilvános interneten. A biztonság később nem engedélyezhető egy nem biztonságos fürtön: a fürt biztonsága engedélyezve van a fürt létrehozási idején.

A fürt biztonsági forgatókönyvei a következők:
* Csomópontok közötti biztonság
* Az ügyfél és a csomópont közötti biztonság
* Szerepköralapú hozzáférés-vezérlés Service Fabric

További információért olvassa el [a fürt biztonságossá tétele](service-fabric-cluster-security.md)című témakört.

### <a name="scaling"></a>Méretezés
Ha új csomópontokat ad hozzá a fürthöz, Service Fabric a csomópontok számának megnövekedésével kiegyensúlyozza a partíció replikáit és példányait. Az alkalmazások teljes teljesítményének növelése és a memória-hozzáférés csökkentése. Ha a fürt csomópontjait nem használják hatékonyan, csökkentheti a fürt csomópontjainak számát. Service Fabric újra kiegyenlíti a partíciók replikáit és példányait a csomópontok számának csökkenésével, hogy jobban használhassa a hardvert az egyes csomópontokon. A fürtöket [manuálisan](service-fabric-cluster-scale-in-out.md) vagy [programozott](service-fabric-cluster-programmatic-scaling.md)módon méretezheti az Azure-ban. Az önálló fürtök méretezése [manuálisan](service-fabric-cluster-windows-server-add-remove-nodes.md)végezhető el.

### <a name="cluster-upgrades"></a>Fürt frissítései
Időnként a Service Fabric Runtime új verziói jelennek meg. Futtasson futásidejű vagy hálós frissítéseket a fürtön, hogy mindig [támogatott verziót](service-fabric-support.md)futtasson. A háló frissítésein kívül a fürtkonfiguráció, például a tanúsítványok vagy az alkalmazások portjai is frissíthetők.

A Service Fabric-fürt olyan erőforrás, amelyet Ön birtokol, de részben a Microsoft felügyeli. A Microsoft felelős a mögöttes operációs rendszer javításához és a háló frissítéseinek a fürtön való frissítéséhez. Beállíthatja, hogy a fürt automatikusan megkapja az automatikus háló frissítéseit, amikor a Microsoft új verziót szabadít fel, vagy válasszon egy támogatott háló-verziót. A háló és a konfiguráció frissítései a Azure Portalon vagy a Resource Manageren keresztül adhatók meg. További információért olvassa el [a Service Fabric-fürt frissítése](service-fabric-cluster-upgrade.md)című témakört. 

Az önálló fürt olyan erőforrás, amely teljesen saját maga. A mögöttes operációs rendszer javítása és a háló frissítéseinek kezdeményezése felelős. Ha a fürt képes csatlakozni a kiszolgálóhoz, beállíthatja [https://www.microsoft.com/download](https://www.microsoft.com/download) , hogy a fürt automatikusan letöltse és kiépítse az új Service Fabric futtatókörnyezet-csomagot. Ezután kezdeményezi a frissítést. Ha a fürt nem fér hozzá [https://www.microsoft.com/download](https://www.microsoft.com/download) , manuálisan letöltheti az új futtatókörnyezet-csomagot egy internetkapcsolattal rendelkező gépről, majd kezdeményezheti a frissítést. További információért olvassa el az [önálló Service Fabric-fürt frissítése](service-fabric-cluster-upgrade-windows-server.md)című témakört.

## <a name="health-monitoring"></a>Állapotfigyelés
Service Fabric a nem kifogástalan állapotú fürtök és az alkalmazások bizonyos entitásokra (például a fürtcsomópontok és a szolgáltatási replikák) jelölésére szolgáló [állapot-modellt](service-fabric-health-introduction.md) vezet be. Az állapot-modell az állapotfigyelő jelentéseket (rendszerösszetevők és watchdogok) használja. A cél egyszerű és gyors diagnosztizálás és javítás. A szolgáltatás-íróknak szembe kell nézniük az állapottal és az [állapot-jelentéskészítés kialakításával](service-fabric-report-health.md#design-health-reporting). Minden olyan feltételt, amely hatással lehet az állapotra, jelenteni kell, különösen akkor, ha segíthet a gyökérhez közeledő problémák megjelölésében. Az állapotadatok segítségével időt és fáradságot takaríthat meg a hibakeresés és a vizsgálat során, ha a szolgáltatás üzembe helyezése az éles környezetben történik.

A Service Fabric Reporter figyeli az azonosított feltételeket. Ezek a feltételek a helyi nézet alapján jelentenek. Az [állapotfigyelő](service-fabric-health-introduction.md#health-store) összesíti az összes riporter által eljuttatott egészségügyi adatokat annak megállapítására, hogy az entitások globálisan kifogástalanok-e. A modell célja, hogy gazdag, rugalmas és könnyen használható legyen. Az állapot-jelentések minősége határozza meg a fürt állapot nézetének pontosságát. A nem kifogástalan állapotú problémák helytelen megjelenítése negatív hatással lehet a frissítésekre vagy más, az állapotadatok használatát használó szolgáltatásokra. Ilyen szolgáltatás például a szolgáltatások és a riasztási mechanizmusok javítása. Ezért bizonyos gondolkodásra van szükség ahhoz, hogy olyan jelentéseket szolgáltasson, amelyek a lehető legjobb módon rögzítik az érdeklődési feltételeket.

Jelentéskészítés a következő helyről végezhető el:
* A figyelt Service Fabric szolgáltatás replikája vagy példánya.
* Service Fabric szolgáltatásként üzembe helyezett belső watchdogok (például egy Service Fabric állapot nélküli szolgáltatás, amely figyeli a feltételeket és a problémákkal kapcsolatos jelentéseket). A watchdogok minden csomóponton üzembe helyezhetők, vagy a figyelt szolgáltatáshoz is összeállíthatók.
* A Service Fabric csomópontokon futó belső watchdogok, de nem Service Fabric-szolgáltatásként valósulnak meg.
* Külső watchdogok, amelyek az erőforrást az Service Fabric-fürtön kívülről vizsgálják (például: figyelési szolgáltatás, mint a Gomez).

A Service Fabric az összetevők állapota a fürt összes entitásán megjelenik. A [rendszerállapot-jelentések](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) biztosítják a fürt és az alkalmazás funkcióinak láthatóságát, valamint az állapottal kapcsolatos problémák megjelölését. Alkalmazások és szolgáltatások esetén a rendszerállapot-jelentések ellenőrzik, hogy az entitások implementálva vannak-e, és megfelelően viselkednek-e a Service Fabric futtatókörnyezet szempontjából. A jelentések nem biztosítják a szolgáltatás üzleti logikájának vagy a nem válaszoló folyamatok észlelésének állapotának figyelését. A szolgáltatás logikájának megfelelő állapotadatok hozzáadásához [implementálja az egyéni állapotadatok bejelentését](service-fabric-report-health.md) a szolgáltatásokban.

Service Fabric több módszert is biztosít az állapot-tárolóban összesített [állapot-jelentések megtekintésére](service-fabric-view-entities-aggregated-health.md) :
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) vagy más vizualizációs eszközöket.
* Állapot-lekérdezések (a [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps), a [CLI](service-fabric-sfctl.md), a [C# FabricClient API](/dotnet/api/system.fabric.fabricclient.healthclient) -k és a [Java FabricClient API](/java/api/system.fabric)-k, illetve a [REST API](/rest/api/servicefabric)-k használatával).
* Általános lekérdezések, amelyek olyan entitások listáját adják vissza, amelyek állapota a tulajdonságok egyike (a PowerShell, a CLI, az API-k vagy a REST használatával).

## <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika
A [monitorozás és a diagnosztika](service-fabric-diagnostics-overview.md) kritikus fontosságú az alkalmazások és szolgáltatások bármilyen környezetben történő fejlesztéséhez, teszteléséhez és üzembe helyezéséhez. A Service Fabric Solutions a legjobb megoldás, ha olyan monitorozást és diagnosztikát tervez és valósít meg, amely segít biztosítani, hogy az alkalmazások és szolgáltatások a várt módon működjenek a helyi fejlesztési környezetekben vagy éles környezetben.

A monitorozás és diagnosztika fő célja a következő:

- Hardver-és infrastrukturális problémák észlelése és diagnosztizálása
- A szoftver-és alkalmazásokkal kapcsolatos problémák észlelése, a szolgáltatás leállásának csökkentése
- Az erőforrás-felhasználás és a Súgó-üzemeltetési döntések ismertetése
- Az alkalmazás, a szolgáltatás és az infrastruktúra teljesítményének optimalizálása
- Üzleti ismereteket hozhat, és azonosíthatja a fejlesztés területeit
 
A monitorozás és diagnosztika általános munkafolyamata három lépésből áll:

1. Esemény létrehozása: az infrastruktúra (fürt), a platform és az alkalmazás/szolgáltatás szintjén eseményeket (naplókat, nyomkövetéseket és egyéni eseményeket) tartalmaz.
2. Esemény összesítése: a generált eseményeket a megjelenítésük előtt össze kell gyűjteni és összesíteni kell
3. Elemzés: az eseményeket bizonyos formátumban kell megjeleníteni és elérhetővé tenni, hogy lehetővé váljon az elemzés és a Megjelenítés igény szerint

Ezen a három területen több termék is elérhető, és mindegyikhez szabadon választhat különböző technológiákat. További információért olvassa el [Az Azure Service Fabric figyelését és diagnosztizálását](service-fabric-diagnostics-overview.md)ismertető témakört.

## <a name="next-steps"></a>Következő lépések
* Megismerkedhet egy [azure-beli fürt](service-fabric-cluster-creation-via-portal.md) vagy egy [önálló windowsbeli fürt](service-fabric-cluster-creation-for-windows-server.md) létrehozásával.
* Próbáljon szolgáltatást létrehozni a [Reliable Services](service-fabric-reliable-services-quick-start.md) vagy a [Reliable Actors](service-fabric-reliable-actors-get-started.md) programozási modellek használatával.
* Ismerje meg, hogyan [telepíthet át Cloud Servicesról](service-fabric-cloud-services-migration-differences.md).
* Megtudhatja, hogyan [figyelheti és diagnosztizálhatja a szolgáltatásokat](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Megtudhatja, hogyan [tesztelheti alkalmazásait és szolgáltatásait](service-fabric-testability-overview.md).
* Ismerje meg, hogyan kezelheti és hangolhatja össze a [fürt erőforrásait](service-fabric-cluster-resource-manager-introduction.md).
* Tekintse át a [Service Fabric mintákat](/samples/browse/?products=azure).
* További információ a [Service Fabric támogatási lehetőségeiről](service-fabric-support.md).
* Cikkek és bejelentések a [csapat blogjában](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) olvashatók.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
