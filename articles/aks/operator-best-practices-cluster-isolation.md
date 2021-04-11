---
title: Ajánlott fürtelkülönítési eljárások
titleSuffix: Azure Kubernetes Service
description: Ismerje meg az Azure Kubernetes Service-ben (ak) való elkülönítésre vonatkozó ajánlott eljárásokat
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: e51689d33711f127f775c63c9d7fc8ad4c901604
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105170"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások a fürtök elkülönítéséhez az Azure Kubernetes szolgáltatásban (ak)

A fürtök Azure Kubernetes szolgáltatásban (ak) való kezelése során gyakran kell elkülönítenie a csapatokat és a munkaterheléseket. Az AK rugalmasságot biztosít a több-bérlős fürtök futtatásához és az erőforrások elkülönítéséhez. A Kubernetes-ben való befektetés maximalizálása érdekében először Ismerje meg és implementálja az AK több-bérlős és elkülönítési funkcióit.

Ez az ajánlott eljárási cikk a fürtcsomópontok elkülönítésére koncentrál. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A több-bérlős fürtök és az erőforrások elkülönítésének megtervezése
> * Logikai vagy fizikai elkülönítés használata az AK-fürtökben

## <a name="design-clusters-for-multi-tenancy"></a>Több-bérlős tervezési fürtök

A Kubernetes lehetővé teszi a csapatok és munkaterhelések logikai elkülönítését ugyanabban a fürtben. A cél az, hogy a lehető legkevesebb jogosultságot adja meg az egyes csapatok erőforrásainak hatókörében. A Kubernetes- [névtér][k8s-namespaces] logikai elkülönítési határt hoz létre. Az elkülönítésre és a több-bérlőre vonatkozó további Kubernetes-funkciók és-megfontolások a következő területeket foglalják magukban:

### <a name="scheduling"></a>Ütemezés

Az *Ütemezés* olyan alapszintű szolgáltatásokat használ, mint például az erőforrás-kvóták és a pod-megszakítási költségvetések. További információ ezekről a funkciókról: [ajánlott eljárások az alapszintű Scheduler-funkciókhoz az AK-ban][aks-best-practices-scheduler].

A speciális Scheduler-funkciók többek között a következők:
* A szennyező és a tolerálás
* Csomópont-választók
* Csomópont-és Pod-affinitás vagy-affinitás. 

További információ ezekről a funkciókról: [ajánlott eljárások a speciális Scheduler-funkciókhoz az AK-ban][aks-best-practices-advanced-scheduler].

### <a name="networking"></a>Hálózatkezelés

A *hálózatkezelés* hálózati házirendeket használ a hüvelyek és kívüli forgalom forgalmának szabályozására.

### <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés

*A hitelesítés és az engedélyezés a* következőket használja:
* Szerepköralapú hozzáférés-vezérlés (RBAC)
* Azure Active Directory (AD) integráció
* Pod-identitások
* Titkok a Azure Key Vaultban 

További információ ezekről a funkciókról: [ajánlott eljárások a hitelesítéshez és engedélyezéshez az AK-ban][aks-best-practices-identity].

### <a name="containers"></a>Tárolók
A *tárolók* a következők:
* A Azure Policy-bővítmény az AK-hoz a pod Security betartatása érdekében.
* A pod biztonsági környezetek használata.
* A lemezképek és a futtatókörnyezet vizsgálata a biztonsági rések esetében. 
* Az App Armor vagy a Seccompot (Secure Computing) használatával korlátozhatja a tárolók elérését az alapul szolgáló csomóponthoz.

## <a name="logically-isolate-clusters"></a>Fürtök logikai elkülönítése

> **Útmutatás az ajánlott eljárásokhoz**
>
> Különálló csoportok és projektek *logikai elkülönítés* használatával. Csökkentse a munkacsoportok vagy alkalmazások elkülönítéséhez üzembe helyezett fizikai AK-fürtök számát.

Logikai elkülönítéssel egyetlen AK-beli fürt használható több számítási feladathoz, csapathoz vagy környezethez. A Kubernetes [névterek][k8s-namespaces] a számítási feladatok és erőforrások logikai elkülönítési határait alkotják.

![Kubernetes-fürt logikai elkülönítése az AK-ban](media/operator-best-practices-cluster-isolation/logical-isolation.png)

A fürtök logikai elkülönítése általában magasabb szintű Pod-sűrűséget biztosít, mint a fizikailag elkülönített fürtök, és a fürtben kevesebb felesleges számítási kapacitás áll rendelkezésre. A Kubernetes-fürt autoskálázásával kombinálva a csomópontok számát felfelé vagy lefelé méretezheti, hogy megfeleljen az igényeknek. Az automatikus skálázással kapcsolatos ajánlott eljárás minimálisra csökkentheti a költségeket, ha csak a szükséges csomópontok számát futtatja.

Jelenleg a Kubernetes-környezetek nem teljesen biztonságosak az ellenséges, több-bérlős használatra. Több-bérlős környezetben több bérlő dolgozik közös, megosztott infrastruktúrán. Ha az összes bérlő nem megbízható, további tervezésre van szükség, hogy a bérlők ne befolyásolják mások biztonságát és szolgáltatását.

A további biztonsági funkciók, például a *Pod biztonsági házirendek* vagy a csomópontok Kubernetes RBAC hatékonyan blokkolják a biztonsági réseket. Az ellenséges több-bérlős számítási feladatok futtatásakor a valódi biztonság érdekében csak a Hypervisort kell megbíznia. A Kubernetes biztonsági tartománya a teljes fürtvé válik, nem önálló csomópontként. 

Az ilyen típusú ellenséges több-bérlős munkaterhelések esetében fizikailag elkülönített fürtöket kell használnia.

## <a name="physically-isolate-clusters"></a>Fürtök fizikai elkülönítése

> **Útmutatás az ajánlott eljárásokhoz** 
>
> Csökkentse a fizikai elkülönítés használatát minden különálló csapatnál vagy alkalmazásnál. Ehelyett használja a *logikai* elkülönítést az előző szakaszban leírtak szerint.

A fizikailag elválasztó AK-fürtök a fürt elkülönítésének közös megközelítése. Ebben az elkülönítési modellben a csapatok vagy munkaterhelések a saját AK-fürthöz vannak rendelve. A fizikai elkülönítés a munkaterhelések és a csapatok elkülönítésének legegyszerűbb módja, így a felügyeleti és a pénzügyi terhelést is felveszi. Most meg kell őriznie ezeket a több fürtöt, és egyenként meg kell adnia a hozzáférést, és engedélyeket kell rendelnie. Az egyes csomópontok esetében a számlázás is megtörténik.

![Az egyes Kubernetes-fürtök fizikai elkülönítése az AK-ban](media/operator-best-practices-cluster-isolation/physical-isolation.png)

A fizikailag különálló fürtök általában alacsony hüvelyi sűrűségtel rendelkeznek. Mivel minden egyes csapatnak vagy munkaterhelésnek saját AK-beli fürtje van, a fürt gyakran túl van kiépítve számítási erőforrásokkal. Gyakran kevés hüvely van ütemezve ezeken a csomópontokon. A nem igényelt csomópont-kapacitás nem használható más csapatok által a fejlesztés alatt álló alkalmazásokhoz és szolgáltatásokhoz. Ezek a felesleges erőforrások a fizikailag különálló fürtök további költségeihez járulnak hozzá.

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
