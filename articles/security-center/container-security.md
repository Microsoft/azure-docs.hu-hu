---
title: Tárolók biztonsága a Azure Security Centerban | Microsoft Docs
description: Ismerkedjen meg Azure Security Center tárolók biztonsági funkcióival.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/07/2021
ms.author: memildin
ms.openlocfilehash: 3b5204f1d390388c2dc9a10ac2ca0234f6b0499b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101341"
---
# <a name="container-security-in-security-center"></a>Tárolóbiztonság a Security Centerben

Azure Security Center az Azure-natív megoldás a tárolók biztonságossá tételéhez.

A Security Center a következő tároló-erőforrástípusok elleni védelemmel rendelkezhet:

| Erőforrás típusa | Security Center által kínált védelem |
|:--------------------:|-----------|
| ![Kubernetes szolgáltatás](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Azure Kubernetes szolgáltatás (ak) fürtök** | – Az AK-fürtök konfigurációinak folyamatos felmérése, amelyekkel láthatóvá teheti a helytelen konfigurációkat, és útmutatást nyújt az észlelt problémák megoldásához.<br>[További információ a környezet megkeményedéséről a biztonsági javaslatok segítségével](#environment-hardening).<br><br>-Veszélyforrások elleni védelem az AK-fürtök és a Linux-csomópontok számára. A gyanús tevékenységekre vonatkozó riasztásokat az opcionális  [Azure Defender a Kubernetes esetében](defender-for-kubernetes-introduction.md)nyújtja.<br>[További információ az AK-csomópontok és-fürtök futásidejű védelméről](#run-time-protection-for-aks-nodes-and-clusters).|
| ![Tároló gazdagépe](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**Tároló gazdagépek**<br>(A Docker-t futtató virtuális gépek) | – A Docker-konfigurációk folyamatos értékelése a helytelen konfigurációkkal való láthatóság biztosításához, valamint irányelvek a választható  [Azure Defender for Servers](defender-for-servers-introduction.md)által felderített problémák megoldásához.<br>[További információ a környezet megkeményedéséről a biztonsági javaslatok segítségével](#environment-hardening).|
| ![Tároló-beállításjegyzék](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**Azure Container Registry (ACR) jegyzékek** | – Sebezhetőségi felmérés és felügyeleti eszközök a Azure Resource Manager-alapú ACR-jegyzékekben található rendszerképekhez az opcionális [Azure Defender a Container-jegyzékek esetében](defender-for-container-registries-introduction.md).<br>[További információ a tárolók rendszerképeinek a biztonsági rések elleni vizsgálatáról](#vulnerability-management---scanning-container-images). |
|||

Ez a cikk azt ismerteti, hogyan használhatók a Security Center, valamint a nem kötelező Azure Defender-csomagok a tároló-beállításjegyzékhez,-Kubernetes és-feladataihoz, valamint a tárolók és az alkalmazások biztonságának javításához, figyeléséhez és karbantartásához.

Megtudhatja, hogyan segíti a Security Center a tárolók biztonságával kapcsolatos alapvető szempontokat:

- [Sebezhetőségi kezelés – tároló lemezképének vizsgálata](#vulnerability-management---scanning-container-images)
- [Környezet megerősítése](#environment-hardening)
- [Futásidejű védelem AK-csomópontokhoz és-fürtökhöz](#run-time-protection-for-aks-nodes-and-clusters)

Az alábbi képernyőfelvételen az eszközök leltára oldal és a Security Center által védett különböző tároló-erőforrástípusok láthatók.

:::image type="content" source="./media/container-security/inventory-container-resources.png" alt-text="Tárolókkal kapcsolatos erőforrások Security Center eszköz leltározási lapján" lightbox="./media/container-security/inventory-container-resources.png":::

## <a name="vulnerability-management---scanning-container-images"></a>Sebezhetőségi kezelés – tároló lemezképének vizsgálata

A Azure Resource Manager-alapú Azure Container-nyilvántartásokban lévő lemezképek figyeléséhez engedélyezze [Az Azure Defender számára a Container-nyilvántartásokat](defender-for-container-registries-introduction.md). Security Center megvizsgálja az elmúlt 30 napban lekért képeket, leküldve a beállításjegyzékbe vagy importálta azokat. Az integrált képolvasót az iparág vezető sebezhetőségi vizsgálatának gyártója, a Qualys.

Ha a rendszer problémákba ütközik – Qualys vagy Security Center – értesítést kap az [Azure Defender irányítópultján](azure-defender-dashboard.md). Security Center a biztonsági rések esetében gyakorlati ajánlásokat és súlyossági besorolást biztosít, valamint útmutatást nyújt a probléma megoldásához. A Security Center a tárolókkal kapcsolatos javaslatairól a [javaslatok hivatkozási listájában](recommendations-reference.md#recs-compute)talál további információt.

Security Center szűrők és a vizsgálati eredmények osztályozása. Ha egy rendszerkép kifogástalan állapotú, Security Center megjelöli. A Security Center csak olyan rendszerképekhez hoz létre biztonsági javaslatokat, amelyeknek feloldhatók a problémák. Ha csak akkor értesíti, ha problémák merülnek fel, Security Center csökkenti a nemkívánatos tájékoztatási riasztások lehetséges lehetőségét.

## <a name="environment-hardening"></a>Környezet megerősítése

### <a name="continuous-monitoring-of-your-docker-configuration"></a>A Docker-konfiguráció folyamatos figyelése

Azure Security Center azonosítja a IaaS Linux rendszerű virtuális gépeken üzemeltetett nem felügyelt tárolókat, illetve a Docker-tárolókat futtató más Linux-gépeket. Security Center folyamatosan elemzi a tárolók konfigurációit. Ezután összehasonlítja azokat a [Center for Internet Security (CIS) Docker-teljesítményteszttel](https://www.cisecurity.org/benchmark/docker/).

Security Center tartalmazza a CIS Docker-teljesítményteszt teljes szabályait, és riasztást küld, ha a tárolók nem elégítik ki a vezérlőelemek egyikét sem. Ha helytelen konfigurációkat talál, Security Center biztonsági javaslatokat hoz létre. A javaslatok megtekintéséhez és a problémák megoldásához használja a Security Center **ajánlásai lapot** . A CIS-alapú teljesítménytesztek ellenőrzése nem fut AK által felügyelt példányokon vagy Databricks által felügyelt virtuális gépeken.

A szolgáltatással kapcsolatos releváns Security Center javaslatok részleteiért tekintse meg az ajánlásokat ismertető táblázat [számítási szakaszát](recommendations-reference.md#recs-compute) .

Ha egy virtuális gép biztonsági problémáit vizsgálja, Security Center további információkat nyújt a gépen lévő tárolókkal kapcsolatban. Ezek az adatok tartalmazzák a Docker-verziót és a gazdagépen futó rendszerképek számát. 

A IaaS Linux rendszerű virtuális gépeken üzemeltetett nem felügyelt tárolók figyeléséhez engedélyezze az opcionális [Azure Defender-kiszolgálókat](defender-for-servers-introduction.md).


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>A Kubernetes-fürtök folyamatos figyelése
A Security Center együttműködik az Azure Kubernetes szolgáltatással (ak), a Microsoft felügyelt tároló-előkészítési szolgáltatásával a tároló alkalmazások fejlesztéséhez, üzembe helyezéséhez és kezeléséhez.

Az AK biztonsági ellenőrzéseket és láthatóságot biztosít a fürtök biztonsági állapotáról. Security Center ezekkel a szolgáltatásokkal folyamatosan figyeli az AK-fürtök konfigurációját, és előkészíti az iparági szabványokhoz igazított biztonsági javaslatokat.

Ez a Azure Security Center, az Azure Kubernetes Service és a Azure Policy közötti interakció magas szintű diagramja:

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Az Azure Security Center, az Azure Kubernetes szolgáltatás és a Azure Policy közötti interakció magas szintű architektúrája" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::

Láthatja, hogy a Security Center kapott és elemzett elemek a következők:

- naplók naplózása az API-kiszolgálóról
- nyers biztonsági események a Log Analytics-ügynökből

    > [!NOTE]
    > Jelenleg nem támogatjuk a Log Analytics ügynök telepítését a virtuálisgép-méretezési csoportokon futó Azure Kubernetes Service-fürtökön.

- a fürt konfigurációs adatai az AK-fürtből
- munkaterhelés-konfiguráció Azure Policy (a **Kubernetes Azure Policy-bővítményének** használatával)

A szolgáltatással kapcsolatos releváns Security Center javaslatok részleteiért tekintse meg az ajánlásokat ismertető táblázat [számítási szakaszát](recommendations-reference.md#recs-compute) .


###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Munkaterhelések elleni védelem – ajánlott eljárások a Kubernetes belépésvezérlés használatával

A Kubernetes-tárolók számítási feladatainak ellátására szolgáló javaslatok csomagjának telepítéséhez telepítse a  **Kubernetes Azure Policy bővítményét**. A bővítmény automatikus központi telepítését a [log Analytics-ügynök és-bővítmények automatikus kiépítése lehetővé tétele](security-center-enable-data-collection.md#auto-provision-mma)című cikkben ismertetett módon is elvégezheti. Ha a bővítmény automatikus kiépítés értéke "on", a bővítmény alapértelmezés szerint engedélyezve van az összes meglévő és jövőbeli fürtben (amelyek megfelelnek a bővítmény telepítési követelményeinek).

Ahogy az a [Kubernetes oldalán is Azure Policy](../governance/policy/concepts/policy-for-kubernetes.md)kifejtettük, a bővítmény kibővíti a nyílt forráskódú [forgalomirányító v3](https://github.com/open-policy-agent/gatekeeper)   belépésvezérlés webhookot a [nyílt házirend-ügynök](https://www.openpolicyagent.org/)számára. A Kubernetes-belépésvezérlés olyan beépülő modulok, amelyek kikényszerítik a fürtök használatának módját. A bővítmény webes hookként regisztrálja a Kubernetes, és lehetővé teszi, hogy központosított, konzisztens módon alkalmazza a fürtökön a fürtre kiterjedő kényszerítéseket és védelmet. 

Az AK-fürthöz tartozó bővítménnyel a Kubernetes API-kiszolgálónak küldött összes kérést a rendszer az előre meghatározott ajánlott eljárások alapján figyeli, mielőtt a fürtön megmarad. Ezután konfigurálhatja az ajánlott eljárások **betartatását** és a jövőbeli munkaterhelések megadását. 

Megadhatja például, hogy az emelt szintű tárolók ne legyenek létrehozva, és minden jövőbeli kérelem le lesz tiltva.

További információ a [Kubernetes számítási feladatainak biztonságáról](kubernetes-workload-protections.md).


## <a name="run-time-protection-for-aks-nodes-and-clusters"></a>Futásidejű védelem AK-csomópontokhoz és-fürtökhöz

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>Következő lépések

Ebben az áttekintésben megtanulta a tárolók biztonságának alapvető elemeit Azure Security Centerban. Kapcsolódó anyagok esetében lásd:

- [Bevezetés az Azure Defender for Kubernetes használatába](defender-for-kubernetes-introduction.md)
- [A tárolóregisztrációs adatbázisokhoz készült Azure Defender bemutatása](defender-for-container-registries-introduction.md)