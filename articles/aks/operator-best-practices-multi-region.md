---
title: Ajánlott eljárások az AK üzleti folytonosságához és a vész-helyreállításhoz
description: Ismerje meg a fürt operátorának ajánlott eljárásait, amelyekkel maximális üzemidőt érhet el alkalmazásai számára, magas rendelkezésre állást biztosítva, és előkészítheti a vész-helyreállítást az Azure Kubernetes szolgáltatásban (ak).
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 3ff8406a3634fa946ab8ce7aca694bbc57d556a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97976401"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások az üzletmenet folytonosságához és a vész-helyreállításhoz az Azure Kubernetes szolgáltatásban (ak)

A fürtök az Azure Kubernetes szolgáltatásban (ak) való kezelésekor az alkalmazás üzemidőe fontos lesz. Alapértelmezés szerint az KABAi szolgáltatás magas rendelkezésre állást biztosít egy [virtuálisgép-méretezési csoport (VMSS)](../virtual-machine-scale-sets/overview.md)több csomópontjának használatával. Ezek a csomópontok azonban nem védik a rendszerét a régió meghibásodása miatt. Az üzemidő maximalizálása érdekében előre tervezze meg az üzletmenet folytonosságát, és készüljön fel a vész-helyreállításra.

Ebből a cikkből megtudhatja, hogyan tervezheti meg az üzleti folytonosságot és a vész-helyreállítást az AK-ban. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Több régióban is megtervezheti az AK-fürtöket.
> * Az Azure Traffic Manager használatával irányíthatja át a forgalmat több fürt között.
> * Használja a Geo-replikációt a tároló képnyilvántartások számára.
> * Tervezze meg az alkalmazás állapotát több fürt között.
> * Tárterület replikálása több régióban.

## <a name="plan-for-multiregion-deployment"></a>A többrégiós telepítés tervezése

**Ajánlott eljárás**: Ha több AK-fürtöt telepít, válassza ki azokat a régiókat, ahol az AK elérhető, és használja a párosított régiókat.

Egy AK-fürt üzembe helyezése egyetlen régióban történik. Ha a rendszerét a régió meghibásodása miatt szeretné biztosítani, a különböző régiókban több AK-alapú fürtbe helyezheti üzembe alkalmazását. Ha az AK-fürt üzembe helyezésének helyét tervezi, vegye figyelembe a következőket:

* [**AK-régiók rendelkezésre állása**](./quotas-skus-regions.md#region-availability): válassza ki a régiókat a felhasználók számára. Az AK folyamatosan bővíti az új régiókat.
* [**Azure párosított régiók**](../best-practices-availability-paired-regions.md): az Ön földrajzi területéhez válasszon két, egymáshoz párosított régiót. A párosított régiók összehangolják a platform frissítéseit, és szükség esetén rangsorolják a helyreállítási erőfeszítéseket.
* **Szolgáltatás rendelkezésre állása**: döntse el, hogy a párosított régióknak meleg/gyors, meleg/meleg vagy meleg/hideg legyen. Egyszerre mindkét régiót szeretné futtatni, és az egyik régió *készen áll* a forgalom kiszolgálására? Vagy szeretné, hogy az egyik régió legyen ideje felkészülni a forgalom kiszolgálására?

Az AK-régiók rendelkezésre állása és a párosított régiók közös szempontot jelentenek. Helyezzen üzembe az AK-fürtöket párosított régiókba, amelyek a régió vész-helyreállításának kezelésére lettek kialakítva. Például az AK az USA keleti régiójában és az USA nyugati régiójában érhető el. Ezek a régiók párosítva vannak. Válassza ezt a két régiót a BC/DR stratégia létrehozásakor.

Ha üzembe helyezi az alkalmazást, adjon hozzá egy másik lépést a CI/CD-folyamathoz a több AK-alapú fürtön való üzembe helyezéshez. Ha nem frissíti az üzembe helyezési folyamatokat, előfordulhat, hogy az alkalmazások csak az egyik régióba vagy AK-beli fürtre lesznek telepítve. A másodlagos régióra irányuló ügyfél-forgalom nem kapja meg a legújabb kód-frissítéseket.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Az Azure Traffic Manager használata a forgalom irányításához

**Ajánlott eljárás**: az Azure Traffic Manager az ügyfeleket a legközelebbi AK-fürthöz és alkalmazás-példányhoz irányíthatja. A legjobb teljesítmény és redundancia érdekében a Traffic Manageron keresztül irányítsa az összes alkalmazás forgalmát, mielőtt az AK-fürtre kerül.

Ha több AK-s fürttel rendelkezik különböző régiókban, a Traffic Manager segítségével szabályozhatja, hogy a forgalom hogyan áramlik az egyes fürtökön futó alkalmazásokkal. Az [Azure Traffic Manager](../traffic-manager/index.yml) egy DNS-alapú forgalmi terheléselosztó, amely a régiók közötti hálózati forgalmat terjesztheti. A Traffic Manager használatával a felhasználók a fürt válaszideje vagy földrajz alapján irányíthatók át.

![AK Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Az egyoszlopos fürttel rendelkező ügyfelek általában egy adott alkalmazás szolgáltatás IP-címéhez vagy DNS-nevéhez csatlakoznak. Többfürtes telepítés esetén az ügyfeleknek csatlakozniuk kell egy Traffic Manager DNS-névhez, amely az egyes AK-fürtök szolgáltatásaira mutat. Adja meg ezeket a szolgáltatásokat Traffic Manager végpontok használatával. Mindegyik végpont a *szolgáltatás terheléselosztó IP-címe*. Ezt a konfigurációt használva irányíthatja a hálózati forgalmat az egyik régióban lévő Traffic Manager végpontról a végpontra egy másik régióban.

![Földrajzi útválasztás Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

A Traffic Manager DNS-lekérdezéseket hajt végre, és visszaadja a felhasználó legmegfelelőbb végpontját. A beágyazott profilok prioritást élveznek az elsődleges helyen. Például a felhasználóknak általában a legközelebbi földrajzi régióhoz kell csatlakozniuk. Ha a régió problémába ütközik, Traffic Manager Ehelyett a felhasználókat egy másodlagos régióra irányítja. Ez a megközelítés biztosítja, hogy az ügyfelek akkor is csatlakozhassanak az alkalmazás-példányhoz, ha a legközelebbi földrajzi régiójuk nem érhető el.

A végpontok és az Útválasztás beállításával kapcsolatos információkért lásd: [a földrajzi forgalom útválasztási módszerének konfigurálása Traffic Manager használatával](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).

### <a name="application-routing-with-azure-front-door-service"></a>Alkalmazás-útválasztás az Azure bejárati ajtó szolgáltatásával

A Split TCP-alapú, az [Azure bejárati szolgáltatásának](../frontdoor/front-door-overview.md) használatával biztosíthatja, hogy a végfelhasználók azonnal kapcsolódjanak a legközelebbi bejárati pop-hoz (jelenléti pont). Az Azure bejárati ajtó szolgáltatás további funkciói közé tartozik a TLS-lezárás, az egyéni tartomány, a webalkalmazási tűzfal, az URL-cím újraírása és a munkamenet-affinitás. Tekintse át az alkalmazás forgalmának igényeit, hogy megértse, melyik megoldás a legmegfelelőbb.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Összekapcsoló régiók globális virtuális hálózati társ-összevonással

Ha a fürtöknek egymással kell beszélgetni, a virtuális hálózatok egymáshoz való csatlakoztatása a [virtuális hálózati](../virtual-network/virtual-network-peering-overview.md)kapcsolaton keresztül is megvalósítható. Ez a technológia összekapcsolódik a virtuális hálózatokkal egymással, és a Microsoft gerinces hálózatán is nagy sávszélességet biztosít, akár különböző földrajzi régiók között is.

A virtuális hálózatok és az AK-fürtök futtatásának előfeltétele, hogy a standard Load Balancer használja az AK-fürtben, hogy a Kubernetes-szolgáltatások elérhetők legyenek a virtuális hálózatok között.

## <a name="enable-geo-replication-for-container-images"></a>Földrajzi replikálás engedélyezése a tároló lemezképei számára

**Ajánlott eljárás**: tárolja a tároló lemezképeit Azure Container Registry és geo-replikálja a beállításjegyzéket az egyes AK-régiókba.

Az AK-ban lévő alkalmazások üzembe helyezéséhez és futtatásához szüksége lesz a tároló lemezképének tárolására és lekérésére. Container Registry integrálódik az AK-val, így biztonságosan tárolhatók a tároló-vagy Helm-diagramok. Container Registry támogatja a több főkiszolgálós geo-replikációt, hogy automatikusan replikálja a lemezképeket az Azure-régiókba világszerte. 

A teljesítmény és a rendelkezésre állás javítása érdekében Container Registry geo-replikáció használatával hozzon létre egy beállításjegyzéket minden olyan régióban, ahol AK-fürt található. Az egyes AK-fürtök ezután lekérik a tároló lemezképeit a helyi tároló beállításjegyzékből ugyanabban a régióban:

![Container Registry geo-replikáció a tároló lemezképei számára](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Ha Container Registry geo-replikálást használ a képek ugyanarról a régióból való lekéréséhez, az eredmények a következők:

* **Gyorsabb**: az azonos Azure-régióban található nagy sebességű, kis késleltetésű hálózati kapcsolatokból származó képek lekérése.
* **Megbízhatóbb**: Ha egy régió nem érhető el, az AK-fürt lekéri a lemezképeket egy elérhető tároló-beállításjegyzékből.
* **Olcsóbb**: az adatközpontok közötti hálózati forgalomért nem számolunk fel díjat.

A Geo-replikáció a *Premium* SKU Container nyilvántartó szolgáltatás. További információ a Geo-replikáció konfigurálásáról: [Container Registry geo-replikáció](../container-registry/container-registry-geo-replication.md).

## <a name="remove-service-state-from-inside-containers"></a>Szolgáltatási állapot eltávolítása a tárolóból

**Ajánlott eljárás**: ha lehetséges, ne tárolja a szolgáltatás állapotát a tárolón belül. Ehelyett használjon olyan Azure-platformot, amely támogatja a többrégiós replikálást.

A *szolgáltatás állapota* arra a memóriára vagy lemezen tárolt adatra utal, amelyet a szolgáltatásnak működnie kell. Az állapot magában foglalja a szolgáltatás által beolvasott és írt adatstruktúrákat és a tagok változóit. A szolgáltatás tervezésének módjától függően előfordulhat, hogy az állapot a lemezen tárolt fájlokat vagy más erőforrásokat is tartalmazhat. Az állapot például tartalmazhat olyan fájlokat, amelyeket az adatbázis az adattárolók és a tranzakciós naplók tárolására használ.

Az állapot lehet külsőleg, vagy elhelyezhető a kóddal, amely az állapotot kezeli. Az állapotot általában egy olyan adatbázis vagy más adattár használatával Externalize, amely a hálózaton keresztül különböző gépeken fut, vagy amelyeken a folyamaton kívül fut ugyanazon a gépen.

A tárolók és a szolgáltatások a legtöbb esetben rugalmasak, ha a bennük futó folyamatok nem őrzik meg az állapotot. Mivel az alkalmazások szinte mindig tartalmaznak valamilyen állapotot, olyan Pásti-megoldást használhatnak, mint például a Azure Cosmos DB, a Azure Database for PostgreSQL, a Azure Database for MySQL vagy a Azure SQL Database.

A hordozható alkalmazások létrehozásához tekintse meg a következő irányelveket:

* [A 12 faktoros alkalmazás módszertana](https://12factor.net/)
* [Webalkalmazás futtatása több Azure-régióban](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Storage-áttelepítési terv létrehozása

**Ajánlott eljárás**: Ha Azure Storage-t használ, készítse elő és tesztelje, hogyan telepítse át a tárolót az elsődleges régióból a biztonsági mentési régióba.

Az alkalmazások az Azure Storage-t használhatják adataik számára. Mivel az alkalmazások különböző régiókban több AK-beli fürtön oszlanak el, meg kell őriznie a tároló szinkronizálását. Íme két gyakori módszer a tárterület replikálására:

* Infrastruktúra-alapú aszinkron replikáció
* Alkalmazás-alapú aszinkron replikáció

### <a name="infrastructure-based-asynchronous-replication"></a>Infrastruktúra-alapú aszinkron replikáció

Előfordulhat, hogy az alkalmazások a pod törlése után is állandó tárterületet igényelnek. A Kubernetes-ben az állandó kötetek használatával megtarthatja az adattárolást. Az állandó kötetek egy csomópont virtuális géphez vannak csatlakoztatva, majd a hüvelyek számára elérhetővé válnak. Az állandó kötetek akkor is követik a hüvelyeket, ha a hüvelyek ugyanazon a fürtön belül egy másik csomópontra kerülnek.

A használt replikációs stratégia a tárolási megoldástól függ. A gyakori tárolási megoldások, például a [Gluster](https://docs.gluster.org/en/latest/Administrator-Guide/Geo-Replication/), a [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/), a [bástya](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)és a [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) , saját útmutatást nyújtanak a vész-helyreállítással és a replikálással kapcsolatban.

A tipikus stratégia egy olyan közös tárolási pont biztosítása, amelyben az alkalmazások írhatják az adatbevitelt. Ezeket az adatfájlokat a rendszer replikálja a régiók között, majd helyileg elérhetővé.

![Infrastruktúra-alapú aszinkron replikáció](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Ha az Azure Managed Disks-t használja, a replikáció és a vész-helyreállítás kezeléséhez több lehetőség is használható. [A Velero az Azure-on][velero] és a [Kasten][kasten] -ben a Kubernetes-ra natív, de nem támogatott megoldások.

### <a name="application-based-asynchronous-replication"></a>Alkalmazás-alapú aszinkron replikáció

A Kubernetes jelenleg nem biztosít natív implementációt az alkalmazás-alapú aszinkron replikációhoz. Mivel a tárolók és a Kubernetes egymástól függetlenek, a hagyományos alkalmazás-vagy nyelvi megközelítésnek működnie kell. Az alkalmazások jellemzően replikálják a tárolási kérelmeket, amelyek ezután az egyes fürtök mögöttes adattárolóba lesznek írva.

![Alkalmazás-alapú aszinkron replikáció](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Következő lépések

Ez a cikk az üzleti folytonossággal és a vész-helyreállítási megfontolásokkal foglalkozik az AK-fürtök esetében. Az AK-beli fürtök műveleteivel kapcsolatos további információkért tekintse meg az ajánlott eljárásokról szóló cikket:

* [Bérlős és fürt elkülönítése][aks-best-practices-cluster-isolation]
* [Alapszintű Kubernetes Scheduler-funkciók][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md

[velero]: https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md
[kasten]: https://www.kasten.io/