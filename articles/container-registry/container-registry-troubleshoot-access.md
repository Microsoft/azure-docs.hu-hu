---
title: A beállításjegyzék hálózati problémáinak elhárítása
description: Az Azure Container Registry virtuális hálózatban vagy tűzfal mögötti elérésével kapcsolatos gyakori problémák tünetei, okai és megoldása
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 0fdedf109703eb443904989d2c0b2d75a6ba5bb1
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481225"
---
# <a name="troubleshoot-network-issues-with-registry"></a>A beállításjegyzék hálózati problémáinak elhárítása

Ez a cikk segítséget nyújt az Azure Container Registry virtuális hálózaton vagy tűzfal vagy proxykiszolgáló mögötti elérésekor felmerülő problémák elhárításában. 

## <a name="symptoms"></a>Hibajelenségek

A következők közül egyet vagy többet tartalmazhat:

* A rendszerképek leküldése vagy leküldése nem sikerült, és hibaüzenetet kap `dial tcp: lookup myregistry.azurecr.io`
* A rendszerképek leküldése vagy leküldése nem sikerült, és hibaüzenetet kap `Client.Timeout exceeded while awaiting headers`
* A rendszerképek leküldése vagy leküldése nem sikerült, és Azure CLI-hibaüzenetet kap `Could not connect to the registry login server`
* Nem sikerült lekért rendszerképek a regisztrációs adatbázisból Azure Kubernetes Service másik Azure-szolgáltatásba
* Nem lehet hozzáférni egy HTTPS-proxy mögötti beállításjegyzékhez, és hibaüzenetet `Error response from daemon: login attempt failed with status: 403 Forbidden` kap, vagy `Error response from daemon: Get <registry>: proxyconnect tcp: EOF Login failed`
* Nem lehet konfigurálni a virtuális hálózat beállításait, és hibaüzenetet kap `Failed to save firewall and virtual network settings for container registry`
* Nem lehet elérni vagy megtekinteni a beállításjegyzék-beállításokat Azure Portal a regisztrációs adatbázist az Azure CLI használatával
* Nem lehet hozzáadni vagy módosítani a virtuális hálózat beállításait vagy a nyilvános hozzáférési szabályokat
* ACR-feladatok nem tud rendszerképeket leküldéses vagy leküldéses
* Azure Security Center a regisztrációs adatbázisban nem lehet képeket beolvasni, vagy a vizsgálati eredmények nem jelennek meg a Azure Security Center
* Amikor privát végponttal konfigurált beállításjegyzékhez próbál hozzáférni, `host is not reachable` hibaüzenet jelenik meg.

## <a name="causes"></a>Okok

* Az ügyfél tűzfala vagy proxyja megakadályozza a hozzáférést – [megoldás](#configure-client-firewall-access)
* A beállításjegyzék nyilvános hálózati hozzáférési szabályai megakadályozzák a hozzáférést – [megoldás](#configure-public-access-to-registry)
* A virtuális hálózati konfiguráció megakadályozza a hozzáférést – [megoldás](#configure-vnet-access)
* Megpróbál integrálni Azure Security Center azure-szolgáltatásokat egy olyan beállításjegyzékbe, amely privát végponttal, szolgáltatásvégponttal vagy nyilvános IP-hozzáférési szabályokkal rendelkezik – [megoldás](#configure-service-access)

## <a name="further-diagnosis"></a>További diagnosztika 

Futtassa [az az acr check-health](/cli/azure/acr#az-acr-check-health) parancsot a beállításjegyzék-környezet állapotáról és opcionálisan a céljegyzékhez való hozzáféréshez. Diagnosztizálhat például bizonyos hálózati kapcsolati vagy konfigurációs problémákat. 

A [parancsokkal kapcsolatos példákért](container-registry-check-health.md) tekintse meg az Azure Container Registry állapotának ellenőrzése. Ha a rendszer hibákat jelent, tekintse át a [hibareferenciát](container-registry-health-error-reference.md) és az alábbi szakaszokat az ajánlott megoldásokért.

Ha problémákat tapasztal egy integrált beállításjegyzék Azure Kubernetes Service-kiszolgálóval való használata során, futtassa [az az aks check-acr](/cli/azure/aks#az_aks_check_acr) parancsot annak ellenőrzéséhez, hogy az AKS-fürt el tudja-e érni a beállításjegyzéket.

> [!NOTE]
> Bizonyos hálózati kapcsolati problémák akkor is jelentkezhetnek, ha problémák merülnek fel a beállításjegyzék hitelesítésével vagy engedélyezésével kapcsolatban. Lásd: [Beállításjegyzékbeli bejelentkezés hibaelhárítása.](container-registry-troubleshoot-login.md)

## <a name="potential-solutions"></a>Lehetséges megoldások

### <a name="configure-client-firewall-access"></a>Ügyfél tűzfal-hozzáférésének konfigurálása

A beállításjegyzék ügyfél tűzfal vagy proxykiszolgáló mögötti eléréséhez konfigurálja a tűzfalszabályokat a beállításjegyzék nyilvános REST- és adatvégpontjainak eléréséhez. Ha engedélyezve vannak a dedikált [adatvégpontok,](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) a hozzáféréshez szabályokra van szükség:

* REST-végpont: `<registryname>.azurecr.io`
* Adatvégpont(k): `<registry-name>.<region>.data.azurecr.io`

Georeplikált beállításjegyzék esetén konfigurálja a hozzáférést az egyes regionális replikák adatvégpontjaihoz.

HTTPS-proxy mögött győződjön meg arról, hogy a Docker-ügyfél és a Docker-démon is konfigurálva van a proxyviselkedéshez. Ha módosítja a Docker-démon proxybeállítását, mindenképpen indítsa újra a démont. 

A ContainerRegistryLoginEvents táblában található beállításjegyzék-erőforrásnaplók segíthetnek diagnosztizálni a blokkolt csatlakozási kísérletet.

Kapcsolódó hivatkozások:

* [Szabályok konfigurálása tűzfal mögötti Azure Container Registry eléréséhez](container-registry-firewall-access-rules.md)
* [HTTP/HTTPS-proxy konfigurálása](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Georeplikáció Azure Container Registry](container-registry-geo-replication.md)
* [Azure Container Registry diagnosztikai kiértékelési és naplózási naplókhoz](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>A beállításjegyzék nyilvános hozzáférésének konfigurálása

Ha a beállításjegyzékhez az interneten keresztül fér hozzá, ellenőrizze, hogy a beállításjegyzék engedélyezi-e a nyilvános hálózati hozzáférést az ügyfélről. Alapértelmezés szerint az Azure Container Registry minden hálózatról engedélyezi a hozzáférést a nyilvános beállításjegyzék végpontjaihoz. A beállításjegyzék korlátozhatja a hozzáférést a kiválasztott hálózatokra vagy kiválasztott IP-címekre. 

Ha a beállításjegyzék szolgáltatásvégponttal konfigurált virtuális hálózathoz van konfigurálva, a nyilvános hálózati hozzáférés letiltása a szolgáltatásvégponton keresztüli hozzáférést is letiltja. Ha a beállításjegyzék konfigurálva van egy virtuális hálózathoz Private Link, az IP-hálózati szabályok nem vonatkoznak a beállításjegyzék privát végpontjaira. 

Kapcsolódó hivatkozások:

* [Nyilvános IP-hálózati szabályok konfigurálása](container-registry-access-selected-networks.md)
* [Privát csatlakozás Azure Container Registryhez a Azure Private Link](container-registry-private-link.md)
* [Tároló-beállításjegyzékhez való hozzáférés korlátozása egy Azure-beli virtuális hálózat szolgáltatásvégpontjának használatával](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>Virtuális hálózati hozzáférés konfigurálása

Győződjön meg arról, hogy a virtuális hálózat privát végponttal van konfigurálva Private Link szolgáltatásvégponthoz (előzetes verzió). A Azure Bastion végpont jelenleg nem támogatott.

Ha privát végpont van konfigurálva, győződjön meg arról, hogy a DNS feloldja a beállításjegyzék nyilvános teljes tartománynevét *(például* myregistry.azurecr.io a beállításjegyzék magánhálózati IP-címére. A DNS-kereséshez használjon hálózati segédprogramot, például a `dig` vagy `nslookup` a segédprogramot. Győződjön meg [arról, hogy a DNS-rekordok](container-registry-private-link.md#dns-configuration-options) konfigurálva vannak a beállításjegyzék teljes tartománynevéhez és az egyes adatvégpont teljes tartománynevéhez.

Tekintse át az NSG-szabályokat és szolgáltatáscímkéket, amelyek a hálózat más erőforrásairól a regisztrációs adatbázisra korlátozzák a forgalmat. 

Ha a beállításjegyzék szolgáltatásvégpontja konfigurálva van, ellenőrizze, hogy hozzá van-e adva egy hálózati szabály a beállításjegyzékhez, amely engedélyezi a hozzáférést az adott hálózati alhálózatról. A szolgáltatásvégpont csak a hálózaton lévő virtuális gépekről és AKS-fürtökről való hozzáférést támogatja.

Ha egy másik Azure-előfizetésben lévő virtuális hálózat használatával szeretné korlátozni a regisztrációs adatbázishoz való hozzáférést, regisztrálja az `Microsoft.ContainerRegistry` erőforrás-szolgáltatót az előfizetésben. [Regisztrálja az](../azure-resource-manager/management/resource-providers-and-types.md) erőforrás-szolgáltatót a Azure Container Registry a Azure Portal, az Azure CLI vagy más Azure-eszközök használatával.

Ha Azure Firewall vagy hasonló megoldás van konfigurálva a hálózaton, ellenőrizze, hogy a más erőforrások, például az AKS-fürtökről származó bejövő forgalom számára engedélyezve van-e a beállításjegyzék végpontjainak elérése.

Kapcsolódó hivatkozások:

* [Privát csatlakozás Azure Container Registryhez a Azure Private Link](container-registry-private-link.md)
* [Az Azure privát végpont kapcsolati problémáinak hibaelhárítása](../private-link/troubleshoot-private-endpoint-connectivity.md)
* [Tároló-beállításjegyzékhez való hozzáférés korlátozása egy Azure-beli virtuális hálózat szolgáltatásvégpontjának használatával](container-registry-vnet.md)
* [Az AKS-fürtökhöz szükséges kimenő hálózati szabályok és teljes tartománycsoportok](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: DNS-feloldás hibakeresése](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [Virtuális hálózati szolgáltatáscímkék](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>Szolgáltatás-hozzáférés konfigurálása

A tároló-beállításjegyzékek hálózati korlátozásokkal való elérése jelenleg nem engedélyezett számos Azure-szolgáltatásban:

* Azure Security Center rendszerkép biztonsági réseinek vizsgálata nem végrehajtásához olyan beállításjegyzékben, amely korlátozza a hozzáférést a privát végpontok, a kiválasztott alhálózatok vagy IP-címek számára. [](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) 
* Bizonyos Azure-szolgáltatások erőforrásai nem tudnak hálózati korlátozásokkal hozzáférni a tároló-beállításjegyzékhez, beleértve a Azure App Service és Azure Container Instances.

Ha ezeknek az Azure-szolgáltatásoknak a tároló-beállításjegyzékével való elérésére vagy integrálására van szükség, távolítsa el a hálózati korlátozást. Eltávolíthatja például a beállításjegyzék privát végpontját, vagy eltávolíthatja vagy módosíthatja a beállításjegyzék nyilvános hozzáférési szabályait.

2021 januártól kezdve konfigurálhat egy hálózatra korlátozott beállításjegyzéket, hogy engedélyezze a hozzáférést [bizonyos](allow-access-trusted-services.md) megbízható szolgáltatásokból.

Kapcsolódó hivatkozások:

* [Azure Container Registry képkeresés a Security Center](../security-center/defender-for-container-registries-introduction.md)
* Visszajelzés [küldése](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)
* [A megbízható szolgáltatások biztonságos hozzáférésének engedélyezése a hálózatra korlátozott tárolójegyzékhez](allow-access-trusted-services.md)


## <a name="advanced-troubleshooting"></a>Speciális hibaelhárítás

Ha [az erőforrásnaplók gyűjteménye](container-registry-diagnostics-audit-logs.md) engedélyezve van a beállításjegyzékben, tekintse át a ContainterRegistryLoginEvents naplót. Ez a napló tárolja a hitelesítési eseményeket és állapotokat, beleértve a bejövő identitást és IP-címet. A beállításjegyzék-hitelesítési [hibákat a naplóban kell lekérdezni.](container-registry-diagnostics-audit-logs.md#registry-authentication-failures) 

Kapcsolódó hivatkozások:

* [Diagnosztikai kiértékelési és naplózási naplók](container-registry-diagnostics-audit-logs.md)
* [Container Registry – gyakori kérdések](container-registry-faq.md)
* [Az Azure biztonsági alapkonfigurációja Azure Container Registry](security-baseline.md)
* [Az Azure Container Registry ajánlott eljárásai](container-registry-best-practices.md)

## <a name="next-steps"></a>Következő lépések

Ha itt nem oldja meg a problémát, tekintse meg az alábbi lehetőségeket.

* A beállításjegyzék egyéb hibaelhárítási témakörei a következők:
  * [A beállításjegyzékbe való bejelentkezés hibaelhárítása](container-registry-troubleshoot-login.md) 
  * [A beállításjegyzék teljesítményével kapcsolatos problémák elhárítása](container-registry-troubleshoot-performance.md)
* [Közösségi támogatási](https://azure.microsoft.com/support/community/) lehetőségek
* [Microsoft Q&A](/answers/products/)
* [Támogatási jegy megnyitása](https://azure.microsoft.com/support/create-ticket/)