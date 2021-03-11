---
title: Az Azure Kubernetes Service bemutatása
description: Megismerheti az Azure Kubernetes Service funkcióit és előnyeit, amelyekkel tárolóalapú alkalmazásokat helyezhet üzembe és felügyelhet az Azure-ban.
services: container-service
ms.topic: overview
ms.date: 02/09/2021
ms.custom: mvc
ms.openlocfilehash: 58a467d697e782b3e21e7b488b7db4c9b8951b2a
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102616842"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Az Azure Kubernetes Service (ak) leegyszerűsíti a felügyelt Kubernetes-fürtök üzembe helyezését az Azure-ban azáltal, hogy az Azure-ban sok összetettséget és működési terhelést kiszervez. Üzemeltetett Kubernetes-szolgáltatásként az Azure kezeli a kritikus feladatokat, például az állapot figyelését és karbantartását.  

Mivel a Kubernetes-főkiszolgálókat az Azure felügyeli, csak az ügynök csomópontjait kell felügyelni és karbantartani. Így a felügyelt Kubernetes szolgáltatásként az AK ingyenes; csak a fürtökön belüli ügynök-csomópontok kell fizetnie, a főkiszolgálók esetében nem.  

Létrehozhat egy AK-fürtöt az Azure Portal, az Azure CLI, a Azure PowerShell vagy a sablonon alapuló telepítési lehetőségek, például a Resource Manager-sablonok és a Terraform használatával. Egy AKS-fürt üzembe helyezésekor a rendszer elvégzi Ön helyett a Kubernetes fő és összes más csomópontjának üzembe helyezését és konfigurálását. Az olyan további funkciók, mint a speciális hálózatkezelés, az Azure Active Directory-integráció és a monitorozás szintén konfigurálhatók az üzembehelyezési folyamat során. A Windows Server-tárolók az AK-ban támogatottak.

A Kubernetes alapjaival kapcsolatos további információkért lásd: [Kubernetes alapfogalmak az AK][concepts-clusters-workloads]-hoz.

Első lépésként végezze el az AK gyors üzembe helyezését [a Azure Portal][aks-portal] vagy [Az Azure CLI-vel][aks-cli].

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="access-security-and-monitoring"></a>Hozzáférés, biztonság és monitoring

A jobb biztonság és felügyelet érdekében az AK lehetővé teszi az integrációt Azure Active Directory (Azure AD) és:
* Használja a Kubernetes szerepköralapú hozzáférés-vezérlést (Kubernetes RBAC). 
* A fürt és az erőforrások állapotának figyelése.

### <a name="identity-and-security-management"></a>Identitás és biztonsági felügyelet

A fürt erőforrásaihoz való hozzáférés korlátozásához az AK támogatja a [KUBERNETES RBAC][kubernetes-rbac]. A Kubernetes RBAC lehetővé teszi a hozzáférés és az engedélyek vezérlését a Kubernetes erőforrásaihoz és névterekhez.  

Beállíthat egy AK-fürtöt is az Azure AD-vel való integrációhoz. Az Azure AD-integrációval a Kubernetes-hozzáférés konfigurálható a meglévő identitás-és csoporttagság alapján. A meglévő Azure AD-felhasználók és-csoportok integrált bejelentkezési tapasztalattal és az AK-erőforrásokhoz való hozzáféréssel is rendelkeznek.  

Az identitással kapcsolatos további információkért lásd: [hozzáférési és identitási beállítások az AK][concepts-identity]-hoz.

Az AKS-fürtök biztonságossá tételéhez tekintse meg [Az Azure Active Directory és az AKS integrációja][aks-aad] című cikket.

### <a name="integrated-logging-and-monitoring"></a>Integrált naplózás és monitorozás

A Container Health Azure Monitor a memória és a processzor teljesítményének mérőszámait gyűjti a tárolók, csomópontok és vezérlők számára az AK-fürtön és a központilag telepített alkalmazásokon belül. Áttekintheti a tároló naplóit és [a Kubernetes fő naplóit][aks-master-logs]is. Ezeket a figyelési adatok egy Azure Log Analytics-munkaterületen tárolódnak, és a Azure Portal, az Azure CLI vagy egy REST-végponton keresztül érhetők el.

További információ: [Az Azure Kubernetes Service tárolóállapot-monitorozása][container-health].

## <a name="clusters-and-nodes"></a>Fürtök és csomópontok

AK-csomópontok futnak az Azure Virtual Machines szolgáltatásban (VM). Az AK-csomópontokkal csatlakoztathatja a tárolót a csomópontokhoz és hüvelyekhez, a fürt összetevőinek frissítéséhez és a GPU-k használatához. Az AK támogatja a több csomópontot futtató Kubernetes-fürtöket a vegyes operációs rendszerek és a Windows Server-tárolók támogatásához.  

A Kubernetes-fürtre, a csomópontra és a csomópont-készletre vonatkozó képességekkel kapcsolatos további információkért lásd: az AK-hoz készült [Kubernetes alapfogalmai][concepts-clusters-workloads].

### <a name="cluster-node-and-pod-scaling"></a>Fürtcsomópont- és podméretezés

Az erőforrások változtatása iránti igények miatt a szolgáltatását futtató fürtcsomópontok és podok száma automatikusan skálázhatók felfelé vagy lefelé. Egyaránt használhatja a podok automatikus vízszintes skálázását és a fürt automatikus skálázását. Ezzel a megközelítéssel az AKS-fürtök a képesek automatikusan alkalmazkodni az igényekhez, és csak a szükséges erőforrásokat használják.

További információ: [Azure Kubernetes Service- (AKS-) fürtök méretezése][aks-scale].

### <a name="cluster-node-upgrades"></a>Fürtcsomópont-frissítés

Az AK több Kubernetes-verziót is kínál. Amint elérhetővé válik egy új verzió az AKS-ben, az Azure Portal vagy az Azure CLI használatával azonnal frissítheti fürtjét. A frissítési folyamat során a csomópontok megfelelően el vannak szigetelve és ki vannak ürítve, hogy minimális hatással legyenek a futó alkalmazásokra.  

A verziók életciklusáról további tudnivalókat [Az AKS-ben támogatott Kubernetes verziók][aks-supported versions] című cikkben talál. A frissítés lépéseit [Az Azure Kubernetes Service- (AKS-) fürtök frissítése][aks-upgrade] című cikk ismerteti.

### <a name="gpu-enabled-nodes"></a>GPU-kompatibilis csomópontok

Az AK támogatja a GPU-kompatibilis csomópont-készletek létrehozását. Az Azure jelenleg egy vagy több GPU-t támogató virtuális gépet biztosít. A GPU-kompatibilis virtuális gépek nagy számítási igényű, grafikus igényű és vizualizációs munkaterhelésekhez készültek.

További információ: [GPU-k használata az AKS-en][aks-gpu].

### <a name="confidential-computing-nodes-public-preview"></a>Bizalmas számítástechnikai csomópontok (nyilvános előzetes verzió)

Az AK támogatja az Intel SGX ENKLÁVÉHOZ-alapú, bizalmas számítástechnikai csomópont-készletek (DCSv2 virtuális gépek) létrehozását. A bizalmas számítástechnikai csomópontok lehetővé teszik, hogy a tárolók hardveres, megbízható végrehajtási környezetben (enklávékban) fussanak. A tárolók közötti elkülönítés, amely a kód integritásával együtt igazolható, segíthet a védelmi részletes biztonsági stratégiában. A bizalmas számítástechnikai csomópontok egyaránt támogatják a bizalmas tárolókat (a meglévő Docker-alkalmazásokat) és az enklávét támogató tárolókat.

További információ: [bizalmas számítástechnikai csomópontok az AK-on][conf-com-node].

### <a name="storage-volume-support"></a>A tárkötetek támogatását

Az alkalmazás számítási feladatának támogatásához csatlakoztathat tárköteteket az állandó adatok tárolásához. Statikus és dinamikus köteteket is használhat. Attól függően, hogy a csatlakoztatott hüvelyek hányan osztják meg a tárolási köteteket, használhatja az Azure-lemezek által támogatott tárterületet az egypod hozzáféréshez, vagy Azure Files több párhuzamos Pod-hozzáféréshez.

További információ: [tárolási lehetőségek az AK-ban lévő alkalmazásokhoz][concepts-storage].

A dinamikus állandó kötetek használatának első lépései az [Azure-lemezek][azure-disk] vagy a [Azure Files][azure-files]használatával.

## <a name="virtual-networks-and-ingress"></a>Virtuális hálózatok és bejövő forgalom

Az AKS-fürtök egy már meglévő virtuális hálózaton is üzembe helyezhetők. Ebben a konfigurációban a fürtben található összes Pod IP-címet kap a virtuális hálózatban, és közvetlenül tud kommunikálni más hüvelyekkel a fürtben és a virtuális hálózat más csomópontjain. A hüvelyek a ExpressRoute-vagy helyek közötti (S2S) VPN-kapcsolatokon keresztül is csatlakozhatnak a csatlakoztatott virtuális hálózatokban lévő más szolgáltatásokhoz, valamint a helyszíni hálózatokhoz is.  

További információ: az [AK-beli alkalmazások hálózati fogalmai][aks-networking].

### <a name="ingress-with-http-application-routing"></a>Bejövő forgalom HTTP-alkalmazásútválasztással

A HTTP-alkalmazásútválasztási bővítménynek köszönhetően egyszerűen hozzáférhet az AKS-fürtjére telepített alkalmazásokhoz. Ha engedélyezve van, a HTTP-alkalmazásútválasztási megoldás egy bejövőforgalom-vezérlőt konfigurál az AKS-fürtjén.  

Az alkalmazások telepítésekor a nyilvánosan elérhető DNS-nevek automatikusan konfigurálva vannak. A HTTP-alkalmazás útválasztása létrehoz egy DNS-zónát, és integrálja azt az AK-fürttel. Ezután a Kubernetes bejövő forgalmának erőforrásai a szokásos módon üzembe helyezhetők.  

A bejövő forgalom megismeréséhez tekintse meg a [HTTP-alkalmazásútválasztás][aks-http-routing] című cikket.

## <a name="development-tooling-integration"></a>Fejlesztési eszközök integrációja

A Kubernetes a fejlesztési és felügyeleti eszközök gazdag ökoszisztémával rendelkezik, amelyek zökkenőmentesen működnek az AK-val. Ezek az eszközök a Helm és a Visual Studio Code Kubernetes bővítményét tartalmazzák. Ezek az eszközök problémamentesen működnek az AKS-szolgáltatással.  

Az Azure emellett számos olyan eszközt biztosít, amelyek megkönnyítik a Kubernetes, például a DevOps Starter-t.  

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
> [AKS – rövid útmutató][aks-cli]

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
