---
title: Hálózatkezelés konfigurálása az Azure dev Spaces szolgáltatáshoz különböző hálózati topológiákban
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Ismerteti az Azure dev Spaces Azure Kubernetes Servicesben való futtatásának hálózati követelményeit
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, CNI, kubenet, SDN, hálózat
ms.openlocfilehash: 09114ab13555cbf9ef42b37c86ffb76a8fe3ab3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91970338"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Hálózatkezelés konfigurálása az Azure dev Spaces szolgáltatáshoz különböző hálózati topológiákban

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Az Azure dev Spaces az alapértelmezett hálózati konfigurációval rendelkező Azure Kubernetes Service (ak) fürtökön fut. Ha módosítani szeretné az AK-fürt hálózati konfigurációját, például a fürt tűzfal mögötti üzembe helyezését, hálózati biztonsági csoportok használatával vagy hálózati házirendek használatával, további szempontokat kell figyelembe vennie az Azure dev Spaces futtatásához.

![Virtuális hálózati konfiguráció](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Virtuális hálózat vagy alhálózati konfigurációk

Az AK-fürt eltérő virtuális hálózattal vagy alhálózat-konfigurációval rendelkezhet, hogy korlátozza a bejövő vagy kimenő forgalmat az AK-fürt számára. Előfordulhat például, hogy a fürt tűzfal mögött található, például a Azure Firewall, vagy hálózati biztonsági csoportokat vagy egyéni szerepköröket használ a hálózati forgalom korlátozásához. Az [Azure dev Spaces minta adattárában a githubon][sample-repo]talál egy példát hálózati konfigurációra.

Az Azure dev Spaces szolgáltatásban a *bejövő és a kimenő* hálózati forgalomra, valamint a *csak* forgalomra vonatkozó követelmények vonatkoznak. Ha az Azure dev Spaces szolgáltatást egy AK-fürtön használja, és olyan virtuális hálózatot vagy alhálózati konfigurációt használ, amely korlátozza az AK-fürt forgalmát, csak a következő bejövő adatokat és a bejövő forgalomra vonatkozó követelményeket kell követnie ahhoz, hogy az Azure dev Spaces megfelelően működjön.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Bejövő és kimenő hálózati forgalomra vonatkozó követelmények

Az Azure dev Spaces szolgáltatásnak a következő teljes tartománynevek felé irányuló bejövő és kimenő forgalomra van szüksége:

| FQDN                       | Port       | Használat      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Docker-rendszerképek lekérése az Azure dev Spaces szolgáltatásban |
| gcr.io                     | HTTPS: 443 | Helm-lemezképek lekérése az Azure dev Spaces szolgáltatásban |
| storage.googleapis.com     | HTTPS: 443 | Helm-lemezképek lekérése az Azure dev Spaces szolgáltatásban |

Frissítse a tűzfalat vagy a biztonsági konfigurációt, hogy engedélyezze a fenti teljes tartománynevek és az [Azure dev Spaces infrastrukturális szolgáltatások][service-tags]hálózati forgalmát. Ha például tűzfalat használ a hálózat biztonságossá tételéhez, a fenti FQDN-ket hozzá kell adni a tűzfal alkalmazási szabályához, és az Azure dev Spaces szolgáltatás címkét is hozzá kell [adni a tűzfalhoz][firewall-service-tags]. A tűzfal mindkét frissítése szükséges ahhoz, hogy engedélyezzék ezen tartományokból érkező és onnan érkező forgalmat.

### <a name="ingress-only-network-traffic-requirements"></a>Csak a hálózati forgalomra vonatkozó követelmények beáramlása

Az Azure dev Spaces lehetővé teszi a Kubernetes, valamint a szolgáltatásokhoz való nyilvános hozzáférést a saját FQDN használatával. Mindkét funkció működéséhez frissítse a tűzfalat vagy a hálózati konfigurációt, hogy lehetővé tegye a nyilvános behatolást a fürtön lévő Azure dev Spaces bemenő vezérlő külső IP-címére. Azt is megteheti, hogy létrehoz egy [belső][aks-internal-lb] terheléselosztó szolgáltatást, és felvesz egy NAT-szabályt a tűzfalon a TŰZFAL nyilvános IP-címének lefordításához a belső terheléselosztó IP-címére. A [traefik][traefik-ingress] és az [NGINX][nginx-ingress] használatával is létrehozhat egy egyéni bejövő vezérlőt.

## <a name="aks-cluster-network-requirements"></a>AK-fürt hálózati követelményei

Az AK lehetővé teszi, hogy [hálózati házirendekkel][aks-network-policies] vezérelje a bejövő és kimenő adatforgalmat a fürtben lévő hüvelyek között, valamint a kimenő forgalmat egy Pod-ból. Az Azure dev Spaces szolgáltatásban a *bejövő és a kimenő* hálózati forgalomra, valamint a *csak* forgalomra vonatkozó követelmények vonatkoznak. Ha az Azure dev Spaces-t egy AK-os hálózati házirendekkel rendelkező AK-fürtön használja, akkor az Azure dev Spaces megfelelő működéséhez az alábbi bejövő adatokat és a kimenő forgalomra vonatkozó követelményeket is be kell tartania.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Bejövő és kimenő hálózati forgalomra vonatkozó követelmények

Az Azure dev Spaces lehetővé teszi, hogy a hibakereséshez közvetlenül kommunikáljon a fürt fejlesztői területein található Pod-mel. Ennek a funkciónak a működéséhez adjon hozzá egy hálózati házirendet, amely lehetővé teszi a bejövő és kimenő kommunikációt az Azure dev Spaces infrastruktúrájának IP-címeire, amelyek [régiónként eltérőek][service-tags].

### <a name="ingress-only-network-traffic-requirements"></a>Csak a hálózati forgalomra vonatkozó követelmények beáramlása

Az Azure dev Spaces lehetővé teszi a hüvelyek közötti útválasztást a névterek között. Például az Azure dev Spaces szolgáltatásban engedélyezett névterek szülő/gyermek kapcsolattal rendelkezhetnek, ami lehetővé teszi a hálózati forgalom átirányítását a hüvelyek között a szülő és a gyermek névterek között. Az Azure dev Spaces a saját FQDN használatával is elérhetővé teszi a szolgáltatási végpontokat. A szolgáltatások megadásának különböző módjai, valamint a névtér szintű útválasztás hatása a [különböző végponti beállítások használata][endpoint-options]című témakörben olvasható.

## <a name="using-azure-cni"></a>Az Azure CNI használata

Alapértelmezés szerint az AK-fürtök úgy vannak konfigurálva, hogy a [kubenet][aks-kubenet] használják a hálózatkezeléshez, ami az Azure dev Spaces szolgáltatással működik. Azt is megteheti, hogy az AK-fürtöt az [Azure Container Network Interface (CNI)][aks-cni]használatára konfigurálja. Ha az Azure dev Spaces-t az AK-fürtön lévő Azure CNI szeretné használni, engedélyezze a virtuális hálózat és az alhálózati címtartomány akár 10 magánhálózati IP-címet az Azure dev Spaces által üzembe helyezett hüvelyek számára. A magánhálózati IP-címek engedélyezésével kapcsolatos további részletek az [AK Azure CNI dokumentációjában][aks-cni-ip-planning]találhatók.

## <a name="using-api-server-authorized-ip-ranges"></a>Az API-kiszolgáló által jóváhagyott IP-címtartományok használata

Az AK-fürtök lehetővé teszik olyan további biztonsági beállítások konfigurálását, amelyek korlátozzák, hogy az IP-címek hogyan kezelhetik a fürtöket, például egyéni virtuális hálózatok használatával vagy [az API-kiszolgálóhoz engedélyezett IP-címtartományok használatával történő hozzáférés biztosításával][aks-ip-auth-ranges]. Ha a fürt [létrehozása][aks-ip-auth-range-create] során további biztonságot kíván használni az Azure dev Spaces szolgáltatásban, akkor a [régión alapuló további tartományokat][service-tags]is engedélyeznie kell. Egy meglévő fürtöt is [frissíthet][aks-ip-auth-range-update] , hogy engedélyezze ezeket a további tartományokat. Emellett engedélyeznie kell az AK-fürthöz csatlakozó fejlesztői gépek IP-címét, hogy hibakeresést végezzenek az API-kiszolgálóhoz való kapcsolódáshoz.

## <a name="using-aks-private-clusters"></a>AK-beli privát fürtök használata

Ebben az esetben az Azure fejlesztői tárhelyek nem támogatottak az AK-beli [privát fürtökön][aks-private-clusters].

## <a name="using-different-endpoint-options"></a>Különböző végponti beállítások használata

Az Azure dev Spaces lehetővé teszi, hogy az AK-on futó szolgáltatásaihoz végpontokat tegyenek elérhetővé. Az Azure dev-helyek fürtön való engedélyezésekor a következő beállításokkal konfigurálhatja a fürt végpontjának típusát:

* Egy *nyilvános* végpont, amely az alapértelmezett, üzembe helyez egy nyilvános IP-címmel rendelkező bejövő vezérlőt. A nyilvános IP-cím regisztrálva van a fürt DNS-jében, amely lehetővé teszi a szolgáltatásokhoz való nyilvános hozzáférést egy URL-cím használatával. Ez az URL-cím a használatával tekinthető meg `azds list-uris` .
* Egy *privát* végpont egy magánhálózati IP-címmel rendelkező beléptetési vezérlőt helyez üzembe. Magánhálózati IP-címmel a fürthöz tartozó terheléselosztó csak a fürt virtuális hálózatán belül érhető el. A terheléselosztó magánhálózati IP-címe regisztrálva van a fürt DNS-jében, hogy a fürt virtuális hálózatán belüli szolgáltatások elérhetők legyenek egy URL-cím használatával. Ez az URL-cím a használatával tekinthető meg `azds list-uris` .
* Ha a végpont beállítás *egyikét sem* állítja be, a rendszer nem helyezi üzembe a bejövő vezérlőket. A beléptetési vezérlő üzembe helyezése nélkül az [Azure dev Spaces útválasztási képességei][dev-spaces-routing] nem fognak működni. Igény szerint a [traefik][traefik-ingress] vagy [NGINX][nginx-ingress]használatával is megvalósíthatja saját bejövő vezérlési megoldását, amely lehetővé teszi, hogy az útválasztási funkciók újra működjenek.

A végpont beállításának konfigurálásához használja az *-e* vagy a *--Endpoint* beállítást, ha engedélyezi az Azure dev Spaces szolgáltatást a fürtön. Például:

> [!NOTE]
> A végpont beállításhoz az Azure CLI 2.2.0 vagy újabb verziójának kell futnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>Ügyfélkövetelmények

Az Azure dev Spaces ügyféloldali eszközöket használ, például az Azure dev Spaces CLI-bővítményt, a Visual Studio Code-bővítményt és a Visual Studio-bővítményt, hogy a hibakereséshez kommunikáljon az AK-fürttel. Az Azure dev Spaces ügyféloldali eszközeinek használatához engedélyezze a fejlesztési gépekről az [Azure dev Spaces infrastruktúrába][dev-spaces-allow-infrastructure]irányuló forgalmat. Ha az [API-kiszolgáló által engedélyezett IP-tartományokat][auth-range-section]használja, engedélyeznie kell az AK-fürthöz csatlakozó fejlesztői gépek IP-címét is, hogy hibakeresést VÉGEZZEN az API-kiszolgálóhoz való kapcsolódáshoz.

## <a name="next-steps"></a>Következő lépések

További információ az Azure dev Spaces működéséről.

> [!div class="nextstepaction"]
> [Az Azure Dev Spaces működése](how-dev-spaces-works.md)

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-allow-infrastructure]: #virtual-network-or-subnet-configurations
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[firewall-service-tags]: ../firewall/service-tags.md
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[service-tags]: ../virtual-network/service-tags-overview.md#available-service-tags