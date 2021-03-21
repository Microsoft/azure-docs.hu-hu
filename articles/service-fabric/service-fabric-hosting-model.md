---
title: Azure Service Fabric üzemeltetési modell
description: Ismerteti egy telepített Service Fabric szolgáltatás replikái (vagy példányai) és a Service-Host folyamat közötti kapcsolatot.
author: harahma
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: harahma
ms.custom: devx-track-csharp
ms.openlocfilehash: 9932c11332a616928d59c213d4f4806feb81cfe2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791645"
---
# <a name="azure-service-fabric-hosting-model"></a>Azure Service Fabric üzemeltetési modell
Ez a cikk áttekintést nyújt az Azure Service Fabric által biztosított alkalmazás-üzemeltetési modellekről, valamint ismerteti a **megosztott folyamat** és az **exkluzív folyamat** modelljei közötti különbségeket. Leírja, hogyan néz ki egy telepített alkalmazás egy Service Fabric csomóponton, valamint a szolgáltatás replikái (vagy példányai) és a Service-Host folyamat közötti kapcsolat.

A folytatás előtt győződjön meg arról, hogy tisztában van a különböző fogalmakkal és kapcsolatokkal az [alkalmazás modellezése Service Fabricban][a1]. 

> [!NOTE]
> Ebben a cikkben, kivéve, ha explicit módon említi, hogy valami más:
>
> - A *replika* egy állapot-nyilvántartó szolgáltatás replikáját és egy állapot nélküli szolgáltatás egy példányát jelenti.
> - A *CodePackage* egy *ServiceHost* -folyamattal egyenértékűként kezeli a rendszer, amely regisztrálja a *ServiceType*, és az adott *ServiceType* tartozó szolgáltatások replikáit tárolja.
>

Az üzemeltetési modell megismeréséhez Lássunk egy példát. Tegyük fel, hogy van egy "MyAppType" *alkalmazásban (* , amely a "MyServiceType" *ServiceType* rendelkezik. A "MyServiceType" a (z) "MyServicePackage" *szervizcsomaggal* rendelkezik, amelynek *CodePackage* "MyCodePackage". A "MyCodePackage" a futtatáskor regisztrálja a "MyServiceType" *ServiceType* .

Tegyük fel, hogy három csomópontos fürttel rendelkezünk, és létrehozunk *egy "* MyAppType" típusú **App1** . Ebben az Application **fabricben:/App1**-ben létrehozunk egy "MyServiceType" típusú Service **Fabric-t:/App1/servicea-** et. Ennek a szolgáltatásnak két partíciója van (például **P1** és **P2**), és a partíciók három replikája. Az alábbi ábrán az alkalmazás nézete látható, amelyet egy csomóponton helyeztek üzembe.


![Olyan diagram, amely az alkalmazás nézetét jeleníti meg a csomóponton való üzembe helyezéskor.][node-view-one]


Service Fabric aktivált "MyServicePackage" ("MyCodePackage"), amely a partíciók replikáit üzemelteti. A fürt összes csomópontja azonos nézettel rendelkezik, mert a partíciók replikáinak számát úgy döntöttük, hogy a fürt csomópontjainak száma egyenlő legyen. Hozzunk létre egy másik szolgáltatást, **Fabric:/App1/ServiceB**, az Application **Fabric:/App1**. A szolgáltatásnak van egy partíciója (például **P3**), és a partíciók három replikája. Az alábbi ábrán a csomópont új nézete látható:


![A csomópont új nézetét ábrázoló diagram.][node-view-two]


Service Fabric helyezte el az új replikát a Service **Fabric:/App1/ServiceB** partíció **P3** -as verziójában a (z) "MyServicePackage" meglévő aktiválása során. Most. hozzunk létre egy másik Application **Fabric-t:/App2** "MyAppType" típussal. A **hálón belül:/App2**, hozzon létre egy Service **Fabric-t:/App2/servicea**. Ennek a szolgáltatásnak két partíciója van (**P4** és **P5**), és a partíciók három replikája. A következő ábrán az új csomópont nézet látható:


![Az új csomópont nézetét bemutató diagram.][node-view-three]


Service Fabric aktiválja a "MyServicePackage" új példányát, amely a "MyCodePackage" új példányát indítja el. A Service **Fabric:/App2/servicea** (**P4** és **P5**) partícióinak replikái az új "MyCodePackage" másolatba kerülnek.

## <a name="shared-process-model"></a>Megosztott folyamat modellje
Az előző szakasz a Service Fabric által biztosított alapértelmezett üzemeltetési modellt ismerteti, amelyet a megosztott folyamat modellnek nevezünk. Ebben a modellben egy adott alkalmazás esetében az adott *szervizcsomag* csak egy példánya aktiválódik egy csomóponton (amely elindítja az összes benne található *CodePackages* ). Az adott *ServiceType* összes szolgáltatásának replikái a *CodePackage* , amelyek regisztrálják a *ServiceType*. Más szóval az adott *ServiceType* egy csomópontján lévő összes szolgáltatás replikái ugyanazt a folyamatot használják.

## <a name="exclusive-process-model"></a>Kizárólagos folyamatmodell
A Service Fabric által biztosított másik üzemeltetési modell az exkluzív folyamatmodell. Ebben a modellben egy adott csomóponton minden replika a saját dedikált folyamatában él. Service Fabric aktiválja a *szervizcsomagok* új példányát (amely elindítja az összes benne található *CodePackages* ). A replikák abban a *CodePackage* vannak elhelyezve, amely regisztrálta annak a szolgáltatásnak a *ServiceType* , amelyhez a replika tartozik. 

Ha a 5,6-es vagy újabb verzióját Service Fabric használja, akkor kiválaszthatja az exkluzív folyamatmodell használatát a szolgáltatás létrehozásakor ( [PowerShell][p1], [Rest][r1]vagy [FabricClient][c1]használatával). A **ServicePackageActivationMode** a következőként kell megadni: "ExclusiveProcess".

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Ha az alkalmazás jegyzékfájljában egy alapértelmezett szolgáltatás található, akkor a **ServicePackageActivationMode** attribútum megadásával kiválaszthatja az exkluzív folyamat típusát:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Most hozzon létre egy másik szolgáltatást, **Fabric:/App1/ServiceC**, az Application **Fabric:/App1**. A szolgáltatás két partícióval rendelkezik (például **P6** és **P7**), és a partíciók három replikáját. A **ServicePackageActivationMode** a "ExclusiveProcess" értékre kell beállítani. A következő ábrán a csomópont új nézete látható:


![Központilag telepített alkalmazás csomópont nézetének diagramja][node-view-four]


Amint láthatja, Service Fabric aktiválta a "MyServicePackage" két új példányát (egyet az egyes replikák számára a Partition **P6** és a **P7**). Service Fabric az egyes replikákat a *CodePackage* dedikált példányán helyezi el. Ha az exkluzív folyamatmodell használatát használja egy adott alkalmazáshoz, egy adott *szervizcsomag* több példánya is aktív lehet egy csomóponton. Az előző példában a "MyServicePackage" három példánya aktív a **Fabric:/App1**. A "MyServicePackage" ezen aktív példányai mindegyike társítva van egy **ServicePackageActivationId** . Ez az azonosító azonosítja az Application **Fabric:/App1** alkalmazáson belüli másolatot.

Ha csak a megosztott folyamat modelljét használja egy alkalmazáshoz, a csomóponton csak egy aktív példány található a *szervizcsomagban* . A *szervizcsomagok* ezen aktiválásának **ServicePackageActivationId** üres karakterlánc. Ebben az esetben például a **Fabric:/App2**.

> [!NOTE]
>- A megosztott folyamat üzemeltetési modellje megegyezik a **ServicePackageActivationMode** **SharedProcess**. Ez az alapértelmezett üzemeltetési modell, és a **ServicePackageActivationMode** nem kell megadni a szolgáltatás létrehozásakor.
>
>- Az exkluzív folyamat üzemeltetési modellje megfelel a **ServicePackageActivationMode** **ExclusiveProcess**. A beállítás használatához explicit módon meg kell adnia azt a szolgáltatás létrehozásakor. 
>
>- A szolgáltatás üzemeltetési modelljének megtekintéséhez kérdezze le a [szolgáltatás leírását][p2], és tekintse meg a **ServicePackageActivationMode** értékét.
>
>

## <a name="work-with-a-deployed-service-package"></a>Üzembe helyezett szervizcsomag használata
Egy csomóponton lévő *szervizcsomag* aktív másolatát központilag [telepített szervizcsomagnak][p3]nevezzük. Ha a kizárólagos folyamatmodell használatával hoz létre szolgáltatásokat, akkor előfordulhat, hogy az adott alkalmazáshoz több üzembe helyezett szervizcsomag is létezik ugyanahhoz a *szervizcsomaghoz*. Ha a központilag telepített szervizcsomaggal kapcsolatos műveleteket hajt végre, meg kell adnia a **ServicePackageActivationId** az adott központilag telepített szervizcsomag azonosításához. Adja meg például az azonosítót, ha a központilag [telepített szervizcsomag állapotát][p4] vagy [egy telepített szervizcsomag kódjának újraindítását][p5]jelenti.

A központilag telepített szervizcsomag **ServicePackageActivationId** a csomóponton [telepített szervizcsomagok][p3] listájának lekérdezésével találhatja meg. Amikor lekérdezi a [telepített szolgáltatások típusait][p6], a [központilag telepített replikákat][p7]és az [üzembe helyezett csomagokat][p8] egy csomóponton, a lekérdezés eredménye a fölérendelt központilag telepített **ServicePackageActivationId** is tartalmazza.

> [!NOTE]
>- Egy adott alkalmazás esetében az adott csomóponton a megosztott folyamat üzemeltetési modellje alatt a *szervizcsomagok* csak egy példánya aktiválva van. A **ServicePackageActivationId** megegyezik az *üres karakterlánccal*, és nem kell megadni az üzembe helyezett szervizcsomaggal kapcsolatos műveletek végrehajtásakor. 
>
> - Egy adott alkalmazás esetében a kizárólagos eljárás-üzemeltetési modellben egy adott alkalmazás esetében a *szervizcsomagok* egy vagy több példánya is aktív lehet. Minden aktiválás *nem üres* **ServicePackageActivationId** rendelkezik, amely a telepített szervizcsomaggal kapcsolatos műveletek végrehajtásakor van megadva. 
>
> - Ha a **ServicePackageActivationId** nincs megadva, az alapértelmezett érték *üres karakterlánc*. Ha a megosztott folyamat modelljében aktivált telepített szervizcsomag található, akkor a rendszer a műveletet hajtja végre. Ellenkező esetben a művelet sikertelen lesz.
>
> - Ne végezzen egyszeri lekérdezést, és gyorsítótárazza a **ServicePackageActivationId**. Az azonosító dinamikusan jön létre, és különböző okok miatt változhat. A **ServicePackageActivationId** igénylő művelet végrehajtása előtt először le kell kérdezni a [telepített szervizcsomagok][p3] listáját egy csomóponton. Ezután használja a lekérdezési eredmény **ServicePackageActivationId** az eredeti művelet végrehajtásához.
>
>

## <a name="guest-executable-and-container-applications"></a>Vendég végrehajtható fájl és tároló alkalmazások
Service Fabric a [vendég végrehajtható fájlját][a2] és a [tároló][a3] alkalmazásait olyan állapot nélküli szolgáltatásokként kezeli, amelyek önállóan vannak tárolva. A *ServiceHost* nem rendelkezik Service Fabric futtatókörnyezettel (folyamat vagy tároló). Mivel ezek a szolgáltatások önállóan vannak tárolva, a *ServiceHost* replikák száma nem alkalmazható ezekre a szolgáltatásokra. Az ezekkel a szolgáltatásokkal használt leggyakoribb konfiguráció egypartíciós, a [InstanceCount][c2] egyenlő-1 (a fürt minden csomópontján futó szolgáltatási kód egy példánya). 

Ezeknek a szolgáltatásoknak az alapértelmezett **ServicePackageActivationMode** a **SharedProcess**, amely esetben a Service Fabric csak a *szervizcsomagok* egy példányát aktiválja egy adott alkalmazás csomópontján.  Ez azt jelenti, hogy a szolgáltatási kód csak egy példánya fog futni egy csomóponton. Ha azt szeretné, hogy a szolgáltatás kódjának több példánya is fusson egy csomóponton, a szolgáltatás létrehozásakor adja meg a **ServicePackageActivationMode** **ExclusiveProcess** . Ezt például akkor teheti meg, ha több szolgáltatást hoz létre (*service1* – *ServiceN*) a *ServiceType* ( *ServiceManifest*-ben megadott), vagy ha a szolgáltatás több particionált. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Meglévő szolgáltatás üzemeltetési modelljének módosítása
Jelenleg nem változtathatja meg egy meglévő szolgáltatás üzemeltetési modelljét a megosztott folyamatról az exkluzív folyamatra (vagy fordítva).

## <a name="choose-between-the-hosting-models"></a>Választás az üzemeltetési modellek között
Értékelje ki, hogy melyik üzemeltetési modell felel meg legjobban az igényeinek. A megosztott folyamat modellje az operációs rendszer erőforrásainak jobb használatát használja, mivel kevesebb folyamat van elindítva, és ugyanabban a folyamatban több replika is megoszthatja a portokat. Ha azonban az egyik replika hibát jelez, amikor le kell leállítania a szolgáltatást a gazdagépen, az hatással van az összes többi replikára ugyanazon a folyamaton belül.

 Az exkluzív folyamatmodell jobb elkülönítést biztosít a saját folyamatában lévő minden replikával. Ha a replikák egyike hibát tartalmaz, az nem érinti a többi replikát. Ez a modell olyan esetekben hasznos, amikor a kommunikációs protokoll nem támogatja a portok megosztását. Lehetővé teszi, hogy az erőforrás-szabályozást a replika szintjén alkalmazza. Az exkluzív folyamat azonban több operációsrendszer-erőforrást használ, mivel egy folyamatot indít el a csomóponton található minden replikához.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Exkluzív folyamatmodell-és alkalmazás-modell megfontolások
A legtöbb alkalmazás esetében az alkalmazást Service Fabric egy *ServiceType* *-t kell* megtartania. 

Bizonyos esetekben Service Fabric több *ServiceType* is *engedélyez (és* egy *CodePackage* több *ServiceType* is regisztrálhat). Az alábbiakban néhány példát láthat a konfigurációk hasznos lehetőségeire:

- Optimalizálni szeretné az erőforrás-használatot a kevesebb folyamat elindításával, és egy folyamatnál nagyobb replika-sűrűséggel.
- A különböző *ServiceTypes* replikáinak meg kell osztaniuk néhány olyan gyakori adatmennyiséget, amely magas inicializálási vagy memória-díjszabással rendelkezik.
- Ingyenes szolgáltatást kínál, és a szolgáltatás összes replikáját ugyanazzal a folyamattal kívánja korlátozni az erőforrás-felhasználáshoz.

Az exkluzív folyamat-üzemeltetési modell nem *koherens egy olyan* alkalmazás modelljével, amely több *ServiceTypes* rendelkezik. Ennek az az oka, hogy a *szervizcsomagok* több *ServiceTypes* -t úgy terveztek, hogy nagyobb erőforrás-megosztást biztosítson a replikák között, és lehetővé teszi a nagyobb replikák sűrűségét Az exkluzív folyamatmodell különböző eredmények elérésére szolgál.

Vegye figyelembe, hogy több *ServiceTypes* - *csomag esetében egy másik* *CodePackage* regisztrálja az egyes *ServiceType*. Tegyük fel, hogy van egy "MultiTypeServicePackage" nevű *szervizcsomagja* , amely két *CodePackages* rendelkezik:

- "MyCodePackageA", amely a "MyServiceTypeA" *ServiceType* regisztrálja.
- "MyCodePackageB", amely a "MyServiceTypeB" *ServiceType* regisztrálja.

Most tegyük fel, hogy létrehozunk egy alkalmazást, **Fabric:/SpecialApp**. A **Fabric:/SpecialApp**-ben a következő két szolgáltatást hozunk létre az exkluzív folyamatmodell-modellel:

- Service **Fabric:/SpecialApp/** a (z) "MyServiceTypeA" típusú Service, két partícióval (például **P1** és **P2**) és partíciók három replikával.
- Service **Fabric:/SpecialApp/ServiceB** "MyServiceTypeB" típusú, két partícióval (**P3** és **P4**) és partíciók három replikával.

Egy adott csomóponton mindkét szolgáltatás két replikával rendelkezik. Mivel az exkluzív folyamatmodell használatával hozza létre a szolgáltatásokat, Service Fabric aktiválja a "MyServicePackage" egy új példányát az egyes replikák esetében. A "MultiTypeServicePackage" minden aktiválása elindítja a "MyCodePackageA" és a "MyCodePackageB" másolatát. Azonban a "MyCodePackageA" vagy a "MyCodePackageB" csak az egyik a replikát futtatja, amelyhez a "MultiTypeServicePackage" aktiválva lett. Az alábbi ábrán a csomópont nézet látható:


![A csomópont nézetét megjelenítő diagram.][node-view-five]


A (z) "MultiTypeServicePackage" a Service **Fabric:/SpecialApp/servicea**, a (z) "MyCodePackageA" **nevű, a** (z) "" a replika tárolására szolgáló replikájának aktiválásakor. A "MyCodePackageB" fut. Hasonlóképpen, a "MultiTypeServicePackage" aktiválása a Service **Fabric:/SpecialApp/ServiceB** partíciójának **P3** -as replikájában a "MyCodePackageB" a replikát üzemelteti. A "MyCodePackageA" fut. Ezért minél több *CodePackages* (a különböző *ServiceTypes* regisztrálása *), annál* nagyobb a redundáns erőforrás-használat. 
 
 Ha azonban a (z) **:/SpecialApp/servicea** és a **Fabric:/SpecialApp/ServiceB** szolgáltatást a megosztott négyszínes modellel hozza létre, Service Fabric aktiválja a "MultiTypeServicePackage" csak egy példányát az Application **Fabric:/SpecialApp**. A "MyCodePackageA" a Service **Fabric:/SpecialApp/servicea** összes replikáját tárolja. A "MyCodePackageB" a Service **Fabric:/SpecialApp/ServiceB** összes replikáját tárolja. A következő ábrán a csomópont nézet látható ebben a beállításban: 


![A telepített alkalmazás csomópont nézetének diagramja][node-view-six]


Az előző példában úgy tűnhet, hogy ha a "MyCodePackageA" regisztrálja a "MyServiceTypeA" és a "MyServiceTypeB" karakterláncot, és nincs "MyCodePackageB", akkor nem fut redundáns *CodePackage* . Bár ez helyes, ez az alkalmazás-modell nem illeszkedik az exkluzív folyamat üzemeltetési modelljéhez. Ha a cél az, hogy az egyes replikákat a saját dedikált folyamatában helyezze el, nem kell mindkét *ServiceTypes* regisztrálnia ugyanabból a *CodePackage*. Ehelyett egyszerűen helyezze el az egyes *ServiceType* a saját *szervizcsomagjában*.

### <a name="reliable-services-and-actor-forking-subprocesses"></a>Reliable Services-és Actor-elágazási alfolyamatok

A Service Fabric nem támogatja a megbízható szolgáltatásokat és a későbbiekben megbízható Actors elágazási alfolyamatokat. A nem támogatott alfolyamatok nem regisztrálhatók, és a visszavonási jogkivonatok csak a regisztrált folyamatoknak [CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext) , ezért nem használhatók. az olyan problémák, mint például a frissítési hibák, ha az alfolyamatok nem zárulnak le, miután a fölérendelt folyamat megkapta a lemondási tokent.

## <a name="next-steps"></a>Következő lépések
[Alkalmazás becsomagolása][a4] és üzembe helyezése készen áll a telepítésre.

[Alkalmazások telepítése és eltávolítása][a5]. Ez a cikk azt ismerteti, hogyan használható a PowerShell az alkalmazások példányainak kezeléséhez.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: /rest/api/servicefabric/sfclient-api-createservice

[c1]: /dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: /dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: /powershell/module/servicefabric/new-servicefabricservice
[p2]: /powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[p4]: /powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: /powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: /powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: /powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
