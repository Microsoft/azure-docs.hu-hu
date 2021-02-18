---
title: Azure arc-kompatibilis Kubernetes-ügynök architektúrája
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ez a cikk az Azure arc-kompatibilis Kubernetes-ügynökök építészeti áttekintését tartalmazza
keywords: Kubernetes, arc, Azure, tárolók
ms.openlocfilehash: 287ffdd40dc9ffdb91abb58b305d8b35b0bc3674
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652564"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Azure arc-kompatibilis Kubernetes-ügynök architektúrája

A [Kubernetes](https://kubernetes.io/) saját maga helyezheti üzembe a konténeres számítási feladatokat a hibrid és a többfelhős környezetekben. Az Azure arc-kompatibilis Kubernetes azonban központosított, konzisztens vezérlési síkon működik, amely különböző-környezetekben kezeli a szabályzatot, a szabályozást és a biztonságot. Ez a cikk a következő információkat tartalmazza:

* A fürtök az Azure-ba való csatlakoztatásának építészeti áttekintése.
* A kapcsolati mintát, majd az ügynököket.
* A fürtözött környezet és az Azure közötti adatcsere leírása.

## <a name="deploy-agents-to-your-cluster"></a>Ügynökök üzembe helyezése a fürtön

A legtöbb helyszíni adatközpont szigorú hálózati szabályokat kényszerít, amelyek megakadályozzák a bejövő kommunikációt a hálózati határ tűzfalan. Az Azure arc-kompatibilis Kubernetes ezekkel a korlátozásokkal működik, mivel nem igényel bejövő portokat a tűzfalon, és csak a kimenő kommunikációhoz használható szelektív kimenő végpontokat engedélyezi. Az Azure arc használatára képes Kubernetes-ügynökök kezdeményezik ezt a kimenő kommunikációt. 

![Az architektúra áttekintése](./media/architectural-overview.png)

### <a name="connect-a-cluster-to-azure-arc"></a>Fürt összekötése az Azure-ív használatával

1. Hozzon létre egy Kubernetes-fürtöt az Ön által választott infrastruktúrán (VMware vSphere, Amazon Web Services, Google Cloud Platform stb.). 

    > [!NOTE]
    > Mivel az Azure arc-kompatibilis Kubernetes jelenleg csak a meglévő Kubernetes-fürtöknek az Azure arc felé való csatolását támogatja, az ügyfeleknek maguknak kell létrehozniuk és kezelnie a Kubernetes-fürt életciklusát.  

1. Indítsa el az Azure arc regisztrációját a fürthöz az Azure CLI használatával.
    * Az Azure CLI a Helm használatával helyezi üzembe az Agent Helm-diagramot a fürtön.
    * A fürtcsomópontok kimenő kommunikációt kezdeményeznek a [Microsoft Container Registry](https://github.com/microsoft/containerregistry) , és lekérik a következő ügynökök létrehozásához szükséges lemezképeket a `azure-arc` névtérben:

        | Ügynök | Leírás |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Az Azure arc-kompatibilis Kubernetes jelenleg csak a [rendszerhez rendelt identitásokat](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)támogatja. `clusteridentityoperator` elindítja az első kimenő kommunikációt. Ez az első kommunikáció lekéri a más ügynökök által az Azure-nal folytatott kommunikációhoz használt Managed Service Identity (MSI) tanúsítványt. |
        | `deployment.apps/config-agent` | A fürtön alkalmazott verziókövetés konfigurációs erőforrásainak figyeli a csatlakoztatott fürtöt. Frissíti a megfelelőségi állapotot. |
        | `deployment.apps/controller-manager` | Az Azure arc-összetevők közötti interakciókat kezelő operátorok. |    
        | `deployment.apps/metrics-agent` | Más ív-ügynökök metrikáit gyűjti az optimális teljesítmény ellenőrzéséhez. |
        | `deployment.apps/cluster-metadata-operator` | Összegyűjti a fürtök metaadatait, beleértve a fürtverziót, a csomópontok számát és az Azure Arc-ügynök verzióját. |
        | `deployment.apps/resource-sync-agent` | Szinkronizálja a fent említett fürt metaadatait az Azure-ba. |
        | `deployment.apps/flux-logs-agent` | Gyűjti a naplókat a verziókövetés konfigurációjának részeként üzembe helyezett Flux-kezelők számára. |
    
1. Ha az összes Azure arc-alapú Kubernetes-ügynök hüvelye `Running` állapotban van, ellenőrizze, hogy a fürt csatlakoztatva van-e az Azure arc-hoz. A következőket kell látnia:
    * Egy Azure arc-kompatibilis Kubernetes erőforrás a [Azure Resource Managerban](../../azure-resource-manager/management/overview.md). Az Azure nyomon követi ezt az erőforrást az ügyfél által felügyelt Kubernetes-fürt kivetítése helyett, nem a tényleges Kubernetes-fürtöt.
    * A fürt metaadatai (például a Kubernetes verziója, az ügynök verziója és a csomópontok száma) az Azure arc-kompatibilis Kubernetes-erőforrás metaadataiként jelennek meg.

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
| Konfiguráció kívánt állapota: a git-tárház URL-címe, a Flux-kezelő paraméterei, a titkos kulcs, ismert gazdagépek tartalma, HTTPS-Felhasználónév, jogkivonat vagy jelszó | Konfiguráció | Ügynök lekérése az Azure-ból |
| A Flux-kezelő telepítésének állapota | Konfiguráció | Az ügynök leküldi az Azure-ba |
| A fürtön belüli forgalomirányító-kényszerítést igénylő hozzárendelések Azure Policy | Azure Policy | Ügynök lekérése az Azure-ból |
| A fürtön belüli házirend-kényszerítések naplózása és megfelelőségi állapota | Azure Policy | Az ügynök leküldi az Azure-ba |
| Az ügyfelek számítási feladatainak metrikái és naplói | Azure Monitor | Az ügynök leküldi Log Analytics munkaterület-erőforrást az ügyfél bérlője és előfizetése számára |

## <a name="connectivity-status"></a>Kapcsolat állapota

| Állapot | Leírás |
| ------ | ----------- |
| Csatlakozás | Az Azure arc-kompatibilis Kubernetes-erőforrás Azure Resource Manager-ben jön létre, de a szolgáltatás még nem kapta meg az ügynök szívverését. |
| Csatlakozva | Az Azure arc-kompatibilis Kubernetes szolgáltatás az előző 15 percen belül egy ügynök szívverését kapta. |
| Offline | Az Azure arc-kompatibilis Kubernetes-erőforrás korábban kapcsolódott, de a szolgáltatás 15 percig nem kapott ügynöki szívverést. |
| Lejárt | Az MSI-tanúsítvány érvényességi ideje 90 nappal a kiállítva után. A tanúsítvány lejárata után az erőforrást figyelembe kell venni, `Expired` és az összes funkció, például a konfiguráció, a figyelés és a házirend nem működik ezen a fürtön. Az Azure arc-kompatibilis Kubernetes-erőforrások kezelésével kapcsolatos további információkért tekintse meg [a gyakori kérdések című cikket](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources). |

## <a name="understand-connectivity-modes"></a>A csatlakozási módok ismertetése

| Kapcsolati mód | Description |
| ----------------- | ----------- |
| Teljesen csatlakoztatva | Az ügynökök csak kis késleltetéssel kommunikálhatnak az Azure-ban a GitOps-konfigurációk propagálásával, Azure Policy és forgalomirányító házirendek betartatásával, valamint a számítási feladatok metrikáinak és naplófájljainak Azure Monitor való összegyűjtésével. |
| Félig csatlakoztatott | A által lekért MSI-tanúsítvány `clusteridentityoperator` legfeljebb 90 napig érvényes a tanúsítvány lejárata előtt. A lejárat után az Azure arc-kompatibilis Kubernetes-erőforrás nem működik. Az Azure arc összes funkciójának újraaktiválásához a fürtön törölje és hozza létre újból az Azure arc-kompatibilis Kubernetes-erőforrást és-ügynököket. A 90 nap során legalább 30 naponta egyszer össze kell kapcsolni a fürtöt. |
| Leválasztott | A leválasztott környezetekben az Azure-t nem elérő Kubernetes-fürtöket jelenleg nem támogatja az Azure arc-kompatibilis Kubernetes. Ha ezt a képességet Ön is érdekli, küldje el vagy szavazzon az [Azure arc UserVoice fórumára](https://feedback.azure.com/forums/925690-azure-arc).

## <a name="next-steps"></a>Következő lépések

* [Fürt összekötése az Azure-ív használatával](./connect-cluster.md)
* [A konfigurációk fogalmi áttekintése](./conceptual-configurations.md)