---
title: Ajánlott fürtelkülönítési eljárások
titleSuffix: Azure Kubernetes Service
description: Ismerje meg az Azure Kubernetes Service-ben (ak) való elkülönítésre vonatkozó ajánlott eljárásokat
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: cdeecabf569e3c6f9b280e6b0179e5378f5b1c95
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011370"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások a fürtök elkülönítéséhez az Azure Kubernetes szolgáltatásban (ak)

A fürtök Azure Kubernetes szolgáltatásban (ak) való kezelése során gyakran kell elkülönítenie a csapatokat és a munkaterheléseket. Az AK rugalmasságot biztosít a több-bérlős fürtök futtatásához és az erőforrások elkülönítéséhez. A Kubernetes-ben való befektetés maximalizálása érdekében ezeket a több-bérlős és elkülönítési funkciókat érdemes értelmezni és megvalósítani.

Ez az ajánlott eljárási cikk a fürtcsomópontok elkülönítésére koncentrál. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A több-bérlős fürtök és az erőforrások elkülönítésének megtervezése
> * Logikai vagy fizikai elkülönítés használata az AK-fürtökben

## <a name="design-clusters-for-multi-tenancy"></a>Több-bérlős tervezési fürtök

A Kubernetes lehetővé teszi az egyazon fürtön található csapatok és számítási feladatok logikai elkülönítését. Arra kell törekedni, hogy az egyes munkacsoportok igényei alapján a lehető legkevesebb jogosultság legyen kiosztva. A Kubernetes egyik [névtere][k8s-namespaces] logikai elkülönítési határt hoz létre. Az elkülönítésre és a több-bérlőre vonatkozó további Kubernetes-funkciók és-megfontolások a következő területeket foglalják magukban:

* Az **Ütemezés** magában foglalja az olyan alapszintű szolgáltatások használatát, mint az erőforrás-kvóták és a pod-megszakadási költségvetés. További információ ezekről a funkciókról: [ajánlott eljárások az alapszintű Scheduler-funkciókhoz az AK-ban][aks-best-practices-scheduler].
  * A fejlettebb Scheduler-funkciók közé tartozik a szennyező elemek és a tolerálás, a csomópont-választó, valamint a csomópont-és a pod-affinitás, illetve a kapcsolati affinitás. További információ ezekről a funkciókról: [ajánlott eljárások a speciális Scheduler-funkciókhoz az AK-ban][aks-best-practices-advanced-scheduler].
* A **hálózatkezelés** magában foglalja a hálózati házirendek használatát a hüvelyen belüli és kívüli adatforgalom szabályozásához.
* A **hitelesítés és engedélyezés** magában foglalja a szerepköralapú hozzáférés-vezérlés (RBAC) és a Azure Active Directory (ad) integrációját, a pod-identitásokat és a titkokat Azure Key Vaultokban. További információ ezekről a funkciókról: [ajánlott eljárások a hitelesítéshez és engedélyezéshez az AK-ban][aks-best-practices-identity].
* A **tárolók** a hüvelyek biztonságának betartatására, a pod biztonsági környezetek használatára és a biztonsági rések futtatására szolgáló rendszerképek és futtatókörnyezet vizsgálatára szolgáló Azure Policy-bővítményt tartalmaznak. Emellett az App Armor vagy a Seccompot (Secure Computing) használatát is magában foglalja a tároló elérésének korlátozásához a mögöttes csomóponthoz.

## <a name="logically-isolate-clusters"></a>Fürtök logikai elkülönítése

**Ajánlott eljárások – útmutató** : logikai elkülönítés használata különálló csapatokhoz és projektekhez. Próbálja meg csökkenteni a munkacsoportok vagy alkalmazások elkülönítéséhez üzembe helyezett fizikai AK-fürtök számát.

Logikai elkülönítéssel egyetlen AK-beli fürt használható több számítási feladathoz, csapathoz vagy környezethez. A Kubernetes [névterek][k8s-namespaces] a számítási feladatok és erőforrások logikai elkülönítési határait alkotják.

![Kubernetes-fürt logikai elkülönítése az AK-ban](media/operator-best-practices-cluster-isolation/logical-isolation.png)

A fürtök logikai elkülönítése általában nagyobb Pod-sűrűséget biztosít, mint a fizikailag elszigetelt fürtök. Kevesebb felesleges számítási kapacitás áll rendelkezésre, amely üresjáratban van a fürtben. A Kubernetes-fürt autoskálázásával kombinálva a csomópontok számát felfelé vagy lefelé méretezheti, hogy megfeleljen az igényeknek. Az automatikus skálázással kapcsolatos ajánlott eljárás lehetővé teszi, hogy csak a szükséges csomópontok számát futtassa, és minimálisra csökkentse a költségeket.

A Kubernetes-környezetek (ak-ban vagy máshol) nem teljesen biztonságosak az ellenséges, több-bérlős használatra. Több-bérlős környezetben több bérlő dolgozik közös, megosztott infrastruktúrán. Ennek eredményeképpen, ha az összes bérlő nem megbízható, további tervezést kell végeznie annak elkerülésére, hogy egy bérlő kihathat egy másik biztonsági és szolgáltatási szolgáltatására. További biztonsági funkciók, mint például a *Pod biztonsági házirend* és a csomópontok részletes, szerepköralapú hozzáférés-vezérlése (RBAC) nehezebbé teszik a kihasználat. Azonban az ellenséges, több-bérlős számítási feladatok futtatásakor a megfelelő biztonság érdekében a hypervisor az egyetlen biztonsági szint, amelyet megbízhatónak tart. A Kubernetes biztonsági tartománya a teljes fürtvé válik, nem önálló csomópontként. Az ilyen típusú ellenséges több-bérlős munkaterhelések esetében fizikailag elkülönített fürtöket kell használnia.

## <a name="physically-isolate-clusters"></a>Fürtök fizikai elkülönítése

**Ajánlott eljárási útmutató** – a fizikai elkülönítés használatának csökkentése minden különálló csapat vagy alkalmazás-telepítés esetén. Ehelyett használja a *logikai* elkülönítést az előző szakaszban leírtak szerint.

A fürtök elkülönítésének közös megközelítése a fizikailag különálló AK-fürtök használata. Ebben az elkülönítési modellben a csapatok vagy munkaterhelések a saját AK-fürthöz vannak rendelve. Ez a megközelítés gyakran hasonlít a munkaterhelések és a csapatok elkülönítésének legegyszerűbb módjára, azonban további felügyeleti és pénzügyi terhelést is felvesz. Most meg kell őriznie ezeket a több fürtöt, és külön kell megadnia a hozzáférést, és engedélyeket kell rendelnie. Az egyes csomópontok díját is felszámítjuk.

![Az egyes Kubernetes-fürtök fizikai elkülönítése az AK-ban](media/operator-best-practices-cluster-isolation/physical-isolation.png)

A fizikailag különálló fürtök általában alacsony hüvelyi sűrűségtel rendelkeznek. Mivel az egyes csapatok vagy munkaterhelések saját AK-fürttel rendelkeznek, a fürt gyakran túl sok számítási erőforrással van kiépítve. Gyakran kevés hüvely van ütemezve ezeken a csomópontokon. A csomópontokon a nem használt kapacitás nem használható más csapatok által a fejlesztés alatt álló alkalmazásokhoz és szolgáltatásokhoz. Ezek a felesleges erőforrások a fizikailag különálló fürtök további költségeihez járulnak hozzá.

## <a name="next-steps"></a>Következő lépések

Ez a cikk a fürt elkülönítésére koncentrál. Az AK-beli fürtműveleteket kapcsolatos további információkért tekintse meg az alábbi ajánlott eljárásokat:

* [Alapszintű Kubernetes Scheduler-funkciók][aks-best-practices-scheduler]
* [Speciális Kubernetes Scheduler-funkciók][aks-best-practices-advanced-scheduler]
* [Hitelesítés és engedélyezés][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
