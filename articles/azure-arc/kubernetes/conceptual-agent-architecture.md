---
title: Azure arc-kompatibilis Kubernetes-ügynök architektúrája
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ez a cikk az Azure arc-kompatibilis Kubernetes-ügynökök építészeti áttekintését tartalmazza
keywords: Kubernetes, arc, Azure, tárolók
ms.openlocfilehash: e1f51f066598b59501b30704cb1475dd5332160e
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561676"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Azure arc-kompatibilis Kubernetes-ügynök architektúrája

A [Kubernetes](https://kubernetes.io/) lehetővé teszi, hogy egységes módon telepítsen tároló alapú számítási feladatokat a hibrid és a többfelhős környezetekben. Az Azure arc-kompatibilis Kubernetes központosított vezérlőként használhatók a házirendek, a szabályozás és a biztonság következetes kezeléséhez ezekben a különböző környezetekben. Ez a cikk a következő információkat tartalmazza:

* A fürtök az Azure-ba való csatlakoztatásának építészeti áttekintése.
* A kapcsolati mintát, majd az ügynököket.
* A fürtözött környezet és az Azure közötti adatcsere leírása.

## <a name="deploy-agents-to-your-cluster"></a>Ügynökök üzembe helyezése a fürtön

A legtöbb helyszíni adatközpont szigorú hálózati szabályokat kényszerít, amelyek megakadályozzák a bejövő kommunikációt a hálózati határnál használt tűzfalon. Az Azure arc-kompatibilis Kubernetes ezekkel a korlátozásokkal működik, így csak a kimenő kommunikáció számára engedélyezi a szelektív kimenő végpontokat, és nem igényel bejövő portokat a tűzfalon. Az Azure arc-kompatibilis Kubernetes-ügynökök kezdeményezik a kimenő kapcsolatokat.

![Az architektúra áttekintése](./media/architectural-overview.png)

Csatlakoztasson egy fürtöt az Azure archoz a következő lépések végrehajtásával:

1. Hozzon létre egy Kubernetes-fürtöt az Ön által választott infrastruktúrán (VMware vSphere, Amazon Web Services, Google Cloud Platform stb.). 

    > [!NOTE]
    > Ügyfeleinknek a Kubernetes-fürt életciklusának létrehozásához és kezeléséhez az Azure arc-kompatibilis Kubernetes jelenleg csak a meglévő Kubernetes-fürtöknek az Azure-ívekhez való csatolását támogatja.  

1. Kezdeményezzen Azure arc-regisztrációt a fürthöz az Azure CLI használatával.
    * Az Azure CLI a Helm használatával helyezi üzembe az Agent Helm-diagramot a fürtön.
    * A fürtcsomópontok kimenő kommunikációt kezdeményeznek a [Microsoft Container Registry](https://github.com/microsoft/containerregistry) , és lekérik a következő ügynökök létrehozásához szükséges lemezképeket a `azure-arc` névtérben:

        | Ügynök | Leírás |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Az Azure arc-kompatibilis Kubernetes jelenleg csak a [rendszerhez rendelt identitásokat](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)támogatja. a clusteridentityoperator az első kimenő kommunikációhoz szükséges ahhoz, hogy beolvassa a más ügynökök által az Azure-nal folytatott kommunikációhoz használt felügyelt szolgáltatás-identitás (MSI) tanúsítványát. |
        | `deployment.apps/config-agent` | A fürtön alkalmazott forrásoldali vezérlési konfigurációs erőforrások, valamint a frissítések megfelelőségi állapotának figyeli a csatlakoztatott fürtöt |
        | `deployment.apps/controller-manager` | Az Azure arc-összetevők közötti interakciókat kezelő operátorok |    
        | `deployment.apps/metrics-agent` | Más ív-ügynökök metrikáit gyűjti, így biztosítva, hogy az ügynökök optimális teljesítményt mutassanak |
        | `deployment.apps/cluster-metadata-operator` | A fürt metaadatait – a fürt verzióját, a csomópontok darabszámát és az Azure arc-ügynök verzióját gyűjti. |
        | `deployment.apps/resource-sync-agent` | Szinkronizálja a fent említett fürt metaadatait az Azure-ba |
        | `deployment.apps/flux-logs-agent` | A a verziókövetés konfigurációjának részeként üzembe helyezett Flux-kezelők naplóit gyűjti. |
    
1. Ha az Azure arc összes engedélyezett Kubernetes-ügynöke `Running` állapotban van, ellenőrizze, hogy a fürt csatlakoztatva van-e az Azure arc-hoz. A következőket kell látnia:
    * Egy Azure arc-kompatibilis Kubernetes erőforrás a [Azure Resource Managerban](../../azure-resource-manager/management/overview.md). Ezt az erőforrást az Azure-ban követik nyomon az ügyfél által felügyelt Kubernetes-fürt kivetítésével, nem pedig maga a tényleges Kubernetes-fürt.
    * A fürt metaadatai, például a Kubernetes verziója, az ügynök verziószáma és a csomópontok száma az Azure arc-kompatibilis Kubernetes-erőforrás metaadatként jelenik meg.

## <a name="data-exchange-between-cluster-environment-and-azure"></a>Adatcsere a fürt környezete és az Azure között

| Adattípus | Eset | Kommunikációs mód |
| --------- | -------- | ------------------ |
| Kubernetes-fürt verziója | Fürt metaadatai | Az ügynök leküldi az Azure-ba |
| Csomópontok száma a fürtben | Fürt metaadatai | Az ügynök leküldi az Azure-ba |
| Ügynök verziója | Fürt metaadatai | Az ügynök leküldi az Azure-ba |
| Kubernetes eloszlás típusa | Fürt metaadatai | Azure CLI leküldés az Azure-ba |
| Infrastruktúra típusa (AWS/GCP/vSphere/...) | Fürt metaadatai | Azure CLI leküldés az Azure-ba |
| a fürt csomópontjainak vCPU száma | Számlázás | Azure CLI leküldés az Azure-ba |
| Ügynök szívverése | Resource Health | Az ügynök leküldi az Azure-ba |
| Erőforrás-felhasználás (memória/CPU) ügynökök szerint | Diagnosztika és támogatás | Az ügynök leküldi az Azure-ba |
| Az összes ügynök tárolójának naplói | Diagnosztika és támogatás | Az ügynök leküldi az Azure-ba |
| Ügynök frissítésének rendelkezésre állása | Ügynök frissítése | Ügynök lekérése az Azure-ból |
| Konfiguráció kívánt állapota – git-tárház URL-címe, Flux-kezelő paraméterei, titkos kulcs, ismert gazdagépek tartalma, HTTPS-Felhasználónév, jogkivonat/jelszó | Konfiguráció | Ügynök lekérése az Azure-ból |
| A Flux-kezelő telepítésének állapota | Konfiguráció | Az ügynök leküldi az Azure-ba |
| A fürtön belüli forgalomirányító-kényszerítést igénylő hozzárendelések Azure Policy | Azure Policy | Ügynök lekérése az Azure-ból |
| A fürtön belüli házirend-kényszerítések naplózása és megfelelőségi állapota | Azure Policy | Az ügynök leküldi az Azure-ba |
| Az ügyfelek számítási feladatainak metrikái és naplói | Azure Monitor | Az ügynök leküldi Log Analytics munkaterület-erőforrást az ügyfél bérlője és előfizetése számára |

## <a name="connectivity-status"></a>Kapcsolat állapota

| Állapot | Leírás |
| ------ | ----------- |
| Csatlakozás | Az Azure arc engedélyezve van a Azure Resource Manager-ben létrehozott Kubernetes-erőforrás, de a szolgáltatás még nem kapta meg az ügynök szívverését. |
| Csatlakozva | Az Azure arc-kompatibilis Kubernetes szolgáltatás az előző 15 percen belül egy ügynök szívverését kapta. |
| Offline | Az Azure arc-kompatibilis Kubernetes-erőforrás korábban kapcsolódott, de a szolgáltatás 15 percig nem kapott ügynöki szívverést. |
| Lejárt | A felügyelt szolgáltatás-identitás (MSI) tanúsítványa érvényességi ideje 90 nappal a kiállítva után. A tanúsítvány lejárata után az erőforrást figyelembe kell venni, `Expired` és az összes funkció, például a konfiguráció, a figyelés és a házirend nem működik ezen a fürtön. A lejárt Azure arc-kompatibilis Kubernetes-erőforrások kezelésével kapcsolatos további információkért tekintse meg [a következőt](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources) |

## <a name="understand-connectivity-modes"></a>A csatlakozási módok ismertetése

| Kapcsolati mód | Leírás |
| ----------------- | ----------- |
| Teljesen csatlakoztatva | Az ügynökök mindig hozzáférhetnek az Azure-hoz. A élmény ideális ebben az esetben, mivel kevés a késés a konfigurációk propagálásában (GitOps esetében), a szabályzatok betartatása (Azure Policy és a forgalomirányító esetében), valamint a metrikák és a számítási feladatok naplóinak begyűjtése (Azure Monitor) |
| Félig csatlakoztatott | A által lekért MSI-tanúsítvány `clusteridentityoperator` legfeljebb 90 napig érvényes a tanúsítvány lejárata előtt. A tanúsítvány lejárata után az Azure arc-kompatibilis Kubernetes-erőforrás működése leáll. Törölje, majd hozza létre újra az Azure arc-kompatibilis Kubernetes-erőforrást és-ügynököket, hogy az összes arc-funkció működjön a fürtön. A 90 napon belül a felhasználóknak legalább 30 naponta egyszer csatlakozniuk kell a fürthöz. |
| Leválasztott | Az Azure arc-kompatibilis Kubernetes jelenleg nem támogatja a leválasztott környezetekben lévő Kubernetes-fürtöket az Azure-hoz való hozzáférés nélkül. Ha ezt a képességet Ön is érdekli, küldje el vagy szavazzon az [Azure arc UserVoice fórumára](https://feedback.azure.com/forums/925690-azure-arc).

## <a name="next-steps"></a>Következő lépések

* [Fürt összekötése az Azure-ív használatával](./connect-cluster.md)
* [A konfigurációk fogalmi áttekintése](./conceptual-configurations.md)