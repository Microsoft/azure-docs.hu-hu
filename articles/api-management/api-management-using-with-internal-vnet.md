---
title: Az Azure API Management használata belső virtuális hálózatokkal
titleSuffix: Azure API Management
description: Ismerje meg, hogyan állíthatja be és konfigurálhatja az Azure API Managementt egy belső virtuális hálózaton
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/09/2021
ms.author: apimpm
ms.openlocfilehash: 10154f496d76ce6b9eb19d610fdff8d7a4023c2d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565954"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Az Azure API Management használata belső virtuális hálózattal
Az Azure Virtual Networks használatával az Azure API Management képes az interneten keresztül nem elérhető API-k kezelésére. Számos VPN-technológia érhető el a kapcsolat létrehozásához. API Management a virtuális hálózaton belül két fő módban is üzembe helyezhető:
* Külső
* Belső

Ha a API Management belső virtuális hálózati módban üzemel, az összes szolgáltatás-végpont (a proxy átjáró, a fejlesztői portál, a közvetlen felügyelet és a git) csak egy olyan virtuális hálózatban látható, amely a hozzáférését szabályozza. A szolgáltatási végpontok egyike sincs regisztrálva a nyilvános DNS-kiszolgálón.

> [!NOTE]
> Mivel a szolgáltatási végpontokhoz nem tartoznak DNS-bejegyzések, ezek a végpontok nem lesznek elérhetők, amíg a [DNS nincs konfigurálva](#apim-dns-configuration) a virtuális hálózathoz.

A API Management belső módban való használata a következő helyzetekben valósítható meg:

* A privát adatközpontban üzemeltetett API-k biztonságosan elérhetők harmadik felektől a helyek közötti vagy az Azure ExpressRoute VPN-kapcsolatok használatával.
* A hibrid felhőalapú forgatókönyvek lehetővé teszik, hogy a felhőalapú API-kat és a helyszíni API-kat egy közös átjárón keresztül tegye elérhetővé.
* Több földrajzi helyen üzemeltetett API-k kezelése egyetlen átjáró-végpont használatával.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következőket kell tennie:

+ **Aktív Azure-előfizetés**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Egy Azure API Management-példány**. További információ: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
+ Ha egy API Management szolgáltatás egy virtuális hálózaton van telepítve, a rendszer a [portok listáját](./api-management-using-with-vnet.md#required-ports) használja, és meg kell nyitni. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>API Management létrehozása belső virtuális hálózaton
A belső virtuális hálózat API Management szolgáltatása a [belső terheléselosztó (klasszikus)](/previous-versions/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud)mögött található. Ez az egyetlen elérhető lehetőség, és nem módosítható.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Virtuális hálózati kapcsolatok engedélyezése a Azure Portal használatával

1. Tallózással keresse meg az Azure API Management-példányát a [Azure Portalban](https://portal.azure.com/).
2. Válassza a **virtuális hálózat** lehetőséget.
3. Konfigurálja a virtuális hálózaton belül telepítendő API Management példányt.

    ![Azure-API Management belső virtuális hálózatban való beállítására szolgáló menü][api-management-using-internal-vnet-menu]

4. Kattintson a **Mentés** gombra.

Az üzembe helyezés sikeres végrehajtása után az Áttekintés panelen látnia kell a API Management szolgáltatás **magánhálózati** virtuális IP-címét és **nyilvános** virtuális IP-címét. A **magánhálózati** virtuális IP-cím egy elosztott terhelésű IP-cím a API Management delegált alhálózaton belül, amelyen keresztül a `gateway` `portal` `management` `scm` végpontok elérhetők. A **nyilvános** virtuális IP-cím **csak** a `management` 3443-es porton keresztüli végpontra irányuló vezérlési sík forgalmára szolgál, és a [ApiManagement][ServiceTags] servicetag is zárolható.

![Irányítópult API Management konfigurált belső virtuális hálózattal][api-management-internal-vnet-dashboard]

> [!NOTE]
> Az Azure Portalon elérhető tesztelési konzol nem fog működni a **belső** VNET üzembe helyezett szolgáltatás esetében, mivel az átjáró URL-címe nincs regisztrálva a nyilvános DNS-ben. Ehelyett a **fejlesztői portálon** elérhető tesztelési konzolt kell használnia.

### <a name="deploy-api-management-into-virtual-network"></a><a name="deploy-apim-internal-vnet"> </a>API Management üzembe helyezése Virtual Network

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-internal-vnet%2Fazuredeploy.json)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

A virtuális hálózati kapcsolatot a PowerShell-parancsmagok használatával is engedélyezheti.

* API Management szolgáltatás létrehozása virtuális hálózaton belül: a [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) parancsmag használatával hozzon létre egy Azure API Management-szolgáltatást egy virtuális hálózaton belül, és konfigurálja úgy, hogy a belső virtuális hálózat típusát használja.

* Egy API Management szolgáltatás meglévő központi telepítésének frissítése egy virtuális hálózaton belül: az [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) parancsmag használatával helyezzen át egy meglévő API Management-szolgáltatást egy virtuális hálózaton belül, és konfigurálja úgy, hogy a belső virtuális hálózat típusát használja.

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>DNS-konfiguráció
Ha API Management külső virtuális hálózati módban van, a DNS-t az Azure felügyeli. Belső virtuális hálózati módban a saját DNS-t kell kezelnie. Az ajánlott lehetőség a Azure DNS privát zóna konfigurálása és a virtuális hálózat API Management szolgáltatáshoz való csatolása.  Ide [kattintva](../dns/private-dns-getstarted-portal.md) megtudhatja, hogyan állítson be egy privát zónát Azure DNSban.

> [!NOTE]
> API Management szolgáltatás nem figyeli az IP-címekről érkező kéréseket. Csak a szolgáltatási végpontokon konfigurált állomásnévre vonatkozó kérelmekre válaszol. Ezek a végpontok közé tartozik az átjáró, a Azure Portal és a fejlesztői portál, a közvetlen felügyeleti végpont és a git.

### <a name="access-on-default-host-names"></a>Hozzáférés az alapértelmezett állomásnevek
Ha például létrehoz egy "contosointernalvnet" nevű API Management szolgáltatást, a következő szolgáltatási végpontok alapértelmezés szerint vannak konfigurálva:

   * Átjáró vagy proxy: contosointernalvnet.azure-api.net

   * Fejlesztői portál: contosointernalvnet.portal.azure-api.net

   * Az új fejlesztői portál: contosointernalvnet.developer.azure-api.net

   * Közvetlen felügyeleti végpont: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Ezen API Management szolgáltatás-végpontok eléréséhez létrehozhat egy virtuális gépet a virtuális hálózathoz csatlakoztatott alhálózatban, amelyben a API Management telepítve van. Feltételezve, hogy a szolgáltatáshoz tartozó belső virtuális IP-cím 10.1.0.5, a%SystemDrive%\drivers\etc\hosts a következő módon képezhető le:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

Ezután az összes szolgáltatás-végpontot elérheti a létrehozott virtuális gépről.
Ha egy virtuális hálózatban egyéni DNS-kiszolgálót használ, létrehozhat egy DNS-rekordot, és elérheti ezeket a végpontokat a virtuális hálózat bármely pontjáról.

### <a name="access-on-custom-domain-names"></a>Hozzáférés egyéni tartománynevek esetén

1. Ha nem szeretné elérni az API Management szolgáltatást az alapértelmezett állomásnévvel, beállíthatja az összes szolgáltatás-végpont egyéni tartománynevét az alábbi képen látható módon:

   ![Egyéni tartomány beállítása a API Managementhoz][api-management-custom-domain-name]

2. Ezután létrehozhat rekordokat a DNS-kiszolgálón a csak a virtuális hálózaton belül elérhető végpontok eléréséhez.

## <a name="routing"></a><a name="routing"></a> Útválasztás

* Az alhálózat tartományának elosztott terhelésű *magánhálózati* virtuális IP-címe le lesz foglalva, és a virtuális hálózatról a API Management szolgáltatási végpontok elérésére szolgál. Ez a *magánhálózati* IP-cím a Azure Portal szolgáltatásának áttekintés paneljén található. Ezt a címeket regisztrálni kell a virtuális hálózat által használt DNS-kiszolgálókkal.
* Egy elosztott terhelésű *nyilvános* IP-cím (VIP) is le lesz foglalva, hogy hozzáférést biztosítson a felügyeleti szolgáltatási végponthoz a 3443-es porton keresztül. Ez a *nyilvános* IP-cím a Azure Portal szolgáltatásának áttekintés paneljén található. A *nyilvános* IP-címet csak a 3443-es porton keresztül a végpont felé irányuló vezérlési síkon lehet használni `management` , és zárolható a [ApiManagement][ServiceTags] servicetag.
* Az alhálózat IP-tartományból (DIP) származó IP-címek a szolgáltatásban lévő egyes virtuális gépekhez lesznek rendelve, és a rendszer a virtuális hálózaton belüli erőforrásokhoz való hozzáférést fogja használni. A virtuális hálózaton kívüli erőforrások eléréséhez nyilvános IP-címet (VIP) kell használni. Ha az IP-korlátozási listát a virtuális hálózaton belüli erőforrások védelmére használja, akkor a szolgáltatáshoz való hozzáférés engedélyezéséhez vagy korlátozásához meg kell adni az alhálózat teljes tartományát, ahol a API Management szolgáltatás telepítve van.
* Az elosztott terhelésű nyilvános és magánhálózati IP-címek a Azure Portal áttekintés paneljén találhatók.
* A nyilvános és a privát hozzáféréshez hozzárendelt IP-címek megváltozhatnak, ha a szolgáltatás el lett távolítva, majd visszakerül a virtuális hálózatba. Ha ez történik, előfordulhat, hogy frissítenie kell a DNS-regisztrációkat, az útválasztási szabályokat és az IP-korlátozási listát a virtuális hálózaton belül.

## <a name="related-content"></a><a name="related-content"> </a>Kapcsolódó tartalom
További tudnivalókért olvassa el a következő cikket:
* [Gyakori hálózati konfigurációs problémák az Azure-API Management virtuális hálózatban való beállítása során][Common network configuration problems]
* [Virtual Network – gyakori kérdések](../virtual-network/virtual-networks-faq.md)
* [Rekord létrehozása a DNS-ben](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
