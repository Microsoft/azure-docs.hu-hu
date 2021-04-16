---
title: Az Azure Service Fabric kiadásai
description: Az Azure-előfizetés kibocsátási Service Fabric. Információkat tartalmaz a legújabb funkciókról és fejlesztésekről a Service Fabric.
ms.date: 04/13/2021
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 2a035f531e03dc42e8be4f3dab403406eb7c8f14
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518626"
---
# <a name="service-fabric-releases"></a>Service Fabric kiadások

Ez a cikk további információkat tartalmaz a legújabb kiadásokról és frissítésekről a Service Fabric és az SDK-khoz.

A következő erőforrások is elérhetők:
- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Hibaelhárítási útmutatók</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">Problémakövetés</a> 
- <a href="/azure/service-fabric/service-fabric-support" target="blank">Támogatási lehetőségek</a> 
- <a href="/azure/service-fabric/service-fabric-versions" target="blank">Támogatott verziók</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Kódminták</a>


## <a name="service-fabric-80"></a>Service Fabric 8.0

Nagy izgatottan jelentjük be, hogy a Service Fabric-futtatás 8.0-s kiadása az eszközök és az SDK frissítései mellett különböző Azure-régiókban is elérhető. A .NET SDK, a Java SDK és Service Fabric futásidejű frissítések a Webplatform-telepítőn, a NuGet-csomagokon és a Maven-adattárakon keresztül érhetők el.

### <a name="key-announcements"></a>Fontos közlemények

- **A .NET** 5 5 támogatás általános rendelkezésre állása Windows rendszeren
- **Stateless** [NodeTypes általános rendelkezésre állása](https://docs.microsoft.com/azure/service-fabric/service-fabric-stateless-node-types)
- Állapot nélküli szolgáltatáspéldányok áthelyezésének képessége
- Paraméteres DefaultLoad hozzáadása az alkalmazásjegyzékhez
- Egyszeres replikafrissítések esetén – lehetőség van néhány fürtszintű beállítás alkalmazásszinten való definiálásra
- Intelligens elhelyezés képessége csomópontcímkék alapján
- Lehetőség a fürt állapotát befolyásoló nem egészségügyi csomópontok százalékos küszöbértékének meghatározására
- Képesség a betöltött szolgáltatások lekérdezésére
- Új hibakódok új intervallumának hozzáadása
- Szolgáltatáspéldány befejezettként való megjelölésének képessége
- Hullámalapú üzembe helyezési modell támogatása az automatikus frissítésekhez
- A tárolóba telepített alkalmazásokhoz hozzá van adva a készenlét-mintavétel
- A UseSeparateSecondaryMoveCost alapértelmezett true (igaz) beállításának engedélyezése
- Ki van javítva a StateManager, hogy a referenciát a lehető leghamarabb kiadja
- A központi titkos kulcsok szolgáltatásának eltávolításának blokkolása a felhasználói titkos kulcsok tárolása közben


### <a name="service-fabric-80-releases"></a>Service Fabric 8.0-s verziók
| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| 2021. április 8. | [Azure Service Fabric 8.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-0-release/ba-p/2260016)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_80.md)|


## <a name="previous-versions"></a>Korábbi verziók

### <a name="service-fabric-72"></a>Service Fabric 7.2

#### <a name="key-announcements"></a>Fontos közlemények

- **Előzetes** verzió: [**Service Fabric felügyelt fürtök**](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-managed-clusters-are-now-in-public-preview/ba-p/1721572) már nyilvános előzetes verzióban állnak. Service Fabric fürtök célja a fürtök üzembe helyezésének és felügyeletének egyszerűsítése azáltal, hogy a fürtöt Service Fabric alapul szolgáló erőforrásokat egyetlen ARM-erőforrásba beágyazva. További részletekért lásd a Service Fabric fürt áttekintését ismertető [témakört.](./overview-managed-cluster.md)
- **Előzetes** verzió: Nyilvános előzetes verzióban elérhető az állapot nélküli szolgáltatások támogatása [**a**](./service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) csomópontok számnál több példányban. Az elhelyezési szabályzatok lehetővé teszik egy partíció több állapot nélküli példányának létrehozását egy csomóponton.
- [**Elérhető a FabricObserver (FO) 3.0.**](https://aka.ms/sf/fabricobserver)
    - Most már futtathatja a FabricObserver virtuális kiszolgálót Linux- és Windows-fürtökben.
    - Most már létrehozhat egyéni megfigyelő beépülő modulokat. A [](https://github.com/microsoft/service-fabric-observer/tree/master/SampleObserverPlugin) [részletekért és a kódért](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Plugins.md) tekintse meg a beépülő modulok fontos fájlját és a minta beépülő modul projektjét.
    - Az alkalmazásparaméterek frissítésével mostantól bármilyen megfigyelő beállítást módosíthat. Ez azt jelenti, hogy már nem kell újra üzembe tartania a FO-t adott megfigyelői beállítások módosításához. Tekintse meg a [mintát.](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Using.md#parameterUpdates)
- [**Az Ubuntu 18.04 OneBox-tároló rendszerképének támogatása.**](https://hub.docker.com/_/microsoft-service-fabric-onebox)
- **Előzetes** verzió: [ **KeyVault-referencia Service Fabric alkalmazások csak **verziószámos titkos kulcsokat támogatnak.** A verzió nélküli titkos kulcsok nem támogatottak.**](./service-fabric-keyvault-references.md)
- Az SF SDK-hoz a VS 2019 legújabb, 16.7.6-os vagy 16.8-as előzetes verziójának frissítése szükséges, hogy új .NET-keretrendszer állapot nélküli/állapot-/ actors-projekteket hoz létre. Ha nem ismeri a legújabb VS-frissítést, a szolgáltatásprojekt létrehozása után a csomagkezelővel telepítse a Microsoft.ServiceFabric.Services (4.2.x verzió) alkalmazást az állapot-/állapot nélküli projektekhez és a Microsoft.ServiceFabric.Actors (4.2.x verzió) az nuget.org-
- **RunToCompletion:** Service Fabric támogatja a futtatható vendég végrehajtható fájlok futtatástól befejezésig fogalmát. Ezzel a frissítéssel, miután a replika lefutott, a replikához lefoglalt fürterőforrások felszabadulnak.
- [**Továbbfejlesztve lett az erőforrás-szabályozás**](./service-fabric-resource-governance.md)támogatása: lehetővé teszi a kérelmek és a processzor- és memória-erőforrásokra vonatkozó specifikációk korlátozását.

#### <a name="service-fabric-72-releases"></a>Service Fabric 7.2-es verziók
| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| 2020. október 21. | [Azure Service Fabric 7.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-release/ba-p/1805653)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72-releasenotes.md)|
| 2020. november 9. | [Az Azure Service Fabric 7.2 másodperces frissítés kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-second-refresh-release/ba-p/1874738) | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU2-releasenotes.md) |
| 2020. november 10.  | Az Azure Service Fabric 7.2 harmadik frissítés kiadása | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU3-releasenotes.md) |
| 2020. december 2. | [Az Azure Service Fabric 7.2 negyedik frissítés kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fourth-refresh-release/ba-p/1950584) | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU4.md)
| 2021. január 25. | [Az Azure Service Fabric 7.2 ötödik frissítés kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fifth-refresh-release/ba-p/2096575) | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU5-ReleaseNotes.md)
| 2021. február 17. | [Az Azure Service Fabric 7.2 hatodik frissítés kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-sixth-refresh-release/ba-p/2144685) | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU6-ReleaseNotes.md)
| 2021. március 10. | [Az Azure Service Fabric 7.2-es verzió hatodik frissítése](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-seventh-refresh-release/ba-p/2201100) | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU7-releasenotes.md)


### <a name="service-fabric-71"></a>Service Fabric 7.1-es

A JELENLEGI COVID-19-válság miatt, valamint az ügyfeleink előtt álló kihívások figyelembe véve elérhetővé teszi a 7.1-es verziókat, de nem frissítjük automatikusan az automatikus frissítésre beállított fürtöt. Az automatikus frissítéseket további értesítésig szüneteltetjük, hogy az ügyfelek a váratlan fennakadások elkerülése érdekében a számukra leginkább megfelelő frissítéseket alkalmazva gondoskodhatnak róla.

A 7.1-es verzióra a(Azure Portal) vagy egy Azure Resource Manager keresztül [frissítheti.](./service-fabric-cluster-upgrade-version-azure.md#resource-manager-template) [](./service-fabric-cluster-upgrade-version-azure.md#manual-upgrades-with-azure-portal)

Service Fabric automatikus frissítéssel rendelkező fürtök automatikusan megkapják a 7.1-es frissítést a normál bevezetési eljárás folytatása után. A bevezetés szokásos megkezdése előtt újabb bejelentést biztosítanak a Service Fabric [Tech Community Webhelyén.](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)
Emellett a 6.5-től 7.1-ig kezdődő nagyobb kiadások támogatási dátumának vége felé közzétett frissítéseket [itt.](./service-fabric-versions.md) 

#### <a name="key-announcements"></a>Fontos közlemények

- **A felügyelt identitások** Service Fabric általános rendelkezésre [ **állása Service Fabric alkalmazásokhoz**](./concepts-managed-identity.md)
- [**Az Ubuntu 18.04 támogatása**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - [**Előzetes verzió:**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)Virtuálisgép-méretezési készlet – Az operációsrendszer-lemezek támogatása **: A hosszú távra készült operációsrendszer-lemezek a helyi virtuális gépen létrehozott tárolók, és nem a távoli Azure Storage-ba vannak mentve. Ezek ajánlottak az összes Service Fabric csomóponttípushoz (elsődleges és másodlagos), mert a hagyományos állandó operációsrendszer-lemezekhez képest a rendszer operációsrendszer-lemezei:
      -  Az operációsrendszer-lemez olvasási/írási késésének csökkentése
      -  Gyorsabb alaphelyzetbe állítási/rendszerkép-újratelepítési csomópontkezelési műveletek engedélyezése
      -  Csökkentheti az általános költségeket (a lemezek ingyenesek, és nem jár további tárolási költségekkel)
- Szolgáltatásvégpont-tanúsítványok deklarációjának támogatása Service Fabric [**alkalmazásokban a tulajdonos közneve alapján.**](./service-fabric-service-manifest-resources.md)
- [**Állapot-mintavételek támogatása tárolóba ezett szolgáltatásokhoz:**](./probes-codepackage.md)A tárolóba telepített alkalmazások liveness-mintavételi mechanizmusának támogatása. A liveness-mintavétel segít a tárolóba helyezni alkalmazás megfelelőségét, és ha nem válaszolnak időben, az újraindítást eredményez. 
- [**Inicializáló kódcsomagok támogatása**](./initializer-codepackages.md) [tárolókhoz és](./service-fabric-containers-overview.md) vendég [végrehajtható alkalmazásokhoz.](./service-fabric-guest-executables-introduction.md) Ez lehetővé teszi kódcsomagok (például tárolók) adott sorrendben való futtatását a szolgáltatáscsomag inicializálásának végrehajtásához.
- **A FabricObserver és a ClusterObserver** állapot nélküli alkalmazások, amelyek Service Fabric SF-fürt különböző aspektusainak telemetriája alapján rögzítik a telemetriai adatokat. Mindkét alkalmazás készen áll az éles Windows-fürtökben való üzembe helyezésre, így gazdag telemetriai adatokat rögzíthet az ApplicationInsights, az EventSource és a LogAnalytics támogatásával.
    - [**FabricObserver (FO) 2.0**](https://github.com/microsoft/service-fabric-observer)– az összes csomóponton fut, állapoteseményeket hoz létre, telemetriát bocsát ki, amikor a felhasználó által konfigurált erőforrás-használati küszöbértékek elérése megtelik. Ez a kiadás számos fejlesztést tartalmaz a monitorozás, az adatkezelés, az állapotesemények részletei és a strukturált telemetria terén.
     - [**ClusterObserver (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) – egyetlen csomóponton fut, és fürtszintű állapot-telemetriát rögzít. Ebben a kiadásban a ClusterObserver a csomópont állapotát is figyeli, és telemetriát küld, ha a csomópont leáll, letiltja/letiltja a felhasználó által megadott időtartamnál hosszabb ideig.

#### <a name="improve-application-life-cycle-experience"></a>Az alkalmazás életciklus-élményének javítása

- **[Előzetes verzió:Kiürítés](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)** kérése: A tervezett szolgáltatáskarbantartás, például a szolgáltatásfrissítések vagy a csomópont-deaktiválás során engedélyezni szeretné, hogy a szolgáltatások elegánsan ürítsék ki a kapcsolatokat. Ez a funkció egy példánylezárási késleltetési időtartamot ad hozzá a szolgáltatás konfigurációjában. A tervezett műveletek során az SF eltávolítja a szolgáltatás címét a felderítésből, majd ezt az időtartamot megvárja a szolgáltatás leállítása előtt.
- **[Automatikus alcsoportészlelés és -terheléselosztás:](./cluster-resource-manager-subclustering.md)** A részcsoport-elosztás akkor történik, ha a különböző elhelyezési korlátozásokkal kapcsolatos szolgáltatások közös terhelési [metrikával vannak.](./service-fabric-cluster-resource-manager-metrics.md) Ha a különböző csomópontkészletek terhelése jelentősen eltér, a Service Fabric fürterőforrás-kezelő úgy véli, hogy a fürt kiegyensúlyozatlan, még akkor is, ha az elhelyezési korlátozások miatt a lehető legjobb egyensúly áll fenn. Ennek eredményeképpen megkísérli újra egyensúlyba hozni a fürtöt, ami szükségtelen szolgáltatásmozgásokat okozhat (mivel az "egyensúlyi" nem javítható jelentősen). A kiadástól kezdve a fürterőforrás-kezelő megpróbálja automatikusan észlelni ezeket a konfigurációkat, és megérteni, hogy mikor javítható a kiegyensúlyozatlanság a mozgással, és mikor kell inkább egyedül hagynia a dolgokat, mivel nem lehet jelentős javulást tenni.  
- [**Eltérő áthelyezési**](./service-fabric-cluster-resource-manager-movement-cost.md)költség a másodlagos replikákhoz: Bevezettük a Nagyon Magas új áthelyezési költségértéket, amely bizonyos forgatókönyvekben további rugalmasságot biztosít annak meghatározásához, hogy érdemes-e külön áthelyezési költséget használni a másodlagos replikákhoz.
- Engedélyezett [**liveness-mintavételi**](./probes-codepackage.md) mechanizmus tárolóba ezett alkalmazásokhoz. Az élőség-mintavétel segít a tárolóba helyezni alkalmazás megfelelőségét, és ha nem válaszolnak időben, az újraindítást eredményez.
- [**Futtatás befejezésig/egyszer a szolgáltatásokhoz**](./run-to-completion.md)**

#### <a name="image-store-improvements"></a>lemezképtároló fejlesztései
 - Service Fabric 7.1-es rendszer alapértelmezés szerint egyéni átvitelt használ a csomópontok közötti **fájlátvitel biztonságossá történő átvitele érdekében.** Az SMB-fájlmegosztás függősége el lett távolítva a 7.1-es verzióból. A biztonságos SMB-fájlmegosztások továbbra is léteznek olyan csomópontokon, amelyek lemezképtároló szolgáltatásreplikát tartalmaznak, hogy az ügyfél levállalja az alapértelmezett beállításokat, illetve hogy frissítsen, és a régi verzióra váltsa vissza.
       
 #### <a name="reliable-collections-improvements"></a>A Reliable Collections fejlesztései

- A memóriában csak a [**Reliable Collectionst**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections)használó állapot-szolgáltatási tárolók támogatása: A volatile Reliable Collections lehetővé teszi az adatok lemezen való megőrzését a nagy léptékű kimaradásokkal szembeni tartósság érdekében, például olyan számítási feladatokhoz használhatók, mint a replikált gyorsítótár, ahol az alkalmankénti adatvesztés elfogadható. A Volatile [Reliable Collections](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)korlátozásai és korlátozásai alapján ezt olyan számítási feladatok esetén javasoljuk, amelyek nem kell állandósodni, olyan szolgáltatások esetén, amelyek a kvórumvesztés ritka alkalmait kezelik.
- [**Előzetes verzió: Service Fabric Backup-kezelő:**](https://github.com/microsoft/service-fabric-backup-explorer)A Reliable Collections biztonsági mentések egyszerű kezelése Service Fabric állapot-Service Fabric Backup-kezelő lehetővé teszi, hogy a felhasználók
    - A Reliable Collections tartalmának naplózása és áttekintése,
    - Aktuális állapot frissítése konzisztens nézetre
    - A Reliable Collections aktuális pillanatképének biztonsági mentése
    - Adatsérülés kijavítása
                 
#### <a name="service-fabric-71-releases"></a>Service Fabric 7.1-es kiadások
| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| 2020. április 20. | [Azure Service Fabric 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 2020. június 16. | [Microsoft Azure Service Fabric 7.1 első frissítés](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 2020. július 20. | [Microsoft Azure Service Fabric 7,1 másodperces frissítés](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 2020. augusztus 12. | [Microsoft Azure Service Fabric 7.1 harmadik frissítés](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| 2020. szeptember 10. | [Microsoft Azure Service Fabric 7.1 negyedik frissítés](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|
| 2020. október 7. | Microsoft Azure Service Fabric 7.1 hatodik frissítés | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU6-releasenotes.md)|
| 2020. november 23. | Microsoft Azure Service Fabric 7.1-es frissítés | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU8-releasenotes.md)|


### <a name="service-fabric-70"></a>Service Fabric 7.0

Az Azure Service Fabric 7.0 már elérhető! Frissítheti a 7.0-s verzióra a Azure Portal egy Azure Resource Manager keresztül. Az ünnepi időszakra vonatkozó kiadásokkal kapcsolatos ügyfél-visszajelzések miatt januárig nem kezdjük el automatikusan frissíteni az automatikus frissítésre beállított fürtök frissítését.
Januárban folytatjuk a normál bevezetési eljárást, és az automatikus frissítésekkel rendelkező fürtök automatikusan megkapják a 7.0-s frissítést. A bevezetés megkezdése előtt egy újabb bejelentést is biztosítanak.
A tervezett kiadási dátumokat is frissítjük, hogy jelezze, figyelembe kell venni ezt a szabályzatot. A jövőbeli kiadási ütemezések frissítéseit [itt lehet keresni.](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)

#### <a name="key-announcements"></a>Fontos közlemények
 - [**KeyVaultReference-támogatás**](./service-fabric-keyvault-references.md)az alkalmazáskulcsokhoz (előzetes verzió): [](./concepts-managed-identity.md) a felügyelt identitásokat engedélyező Service Fabric-alkalmazások mostantól közvetlenül hivatkozhat egy Key Vault titkos URL-címre környezeti változóként, alkalmazásparaméterként vagy tárolóadattár hitelesítő adataiként. Service Fabric automatikusan feloldja a titkos adatokat az alkalmazás felügyelt identitásával. 
     
- **Továbbfejlesztett** frissítési biztonság állapot nélküli szolgáltatások esetén: Az alkalmazásfrissítések során a [](/dotnet/api/system.fabric.description.statelessservicedescription) rendelkezésre állás biztosítása érdekében új konfigurációkat vezettünk be, amelyek meghatározzák az állapot nélküli szolgáltatások által elérhetőnek tekinthető példányok minimális számát. Korábban ez az érték 1 volt az összes szolgáltatáshoz, és nem volt megváltoztatható. Ezzel az új, szolgáltatásonkénti biztonsági ellenőrzéssel biztosíthatja, hogy a szolgáltatások minimális számú példányt őriznek meg az alkalmazásfrissítések, fürtfrissítések és egyéb karbantartások során, amelyek az Service Fabric állapot- és biztonsági ellenőrzésére támaszkodnak.
  
- [**Erőforráskorlátok**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services)a felhasználói szolgáltatásokhoz: A felhasználók erőforráskorlátokat állíthatnak be a felhasználói szolgáltatásokhoz egy csomóponton, hogy megakadályozzák az olyan forgatókönyveket, mint a Service Fabric erőforrás-kimerítése. 
  
- [**Egy replikatípus nagyon**](./service-fabric-cluster-resource-manager-movement-cost.md) magas szolgáltatási áthelyezési költsége. A nagyon magas áthelyezési költségeket eredményező replikák csak akkor lesznek áthelyezve, ha a fürtön olyan korlátozás van megsértve, amely semmilyen más módon nem javítható. Ha egy "Nagyon magas" áthelyezési költség ésszerű, és további szempontokat is figyelembe kell vennie, tekintse meg a csatolt dokumentumot.
  
-  **További fürtbiztonsági ellenőrzések:** Ebben a kiadásban bevezettünk egy konfigurálható magcsomópont kvórumbiztonsági ellenőrzését. Ez lehetővé teszi annak testreszabását, hogy hány magcsomópontnak kell elérhetőnek lennie a fürt életciklusa és a felügyeleti forgatókönyvek során. Azok a műveletek, amelyek a fürtöt a konfigurált érték alatt érnék el, le vannak tiltva. Jelenleg az alapértelmezett érték mindig a magcsomópontok kvóruma, például ha 7 magcsomóponttal bír, alapértelmezés szerint le van tiltva egy olyan művelet, amely 5 magcsomópont alá kerül. Ezzel a módosítással a minimálisan biztonságos értéket 6-ra teheti, amely egyszerre csak egy magcsomópont leállhat.
   
- Támogatás hozzáadva a Biztonsági mentési és visszaállítási [**szolgáltatás kezeléséhez a Service Fabric Explorer.**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md) Ez a következő tevékenységeket teszi lehetővé közvetlenül az SFX-ről: a biztonsági mentési és visszaállítási szolgáltatás felderítése, biztonsági mentési szabályzat létrehozása, automatikus biztonsági mentések engedélyezése, adhoc biztonsági mentések készítése, visszaállítási műveletek aktiválása és a meglévő biztonsági mentések böngészése.

- A [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)elérhetőségének bejelentése: Ez az eszköz segít ellenőrizni, hogy a megbízható gyűjteményekben használt típusok előre és visszamenőlegesen kompatibilisek-e a működés közbeni alkalmazásfrissítés során. Ez segít megelőzni a frissítési hibákat, az adatvesztést és az adatsérülést hiányzó vagy nem kompatibilis típusok miatt.

- [**Engedélyezze a stabil olvasást**](./service-fabric-reliable-services-configuration.md#configuration-names-1)a másodlagos replikákon: A stabil olvasások a másodlagos replikákat a kvórum által nyugtázású értékek visszaadása érdekében korlátozzák.

Ez a kiadás emellett további új funkciókat, hibajavításokat, valamint támogatási, megbízhatósági és teljesítménybeli fejlesztéseket is tartalmaz. A módosítások teljes listájáért tekintse meg a kibocsátási [megjegyzéseket.](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)

#### <a name="service-fabric-70-releases"></a>Service Fabric 7.0-s kiadások

| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| 2019. november 18. | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 2020. január 30. | [Az Azure Service Fabric 7.0 frissítési kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 2020. február 6. | [Az Azure Service Fabric 7.0 frissítési kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2020. március 2. | [Az Azure Service Fabric 7.0 frissítési kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)
| 2020. május 6. | [Az Azure Service Fabric 7.0 hatodik frissítés kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-sixth-refresh-release/ba-p/1365709) | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU6-releasenotes.md)|
| 2020. október 9. | Az Azure Service Fabric 7.0 frissítési kiadása | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU9-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6.5

Ez a kiadás támogatási, megbízhatósági és teljesítménybeli fejlesztéseket, új funkciókat, hibajavításokat és fejlesztéseket tartalmaz a fürtök és alkalmazások életciklusának felügyeletének könnyítése érdekében.

> [!IMPORTANT]
> Service Fabric 6.5 a 2015-ös Service Fabric-eszközök támogatásával Visual Studio végleges kiadás. Javasoljuk, hogy a [2019 Visual Studio re](https://visualstudio.microsoft.com/vs/) való átköltöztetve lépjen tovább.

A 6.5-ös Service Fabric újdonsága:

- Service Fabric Explorer tartalmaz egy [lemezképtároló Viewert](service-fabric-visualizing-your-cluster.md#image-store-viewer) a képtárba feltöltött alkalmazások vizsgálathoz.

- [A Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) [1.4.0-s](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) verziója számos öndiagnosztikai fejlesztést tartalmaz. A POA ügyfeleinek javasoljuk, hogy lépjenek erre a verzióra.

- [Az EventStore szolgáltatás alapértelmezés](service-fabric-visualizing-your-cluster.md#event-store) szerint engedélyezve van Service Fabric 6.5-ös fürtökhöz, kivéve, ha ön kikapcsolta a használatát.

- [Replika életciklus-események hozzáadva](service-fabric-diagnostics-event-generation-operational.md#replica-events) az állapot-állapot-szolgáltatásokat.

- [A magcsomópont](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)állapotának jobb láthatósága, beleértve a fürtszintű figyelmeztetéseket, ha egy magcsomópont nem megfelelő állapotú (*Le,* *Eltávolítva* vagy *Ismeretlen*).

- [Service Fabric Application Disaster Recovery Tool](https://github.com/Microsoft/Service-Fabric-AppDRTool) lehetővé teszi, Service Fabric állapot-állapot-szolgáltatásokat gyorsan helyreállítsa, amikor az elsődleges fürt katasztrófa történik. Az elsődleges fürtből származó adatok rendszeres biztonsági mentéssel és visszaállítással folyamatosan szinkronizálódnak a másodlagos készenléti alkalmazásban.

- Visual Studio [.NET Core-alkalmazások Linux-alapú fürtökön való közzétételének támogatása.](service-fabric-how-to-publish-linux-app-vs.md)

- [Az Azure Service Fabric CLI (SFCTL)](./service-fabric-cli.md) automatikusan telepítve lesz az Service Fabric 6.5-ös (és újabb) verzióihoz, amikor új Linux-fürtöt frissít vagy hoz létre az Azure-ban.

- [Az SFCTL](./service-fabric-cli.md) alapértelmezés szerint telepítve van a MacOS-/Linux OneBox-fürtökön.

További részletekért tekintse meg a [Service Fabric 6.5 kibocsátási megjegyzéseit.](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)

#### <a name="service-fabric-65-releases"></a>Service Fabric 6.5-ös verziók

| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| 2019. június 11. | [Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019. július 2. | [Az Azure Service Fabric 6.5 frissítésének kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019. július 29. | [Az Azure Service Fabric 6.5 frissítésének kiadása](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 2019. aug. 23. | [Az Azure Service Fabric 6.5 frissítésének kiadása](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 2019. október 14. | [Az Azure Service Fabric 6.5 frissítésének kiadása](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Kibocsátási megjegyzések] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


### <a name="service-fabric-64-releases"></a>Service Fabric 6.4-es kiadások

| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| 2018. november 30. | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018. december 12. | [Az Azure Service Fabric 6.4 Frissítés kiadása Windows-fürtökhöz](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019. február 4. | [Az Azure Service Fabric 6.4 frissítés kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019. március 4. | [Az Azure Service Fabric 6.4 frissítés kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019. április 8. | [Az Azure Service Fabric 6.4 frissítés kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019. május 2. | [Az Azure Service Fabric 6.4 frissítés kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019. május 28. | [Az Azure Service Fabric 6.4 frissítés kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
