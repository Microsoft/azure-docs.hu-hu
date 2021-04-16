---
title: Az Azure API Management használata belső virtuális hálózatokkal
titleSuffix: Azure API Management
description: Megtudhatja, hogyan állíthatja be és konfigurálhatja az Azure API Management egy belső virtuális hálózaton
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: how-to
ms.date: 04/12/2021
ms.author: apimpm
ms.openlocfilehash: 4298b291e5d183c31d30a548751599aeb3746c47
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534609"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Az Azure API Management használata belső virtuális hálózattal
Az Azure Virtual Networks segítségével az Azure API Management kezelheti az interneten nem elérhető API-kat. A kapcsolat létrehozásához számos VPN-technológia áll rendelkezésre. API Management két fő módban helyezhetők üzembe egy virtuális hálózaton belül:
* Külső
* Belső

Amikor API Management virtuális hálózati módban helyez üzembe, az összes szolgáltatásvégpont (a proxyátjáró, a fejlesztői portál, a közvetlen felügyelet és a Git) csak az Ön által vezérelt virtuális hálózaton belül látható. Egyik szolgáltatásvégpont sincs regisztrálva a nyilvános DNS-kiszolgálón.

> [!NOTE]
> Mivel a szolgáltatásvégponthoz nem érhetők el DNS-bejegyzések, ezek a végpontok addig nem lesznek elérhetők, amíg a [DNS](#apim-dns-configuration) nincs konfigurálva a virtuális hálózathoz.

A API Management módban a következő forgatókönyveket érheti el:

* Tegye biztonságossá a privát adatközpontban üzemeltetett API-kat külső felek számára is, webhely–hely kapcsolat vagy vpn Azure ExpressRoute használatával.
* Hibrid felhőalapú forgatókönyveket engedélyez azáltal, hogy a felhőalapú API-kat és a helyszíni API-kat egy közös átjárón keresztül teszi lehetővé.
* Kezelheti a több földrajzi helyen üzemeltetett API-kat egyetlen átjáróvégpont használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések végrehajtásához az alábbiakra van szükség:

+ **Aktív Azure-előfizetés.**

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Egy Azure API Management-példány.** További információ: [Azure-beli virtuális API Management létrehozása.](get-started-create-service-instance.md)

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

Amikor egy API Management-szolgáltatást telepít egy virtuális hálózaton, [](./api-management-using-with-vnet.md#required-ports) a rendszer a portok listáját használja, és meg kell nyitnia. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>Virtuális API Management létrehozása belső virtuális hálózaton
A API Management belső virtuális hálózatban lévő szolgáltatás egy belső terheléselosztási alapszintű termékváltozat mögött üzemel, ha a szolgáltatást a 2020-12-01-es ügyfél API-verzióval hozták létre. A 2021-01-01-preview API-verzióval rendelkező és az ügyfél előfizetéséből nyilvános IP-címmel rendelkező ügyfelekkel létrehozott szolgáltatások esetén a szolgáltatás egy belső terheléselosztó Standard termékváltozata mögött üzemel. További információ: Azure Load Balancer [SKUs.](../load-balancer/skus.md)

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Virtuális hálózati kapcsolat engedélyezése a Azure Portal

1. Keresse meg az Azure API Management-példányt a [Azure Portal.](https://portal.azure.com/)
1. Válassza **a Virtuális hálózat lehetőséget.**
1. Konfigurálja **a belső hozzáférés** típusát. Részletes lépésekért lásd: [VNET-kapcsolat engedélyezése a Azure Portal.](api-management-using-with-vnet.md#enable-vnet-connectivity-using-the-azure-portal)

    ![Azure-beli virtuális API Management belső virtuális hálózatban való beállítására vonatkozó menü][api-management-using-internal-vnet-menu]

4. Kattintson a **Mentés** gombra.

Miután az üzembe helyezés sikeres  volt, az  áttekintési panelen meg kell API Management virtuális ip-címe és nyilvános virtuális IP-címe. A **privát** virtuális IP-cím egy elosztott terhelésű IP-cím a API Management alhálózatból, amelyen keresztül , és végpontok `gateway` `portal` `management` `scm` érhetők el. A **nyilvános virtuális** IP-cím csak a vezérlősík végpontra, a 3443-as porton keresztüli forgalmára használatos, és az  `management` [ApiManagement szolgáltatás][ServiceTags] címkéjére zárolható.

![API Management konfigurált belső virtuális hálózattal][api-management-internal-vnet-dashboard]

> [!NOTE]
> Az Azure Portalon elérhető tesztkonzol nem fog működni a belső **VNET** által üzembe helyezett szolgáltatáshoz, mivel az átjáró URL-címe nincs regisztrálva a nyilvános DNS-ben. Ehelyett használja a fejlesztői portálon elérhető **tesztkonzolt.**

### <a name="deploy-api-management-into-virtual-network"></a><a name="deploy-apim-internal-vnet"> </a>Üzembe API Management a Virtual Network

A virtuális hálózati kapcsolatot az alábbi módszerekkel is engedélyezheti.


### <a name="api-version-2020-12-01"></a>API-verzió: 2020. 12. 01.

* Azure Resource Manager [sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-internal-vnet)

     [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-internal-vnet%2Fazuredeploy.json)

* Azure PowerShell létrehozása – [Virtuális](/powershell/module/az.apimanagement/new-azapimanagement) [hálózatban](/powershell/module/az.apimanagement/update-azapimanagementregion) API Management-példány létrehozása vagy frissítése

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>DNS-konfiguráció
Ha API Management virtuális hálózati módban van, a DNS-t az Azure kezeli. Belső virtuális hálózati mód esetén saját DNS-t kell kezelnie. A privát Azure DNS konfigurálása és összekapcsolása a virtuális hálózattal API Management szolgáltatás üzembe helyezése a következőben ajánlott. Megtudhatja, [hogyan állíthat be privát zónát a Azure DNS.](../dns/private-dns-getstarted-portal.md)

> [!NOTE]
> API Management szolgáltatás nem figyel az IP-címekről érkező kérelmekre. Csak a szolgáltatásvégpontjaikon konfigurált gazdagépnévre vonatkozó kérésekre válaszol. Ilyen végpont például az átjáró, Azure Portal a fejlesztői portál, a közvetlen felügyeleti végpont és a Git.

### <a name="access-on-default-host-names"></a>Hozzáférés az alapértelmezett gazdagépnevekhez
Egy "contosointernalvnet" nevű API Management létrehozásakor alapértelmezés szerint a következő szolgáltatásvégpontokat konfigurálja:

   * Átjáró vagy proxy: contosointernalvnet.azure-api.net

   * A fejlesztői portál: contosointernalvnet.portal.azure-api.net

   * Az új fejlesztői portál: contosointernalvnet.developer.azure-api.net

   * Közvetlen felügyeleti végpont: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Ezen API Management szolgáltatásvégpont eléréséhez létrehozhat egy virtuális gépet egy olyan alhálózatban, amely ahhoz a virtuális hálózathoz csatlakozik, amelyben a API Management üzembe van gombra. Feltételezve, hogy a szolgáltatás belső virtuális IP-címe 10.1.0.5, a következőképpen leképezheti a %SystemDrive%\drivers\etc\hosts gazdagépfájlt:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

Ezután az összes szolgáltatásvégpontot elérheti a létrehozott virtuális gépről.
Ha egyéni DNS-kiszolgálót használ egy virtuális hálózatban, dns-rekordokat is létrehozhat, és a virtuális hálózata bárhonnan elérheti ezeket a végpontokat.

### <a name="access-on-custom-domain-names"></a>Hozzáférés egyéni tartománynevekhez

1. Ha nem szeretné elérni az API Management-szolgáltatást az alapértelmezett állomásnevekkel, az alábbi képen látható módon egyéni tartományneveket állíthat be az összes szolgáltatásvégponthoz:

   ![Egyéni tartomány beállítása a API Management][api-management-custom-domain-name]

2. Ezután létrehozhat rekordokat a DNS-kiszolgálón a csak a virtuális hálózaton belülről elérhető végpontok eléréséhez.

## <a name="routing"></a><a name="routing"></a> Útválasztás

* A rendszer lefoglal *egy* elosztott terhelésű privát virtuális IP-címet az alhálózati tartományból, és API Management a virtuális hálózaton belüli szolgáltatásvégpontokat. Ez *a* magánhálózati IP-cím a szolgáltatás Áttekintés panelen található a Azure Portal. Ezt a címet regisztrálni kell a virtuális hálózat által használt DNS-kiszolgálókon.
* Egy elosztott *terhelésű nyilvános* IP-cím (VIP) is fenn lesz tartva, hogy a 3443-as porton keresztül hozzáférést biztosítson a felügyeleti szolgáltatásvégponthoz. Ez *a nyilvános* IP-cím a szolgáltatás Áttekintés panelen található a Azure Portal. A *nyilvános* IP-cím csak a vezérlősík végpontra, a 3443-as porton keresztüli forgalmára használatos, és az `management` [ApiManagement][ServiceTags] szolgáltatáscímkéjére zárolható.
* Az alhálózati IP-címtartományból (DIP) származó IP-címek a szolgáltatásban lévő összes virtuális géphez hozzá lesznek rendelve, és a virtuális hálózaton belüli erőforrások eléréséhez lesznek használva. A virtuális hálózaton kívüli erőforrásokhoz nyilvános IP-cím (VIP) lesz használva. Ha ip-korlátozási listákat használ a virtuális hálózaton belüli erőforrások biztonságossá helyezéséhez, a szolgáltatáshoz való hozzáférés megadásához vagy korlátozásához meg kell adni annak az alhálózatnak a teljes tartományát, ahol a API Management-szolgáltatás üzembe van adva.
* Az elosztott terhelésű nyilvános és magánhálózati IP-címek az Áttekintés panelen találhatók a Azure Portal.
* A nyilvános és privát hozzáféréshez hozzárendelt IP-címek megváltozhatnak, ha a szolgáltatást eltávolítják a szolgáltatásból, majd újra hozzáadják a virtuális hálózathoz. Ebben az esetben szükség lehet a DNS-regisztrációk, útválasztási szabályok és IP-korlátozási listák frissítésére a virtuális hálózaton belül.

## <a name="related-content"></a><a name="related-content"> </a>Kapcsolódó tartalom
További tudnivalókért olvassa el a következő cikket:
* [Gyakori hálózati konfigurációs problémák az Azure-API Management virtuális hálózaton való beállításakor][Common network configuration problems]
* [Virtuális hálózat – gyakori kérdések](../virtual-network/virtual-networks-faq.md)
* [Rekord létrehozása a DNS-ben](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
