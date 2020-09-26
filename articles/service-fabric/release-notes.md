---
title: Azure Service Fabric kiadások
description: Az Azure Service Fabric kibocsátási megjegyzései. A Service Fabric legújabb szolgáltatásairól és tökéletesítéséről tartalmaz információkat.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 467018e57a0783839cb9495fcc6df89aad9daa8c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280608"
---
# <a name="service-fabric-releases"></a>Service Fabric kiadások

- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Hibaelhárítási útmutatók</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">Probléma követése</a> 
- <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">Támogatási lehetőségek</a> 
- <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">Támogatott verziók</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Kód mintái</a>

Ez a cikk további információkat tartalmaz a Service Fabric futtatókörnyezet és SDK-k legújabb kiadásairól és frissítéseiről.

## <a name="whats-new-in-service-fabric"></a>A Service Fabric újdonságai

### <a name="service-fabric-71"></a>Service Fabric 7,1
A jelenlegi COVID válság miatt, valamint az ügyfelek által felmerülő kihívásokat figyelembe véve az 7,1-as verzió érhető el, de nem frissíti automatikusan a fürtöket automatikus verziófrissítések fogadására. A váratlan fennakadások elkerülése érdekében az automatikus frissítést addig szüneteltetjük, amíg további értesítést nem biztosítunk arról, hogy az ügyfelek a legmegfelelőbb módon tudják alkalmazni a frissítéseket.

A 7,1-re a [Azure Portal](./service-fabric-cluster-upgrade-version-azure.md#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) vagy egy Azure Resource Manager üzemelő [példányon](./service-fabric-cluster-upgrade-version-azure.md#set-the-upgrade-mode-using-a-resource-manager-template)keresztül tud frissíteni.

Az automatikus frissítésekkel rendelkező Service Fabric fürtök automatikusan megkapják az 7,1-es frissítést, ha folytatjuk a normál bevezetési eljárást. A standard bevezetésének megkezdése előtt újabb bejelentést fogunk benyújtani a [Service Fabric technikai közösségi webhelyről](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).
Közzétettük a frissítéseket a támogatási dátum végéig a 6,5 7,1-ig terjedő fő kiadásokra vonatkozóan [.](./service-fabric-versions.md#supported-versions) 

## <a name="what-is-new-in-service-fabric-71"></a>Mi az újdonság a-Service Fabric 7,1-ben?
Örömmel jelentjük be Service Fabric következő kiadását. Ez a kiadás a legfontosabb funkciókkal és a frissítésekkel tölthető be. A főbb funkciók némelyikét az alábbiakban találja:
## <a name="key-announcements"></a>Legfontosabb közlemények
- **General Availability** [ **Service Fabric felügyelt identitások általános elérhetősége Service Fabric alkalmazásokhoz**](./concepts-managed-identity.md)
- [**Ubuntu 18,04-támogatás**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - [**Előzetes verzió: virtuálisgép-méretezési csoport ideiglenes operációsrendszer-lemezének támogatása**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)* *: az elmúló operációsrendszer-lemezek a helyi virtuális gépen létrehozott tárolók, és nem menthetők a távoli Azure-tárolóba. A rendszer az összes Service Fabric csomópont-típushoz (elsődleges és másodlagos) ajánlott, mivel a hagyományos állandó operációsrendszer-lemezekhez, az ideiglenes operációsrendszer-lemezekhez képest:
      -  Olvasási/írási késés csökkentése operációsrendszer-lemezre
      -  Gyorsabb visszaállítási/Rendszerképbeli csomópont-felügyeleti műveletek engedélyezése
      -  Csökkentse a teljes költséget (a lemezek ingyenesek, és nem merülnek fel további tárolási költségek)
- [**Service Fabric alkalmazásokhoz tartozó szolgáltatás-végponti tanúsítványok deklarációjának támogatása a tulajdonos köznapi neve alapján**](./service-fabric-service-manifest-resources.md).
- A [**tároló szolgáltatásokhoz tartozó állapot**](./probes-codepackage.md)-mintavételek támogatása: a tároló alkalmazások működés közbeni mintavételi mechanizmusának támogatása. Az élettartam mintavételi súgója bejelenti a tároló alkalmazás élettartamát, és ha nem válaszol időben, akkor a rendszer újraindítást eredményez. 
- [**Inicializálási kód csomagjainak támogatása**](./initializer-codepackages.md) [tárolók](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-containers-overview) és [vendég végrehajtható](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-guest-executables-introduction) alkalmazások számára. Ez lehetővé teszi a csomagok (például tárolók) futtatását egy megadott sorrendben a szervizcsomag inicializálásának végrehajtásához.
- A **FabricObserver és a ClusterObserver** állapot nélküli alkalmazások, amelyek az SF-fürtök különböző szempontjaihoz kapcsolódó, Service Fabric telemetria rögzítik. Mindkét alkalmazás készen áll a Windows éles fürtökre való központi telepítésre, hogy a ApplicationInsights, a EventSource és a LogAnalytics megvalósított támogatással rendelkező gazdag telemetria rögzítsen.
    - [**FabricObserver (fo) 2,0**](https://github.com/microsoft/service-fabric-observer)– az összes csomóponton fut, egészségügyi eseményeket generál, és telemetria bocsát ki a felhasználó által konfigurált erőforrás-használati küszöbértékek elérésekor. Ez a kiadás számos fejlesztést tartalmaz a monitorozás, az adatkezelés, az állapotadatok részletei és a strukturált telemetria között.
     - [**ClusterObserver (CO) 1,1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) – egy csomóponton fut, rögzíti a fürt szintjének állapotának telemetria. Ebben a kiadásban a ClusterObserver figyeli a csomópontok állapotát, és telemetria bocsát ki, ha a csomópont a felhasználó által megadott időtartamnál hosszabb ideig nem működik le, illetve tiltja le/le van tiltva.

### <a name="improve-application-life-cycle-experience"></a>Az alkalmazás életciklus-élményének javítása

- **[Előnézet: kérések kiürítése](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)**: a szolgáltatás tervezett karbantartása (például a szolgáltatás frissítése vagy a csomópont inaktiválása) során lehetővé szeretné tenni a szolgáltatások számára, hogy szabályosan kiürítse a kapcsolatokat. Ez a szolgáltatás egy példány záró késleltetési időtartamát adja a szolgáltatás konfigurációjában. A tervezett műveletek során az SF eltávolítja a szolgáltatás címeit a felderítésből, majd megvárja ezt az időtartamot a szolgáltatás leállítása előtt.
- **[Automatikus alfürt észlelése és kiegyensúlyozása](./cluster-resource-manager-subclustering.md)**: az alfürtözés akkor történik meg, ha a különböző elhelyezési korlátozásokkal rendelkező szolgáltatások közös [terhelési metrikával](./service-fabric-cluster-resource-manager-metrics.md)rendelkeznek. Ha a különböző típusú csomópontok terhelése jelentősen eltér, a Service Fabric fürterőforrás-kezelő úgy véli, hogy a fürt kiegyensúlyozatlan, még akkor is, ha az elhelyezési korlátozások miatt a lehető legjobb egyensúlyt látja el. Ennek eredményeképpen megkísérli a fürt újraelosztását, ami esetleg szükségtelen szolgáltatási mozgásokat okoz (mivel az "egyensúlyhiány" nem lehet lényegesen javítható). Ettől a kiadástól kezdve a fürterőforrás-kezelő megkísérli automatikusan felderíteni ezeket a konfigurációkat, és megismerni, hogy mikor lehet a mozgással javítani az egyensúlyhiányt  
- [**A másodlagos replikák eltérő áthelyezési díja**](./service-fabric-cluster-resource-manager-movement-cost.md): új VeryHigh váltunk ki, amely további rugalmasságot biztosít bizonyos helyzetekben, hogy meghatározza, hogy a másodlagos replikák esetében érdemes-e külön áthelyezési költségeket használni.
- Engedélyezve van az [**élettartam**](./probes-codepackage.md) mintavételi mechanizmusa a tároló alkalmazások számára. Az élettartam mintavételi súgója bejelenti a tároló alkalmazás élettartamát, és ha nem válaszol időben, akkor a rendszer újraindítást eredményez.
- [**Futtatás befejezésre/egyszer a szolgáltatások számára**](./run-to-completion.md)**

### <a name="image-store-improvements"></a>lemezképtároló fejlesztése
 - Az Service Fabric 7,1 **Egyéni átvitelt használ a csomópontok közötti, alapértelmezés szerint a fájlátvitel biztonságossá tételéhez**. A rendszer eltávolítja az SMB-fájlmegosztás függőségét az 7,1-es verzióról. A biztonságos SMB-fájlmegosztás továbbra is olyan csomópontokon van, amelyek lemezképtároló szolgáltatás-replikát tartalmaznak az ügyfél számára, hogy kikapcsolják az alapértelmezett értéket, és a frissítés és a visszalépés a régi verzióra.
       
 ### <a name="reliable-collections-improvements"></a>A megbízható gyűjtemények fejlesztése

- [**A memóriában csak a megbízható gyűjteményeket használó állapot-nyilvántartó szolgáltatások támogatása: az**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections)illékony megbízható gyűjtemények lehetővé teszik, hogy az adatok a nagy léptékű leállások miatt tartósak legyenek a lemezre, például a replikált gyorsítótárhoz, ahol az alkalmi adatvesztés is elfogadható. Az állandó [megbízhatóságú gyűjtemények korlátai és korlátozásai](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)alapján ajánlott ezt olyan számítási feladatokhoz használni, amelyek nem igényelnek adatmegőrzést, ezért olyan szolgáltatások esetén, amelyek a kvórum elvesztésének ritka eseményeit kezelik.
- [**Előzetes verzió: Service Fabric Backup Explorer**](https://github.com/microsoft/service-fabric-backup-explorer): a megbízható gyűjtemények biztonsági másolatainak Service Fabric állapot-nyilvántartó alkalmazások általi kezelésének megkönnyítése érdekében Service Fabric Backup Explorer lehetővé teszi a felhasználók számára, hogy
    - A megbízható gyűjtemények tartalmának naplózása és áttekintése
    - Aktuális állapot frissítése konzisztens nézetre
    - Biztonsági másolat készítése a megbízható gyűjtemények aktuális pillanatképéről
    - Az adatsérülés javítása
                 
### <a name="service-fabric-71-releases"></a>Service Fabric 7,1 kiadás
| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| 2020. április 20. | [Azure Service Fabric 7,1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| Június 16., 2020 | [Microsoft Azure Service Fabric 7,1 első frissítés](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 2020. július 20. | [Microsoft Azure Service Fabric 7,1 második frissítés](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 2020. augusztus 12. | [Microsoft Azure Service Fabric 7,1 harmadik frissítés](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| Szeptember 10., 2020 | [Microsoft Azure Service Fabric 7,1 negyedik frissítés](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|

### <a name="service-fabric-70"></a>Service Fabric 7,0

Az Azure Service Fabric 7,0 már elérhető! A 7,0-re a Azure Portal vagy egy Azure Resource Manager üzemelő példányon keresztül frissítheti. Az üdülési időszakon kívüli kiadásokra vonatkozó vásárlói visszajelzések miatt a rendszer nem kezdi automatikusan frissíteni a fürtöket, hogy az automatikus frissítéseket a januárig kapják meg.
Januárban folytatjuk a normál kiindulási eljárást, és az automatikus frissítésekkel rendelkező fürtök automatikusan megkapják az 7,0-es frissítést. A kiindulási folyamat megkezdése előtt egy újabb bejelentést fogunk benyújtani.
A tervezett kiadási dátumokat is frissítjük, jelezve, hogy figyelembe vesszük ezt a szabályzatot. A jövőbeli [kiadási ütemtervekkel](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)kapcsolatos frissítéseket itt találja.
 
Ez a Service Fabric legújabb kiadása, amely kulcsfontosságú funkciókkal és újdonságokkal van betöltve.

### <a name="key-announcements"></a>Legfontosabb közlemények
 - [**Az KeyVaultReference támogatása (előzetes verzió)**](./service-fabric-keyvault-references.md): Service Fabric [felügyelt identitásokat](./concepts-managed-identity.md) engedélyező alkalmazások mostantól közvetlenül hivatkozhatnak Key Vault titkos URL-címre környezeti változóként, Application paraméterként vagy Container adattárbeli hitelesítő adatokként. Service Fabric automatikusan feloldja a titkot az alkalmazás felügyelt identitásával. 
     
- **Javított biztonság az állapot nélküli szolgáltatások esetében**: az alkalmazások frissítése során a rendelkezésre állás biztosítása érdekében új konfigurációkat vezettünk be, amelyekkel meghatározható, hogy az [állapot nélküli szolgáltatások milyen számú példányban](/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) legyenek elérhetők. Korábban ez az érték 1 volt az összes szolgáltatás esetében, és nem módosítható. Ezzel az új, szolgáltatásként nyújtott biztonsági ellenőrzéssel biztosíthatja, hogy a szolgáltatások az alkalmazások frissítése, a fürtök frissítése és a Service Fabric állapotának és biztonsági ellenőrzésének egyéb karbantartása során minimális számú példányban maradjanak.
  
- [**Felhasználói szolgáltatások erőforrás-korlátai**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services): a felhasználók erőforrás-korlátozásokat állíthatnak be a csomópont felhasználói szolgáltatásaihoz, így elkerülhetők például az Service Fabric rendszerszolgáltatások erőforrás-kimerülése. 
  
- A replika típusának [**nagyon magas szolgáltatási mozgatási díja**](./service-fabric-cluster-resource-manager-movement-cost.md) . A nagyon magas áthelyezési költségeket tartalmazó replikák csak akkor lesznek áthelyezve, ha a fürtben nem lehet más módon megállapítani a korlátozás megsértését. Tekintse át a csatolt dokumentumot, ahol további információkat talál a "nagyon magas" áthelyezési költségeket illetően, és további szempontokat is figyelembe kell vennie.
  
-  **További fürt biztonsági ellenőrzései**: ebben a kiadásban egy konfigurálható mag-csomópont kvórum biztonsági ellenőrzését vezettünk be. Ez lehetővé teszi annak testreszabását, hogy hány mag-csomópontnak kell elérhetőnek lennie a fürt életciklusa és kezelési forgatókönyvei során. Azok a műveletek, amelyeken a fürt a konfigurált érték alá kerülne, le vannak tiltva. Napjainkban az alapértelmezett érték mindig a vetőmag-csomópontok kvóruma, például ha 7 vetőmag-csomóponttal rendelkezik, a rendszer alapértelmezés szerint letilt egy műveletet, amely 5 mag csomópont alatt lenne. Ezzel a módosítással megteheti a minimálisan szükséges 6 értéket, ami lehetővé tenné, hogy egyszerre csak egy mag-csomópont legyen letiltva.
   
- [**A Service Fabric Explorer-ben a biztonsági mentési és visszaállítási szolgáltatás felügyeletének**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)támogatása. Így a következő tevékenységek közvetlenül elérhetővé tehetők az SFX környezetből: a biztonsági mentési és visszaállítási szolgáltatás, a biztonsági mentési szabályzat létrehozása, az automatikus biztonsági mentések, az alkalmi biztonsági másolatok, a visszaállítási műveletek elindítása és a meglévő biztonsági másolatok tallózása.

- A [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)rendelkezésre állásának bejelentése: ez az eszköz segít ellenőrizni, hogy a megbízható gyűjteményekben használt típusok továbbítva és visszamenőlegesen kompatibilisek-e a működés közbeni alkalmazások frissítésekor. Ez segít megelőzni a frissítési hibákat, illetve az adatvesztést és az adatsérülést a hiányzó vagy inkompatibilis típusok miatt.

- [**Stabil olvasások engedélyezése a másodlagos replikák**](./service-fabric-reliable-services-configuration.md#configuration-names-1)esetében: a stabil olvasások a kvórum nyugtázva tartozó értékek visszaadása érdekében korlátozzák a másodlagos replikákat.

Emellett ez a kiadás más új funkciókat, hibajavításokat, valamint a támogatás, a megbízhatóság és a teljesítmény javítását is tartalmazza. A módosítások teljes listájáért tekintse meg a [kibocsátási megjegyzéseket](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

### <a name="service-fabric-70-releases"></a>Service Fabric 7,0 kiadás

| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| 2019. november 18. | [Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 2020. január 30-ig | [Azure Service Fabric 7,0 frissítési kiadás](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| Február 6., 2020 | [Azure Service Fabric 7,0 frissítési kiadás](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2020. március 2. | [Azure Service Fabric 7,0 frissítési kiadás](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)

### <a name="service-fabric-65"></a>Service Fabric 6,5

Ez a kiadás támogatja a támogatás, a megbízhatóság és a teljesítmény fejlesztését, az új funkciókat, hibajavításokat és fejlesztéseket a fürt és az alkalmazások életciklus-felügyeletének megkönnyítéséhez.

> [!IMPORTANT]
> A Service Fabric 6,5 a Visual Studio 2015 Service Fabric-eszközök támogatásának végső kiadása. Javasoljuk, hogy a [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -es verzióra lépjen tovább.

A Service Fabric 6,5 újdonságai:

- Service Fabric Explorer tartalmaz egy [lemezképtároló-megjelenítőt](service-fabric-visualizing-your-cluster.md#image-store-viewer) a rendszerkép-áruházba feltöltött alkalmazások vizsgálatához.

- A [patch-előkészítési alkalmazás (Poa)](service-fabric-patch-orchestration-application.md) [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) verziója számos öndiagnosztika fejlesztést tartalmaz. Javasoljuk, hogy erre a verzióra térjen át a POA ügyfeleinek.

- A [EventStore szolgáltatás alapértelmezés szerint engedélyezve van](service-fabric-visualizing-your-cluster.md#event-store) Service Fabric 6,5-fürtökön, hacsak nem választotta ki.

- Az állapot-nyilvántartó szolgáltatásokhoz hozzáadott [replika-Életciklus eseményei](service-fabric-diagnostics-event-generation-operational.md#replica-events) .

- [A vetőmag-csomópontok állapotának jobb láthatósága](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), beleértve a fürtre vonatkozó figyelmeztetéseket, ha a vetőmag-csomópontok állapota nem*megfelelő (* leállítva, *eltávolítva* vagy *ismeretlen*).

- [Service Fabric alkalmazás vész-helyreállítási eszköze](https://github.com/Microsoft/Service-Fabric-AppDRTool) lehetővé teszi, hogy Service Fabric állapot-nyilvántartó szolgáltatások gyorsan helyreállíthatók, amikor az elsődleges fürt vészhelyzetet tapasztal. Az elsődleges fürtből származó adatok folyamatos szinkronizálása a másodlagos készenléti alkalmazásban rendszeres biztonsági mentés és visszaállítás használatával történik.

- A Visual Studio támogatja a [.net Core-alkalmazások Linux-alapú fürtökre való közzétételét](service-fabric-how-to-publish-linux-app-vs.md).

- Az [azure Service FABRIC CLI (SFCTL)](./service-fabric-cli.md) automatikusan települ a Service Fabric 6,5 (és újabb verziók esetében), amikor új Linux-fürtöt frissít vagy hoz létre az Azure-ban.

- A [SFCTL](./service-fabric-cli.md) a MacOS/Linux beépített-fürtökön alapértelmezés szerint telepítve van.

További részletekért tekintse meg a [Service Fabric 6,5 kibocsátási megjegyzéseit](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Service Fabric 6,5 kiadás

| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| Június 11.2019 | [Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019. július 2. | [Azure Service Fabric 6,5 frissítési kiadás](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019. július 29. | [Azure Service Fabric 6,5 frissítési kiadás](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Augusztus 23., 2019 | [Azure Service Fabric 6,5 frissítési kiadás](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| Október 14., 2019 | [Azure Service Fabric 6,5 frissítési kiadás](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Kibocsátási megjegyzések] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


## <a name="previous-versions"></a>Korábbi verziók

### <a name="service-fabric-64-releases"></a>Service Fabric 6,4 kiadás

| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| November 30., 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018. december 12. | [Azure Service Fabric 6,4 Windows-fürtök frissítési kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019. február 4. | [Azure Service Fabric 6,4 frissítési kiadás](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019. március 4. | [Azure Service Fabric 6,4 frissítési kiadás](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019. április 8. | [Azure Service Fabric 6,4 frissítési kiadás](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019. május 2. | [Azure Service Fabric 6,4 frissítési kiadás](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| Május 28., 2019 | [Azure Service Fabric 6,4 frissítési kiadás](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
