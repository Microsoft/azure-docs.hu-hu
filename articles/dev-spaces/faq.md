---
title: Gyakran ismételt kérdések az Azure dev Spaces-ről
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Válaszok az Azure dev Spaces használatával kapcsolatos gyakori kérdésekre
keywords: 'Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s '
ms.openlocfilehash: e83bed86714e4b92c63f4e7b7eb55df7a2a7eaff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96548834"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Gyakran ismételt kérdések az Azure dev Spaces-ről

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Ez az Azure dev Spaces-re vonatkozó gyakori kérdéseket tárgyalja.

## <a name="what-versions-of-kubernetes-are-supported-for-azure-dev-spaces"></a>Milyen verziójú Kubernetes támogatottak az Azure dev Spaces szolgáltatásban?

Az Azure dev Spaces a [Kubernetes jelenleg támogatott általánosan elérhető (GA) verzióit támogatja az AK-ban, akár 1,18-ig][aks-supported-k8s]. Az Kubernetes 1,19-es és újabb verziók tároló-futtatókörnyezetként tárolóként használják, amely nem működik az Azure dev Spaces szolgáltatással.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Mely Azure-régiók biztosítanak Azure dev Spaces-helyeket?

Az elérhető régiók teljes listájáért tekintse meg a [támogatott régiók][supported-regions] című témakört.

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Áttelepíthetem az AK-fürtöt az Azure dev Spaces használatával egy másik régióba?

Igen, ha szeretné áthelyezni az AK-fürtöt az Azure dev Spaces-szel egy másik [támogatott régióba][supported-regions], javasoljuk, hogy hozzon létre egy új fürtöt a másik régióban, majd telepítse és konfigurálja az Azure dev Spaces-t, és helyezze üzembe az erőforrásokat és az alkalmazásokat az új fürtön. Az AK áttelepítésével kapcsolatos további információkért lásd: [áttelepítés az Azure Kubernetes Service (ak) szolgáltatásba][aks-migration].

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Használhatom az Azure dev Spaces szolgáltatást meglévő Dockerfiles vagy Helm-diagramok segítségével?

Igen, ha a projekt már rendelkezik egy Docker vagy egy Helm-diagrammal, ezeket a fájlokat használhatja az Azure dev Spaces használatával. A futtatásakor `azds prep` használja a `--chart` paramétert, és adja meg a diagram helyét. Az Azure dev Spaces továbbra is létrehozza a *azds. YAML* és a *Docker. fejlessze* a fájlt, de nem cseréli le és nem módosítja a meglévő Docker vagy a Helm diagramot. Előfordulhat, hogy módosítania kell a *azds. YAML* és a *Docker.* Files fájlt, hogy minden megfelelően működjön a meglévő alkalmazással, ha fut `azds up` .

Saját Docker vagy Helm-diagram használata esetén a következő korlátozások érvényesek:
* Ha csak egy Docker használ, tartalmaznia kell mindazt, amire szüksége lehet a fejlesztési forgatókönyvek engedélyezéséhez, például a nyelvi SDK-t, nem csak a futtatókörnyezetet. Ha külön Docker használ az Azure dev Spaces szolgáltatáshoz, például egy Docker. fejlesszen, a fejlesztési forgatókönyvek engedélyezéséhez szükséges mindennek szerepelnie kell a Docker.
* A Helm-diagramnak támogatnia kell a vagy a teljes képcímkét a Values *. YAML* értékének átadásával.
* Ha bármit módosít a bejövő forgalomban, akkor a Helm diagramot is frissítheti az Azure dev Spaces által biztosított bejövő megoldás használatára.
* Ha az [Azure dev Spaces által biztosított útválasztási funkciókat][dev-spaces-routing]szeretné használni, az egyes projektekhez tartozó összes szolgáltatásnak egyetlen Kubernetes-névtérben kell lennie, és egyszerű elnevezéssel kell rendelkeznie, például a *Service-a szolgáltatáshoz*. A standard Helm-diagramok esetében ez az elnevezési frissítés a *fullnameOverride* tulajdonság értékének megadásával végezhető el.

## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Módosíthatom az Azure dev Spaces által generált fájlokat?

Igen, módosíthatja az [Azure dev Spaces által generált][dev-spaces-prep] *azds. YAML* fájlt, Docker és Helm diagramot a projekt előkészítésekor. A fájlok módosítása megváltoztatja a projekt felépítésének és futtatásának módját.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Használhatom nyilvános IP-cím nélkül az Azure dev Spaces szolgáltatást?

Nem, nyilvános IP-cím nélkül nem lehet Azure dev-helyeket kiépíteni egy AK-fürtön. Az [Azure dev Spaces for Routing][dev-spaces-routing]szolgáltatáshoz nyilvános IP-cím szükséges.

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Használhatom a saját beáramlást az Azure dev Spaces szolgáltatással?

Igen, a bejövő Azure dev Spaces létrehozásával párhuzamosan is konfigurálhatja a belépőket. Használhatja például a [traefik][ingress-traefik] vagy az [NGINX][ingress-nginx]-et.

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Használhatom a HTTPS-t az Azure dev Spaces használatával?

Igen, a [traefik][ingress-https-traefik] vagy [NGINX][ingress-https-nginx]használatával konfigurálhatja saját bejövő adatait HTTPS-kapcsolaton keresztül.

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Használhatom az Azure dev Spaces szolgáltatást olyan fürtön, amely nem kubenet, hanem CNI használ? 

Igen, használhatja az Azure dev Spaces szolgáltatást egy olyan AK-fürtön, amely CNI használ a hálózatkezeléshez. Használhatja például az Azure dev Spaces szolgáltatást egy AK-fürtön [meglévő Windows-tárolókkal][windows-containers], amelyek hálózati CNI használnak. A CNI és az Azure dev Spaces közötti hálózatkezeléssel kapcsolatos további információk [itt](configure-networking.md#using-azure-cni)érhetők el.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Használhatom az Azure dev Spaces szolgáltatást Windows-tárolókkal?

Jelenleg az Azure dev Spaces kizárólag Linux-hüvelyeken és-csomópontokon fut, de az Azure fejlesztői tárhelyeit egy AK-fürtön futtathatja [meglévő Windows-tárolók][windows-containers]használatával.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Használhatok Azure dev Spaces-et az AK-fürtökön, ha engedélyezve vannak az API Server által engedélyezett IP-címtartományok?

Igen, az Azure dev-szóközöket az AK-fürtökön használhatja az [API-kiszolgáló által engedélyezett IP-címtartományok][aks-auth-range] engedélyezésével. További információ az Azure dev Spaces szolgáltatással rendelkező, az API Server által engedélyezett IP-címtartományok használatával rendelkező AK-fürtök használatáról [itt](configure-networking.md#using-api-server-authorized-ip-ranges)található.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Használhatom az Azure dev Spaces-t az AK-fürtökön a fürtcsomópontok korlátozott kimenő forgalmával?

Igen, a megfelelő teljes tartománynevek engedélyezése után használhatja az Azure dev-szóközöket az AK-fürtökön a [korlátozott kimenő forgalommal rendelkező fürtcsomópontok esetében][aks-restrict-egress-traffic] . [Itt](configure-networking.md#ingress-and-egress-network-traffic-requirements)talál további információt arról, hogyan használhatók a korlátozott kimenő forgalommal rendelkező AK-fürtök az Azure dev Spaces szolgáltatással engedélyezett fürtcsomópontok esetében.

## <a name="can-i-use-azure-dev-spaces-on-kubernetes-rbac-enabled-aks-clusters"></a>Használhatom az Azure dev Spaces szolgáltatást a Kubernetes RBAC-kompatibilis AK-fürtökön?

Igen, a Kubernetes szerepköralapú hozzáférés-vezérlés (Kubernetes RBAC) segítségével vagy anélkül is használhatja az Azure dev-szóközöket az AK-fürtökön.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Mi történik, ha engedélyezem a bejövő forgalmat a Visual Studióban?

Ha a Visual studiót használja a projekt előkészítéséhez, lehetősége van engedélyezni a bejövő forgalmat a szolgáltatásban. A bejövő forgalom engedélyezése nyilvános végpontot hoz létre a szolgáltatás eléréséhez, ha az AK-fürtön fut, ami nem kötelező. Ha nem engedélyezi a bejövő forgalmat, a szolgáltatás csak az AK-fürtön belül érhető el.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Használhatok Pod felügyelt identitásokat az Azure dev Spaces szolgáltatásban?

Igen, a pod által [felügyelt identitások][aks-pod-managed-id] használhatók az AK-fürtökön az Azure dev Spaces használatával, de [további konfigurációs lépések][dev-spaces-pod-managed-id-steps] is szükségesek, miután engedélyezte az Azure dev Spaces szolgáltatást a fürtön a pod által felügyelt identitásokkal. Ha telepítette a pod által felügyelt identitásokat, és el szeretné távolítani, további részleteket az [eltávolítási megjegyzések][aks-pod-managed-id-uninstall]között találhat.

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>Használhatok az Azure dev Spaces szolgáltatást több szolgáltatással is egy alkalmazásban?

Igen, használhatja az Azure dev Spaces szolgáltatást egy olyan alkalmazásban, amelyben több szolgáltatás is található, de az egyes szolgáltatásait a gyökérkönyvtárában kell előkészítenie és futtatnia. Az Azure dev Spaces CLI, az Azure dev Spaces VS Code bővítmény és a Visual Studio Azure-fejlesztési számítási feladat elvárja, hogy a *azds. YAML* fájl a szolgáltatás gyökerében legyen a Futtatás és a hibakeresés érdekében. Tekintse meg a [Bike Sharing minta alkalmazást][bike-sharing] , amely egy adott alkalmazás több szolgáltatását is tartalmazza.

A Visual Studio Code-ban [egy különálló projekt is megnyitható egyetlen munkaterületen][vs-code-multi-root-workspaces] , és az Azure dev Spaces használatával külön is feloldhatók. Minden projektnek önállónak kell lennie, és fel kell készülnie az Azure dev Spaces szolgáltatásba.

A Visual Studióban lehetősége van .NET Core-megoldások konfigurálására az Azure dev Spaces használatával.

## <a name="can-i-use-azure-dev-spaces-with-a-service-mesh"></a>Használhatom az Azure dev Spaces szolgáltatást a Service Mesh segítségével?

Jelenleg nem használhatja az Azure dev Spaces szolgáltatást olyan rácsvonalak használatával, mint például a [Istio][istio] vagy a [Linkerd][linkerd]. Az Azure dev Spaces és a Service Mesh ugyanazon az AK-fürtön is futtatható, de az Azure dev Spaces és a Service Mesh szolgáltatás nem használható ugyanabban a névtérben.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[aks-supported-k8s]: ../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-pod-managed-id-steps]: troubleshooting.md#error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[istio]: https://istio.io/
[linkerd]: https://linkerd.io/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md