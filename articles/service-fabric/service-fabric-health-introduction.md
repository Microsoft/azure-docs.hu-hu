---
title: Állapot-figyelés Service Fabric
description: Bevezetés az Azure Service Fabric Health monitoring modellbe, amely a fürt és az alkalmazásai és szolgáltatásai figyelését teszi lehetővé.
ms.topic: conceptual
ms.date: 2/28/2018
ms.openlocfilehash: a1c545048739182e3baba3e3d94da1accca227d1
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627415"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>A Service Fabric állapotmonitorozásának bemutatása
Az Azure Service Fabric egy olyan egészségügyi modellt vezet be, amely gazdag, rugalmas és bővíthető egészségügyi értékelést és jelentéskészítést tesz lehetővé. A modell lehetővé teszi a fürt állapotának és a rajta futó szolgáltatásoknak a közel valós idejű figyelését. Egyszerűen beszerezhet egészségügyi információkat, és kiválaszthatja a potenciális problémákat, mielőtt lépcsőzetesen kiesést okoz. A tipikus modellben a szolgáltatások a helyi nézeteik alapján küldenek jelentéseket, és ezek az információk összesítve biztosítják a teljes fürt szintű nézetet.

Service Fabric összetevők ezt a gazdag állapotú modellt használják a jelenlegi állapotuk jelentésére. Ugyanezt a mechanizmust használhatja az alkalmazások állapotának jelentéséhez. Ha magas színvonalú állapot-jelentéskészítést végez, amely az egyéni feltételeket rögzíti, sokkal könnyebben észlelheti és javíthatja a futó alkalmazással kapcsolatos problémákat.

> [!NOTE]
> Elindítottuk az állapot-alrendszert a figyelt frissítések szükségességének kielégítéséhez. Service Fabric biztosít a megfigyelt alkalmazások és fürtök frissítéseit, amelyek biztosítják a teljes rendelkezésre állást, a leállás és a minimális felhasználói beavatkozás nélkül. A célok eléréséhez a frissítés a konfigurált frissítési szabályzatok alapján ellenőrzi az állapotot. A frissítés csak akkor folytatható, ha az állapot tiszteletben tartja a kívánt küszöbértékeket. Ellenkező esetben a rendszer automatikusan visszaállítja a frissítést, vagy szünetelteti a problémát, hogy a rendszergazdák egy esélyt adjanak a problémák megoldására. Az alkalmazások frissítéseivel kapcsolatos további tudnivalókért tekintse meg [ezt a cikket](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Health Store
Az állapotfigyelő szolgáltatás az állapottal kapcsolatos információkat biztosít a fürtben található entitásokról az egyszerű lekérés és értékelés céljából. Service Fabric megőrzött állapot-nyilvántartó szolgáltatásként valósul meg a magas rendelkezésre állás és a méretezhetőség biztosítása érdekében. Az állapotfigyelő a **Fabric:/System** alkalmazás része, és akkor érhető el, amikor a fürt működik.

## <a name="health-entities-and-hierarchy"></a>Állapot entitások és hierarchia
Az állapotú entitások logikai hierarchiába vannak rendezve, amely a különböző entitások közötti interakciókat és függőségeket rögzíti. Az állapotfigyelő szolgáltatás a Service Fabric-összetevőktől kapott jelentések alapján automatikusan létrehozza az állapot entitásokat és a hierarchiát.

Az állapot entitások az Service Fabric entitásokat tükrözik. (Az állapotfigyelő **alkalmazás entitása** például megegyezik a fürtben üzembe helyezett alkalmazás-példányokkal, míg az **állapot-csomópont entitása** megegyezik egy Service Fabric fürtcsomópont-csomóponttal.) Az állapot-hierarchia rögzíti a rendszerentitások interakcióit, és ez a fejlett állapot kiértékelésének alapja. Az [Service Fabric technikai áttekintésében](service-fabric-technical-overview.md)megismerheti a kulcsfontosságú Service Fabric fogalmakat. További információ az alkalmazásról: [Service Fabric Application Model](service-fabric-application-model.md).

Az állapot entitások és a hierarchia lehetővé teszik a fürt és az alkalmazások hatékony jelentését, hibakeresését és figyelését. Az állapot modellje a fürtben található számos mozgó darab állapotának pontos és *részletes megjelenítését* biztosítja.

![Állapot entitások.][1]
Az állapotadatok a szülő-gyermek kapcsolatokon alapuló hierarchiában vannak rendezve.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Az állapot-entitások a következők:

* **Fürt**. Egy Service Fabric-fürt állapotát jelöli. A fürt állapotáról szóló jelentések a teljes fürtöt érintő feltételeket írják le. Ezek a feltételek a fürt vagy a fürt több entitására is hatással vannak. A feltételtől függően a jelentéskészítő nem tudja leszűkíteni a problémát egy vagy több sérült gyermekre. Ilyenek például a fürt felosztásának agya a hálózati particionálás vagy a kommunikációs problémák miatt.
* **Csomópont**. Egy Service Fabric csomópont állapotát jelöli. A csomópontok állapotáról szóló jelentések a csomópont funkcióit érintő feltételeket írják le. Általában a rajta futó összes telepített entitásra hatással vannak. Ilyenek például a szabad lemezterület (vagy más, a memóriára, a kapcsolatokra vonatkozó egyéb tulajdonságok), valamint a csomópont leállása esetén. A csomópont entitást a csomópont neve (karakterlánc) azonosítja.
* **Alkalmazás**. A fürtben futó alkalmazás-példány állapotát jelöli. Az alkalmazás állapotával kapcsolatos jelentések az alkalmazás általános állapotát befolyásoló feltételeket írják le. Nem szűkíthető le az egyes gyermekekre (szolgáltatások vagy központilag telepített alkalmazások). Ilyenek például az alkalmazás különböző szolgáltatásai közötti teljes körű interakciók. Az alkalmazás entitását az alkalmazás neve (URI) azonosítja.
* **Szolgáltatás**. A fürtben futó szolgáltatás állapotát jelöli. A szolgáltatás állapotáról szóló jelentések a szolgáltatás általános állapotát befolyásoló feltételeket írják le. A jelentéskészítő nem tudja leszűkíteni a problémát egy nem megfelelő állapotú partícióra vagy replikára. Ilyenek például a szolgáltatás konfigurációja (például port vagy külső fájlmegosztás), amely az összes partícióval kapcsolatos problémákat okoz. A szolgáltatás entitását a szolgáltatás neve (URI) azonosítja.
* **Partíció**. Egy szolgáltatás partíciójának állapotát jelöli. A partíciós állapotra vonatkozó jelentések a teljes replikakészlet-készletet érintő feltételeket írják le. Ilyenek például akkor, ha a replikák száma a cél száma alatt van, és ha egy partíció kvórum elvesztése miatt van. A partíciós entitást a partíció azonosítója (GUID) azonosítja.
* **Replika**. Egy állapot-nyilvántartó szolgáltatás replikájának vagy állapot nélküli szolgáltatási példányának állapotát jelöli. A replika az a legkisebb egység, amelyet a watchdogok és a rendszerösszetevők jelenthetnek az alkalmazások számára. Az állapot-nyilvántartó szolgáltatások esetében a példák olyan elsődleges replikát tartalmaznak, amely nem tudja replikálni a műveleteket formátumú másodlagos zónák és lassú replikálásra. Emellett az állapot nélküli példányok is jelenthetnek jelentést, ha elfogy az erőforrások, vagy kapcsolódási problémák léptek fel. A replika entitást a partíció azonosítója (GUID) és a replika vagy a példány azonosítója (Long) azonosítja.
* **DeployedApplication**. *Egy csomóponton futó alkalmazás* állapotát jelöli. A központilag telepített alkalmazások állapotáról szóló jelentések a csomóponton az alkalmazásra vonatkozó feltételeket írják le, amelyek nem szűkíthető le az ugyanazon a csomóponton telepített szervizcsomagokra. Ilyenek például azok a hibák, amikor az alkalmazáscsomag nem tölthető le a csomóponton, és az alkalmazás rendszerbiztonsági tagjának a csomóponton való beállításával kapcsolatos problémákat okoz. A központilag telepített alkalmazást az alkalmazásnév (URI) és a csomópont neve (karakterlánc) azonosítja.
* **DeployedServicePackage**. A fürt egy csomópontján futó szervizcsomag állapotát jelöli. Olyan szolgáltatási csomagokra vonatkozó feltételeket ír le, amelyek nem érintik ugyanazon alkalmazás ugyanazon csomópontján lévő más szolgáltatási csomagokat. Ilyenek például a nem indítható alkalmazáscsomag, valamint a nem olvasható konfigurációs csomag. Az üzembe helyezett szervizcsomagot az alkalmazásnév (URI), a csomópont neve (karakterlánc), a szolgáltatás jegyzékfájljának neve (karakterlánc) és a szervizcsomag aktiválási azonosítója (string) azonosítja.

Az állapot modell részletessége megkönnyíti a problémák észlelését és kijavítani. Ha például egy szolgáltatás nem válaszol, lehetséges, hogy az alkalmazás példánya nem kifogástalan állapotú. Az adott szinten történő jelentéskészítés nem ideális, mivel előfordulhat, hogy a probléma nem érinti az adott alkalmazáson belüli összes szolgáltatást. A jelentést a nem megfelelő állapotú szolgáltatásra vagy egy adott alárendelt partícióra kell alkalmazni, ha a partícióra további információk mutatnak. Az adatszerkezetek automatikusan felfedik a hierarchiát, és egy nem kifogástalan állapotú partíció látható a szolgáltatás és az alkalmazás szintjén. Ez az Összesítés segít a probléma gyökerének gyors meghatározásában és megoldásában.

Az állapot-hierarchia szülő-gyermek kapcsolatokból áll. A fürtök csomópontokból és alkalmazásokból állnak. Az alkalmazások rendelkeznek szolgáltatásokkal és telepített alkalmazásokkal. A központilag telepített alkalmazások telepített szervizcsomagokkal rendelkeznek. A szolgáltatások partíciókkal rendelkeznek, és mindegyik partíció egy vagy több replikával rendelkezik. A csomópontok és a központilag telepített entitások között különleges kapcsolat áll fenn. A szervezete rendszerösszetevője által jelentett, nem kifogástalan állapotú csomópont, amely Feladatátvételi felügyelő a telepített alkalmazásokat, szervizcsomagokat és a rajta telepített replikákat érinti.

Az állapot-hierarchia a rendszer legújabb állapotát képviseli a legújabb állapotjelentés alapján, ami szinte valós idejű információ.
A belső és külső watchdogok az alkalmazás-specifikus logika vagy az egyéni figyelt feltételek alapján is jelenthetik az entitásokat. A felhasználói jelentések együtt léteznek a rendszerjelentésekkel.

Tervezze meg, hogyan jelentheti be és reagálhat az állapotra a nagyméretű felhőalapú szolgáltatások kialakítása során. Ez a kezdeti beruházás megkönnyíti a szolgáltatás hibakeresését, monitorozását és működését.

## <a name="health-states"></a>Állapotok
A Service Fabric három állapotot használ annak leírására, hogy az entitás kifogástalan-e vagy sem: OK, figyelmeztetés és hiba. Az állapotfigyelő szolgáltatásnak eljuttatott jelentéseknek ezen állapotok egyikét kell megadniuk. Az állapot kiértékelésének eredménye ezen állapotok egyike.

A lehetséges [állapotok](/dotnet/api/system.fabric.health.healthstate) a következők:

* **OK**. Az entitás kifogástalan. Nincsenek ismert problémák az informatikai vagy a gyermekeik számára (ha vannak ilyenek).
* **Figyelmeztetés**. Az entitásnak vannak problémái, de továbbra is megfelelően működhet. Például késések fordulnak elő, de még nem okoznak működési problémákat. Bizonyos esetekben a figyelmeztetési feltétel külső beavatkozás nélkül is megoldható. Ezekben az esetekben az állapot-jelentések a tudatosságot és a folyamatban lévők láthatóságát teszik lehetővé. Más esetekben előfordulhat, hogy a figyelmeztetési feltétel a felhasználói beavatkozás nélkül súlyos hibába ütközik.
* **Hiba történt**. Az entitás állapota nem kifogástalan. Az entitás állapotának kijavításához műveletet kell végrehajtani, mert az nem működik megfelelően.
* **Ismeretlen**. Az entitás nem létezik a Health Store-ban. Ezt az eredményt a több összetevőből származó eredményeket egyesítő elosztott lekérdezésekből lehet beszerezni. A csomópont-lista lekérése lekérdezés például a **FailoverManager**, a **ClusterManager** és a **HealthManager**; az alkalmazások listájának lekérése a **ClusterManager** és a **HealthManager**. Ezek a lekérdezések több rendszerösszetevőből egyesítik az eredményeket. Ha egy másik rendszerösszetevő olyan entitást ad vissza, amely nem szerepel az állapotfigyelő tárolóban, az egyesített eredmény ismeretlen állapottal rendelkezik. Az entitások nincsenek tárolva, mert az állapot-jelentések még nem lettek feldolgozva, vagy az entitást a törlés után törölték.

## <a name="health-policies"></a>Állapotfigyelő házirendek
Az állapotfigyelő az állapotadatok alapján határozza meg, hogy az entitás kifogástalan állapotú-e a jelentései és gyermekei alapján.

> [!NOTE]
> Az Állapotházirendek megadhatók a fürt jegyzékfájljában (a fürt és a csomópont állapotának kiértékeléséhez) vagy az alkalmazás jegyzékfájljában (az alkalmazás kiértékeléséhez és annak gyermekeihez). Az állapot-kiértékelési kérelmek a kizárólag az adott értékeléshez használt egyéni állapot-értékelési szabályzatokban is beadhatók.
> 
> 

Alapértelmezés szerint a Service Fabric szigorú szabályokat alkalmaz (mindennek kifogástalannak kell lennie) a szülő-gyermek hierarchikus kapcsolathoz. Ha még az egyik gyermek egy nem megfelelő állapotú eseményt tartalmaz, a szülő nem kifogástalannak minősül.

### <a name="cluster-health-policy"></a>Fürt állapotára vonatkozó házirend
A [fürt állapot-házirendje](/dotnet/api/system.fabric.health.clusterhealthpolicy) a fürt állapotának és a csomópont állapotának kiértékelésére szolgál. A házirend a fürt jegyzékfájljában határozható meg. Ha nincs jelen, a rendszer az alapértelmezett szabályzatot (a tolerálatlan hibákat) használja.

A fürt állapotára vonatkozó házirend a következőket tartalmazza:

* [ConsiderWarningAsError](/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Azt határozza meg, hogy a figyelmeztetési állapot jelentéseit hibaként kell-e kezelni az állapot értékelése során. Alapértelmezett érték: false (hamis).
* [MaxPercentUnhealthyApplications](/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Meghatározza, hogy a fürt miért nem megfelelő állapotba kerül, mielőtt a rendszer hibát észlel.
* [MaxPercentUnhealthyNodes](/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Meghatározza a csomópontok maximálisan megengedett százalékos arányát, amely a fürt hibás állapotba való beszámításához szükséges. A nagyméretű fürtökben egyes csomópontok mindig kimaradnak vagy kifogynak a javításokhoz, ezért ezt a százalékos arányt úgy kell konfigurálni, hogy eltűri ezt.
* [ApplicationTypeHealthPolicyMap](/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). A fürt állapotának kiértékelése során a speciális alkalmazások típusának leírására használhatja az állapotfigyelő állapotra vonatkozó házirend-hozzárendelést. Alapértelmezés szerint a rendszer az összes alkalmazást egy készletbe helyezi, és kiértékeli a MaxPercentUnhealthyApplications. Ha egyes alkalmazás-típusokat másképp kell kezelni, akkor kivehetők a globális készletből. Ehelyett a rendszer kiértékeli azokat a százalékokat, amelyek az alkalmazás típusa nevével vannak társítva a térképen. Egy fürtben például több ezer különböző típusú alkalmazás létezik, és a speciális alkalmazás néhány vezérlő alkalmazás-példánya. A vezérlő alkalmazásoknak soha nem lehetnek hiba. Megadhatja a globális MaxPercentUnhealthyApplications 20%-ra a hibák kihasználása érdekében, de a "ControlApplicationType" alkalmazás típusához állítsa a MaxPercentUnhealthyApplications 0-ra. Így ha a sok alkalmazás nem kifogástalan, de a globális nem kifogástalan százalék alatt van, a rendszer figyelmeztetésként értékeli ki a fürtöt. A figyelmeztetési állapot nem befolyásolja a fürt frissítését vagy a hiba állapotával indított egyéb figyelési állapotot. Egy hiba esetén azonban még egy vezérlő alkalmazás sem fogja a fürtöt sérült állapotba hozni, amely a frissítés konfigurációjától függően visszaállítja vagy szünetelteti a fürt frissítését.
  A térképen definiált alkalmazások típusai esetében az összes alkalmazás-példány kikerül a globális készletből. A kiértékelésük az alkalmazás típusú alkalmazások teljes száma alapján történik, a Térkép adott MaxPercentUnhealthyApplications használatával. Az összes többi alkalmazás a globális készletben marad, és a MaxPercentUnhealthyApplications-mel van kiértékelve.

  A következő példa egy fürt jegyzékfájljának kivonata. Ha bejegyzéseket szeretne megadni az Application Type Térképben, előtagként adja meg a paraméter nevét a "ApplicationTypeMaxPercentUnhealthyApplications-" kifejezéssel, amelyet az alkalmazásnév neve követ.

  ```xml
  <FabricSettings>
    <Section Name="HealthManager/ClusterHealthPolicy">
      <Parameter Name="ConsiderWarningAsError" Value="False" />
      <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
      <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
      <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
    </Section>
  </FabricSettings>
  ```

* [NodeTypeHealthPolicyMap](/dotnet/api/system.fabric.health.clusterhealthpolicy.nodetypehealthpolicymap). A fürt állapotának kiértékelése során felhasználható a csomópont-állapotra vonatkozó szabályzat-hozzárendelés a speciális csomópont-típusok leírásához. A csomópont-típusokat a rendszer kiértékeli a csomópont-típus nevével társított százalékok alapján. Ennek az értéknek a beállítása nincs hatással a számára használt csomópontok globális készletére `MaxPercentUnhealthyNodes` . Egy fürt például több száz különböző típusú csomópontot tartalmaz, és néhány csomópont-típust, amelyek fontos munkát futtatnak. Az adott típusú csomópontoknak nem kell megjelenniük. Megadhatja, hogy a globális `MaxPercentUnhealthyNodes` érték 20% legyen az összes csomópont meghibásodása esetén, de a csomópont típusa esetén `SpecialNodeType` állítsa a `MaxPercentUnhealthyNodes` értéket 0-ra. Így ha a sok csomópont állapota nem kifogástalan, de a globális nem kifogástalan százalék alatt van, a rendszer kiértékeli a fürtöt a figyelmeztetési állapotnak megfelelően. A figyelmeztetési állapot nem befolyásolja a fürt frissítését vagy a hiba állapota által aktivált egyéb figyelést. `SpecialNodeType`Egy hiba állapotú csomópontban azonban a fürt nem Kifogástalan állapotba kerül, és a frissítési konfigurációtól függően a fürt frissítését vagy szüneteltetését is elvégezheti. Ezzel szemben a globális érték 0 értékre állítása `MaxPercentUnhealthyNodes` és a nem kifogástalan `SpecialNodeType` állapotú csomópontok maximális százalékos aránya 100-re egy hibás állapotú csomópontnál a fürt a hiba `SpecialNodeType` állapotában marad, mert a globális korlátozás szigorúbb ebben az esetben. 

  A következő példa egy fürt jegyzékfájljának kivonata. A csomópont típusú leképezésben szereplő bejegyzések definiálásához előtagként adja meg a paraméter nevét a "NodeTypeMaxPercentUnhealthyNodes-" kifejezéssel, majd a csomópont típusának nevét.

  ```xml
  <FabricSettings>
    <Section Name="HealthManager/ClusterHealthPolicy">
      <Parameter Name="ConsiderWarningAsError" Value="False" />
      <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
      <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
      <Parameter Name="NodeTypeMaxPercentUnhealthyNodes-SpecialNodeType" Value="0" />
    </Section>
  </FabricSettings>
  ```

### <a name="application-health-policy"></a>Alkalmazás állapotára vonatkozó házirend
Az [alkalmazás állapotára vonatkozó házirend](/dotnet/api/system.fabric.health.applicationhealthpolicy) leírja, hogyan történik az események és a gyermekek összesítésének kiértékelése az alkalmazásokhoz és a gyermekeik számára. Az alkalmazás jegyzékfájljában, **ApplicationManifest.xml** az alkalmazáscsomagban definiálható. Ha nincsenek megadva szabályzatok, Service Fabric feltételezi, hogy az entitás nem kifogástalan állapotú, ha a figyelmeztetés vagy a hiba állapot állapota jelentésben vagy gyermeken van.
A konfigurálható szabályzatok a következők:

* [ConsiderWarningAsError](/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Azt határozza meg, hogy a figyelmeztetési állapot jelentéseit hibaként kell-e kezelni az állapot értékelése során. Alapértelmezett érték: false (hamis).
* [MaxPercentUnhealthyDeployedApplications](/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Meghatározza a telepített alkalmazások maximálisan megengedett százalékos arányát, amely nem kifogástalan, mielőtt az alkalmazás hibát észlel. Ezt a százalékarányt úgy számítjuk ki, hogy a nem kifogástalan állapotú központilag telepített alkalmazások számát a fürtben jelenleg telepített alkalmazások csomópontjain használják. A számítások egy kis számú csomóponton fellépő meghibásodást okoznak. Alapértelmezett százalék: nulla.
* [DefaultServiceTypeHealthPolicy](/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Meghatározza a szolgáltatástípus alapértelmezett állapotát, amely lecseréli az alkalmazásban lévő összes szolgáltatástípus alapértelmezett állapotfigyelő házirendjét.
* [ServiceTypeHealthPolicyMap](/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). A szolgáltatási állapot házirendjeinek egy-egy térképét adja meg. Ezek a szabályzatok az alapértelmezett szolgáltatástípus-állapotra vonatkozó házirendeket helyettesítik az egyes szolgáltatástípus-típusokhoz. Ha például egy alkalmazás állapot nélküli átjáró szolgáltatás típussal és egy állapot-nyilvántartó szolgáltatás típussal rendelkezik, a kiértékeléshez különböző állapotokra állíthatja be az állapotfigyelő házirendeket. Ha a házirendet szolgáltatási típusként adja meg, a szolgáltatás állapotának részletesebb szabályozását is megteheti.

### <a name="service-type-health-policy"></a>Szolgáltatástípus állapot-házirendje
A [szolgáltatástípus állapot-szabályzata](/dotnet/api/system.fabric.health.servicetypehealthpolicy) határozza meg, hogy miként értékelhető ki és összesíthető a szolgáltatások és a szolgáltatások gyermekei. A szabályzat a következőket tartalmazza:

* [MaxPercentUnhealthyPartitionsPerService](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Meghatározza a nem kifogástalan állapotú partíciók maximálisan megengedett százalékos arányát, mielőtt egy szolgáltatás nem kifogástalannak minősül. Alapértelmezett százalék: nulla.
* [MaxPercentUnhealthyReplicasPerPartition](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Meghatározza a nem kifogástalan állapotú replikák maximálisan megengedett százalékos arányát, mielőtt a partíció nem megfelelőnek minősül. Alapértelmezett százalék: nulla.
* [MaxPercentUnhealthyServices](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Meghatározza a nem kifogástalan állapotú szolgáltatások maximálisan megengedett százalékos arányát, mielőtt az alkalmazás nem megfelelőnek minősül. Alapértelmezett százalék: nulla.

Az alábbi példa egy alkalmazási jegyzékből származó részlet:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Állapot kiértékelése
A felhasználók és az automatizált szolgáltatások bármikor kiértékelik az adott entitás állapotát. Egy entitás állapotának kiértékeléséhez az állapotfigyelő összesíti az entitás összes állapotjelentést, és kiértékeli az összes gyermekét (ha alkalmazható). Az állapot-összesítési algoritmus olyan állapotfigyelő házirendeket használ, amelyek meghatározzák az állapotadatok kiértékelésének és a gyermek állapotának összesítésének módját (ha alkalmazható).

### <a name="health-report-aggregation"></a>Állapotjelentés összesítése
Az egyes entitások különböző tulajdonságokon keresztül különböző jelentéskészítők (rendszerösszetevők vagy watchdog) által elküldhető több állapotjelentést is tartalmazhatnak. Az Összesítés a társított állapotházirendek, különösen az alkalmazás vagy a fürt ConsiderWarningAsError tagja. A ConsiderWarningAsError meghatározza a figyelmeztetések kiértékelésének módját.

Az összesített állapotot a rendszer az entitásban található *legrosszabb* állapotjelentés alapján indítja el. Ha van legalább egy hiba állapota jelentés, az összesített állapot hiba.

![Állapotjelentés összesítése hibajelentéssel.][2]

Egy vagy több hibás állapotú jelentést tartalmazó állapotot a rendszer hibaként értékel ki. Ugyanez érvényes a lejárt állapotjelentés esetében is, függetlenül attól, hogy milyen állapotú.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Ha nincsenek hibajelentések, és egy vagy több figyelmeztetés is van, az összesített állapot figyelmeztetés vagy hiba lehet a ConsiderWarningAsError házirend-jelzőtől függően.

![Állapotjelentés összesítése figyelmeztetési jelentéssel és hamis ConsiderWarningAsError.][3]

Állapotjelentés összesítése a figyelmeztetési jelentéssel és a ConsiderWarningAsError false (alapértelmezett) értékre állítva.

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Alárendelt állapot összesítése
Az entitás összesített állapota a gyermek állapotot (ha alkalmazható) tükrözi. A gyermek állapot-állapotok összesítésére szolgáló algoritmus az entitás típusa alapján érvényes állapot-szabályzatokat használja.

![Alárendelt entitások állapotának összesítése][4]

Alárendelt összesítés az állapotfigyelő házirendek alapján.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Miután az állapotfigyelő kiértékelte az összes gyermeket, a rendszer a nem kifogástalan állapotú gyermekek beállított maximális százalékos arányán alapuló állapotot összesíti. A rendszer ezt a százalékértéket az entitás és a gyermek típusa alapján veszi át a szabályzatból.

* Ha minden gyermek rendelkezik OK állapottal, a gyermek aggregált állapota OK.
* Ha a gyerekek mind az OK, mind a figyelmeztetési állapottal rendelkeznek, a gyermek összesített állapota figyelmeztetés.
* Ha vannak olyan hibákkal rendelkező gyermekek, amelyek nem veszik figyelembe a nem kifogástalan állapotú gyermekek maximálisan megengedett százalékos arányát, akkor az összesített szülő állapot hiba.
* Ha a hibás állapotú gyermekek a nem kifogástalan állapotú gyermekek maximálisan megengedett százalékos arányát veszik figyelembe, az összesített szülő állapot figyelmeztetés.

## <a name="health-reporting"></a>Állapotadatok jelentése
A rendszerösszetevők, a rendszerháló alkalmazások és a belső/külső watchdogok jelenthetnek Service Fabric entitásokat. A jelentéskészítők a figyelt entitások állapotának *helyi* meghatározását teszik elérhetővé a figyelés feltételei alapján. Nem kell semmilyen globális vagy összesített adatokat megvizsgálniuk. A kívánt viselkedés az egyszerű jelentéskészítők használata, és nem olyan összetett szervezetek, amelyeknek sok dolgot kell megvizsgálniuk, hogy milyen információkat kell elküldeni.

Az állapotadatok az állapotfigyelő tárolóba való küldéséhez a Jelentéskészítőnek azonosítania kell az érintett entitást, és létre kell hoznia egy állapotjelentést. A jelentés elküldéséhez használja a [FabricClient. HealthClient. ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API-t, a Report Health API-kat, amelyek elérhetők a `Partition` vagy az `CodePackageActivationContext` objektumok, a PowerShell-parancsmagok vagy a REST számára.

### <a name="health-reports"></a>Állapotjelentés
A fürt minden entitásához tartozó [Állapotjelentés](/dotnet/api/system.fabric.health.healthreport) a következő információkat tartalmazza:

* **SourceId forrásazonosító**. Egy olyan karakterlánc, amely egyedileg azonosítja az állapotadatok jelentéskészítőjét.
* Az **entitás azonosítója**. Azonosítja azt az entitást, amelyben a jelentés alkalmazva van. Az [entitás típusa](service-fabric-health-introduction.md#health-entities-and-hierarchy)alapján különbözik:
  
  * Fürt. Nincsenek.
  * Csomópont. Csomópont neve (karakterlánc).
  * Alkalmazás. Az alkalmazás neve (URI). A fürtben üzembe helyezett alkalmazás példányának nevét jelöli.
  * Szolgáltatás. Szolgáltatás neve (URI). A fürtben üzembe helyezett szolgáltatási példány nevét jelöli.
  * Partíció. Partíció-azonosító (GUID). A partíció egyedi azonosítóját jelöli.
  * Replika. Az állapot-nyilvántartó szolgáltatás replikájának azonosítója vagy az állapot nélküli szolgáltatás példányának azonosítója (INT64).
  * DeployedApplication. Az alkalmazás neve (URI) és a csomópont neve (karakterlánc).
  * DeployedServicePackage. Az alkalmazás neve (URI), a csomópont neve (karakterlánc) és a szolgáltatás jegyzékfájljának neve (karakterlánc).
* **Tulajdonság**. Egy *karakterlánc* (nem rögzített enumerálás), amely lehetővé teszi a jelentéskészítő számára az entitás adott tulajdonságának állapotának kategorizálását. Az A Reporter például jelentést készíthet a "Storage" Node01 állapotáról, a "B" Reporter pedig a Node01 "connectivity" tulajdonságának állapotát. A Health Store-ban ezek a jelentések különálló állapotadatokként lesznek kezelve a Node01 entitás számára.
* **Leírás**. Egy karakterlánc, amely lehetővé teszi, hogy a jelentéskészítő részletes információkat szolgáltasson az állapottal kapcsolatos eseményről. A **SourceId forrásazonosító**, a **tulajdonságnak** és a **HealthState** teljes mértékben le kell írnia a jelentést. A leírás a jelentésre vonatkozó, ember által olvasható adatokat adja meg. A szöveg megkönnyíti a rendszergazdák és a felhasználók számára az állapotjelentés megismerését.
* **HealthState**. Egy [enumerálás](service-fabric-health-introduction.md#health-states) , amely leírja a jelentés állapotát. Az elfogadott értékek: OK, figyelmeztetés és hiba.
* **TimeToLive**. Egy TimeSpan, amely azt jelzi, hogy mennyi ideig érvényes az állapotjelentés. A **eltávolításlejáratkor** párosulva lehetővé teszi az állapotfigyelő számára, hogy megismerje a lejárt események kiértékelését. Alapértelmezés szerint az érték végtelen, és a jelentés örökre érvényes.
* **Eltávolításlejáratkor**. Egy logikai érték. Ha igaz értékre van állítva, a rendszer automatikusan eltávolítja a lejárt állapotjelentést az állapotfigyelő tárolóból, és a jelentés nem befolyásolja az entitások állapotának kiértékelését. Akkor használatos, ha a jelentés csak a megadott időszakra érvényes, és a Jelentéskészítőnek nem kell explicit módon törölnie. Emellett a jelentések törlésére is használható a Health Store áruházból (például egy watchdog megváltozása, és nem áll le az előző forrással és tulajdonsággal rendelkező jelentések küldése). A jelentés elküldhető egy rövid TimeToLive, valamint a Eltávolításlejáratkor, amelyekkel törölheti az összes korábbi állapotot az állapotfigyelő áruházból. Ha az érték false (hamis), akkor a rendszer a lejárt jelentést az állapot kiértékelése során észlelt hibaként kezeli. A hamis érték jelzi az állapot-tárolót, hogy a forrásnak rendszeresen jelentenie kell ezt a tulajdonságot. Ha nem, akkor nem kell valami baj a watchdoggal. A watchdog állapotának rögzítése az esemény hibájának figyelembevételével történik.
* **Sorszám**. Egy pozitív egész szám, amelynek folyamatosan növekedni kell, a jelentések sorrendjét jelöli. Az állapotfigyelő szolgáltatás a hálózati késések vagy más problémák miatt későn fogadott elavult jelentések észlelésére szolgál. A rendszer elutasítja a jelentést, ha a sorozatszám kisebb vagy egyenlő, mint a legutóbb alkalmazott szám az azonos entitáshoz, forráshoz és tulajdonsághoz. Ha nincs megadva, a sorozatszám automatikusan létrejön. A sorszámot csak az állapot-átváltásokról szóló jelentések esetén kell végrehajtani. Ebben az esetben a forrásnak emlékeznie kell arról, hogy mely jelentések lettek elküldve, és őrizze meg a feladatátvételhez szükséges adatokat.

Ez a négy információ – SourceId forrásazonosító, entitás-azonosító, tulajdonság és HealthState – minden állapotjelentés esetében kötelező. A SourceId forrásazonosító karakterlánc nem kezdődhet a rendszerjelentések számára fenntartott "**System.**" előtaggal. Ugyanahhoz az entitáshoz csak egy jelentés létezik ugyanahhoz a forráshoz és tulajdonsághoz. Ugyanahhoz a forráshoz és tulajdonsághoz több jelentés is bírálható felül, akár az állapot-ügyfél oldalán (ha a kötegben vannak), vagy az állapotfigyelő oldalon. A pótlás a sorozatszámok alapján történik; az újabb jelentések (nagyobb sorszámokkal) lecserélik a régebbi jelentéseket.

### <a name="health-events"></a>Állapotadatok
Belsőleg az állapotfigyelő tárolja a jelentésekben szereplő összes információt és a további metaadatokat tartalmazó [állapot-eseményeket](/dotnet/api/system.fabric.health.healthevent). A metaadatok között megtalálhatók a jelentésnek az állapot-ügyfélnek adott időpontja, valamint a kiszolgáló oldalán módosított idő. Az állapotadatok visszaadását az [állapot-lekérdezések](service-fabric-view-entities-aggregated-health.md#health-queries)adják vissza.

A hozzáadott metaadatok a következőket tartalmazzák:

* **SourceUtcTimestamp**. Az az idő, amikor a jelentés megkapta az állapotfigyelő ügyfelet (egyezményes világidő).
* **LastModifiedUtcTimestamp**. A jelentés időpontjának utolsó módosításának időpontja a kiszolgáló oldalán (egyezményes világidő).
* **IsExpired**. Egy jelző, amely jelzi, hogy a jelentés elévült-e, amikor az állapotfigyelő futtatta a lekérdezést. Egy esemény csak akkor lehet lejárt, ha a Eltávolításlejáratkor hamis. Ellenkező esetben az eseményt a lekérdezés nem adja vissza, és a rendszer eltávolítja az áruházból.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. Az OK/figyelmeztetés/hiba miatti váltás utolsó időpontja. Ezek a mezők az esemény állapotára váltások előzményeit adják meg.

Az állapot-átváltási mezők intelligensebb riasztásokhoz vagy "előzmények" állapotra vonatkozó információkhoz is használhatók. Olyan forgatókönyveket tesznek lehetővé, mint például:

* Riasztás megjelenítése, ha egy tulajdonság több mint X percen keresztül figyelmeztetés/hiba volt. Egy adott időszak feltételének ellenőrzése elkerüli a riasztások ideiglenes feltételeit. Ha például egy riasztás, ha az állapot több mint öt percig figyelmeztetést kapott, lefordítható a következőre: (HealthState = = Warning és Now-LastWarningTransitionTime > 5 perc).
* Riasztás csak az utolsó X percben megváltoztatott feltételeknél. Ha egy jelentés már a megadott időpont előtt hibát jelzett, figyelmen kívül hagyható, mert már korábban is jelezte.
* Ha egy tulajdonságot a figyelmeztetés és a hiba között kapcsol be, állapítsa meg, hogy mennyi ideig tart a állapota (nem OK). Ha például egy riasztás, ha a tulajdonság több mint öt percen belül nem kifogástalan állapotú, lefordítható (HealthState! = ok és Now-LastOkTransitionTime > 5 perc).

## <a name="example-report-and-evaluate-application-health"></a>Példa: jelentés és az alkalmazás állapotának kiértékelése
Az alábbi példa egy állapotjelentést küld a PowerShellen keresztül az Application **Fabric:/WordCount** a forrás **MyWatchdog**. Az állapotjelentés a "rendelkezésre állás" állapot tulajdonságával kapcsolatos információkat tartalmaz egy hiba állapotában, amely végtelen TimeToLive. Ezután lekérdezi az alkalmazás állapotát, amely összesített állapotú hibákat ad vissza, valamint a jelentett egészségügyi eseményeket az állapotadatok listájában.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Egészségügyi modell használata
Az állapotfigyelő modell lehetővé teszi a Cloud Services és a mögöttes Service Fabric platform méretezését, mivel a figyelési és állapot-meghatározások elosztása a fürtben lévő különböző figyelők között történik.
Más rendszerek egyetlen, központosított szolgáltatással rendelkeznek a fürt szintjén, amely elemzi a szolgáltatások által kibocsátott *esetleges* hasznos információkat. Ez a megközelítés akadályozza a méretezhetőséget. Emellett nem teszi lehetővé, hogy konkrét információkat gyűjtsön a problémák és a lehetséges problémák azonosításához a lehető leghamarabb.

Az állapotfigyelő modell nagy mértékben használatos figyelésre és diagnosztizálásra, a fürt és az alkalmazás állapotának kiértékeléséhez, valamint a figyelt frissítésekhez. Más szolgáltatások az állapotadatok használatával végzik el az automatikus javításokat, felépítik a fürt állapotának előzményeit, és bizonyos körülmények között riasztásokat adnak ki.

## <a name="next-steps"></a>Következő lépések
[Service Fabric állapottal kapcsolatos jelentések megtekintése](service-fabric-view-entities-aggregated-health.md)

[Rendszerállapot-jelentések használata a hibaelhárításhoz](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[A szolgáltatás állapotának jelentése és ellenõrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Egyéni Service Fabric állapotjelentés hozzáadása](service-fabric-report-health.md)

[A szolgáltatások helyi figyelése és diagnosztikája](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric alkalmazás frissítése](service-fabric-application-upgrade.md)
