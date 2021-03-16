---
title: Az Azure Kubernetes Service bemutatása
description: Megismerheti az Azure Kubernetes Service funkcióit és előnyeit, amelyekkel tárolóalapú alkalmazásokat helyezhet üzembe és felügyelhet az Azure-ban.
services: container-service
ms.topic: overview
ms.date: 02/24/2021
ms.custom: mvc
ms.openlocfilehash: bb4adac1f59370959830f418d27bc27f9aaf63d2
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493016"
---
# <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Az Azure Kubernetes Service (ak) leegyszerűsíti a felügyelt Kubernetes-fürt üzembe helyezését az Azure-ban az üzemeltetési terhelés Azure-ba való kiszervezésével. Az Azure üzemeltetett Kubernetes szolgáltatásként kezeli a kritikus feladatokat, például az állapot figyelését és karbantartását. Mivel a Kubernetes-főkiszolgálókat az Azure felügyeli, csak az ügynök csomópontjait kell felügyelni és karbantartani. Így az AK ingyenes; csak a fürtökön belüli ügynök-csomópontok kell fizetnie, a főkiszolgálók esetében nem.  

Létrehozhat egy AK-fürtöt a használatával:
* [Azure CLI](kubernetes-walkthrough.md)
* [Az Azure Portal](kubernetes-walkthrough-portal.md)
* [Azure PowerShell](kubernetes-walkthrough-powershell.md)
* Sablon alapú központi telepítési lehetőségek, például [Azure Resource Manager sablonok](kubernetes-walkthrough-rm-template.md) és Terraform használata 

Egy AKS-fürt üzembe helyezésekor a rendszer elvégzi Ön helyett a Kubernetes fő és összes más csomópontjának üzembe helyezését és konfigurálását. A speciális hálózatkezelés, a Azure Active Directory (Azure AD) integrációja, monitorozása és egyéb funkciói a telepítési folyamat során konfigurálhatók. 

A Kubernetes alapjaival kapcsolatos további információkért lásd: [Kubernetes alapfogalmak az AK][concepts-clusters-workloads]-hoz.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]
> Az AK támogatja a Windows Server-tárolókat is.

## <a name="access-security-and-monitoring"></a>Hozzáférés, biztonság és monitoring

A jobb biztonság és felügyelet érdekében az AK lehetővé teszi az integrációt az Azure AD-vel a következőkhöz:
* Használja a Kubernetes szerepköralapú hozzáférés-vezérlést (Kubernetes RBAC). 
* A fürt és az erőforrások állapotának figyelése.

### <a name="identity-and-security-management"></a>Identitás és biztonsági felügyelet

#### <a name="kubernetes-rbac"></a>A Kubernetes szerepköralapú hozzáférés-vezérlése (RBAC)

A fürt erőforrásaihoz való hozzáférés korlátozásához az AK támogatja a [KUBERNETES RBAC][kubernetes-rbac]. A Kubernetes RBAC szabályozza a hozzáférést és az engedélyeket a Kubernetes erőforrásaihoz és névterekhez.  

#### <a name="azure-ad"></a>Azure AD

Beállíthat egy AK-fürtöt az Azure AD-vel való integrációhoz. Az Azure AD-integrációval beállíthatja a Kubernetes-hozzáférést a meglévő identitás-és csoporttagság alapján. A meglévő Azure AD-felhasználók és-csoportok integrált bejelentkezési tapasztalattal és az AK-erőforrásokhoz való hozzáféréssel is rendelkeznek.  

Az identitással kapcsolatos további információkért lásd: [hozzáférési és identitási beállítások az AK][concepts-identity]-hoz.

Az AKS-fürtök biztonságossá tételéhez tekintse meg [Az Azure Active Directory és az AKS integrációja][aks-aad] című cikket.

### <a name="integrated-logging-and-monitoring"></a>Integrált naplózás és monitorozás

A Container Health Azure Monitor a memória és a processzor teljesítményének mérőszámait gyűjti a tárolók, csomópontok és vezérlők számára az AK-fürtön és a központilag telepített alkalmazásokon belül. Áttekintheti a tároló naplóit és [a Kubernetes fő naplóit][aks-master-logs]is, amelyek a következők:
* Egy Azure Log Analytics-munkaterületen van tárolva.
* A Azure Portalon, az Azure CLI-n vagy egy REST-végponton keresztül érhető el.

További információ: [Az Azure Kubernetes Service tárolóállapot-monitorozása][container-health].

## <a name="clusters-and-nodes"></a>Fürtök és csomópontok

AK-csomópontok futnak az Azure Virtual Machines szolgáltatásban (VM). Az AK-csomópontokkal csatlakoztathatja a tárolót a csomópontokhoz és hüvelyekhez, a fürt összetevőinek frissítéséhez és a GPU-k használatához. Az AK támogatja a több csomópontot futtató Kubernetes-fürtöket a vegyes operációs rendszerek és a Windows Server-tárolók támogatásához.  

További információ a Kubernetes-fürtről, a csomópontról és a csomópont-készlet funkcióival kapcsolatban: [Kubernetes Core Concepts for AK][concepts-clusters-workloads].

### <a name="cluster-node-and-pod-scaling"></a>Fürtcsomópont- és podméretezés

Az erőforrások igény szerinti változása esetén a szolgáltatásokat futtató fürtcsomópontok vagy hüvelyek száma automatikusan felfelé vagy lefelé változik. A horizontális Pod automéretezőt vagy a fürt automéretezőjét úgy is beállíthatja, hogy az igényekhez alkalmazkodjon, és csak a szükséges erőforrásokat futtassa.

További információ: [Azure Kubernetes Service- (AKS-) fürtök méretezése][aks-scale].

### <a name="cluster-node-upgrades"></a>Fürtcsomópont-frissítés

Az AK több Kubernetes-verziót is kínál. Mivel az új verziók az AK-ban válnak elérhetővé, a Azure Portal vagy az Azure CLI használatával frissítheti a fürtöt. A frissítési folyamat során a csomópontok megfelelően el vannak szigetelve és ki vannak ürítve, hogy minimális hatással legyenek a futó alkalmazásokra.  

A verziók életciklusáról további tudnivalókat [Az AKS-ben támogatott Kubernetes verziók][aks-supported versions] című cikkben talál. A frissítés lépéseit [Az Azure Kubernetes Service- (AKS-) fürtök frissítése][aks-upgrade] című cikk ismerteti.

### <a name="gpu-enabled-nodes"></a>GPU-kompatibilis csomópontok

Az AK támogatja a GPU-kompatibilis csomópont-készletek létrehozását. Az Azure jelenleg egy vagy több GPU-t támogató virtuális gépet biztosít. A GPU-kompatibilis virtuális gépek nagy számítási igényű, grafikus igényű és vizualizációs munkaterhelésekhez készültek.

További információ: [GPU-k használata az AKS-en][aks-gpu].

### <a name="confidential-computing-nodes-public-preview"></a>Bizalmas számítástechnikai csomópontok (nyilvános előzetes verzió)

Az AK támogatja az Intel SGX ENKLÁVÉHOZ-alapú, bizalmas számítástechnikai csomópont-készletek (DCSv2 virtuális gépek) létrehozását. A bizalmas számítástechnikai csomópontok lehetővé teszik, hogy a tárolók hardveres, megbízható végrehajtási környezetben (enklávékban) fussanak. A tárolók közötti elkülönítés, amely a kód integritásával együtt igazolható, segíthet a védelmi részletes biztonsági stratégiában. A bizalmas számítástechnikai csomópontok egyaránt támogatják a bizalmas tárolókat (a meglévő Docker-alkalmazásokat) és az enklávét támogató tárolókat.

További információ: [bizalmas számítástechnikai csomópontok az AK-on][conf-com-node].

### <a name="storage-volume-support"></a>A tárkötetek támogatását

Az alkalmazások számítási feladatainak támogatásához statikus vagy dinamikus tárolási köteteket is csatlakoztathat az állandó adatokhoz. Attól függően, hogy hány csatlakoztatott hüvelynek kell megosztania a tárolási köteteket, a következők egyikét használhatja:
* Azure-lemezek egyetlen Pod-hozzáféréshez, vagy 
* Azure Files több, egyidejű Pod-hozzáféréshez.

További információ: [tárolási lehetőségek az AK-ban lévő alkalmazásokhoz][concepts-storage].

A dinamikus állandó kötetek használatának első lépései az [Azure-lemezek][azure-disk] vagy a [Azure Files][azure-files]használatával.

## <a name="virtual-networks-and-ingress"></a>Virtuális hálózatok és bejövő forgalom

Az AKS-fürtök egy már meglévő virtuális hálózaton is üzembe helyezhetők. Ebben a konfigurációban a fürtben található összes Pod IP-címet kap a virtuális hálózatban, és közvetlenül tud kommunikálni a következővel:
* Más hüvelyek a fürtben 
* A virtuális hálózat többi csomópontja. 

A hüvelyek a ExpressRoute-vagy helyek közötti (S2S) VPN-kapcsolatokon keresztül is csatlakozhatnak a csatlakoztatott virtuális hálózatokban lévő más szolgáltatásokhoz, valamint a helyszíni hálózatokhoz is.  

További információ: az [AK-beli alkalmazások hálózati fogalmai][aks-networking].

### <a name="ingress-with-http-application-routing"></a>Bejövő forgalom HTTP-alkalmazásútválasztással

A HTTP-alkalmazás útválasztási bővítménye segít az AK-fürtön üzembe helyezett alkalmazások egyszerű elérésében. Ha engedélyezve van, a HTTP-alkalmazásútválasztási megoldás egy bejövőforgalom-vezérlőt konfigurál az AKS-fürtjén.  

Az alkalmazások telepítésekor a nyilvánosan elérhető DNS-nevek automatikusan konfigurálva vannak. A HTTP-alkalmazás útválasztása létrehoz egy DNS-zónát, és integrálja azt az AK-fürttel. Ezután a Kubernetes bejövő forgalmának erőforrásai a szokásos módon üzembe helyezhetők.  

A bejövő forgalom megismeréséhez tekintse meg a [HTTP-alkalmazásútválasztás][aks-http-routing] című cikket.

## <a name="development-tooling-integration"></a>Fejlesztési eszközök integrációja

A Kubernetes a fejlesztési és felügyeleti eszközök gazdag ökoszisztémával rendelkezik, amelyek zökkenőmentesen működnek az AK-val. Ezek az eszközök a Helm és a Visual Studio Code Kubernetes bővítményét tartalmazzák.   

Az Azure számos olyan eszközt biztosít, amelyek megkönnyítik a Kubernetes, például az Azure dev Spaces és a DevOps Starter platformot.  

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Az Azure Dev Spaces gyors, iteratív Kubernetes-fejlesztési élményt biztosít a csapatok számára. Minimális konfigurálással futtathat tárolókat és végezhet rajtuk hibakeresést közvetlenül az AKS-ben. Ismerkedés az [Azure Dev Spaces][azure-dev-spaces] használatával.

### <a name="devops-starter"></a>DevOps Starter

A DevOps Starter egyszerű megoldást kínál a meglévő kódok és git-adattárak Azure-ba való bevezetésére. DevOps kezdő automatikus:
* Azure-erőforrásokat hoz létre (például ak); 
* Olyan kiadási folyamatot konfigurál az Azure DevOps-szolgáltatásokban, amely tartalmazza a CI-hez készült Build folyamatot. 
* A CD-re vonatkozó kiadási folyamat beállítása; és 
* Létrehoz egy Azure Application Insights-erőforrást a figyeléshez. 

További információ: [DevOps Starter][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Docker-rendszerképek támogatása és privát tárolóregisztrációs adatbázis

Az AKS támogatja a Docker rendszerképformátumát. Integrálhatja az AKS-szolgáltatást az Azure Container Registryvel (ACR-rel) a Docker-rendszerképek privát tárolásához.

Privát rendszerkép-tároló létrehozásához lásd: [Azure Container Registry][acr-docs].

## <a name="kubernetes-certification"></a>Kubernetes-tanúsítvány

Az AK-t Kubernetes-megfelelőséggel CNCF minősítette.

## <a name="regulatory-compliance"></a>Előírásoknak való megfelelés

Az AK megfelel a SOC, az ISO, a PCI DSS és a HIPAA. További információ: [az Microsoft Azure megfelelőségének áttekintése][compliance-doc].

## <a name="next-steps"></a>Következő lépések

További információ az AK üzembe helyezéséről és kezeléséről az Azure CLI rövid útmutatójában.

> [!div class="nextstepaction"]
> [AK-fürt üzembe helyezése az Azure CLI-vel][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://azure.microsoft.com/en-us/overview/trusted-cloud/compliance/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration-cli.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: ../dev-spaces/index.yml
[azure-devops]: ../devops-project/overview.md
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../azure-monitor/containers/container-insights-overview.md
[aks-master-logs]: ./view-control-plane-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
[conf-com-node]: ../confidential-computing/confidential-nodes-aks-overview.md
