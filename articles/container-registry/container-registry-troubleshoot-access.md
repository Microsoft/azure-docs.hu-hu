---
title: A beállításjegyzék hálózati problémáinak elhárítása
description: Az Azure Container Registry virtuális hálózatban való elérésekor vagy tűzfal mögötti gyakori problémák tünetei, okai és megoldása
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: cf2f308f782ac7d6011c98afd181b194f2b3e09f
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525076"
---
# <a name="troubleshoot-network-issues-with-registry"></a>A beállításjegyzék hálózati problémáinak elhárítása

Ebből a cikkből megtudhatja, milyen problémák merülhetnek fel az Azure Container Registry virtuális hálózatban való elérésekor vagy a tűzfal mögött. 

## <a name="symptoms"></a>Hibajelenségek

A következők közül egyet vagy többet is tartalmazhat:

* Nem lehet leküldeni vagy lekérni a képeket, és hibaüzenetet kap `dial tcp: lookup myregistry.azurecr.io`
* Nem lehet leküldeni vagy lekérni a képeket, és Azure CLI-hibát kap `Could not connect to the registry login server`
* Nem sikerült lekérni a lemezképeket a beállításjegyzékből az Azure Kubernetes Service-be vagy egy másik Azure-szolgáltatásba
* Nem lehet hozzáférni egy, a HTTPS-proxy mögötti beállításjegyzékhez, és hibaüzenetet kap `Error response from daemon: login attempt failed with status: 403 Forbidden`
* Nem lehet konfigurálni a virtuális hálózati beállításokat, és hibaüzenetet kap `Failed to save firewall and virtual network settings for container registry`
* Nem lehet hozzáférni vagy megtekinteni a beállításjegyzék beállításait Azure Portal vagy a beállításjegyzéket az Azure CLI használatával felügyelni
* Nem lehet virtuális hálózati beállításokat vagy nyilvános hozzáférési szabályokat felvenni vagy módosítani
* Az ACR-feladatok nem képesek leküldeni vagy lekérni a képeket
* Azure Security Center nem tud képeket beolvasni a beállításjegyzékben, vagy a vizsgálati eredmények nem jelennek meg a Azure Security Center

## <a name="causes"></a>Okok

* Az ügyfél tűzfala vagy proxyja megakadályozza A hozzáférést – [megoldás](#configure-client-firewall-access)
* A beállításjegyzék nyilvános hálózati hozzáférési szabályai akadályozzák a hozzáférést – [megoldás](#configure-public-access-to-registry)
* A virtuális hálózati konfiguráció megakadályozza a hozzáférést – [megoldás](#configure-vnet-access)
* A Azure Security Center vagy bizonyos egyéb Azure-szolgáltatásokat olyan beállításjegyzékkel próbálja meg integrálni, amely rendelkezik saját végponttal, szolgáltatási végponttal vagy nyilvános IP-hozzáférési szabályokkal – [megoldás](#configure-service-access)

## <a name="further-diagnosis"></a>További diagnosztika 

Futtassa az az [ACR renézz-Health](/cli/azure/acr#az-acr-check-health) parancsot, hogy további információkat kapjon a beállításjegyzék-környezet állapotáról, és igény szerint hozzáférhessen a cél beállításjegyzékhez. Például bizonyos hálózati kapcsolatok vagy konfigurációs problémák diagnosztizálása. 

A példákat az [Azure Container Registry állapotának ellenőrzését](container-registry-check-health.md) ismertető szakaszban találja. Ha a rendszer hibákat jelez, tekintse át a hibákra vonatkozó [referenciát](container-registry-health-error-reference.md) és a javasolt megoldásokat a következő fejezetekben.

Ha problémákat tapasztal az Azure Kubernetes szolgáltatás beállításjegyzék-wih, futtassa az az [AK-ellenőrzés – ACR](/cli/azure/aks#az_aks_check_acr) parancsot annak ellenőrzéséhez, hogy a beállításjegyzék elérhető-e az AK-fürtről.

> [!NOTE]
> Bizonyos hálózati kapcsolati tünetek akkor is előfordulhatnak, ha a beállításjegyzék-hitelesítéssel vagy-engedélyezéssel kapcsolatos probléma merül fel. Lásd: a [beállításjegyzék bejelentkezési hibáinak megoldása](container-registry-troubleshoot-login.md).

## <a name="potential-solutions"></a>Lehetséges megoldások

### <a name="configure-client-firewall-access"></a>Az ügyfél tűzfal-hozzáférésének konfigurálása

Ha egy beállításjegyzéket szeretne elérni egy ügyfél-tűzfal vagy proxykiszolgáló mögött, konfigurálja a tűzfalszabályokat a beállításjegyzék nyilvános REST és adatvégpontok eléréséhez. Ha a [dedikált adatvégpontok](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) engedélyezve vannak, az eléréséhez szabályokra van szükség:

* REST-végpont: `<registryname>.azurecr.io`
* Adatvégpont (ok): `<registry-name>.<region>.data.azurecr.io`

Földrajzilag replikált beállításjegyzék esetében konfigurálja az adatvégponthoz való hozzáférést az egyes regionális replikák esetében.

HTTPS-proxy mögött ellenőrizze, hogy a Docker-ügyfél és a Docker-démon is konfigurálva van-e a proxy működéséhez.

A ContainerRegistryLoginEvents tábla beállításjegyzék-erőforrás-naplófájljai segíthetnek a blokkolt megkísérelt kapcsolatok diagnosztizálásában.

Kapcsolódó hivatkozások:

* [Szabályok konfigurálása az Azure Container Registry tűzfal mögötti eléréséhez](container-registry-firewall-access-rules.md)
* [HTTP/HTTPS-proxy konfigurálása](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Geo-replicationin Azure Container Registry](container-registry-geo-replication.md)
* [Azure Container Registry naplók a diagnosztika kiértékeléséhez és a naplózáshoz](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>Nyilvános hozzáférés konfigurálása a beállításjegyzékhez

Ha az interneten keresztül éri el a beállításjegyzéket, ellenőrizze, hogy a beállításjegyzék lehetővé teszi-e az ügyfél nyilvános hálózati hozzáférését. Alapértelmezés szerint az Azure Container Registry lehetővé teszi, hogy az összes hálózatról hozzáférjen a nyilvános beállításjegyzékbeli végpontokhoz. A beállításjegyzék a kiválasztott hálózatokhoz vagy a kiválasztott IP-címekhez is korlátozhatja a hozzáférést. 

Ha a beállításjegyzék egy szolgáltatás-végponttal rendelkező virtuális hálózathoz van konfigurálva, a nyilvános hálózati hozzáférés letiltása szintén letiltja a hozzáférést a szolgáltatási végponton keresztül. Ha a beállításjegyzék egy privát kapcsolattal rendelkező virtuális hálózatra van konfigurálva, az IP-hálózati szabályok nem vonatkoznak a beállításjegyzék saját végpontokra. 

Kapcsolódó hivatkozások:

* [Nyilvános IP-hálózati szabályok konfigurálása](container-registry-access-selected-networks.md)
* [Privát csatlakozás Azure Container registryhez az Azure Private link használatával](container-registry-private-link.md)
* [Tároló-beállításjegyzékhez való hozzáférés korlátozása egy Azure-beli virtuális hálózat szolgáltatási végpontjának használatával](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>VNet-hozzáférés konfigurálása

Győződjön meg arról, hogy a virtuális hálózat magánhálózati vagy szolgáltatási végpontként (előzetes verzió) konfigurált privát végponttal van konfigurálva. Jelenleg egy Azure Bastion-végpont nem támogatott.

Tekintse át a hálózat más erőforrásairól a beállításjegyzékbe irányuló forgalom korlátozásához használt NSG-szabályokat és-szolgáltatási címkéket. 

Ha konfigurálva van egy szolgáltatási végpont a beállításjegyzékhez, győződjön meg arról, hogy egy hálózati szabály van hozzáadva a beállításjegyzékhez, amely engedélyezi az adott hálózati alhálózat elérését. A szolgáltatási végpont csak a hálózatban lévő virtuális gépek és AK-fürtök elérését támogatja.

Ha egy másik Azure-előfizetésben lévő virtuális hálózat használatával szeretné korlátozni a beállításjegyzék-hozzáférést, ügyeljen arra, hogy regisztrálja az `Microsoft.ContainerRegistry` erőforrás-szolgáltatót az előfizetésben. [Regisztrálja Azure Container Registry erőforrás-szolgáltatóját](../azure-resource-manager/management/resource-providers-and-types.md) a Azure Portal, az Azure CLI vagy más Azure-eszközök használatával.

Ha Azure Firewall vagy hasonló megoldás van konfigurálva a hálózaton, ellenőrizze, hogy a kimenő forgalom más erőforrásokból, például egy AK-fürtből van-e engedélyezve a beállításjegyzékbeli végpontok eléréséhez.

Ha egy privát végpont konfigurálva van, ellenőrizze, hogy a DNS feloldja-e a beállításjegyzék nyilvános FQDN-jét, például a *myregistry.azurecr.IO* a beállításjegyzék magánhálózati IP-címére. Használjon olyan hálózati segédprogramot, mint a `dig` vagy `nslookup` a DNS-keresés.

Kapcsolódó hivatkozások:

* [Privát csatlakozás Azure Container registryhez az Azure Private link használatával](container-registry-private-link.md)
* [Tároló-beállításjegyzékhez való hozzáférés korlátozása egy Azure-beli virtuális hálózat szolgáltatási végpontjának használatával](container-registry-vnet.md)
* [Szükséges kimenő hálózati szabályok és teljes tartománynevek az AK-fürtökhöz](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: DNS-feloldás hibakeresése](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [Virtuális hálózati szolgáltatás címkéi](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>Szolgáltatás-hozzáférés konfigurálása

Jelenleg egy hálózati korlátozásokkal rendelkező tároló-beállításjegyzékhez való hozzáférés nem engedélyezett több Azure-szolgáltatásból:

* Azure Security Center nem tudja végrehajtani a [képbiztonsági rések vizsgálatát](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) egy olyan beállításjegyzékben, amely korlátozza a privát végpontokhoz, a kiválasztott alhálózatokhoz vagy IP-címekhez való hozzáférést. 
* Bizonyos Azure-szolgáltatások erőforrásai nem férnek hozzá hálózati korlátozásokkal rendelkező tároló-beállításjegyzékhez, beleértve a Azure App Service és a Azure Container Instances.

Ha az Azure-szolgáltatások hozzáférése vagy integrálása szükséges a tároló beállításjegyzékével, távolítsa el a hálózati korlátozást. Távolítsa el például a beállításjegyzék saját végpontját, vagy távolítsa el vagy módosítsa a beállításjegyzék nyilvános hozzáférési szabályait.

Január 2021-től kezdődően a hálózatra korlátozott beállításjegyzék konfigurálható úgy, hogy [engedélyezze a hozzáférést](allow-access-trusted-services.md) a megbízható szolgáltatások közül.

Kapcsolódó hivatkozások:

* [Security Center Azure Container Registry képvizsgálat](../security-center/defender-for-container-registries-introduction.md)
* [Visszajelzés küldése](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)
* [A megbízható szolgáltatások biztonságos elérésének engedélyezése a korlátozott hálózati tárolók beállításjegyzékében](allow-access-trusted-services.md)


## <a name="advanced-troubleshooting"></a>Speciális hibaelhárítás

Ha a beállításjegyzékben engedélyezve van az [erőforrás-naplók gyűjtése](container-registry-diagnostics-audit-logs.md) , tekintse át a ContainterRegistryLoginEvents-naplót. Ez a napló a hitelesítési eseményeket és az állapotot tárolja, beleértve a bejövő identitást és az IP-címet is. A [beállításjegyzék-hitelesítési hibák](container-registry-diagnostics-audit-logs.md#registry-authentication-failures)naplójának lekérdezése. 

Kapcsolódó hivatkozások:

* [Naplók a diagnosztika kiértékeléséhez és a naplózáshoz](container-registry-diagnostics-audit-logs.md)
* [Container Registry – gyakori kérdések](container-registry-faq.md)
* [Azure Container Registry Azure biztonsági alapterve](security-baseline.md)
* [Az Azure Container Registry ajánlott eljárásai](container-registry-best-practices.md)

## <a name="next-steps"></a>Következő lépések

Ha itt nem oldja meg a problémát, tekintse meg a következő beállításokat.

* A beállításjegyzék egyéb hibaelhárítási témakörei a következők:
  * [Beállításjegyzékbeli bejelentkezés – problémamegoldás](container-registry-troubleshoot-login.md) 
  * [Beállításjegyzékbeli teljesítmény – problémamegoldás](container-registry-troubleshoot-performance.md)
* [Közösségi támogatási](https://azure.microsoft.com/support/community/) lehetőségek
* [Microsoft Q&A](https://docs.microsoft.com/answers/products/)
* [Támogatási jegy megnyitása](https://azure.microsoft.com/support/create-ticket/)
