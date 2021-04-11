---
title: Fogalmak – a fenntartható szoftverfejlesztés az Azure Kubernetes Servicesben (ak)
description: Ismerje meg az Azure Kubernetes Service (ak) fenntartható szoftverfejlesztés szolgáltatását.
services: container-service
ms.topic: conceptual
ms.date: 03/29/2021
ms.openlocfilehash: c43c65dfa2f3930510bd59aaa24c798525bd691b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011491"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Fenntartható szoftverfejlesztés alapelvei az Azure Kubernetes szolgáltatásban (ak)

A fenntartható szoftverfejlesztés alapelvei olyan kompetenciák, amelyek segítenek a fenntartható alkalmazások definiálásában, létrehozásában és futtatásában. Az általános cél az, hogy csökkentse az alkalmazás minden aspektusának széndioxid-lábnyomát. [A fenntartható szoftverfejlesztés alapelvei][principles-sse] áttekintést nyújtanak a fenntartható szoftverfejlesztés elveiről.

A fenntartható szoftverfejlesztés a prioritások és a hangsúly eltolódása. Sok esetben a legtöbb szoftver tervezése és futtatása kiemeli a gyors teljesítményt és az alacsony késést. Eközben a fenntartható szoftverfejlesztés a lehető legnagyobb széndioxid-kibocsátás csökkentésére koncentrál. Megfontolandó szempontok:

* A fenntartható szoftverfejlesztés alapelveinek alkalmazása gyorsabb teljesítményt vagy kisebb késést biztosít, például a teljes hálózati utazás csökkentésével. 
* A széndioxid-kibocsátás csökkentése csökkentheti a teljesítményt vagy a megnövekedett késést, például az alacsony prioritású munkaterhelések késleltetését. 

Mielőtt alkalmazza a fenntartható szoftverfejlesztés alapelveit az alkalmazásra, tekintse át az alkalmazás prioritásait, igényeit és kompromisszumait.

## <a name="measure-and-optimize"></a>Mérés és optimalizálás

Az AK-fürtök széndioxid-kibocsátásának csökkentéséhez ismernie kell a fürt erőforrásainak használatát. [Azure monitor][azure-monitor] információt nyújt a fürt erőforrás-használatáról, például a memória és a CPU használatáról. Ezek az adatmennyiség tájékoztatja a döntést, hogy csökkentse a fürt széndioxid-lábnyomát, és figyelje a módosítások hatásait. 

A [Microsoft fenntarthatósági számológépét][sustainability-calculator] is telepítheti az összes Azure-erőforrás széndioxid-lábnyomának megtekintéséhez.

## <a name="increase-resource-utilization"></a>Erőforrás-használat javítása

A széndioxid-kibocsátás csökkentésének egyik módja a tétlen idő csökkentése. A tétlen idő csökkentése magában foglalja a számítási erőforrások kihasználtságának növelését. Például:
1. Négy csomópontja volt a fürtben, amelyek mindegyike 50%-os kapacitással fut. Tehát mind a négy csomópont 50%-ban fel nem használt kapacitása tétlen marad. 
1. A fürt három csomópontra csökkent, és mindegyik a 67%-os kapacitással fut ugyanazzal a számítási feladattal. Az egyes csomópontokon a fel nem használt kapacitás 33%-ra csökkenthető volna, és növelhető a kihasználtsága.

> [!IMPORTANT]
> Ha a fürt erőforrásainak módosítását fontolgatja, ellenőrizze, hogy a [Rendszerkészletek][system-pools] rendelkeznek-e elegendő erőforrással a fürt alapvető rendszerösszetevőinek stabilitásának fenntartásához. **Soha ne** csökkentse a fürt erőforrásait arra a pontra, ahol a fürt instabillá válhat.

A fürt kihasználtságának áttekintése után érdemes lehet [több Node-készlet][multiple-node-pools]által kínált funkciókat használni: 

* Csomópontok méretezése

    A csomópontok [méretezésével][node-sizing] meghatározott CPU-és memória-profillal rendelkező csomópont-készleteket határozhat meg, így a csomópontjait testre szabhatja a számítási feladatok igényeinek megfelelően. Ha a csomópontokat a számítási feladatok igényei szerint szeretné átméretezéssel elvégezheti, néhány csomópontot futtathat magasabb kihasználtsággal. 

* Fürtskálázás

    A fürt [méretezésének][scale]beállítása. A [vízszintes hüvely automatikus méretezése][scale-horizontal] és a [fürt automatikus méretezése][scale-auto] használatával a fürt automatikusan méretezhető a konfiguráció alapján. Annak szabályozása, hogy a fürtök Hogyan méretezhetők úgy, hogy az összes csomópont magas kihasználtsággal fusson, miközben szinkronban marad a fürt számítási feladatainak változásaival. 

* Direktszínes készletek

    Azokban az esetekben, amikor egy munkaterhelés a hirtelen megszakítások vagy a leállás miatt toleráns, használhat [direktszíneket][spot-pools]. A helyszíni készletek kihasználják az Azure-beli üresjárati kapacitás előnyeit. Előfordulhat például, hogy a helyszíni készletek jól működnek a Batch-feladatokhoz vagy a fejlesztői környezetekhez.

> [!NOTE]
>A növekvő kihasználtság emellett csökkentheti a felesleges csomópontokat is, ami csökkenti az erőforrás-foglalások által felhasznált energiát az [egyes csomópontokon][resource-reservations].

Végezetül tekintse át a processzor-és memória- *kérelmeket* és- *korlátozásokat* az alkalmazások Kubernetes-jegyzékében. 
* Ahogy csökkenti a memória-és a CPU-értékeket, több memória és CPU is elérhető a fürt számára más számítási feladatok futtatásához. 
* Az alacsonyabb CPU-és memória-munkaterhelések futtatásakor a fürt sűrűbben lesz kiosztva, ami növeli a kihasználtságot. 

Ha csökkenti az alkalmazások PROCESSZORát és memóriáját, az alkalmazások viselkedése csökkenhet vagy instabillá válhat, ha a CPU-és a memória-értékeket túl alacsonyra állítja. A CPU-és memória- *kérelmek* és- *korlátok* módosítása előtt futtasson néhány teljesítményteszt-tesztet annak ellenőrzéséhez, hogy az értékek megfelelően vannak-e beállítva. Soha ne csökkentse ezeket az értékeket az alkalmazás instabilitásának pontjára.

## <a name="reduce-network-travel"></a>Hálózati utazás csökkentése

Ha csökkenti a kérések és válaszok közötti távolságot a fürttől, a hálózati eszközökön csökkentheti a széndioxid-kibocsátást és a villamosenergia-felhasználást. A hálózati forgalom áttekintése után érdemes lehet fürtöket létrehozni a hálózati forgalom forrásához közelebbi [régiókban][regions] . Az [azure Traffic Manager][azure-traffic-manager] segítségével átirányíthatja a forgalmat a legközelebbi fürt-és [közelségi csoportokba][proiximity-placement-groups] , és csökkentheti az Azure-erőforrások közötti távolságot.

> [!IMPORTANT]
> Ha a fürt hálózatkezelésének módosítását tervezi, soha ne csökkentse a hálózati utazást a munkaterhelés követelményeinek teljesítése során. Előfordulhat például, hogy a [rendelkezésre állási zónák][availability-zones] használata több hálózati utazást okoz a fürtön, a rendelkezésre állási zónák a munkaterhelés-követelmények kezeléséhez szükségesek.

## <a name="demand-shaping"></a>Igény kialakítása

Ha lehetséges, érdemes lehet átváltani a fürt erőforrásainak igényét arra, hogy olyan időpontokra vagy régiókra váltsanak, ahol felesleges kapacitást használhat. Vegyük például a következőket:
* A Batch-feladatok futtatásához szükséges idő vagy régió módosítása.
* A [helyszíni készletek][spot-pools]használata. 
* Az alkalmazás újrabontása a várólista használatára, hogy elhalasztsa a futó munkaterheléseket, amelyek nem igényelnek azonnali feldolgozást.

## <a name="next-steps"></a>Következő lépések

További információ a jelen cikkben említett AK-funkciókról:

* [Több csomópontos készlet][multiple-node-pools]
* [Csomópontok méretezése][node-sizing]
* [Fürt skálázása][scale]
* [A podok automatikus horizontális skálázási eszköze][scale-horizontal]
* [Automatikus fürtskálázási eszköz][scale-auto]
* [Direktszínes készletek][spot-pools]
* [Rendszerkészletek][system-pools]
* [Erőforrás-foglalások][resource-reservations]
* [Közelségi elhelyezési csoportok][proiximity-placement-groups]
* [Rendelkezésre állási zónák][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principles-sse]: https://docs.microsoft.com/learn/modules/sustainable-software-engineering-overview/