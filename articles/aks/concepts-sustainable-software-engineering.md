---
title: Fogalmak – a fenntartható szoftverfejlesztés az Azure Kubernetes Servicesben (ak)
description: Ismerje meg az Azure Kubernetes Service (ak) fenntartható szoftverfejlesztés szolgáltatását.
services: container-service
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: e179b49879b78b2bab738407984c0f50d161114b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572683"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Fenntartható szoftverfejlesztés alapelvei az Azure Kubernetes szolgáltatásban (ak)

A fenntartható szoftverfejlesztés alapelvei olyan kompetenciák, amelyek segítenek a fenntartható alkalmazások definiálásában, létrehozásában és futtatásában. Az általános cél az, hogy csökkentse az alkalmazás minden aspektusának széndioxid-lábnyomát. [A fenntartható szoftverfejlesztés alapelvei][principles-sse] áttekintést nyújtanak a fenntartható szoftverfejlesztés elveiről.

A fenntartható szoftverfejlesztés fontos elképzelése, hogy ez a prioritások és a hangsúly változása. Sok esetben a szoftver úgy lett kialakítva és futtatva, hogy a gyors teljesítményre és az alacsony késésre koncentráljon. A fenntartható szoftverfejlesztés a lehető legnagyobb széndioxid-kibocsátás csökkentésére koncentrál. Bizonyos esetekben a fenntartható szoftverfejlesztés alapelveinek alkalmazása gyorsabb teljesítményt vagy kisebb késést biztosít, például a teljes hálózati utazások csökkentésével. Más esetekben a széndioxid-kibocsátás csökkentése csökkentheti a teljesítményt vagy a megnövekedett késést, például az alacsony prioritású munkaterhelések késleltetését. Mielőtt fontolóra venné az alkalmazásra vonatkozó fenntartható szoftverfejlesztés alapelveinek alkalmazását, tekintse át az alkalmazás prioritásait, igényeit és kompromisszumait.

## <a name="measure-and-optimize"></a>Mérés és optimalizálás

Az AK-fürtök széndioxid-kibocsátásának csökkentéséhez ismernie kell a fürt erőforrásainak használatát. [Azure monitor][azure-monitor] információt nyújt a fürt erőforrás-használatáról, például a memória és a CPU használatáról. Ezek az adatai segíthetnek a döntések meghozatalában, hogy csökkentsék a fürt széndioxid-lábnyomát, és figyelje a módosítások hatásait. A [Microsoft fenntarthatósági számológépét][sustainability-calculator] is telepítheti az összes Azure-erőforrás széndioxid-lábnyomának megtekintéséhez.

## <a name="increase-resource-utilization"></a>Erőforrás-használat javítása

A széndioxid-kibocsátás csökkentésének egyik módja a számítási erőforrások üresjárati idejének csökkentése. A tétlen idő csökkentése magában foglalja a számítási erőforrások kihasználtságának növelését. Ha például négy csomópontja volt a fürtben, és mindegyik a 50%-os kapacitásban fut, akkor mind a négy csomópont esetében a maximálisan kihasználatlan kapacitás a 50%-kal továbbra is üresjáratban marad. Ha három csomópontra csökkentette a fürtöt, akkor ugyanaz a számítási feladat azt eredményezi, hogy a három csomópont 67%-os kapacitással fog futni, ami csökkenti a fel nem használt kapacitást 33%-ra az egyes csomópontokon, és növeli a kihasználtságot.

> [!IMPORTANT]
> Ha a fürt erőforrásainak módosítását tervezi, ellenőrizze, hogy a [Rendszerkészletek][system-pools] rendelkeznek-e elegendő erőforrással a fürt alapvető rendszerösszetevői stabilitásának fenntartásához. Soha ne csökkentse a fürt erőforrásait arra a pontra, ahol a fürt instabillá válhat.

A fürt kihasználtságának áttekintése után érdemes lehet [több Node-készlet][multiple-node-pools]által kínált funkciókat használni. A csomópontok [méretezésével][node-sizing] meghatározott CPU-és memória-profillal rendelkező csomópont-készleteket határozhat meg, így a csomópontjait testre szabhatja a számítási feladatok igényeinek megfelelően. Ha a csomópontokat a számítási feladatokhoz igazítja, a nagyobb kihasználtságú csomópontok futtatásához is segítséget nyújthat. Azt is beállíthatja, hogyan [méretezi][scale] a fürt méretét, és hogyan használhatja a [vízszintes hüvely automatikus méretezését][scale-horizontal] és a [fürt automatikus méretezését][scale-auto] a fürt automatikus méretezéséhez a konfiguráció alapján. Annak szabályozása, hogy a fürt skálázása milyen módon biztosíthatja, hogy az összes csomópont magas kihasználtsággal fusson, miközben a fürt számítási feladatának módosításait is megtarthatja. Azokban az esetekben, amikor a számítási feladatok a hirtelen megszakítások vagy a leállások szempontjából toleránsak, a [helyszíni készletek][spot-pools] segítségével kihasználhatja az Azure-ban lévő üresjárati kapacitás előnyeit. Előfordulhat például, hogy a helyszíni készletek jól működnek a Batch-feladatokhoz vagy a fejlesztői környezetekhez.

A növekvő kihasználtság emellett csökkentheti a felesleges csomópontokat is, ami csökkenti az erőforrás-foglalások által felhasznált energiát az [egyes csomópontokon][resource-reservations].

Tekintse át a processzor-és memória- *kérelmeket* és- *korlátozásokat* is az alkalmazások Kubernetes-jegyzékében. Ahogy csökkenti a memória és a CPU értékeit, több memória és CPU is elérhető a fürt számára más számítási feladatok futtatásához. Az alacsonyabb CPU-és memória-számítási feladatok futtatásakor a fürt sűrűbben lesz kiosztva, ami növeli a kihasználtságot. Ha csökkenti az alkalmazások PROCESSZORát és memóriáját, az alkalmazások viselkedése csökkenhet vagy instabillá válhat, ha túl alacsonyra állítja be ezeket az értékeket. A CPU-és memória- *kérelmek* és- *korlátok* módosítása előtt érdemes lehet néhány teljesítményteszt-tesztet futtatni, hogy megértsük, hogy ezek az értékek megfelelően vannak-e beállítva. Emellett soha ne csökkentse ezeket az értékeket arra a pontra, amikor az alkalmazása instabillá válik.

## <a name="reduce-network-travel"></a>Hálózati utazás csökkentése

A távolsági kérések és a fürtre küldött válaszok csökkentése általában csökkenti a hálózati eszközök energiafogyasztását, és csökkenti a széndioxid-kibocsátást. A hálózati forgalom áttekintése után érdemes lehet fürtöket létrehozni a hálózati forgalom forrásához közelebbi [régiókban][regions] . Az [azure Traffic Manager][azure-traffic-manager] használatával az Azure-erőforrások közötti távolság csökkentése érdekében a legközelebbi fürt és [közelségi csoportok][proiximity-placement-groups] útválasztási forgalmát is segítheti.

> [!IMPORTANT]
> Ha a fürt hálózatkezelésének módosítását tervezi, soha ne csökkentse a hálózati utazást a munkaterhelés követelményeinek teljesítése során. A [rendelkezésre állási zónák][availability-zones] használata például nagyobb hálózati utazást okoz a fürtön, de a funkció használata szükséges lehet a munkaterhelés követelményeinek kezeléséhez.

## <a name="demand-shaping"></a>Igény kialakítása

Ha lehetséges, érdemes lehet átváltani a fürt erőforrásainak igényét arra, hogy olyan időpontokra vagy régiókra váltsanak, ahol felesleges kapacitást használhat. Tegyük fel például, hogy egy batch-feladatokhoz tartozó időt vagy régiót szeretné futtatni, vagy a [direktszíneket][spot-pools]használni. Azt is érdemes megfontolni, hogy az alkalmazás egy üzenetsor használatával elhalasztja a futó munkaterhelések használatát, amelyek nem igényelnek azonnali feldolgozást.

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