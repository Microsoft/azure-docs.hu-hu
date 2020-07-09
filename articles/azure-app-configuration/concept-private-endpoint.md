---
title: Privát végpontok használata az Azure app Configuration szolgáltatáshoz
description: Az alkalmazás konfigurációs tárolójának védelme privát végpontok használatával
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: bbf2039ad695f332b69bd5429ff527a4a2534e26
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86026984"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Privát végpontok használata az Azure app Configuration szolgáltatáshoz

Az Azure-alkalmazás konfigurálásához [privát végpontokat](../private-link/private-endpoint-overview.md) is használhat, amelyek lehetővé teszik, hogy a virtuális hálózat (VNet) ügyfelei biztonságosan hozzáférjenek az [adatkapcsolatokhoz](../private-link/private-link-overview.md). A privát végpont IP-címet használ az alkalmazás konfigurációs tárolójának VNet. A VNet lévő ügyfelek és az alkalmazás konfigurációs tárolója közötti hálózati forgalom a VNet keresztül halad át a Microsoft gerinc hálózatán lévő privát kapcsolaton keresztül, ami kiküszöböli a nyilvános internetre való kitettséget.

Az alkalmazás konfigurációs tárolójához saját végpontok használata lehetővé teszi a következőket:
- Gondoskodjon az alkalmazás konfigurációjának részleteiről, ha úgy konfigurálja a tűzfalat, hogy a nyilvános végponton az alkalmazás konfigurációjának minden kapcsolatát letiltsa.
- A virtuális hálózat (VNet) biztonságának növelésével biztosítható, hogy az adatok ne legyenek a VNet.
- Biztonságosan csatlakozhat az alkalmazás-konfigurációs tárolóhoz a helyszíni hálózatokról, amelyek a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) -en vagy a [expressroute](../expressroute/expressroute-locations.md) -en keresztül csatlakoznak a VNet.

> [!NOTE]
> A Private Endpoint funkció mostantól általánosan elérhető minden régióban, *kivéve* a közép-indiai régiót. A **Közép-indiai** régióban az Azure-alkalmazás konfigurációja nyilvános előzetesként kínálja a privát végpontok használatát. A nyilvános előzetes ajánlatok lehetővé teszik, hogy az ügyfelek a hivatalos kiadásuk előtt új funkciókkal kísérletezzenek.  A nyilvános előzetes verzió funkcióit és szolgáltatásait nem éles használatra szánták.

## <a name="conceptual-overview"></a>Fogalmi áttekintés

A privát végpontok egy speciális hálózati adapterek egy Azure-szolgáltatáshoz a [Virtual Networkban](../virtual-network/virtual-networks-overview.md) (VNet). Amikor létrehoz egy privát végpontot az alkalmazás konfigurációs tárolójához, biztonságos kapcsolatot biztosít a VNet és a konfigurációs tároló ügyfelei között. A magánhálózati végpont IP-címet kap a VNet IP-címének tartományához. A magánhálózati végpont és a konfigurációs tároló közötti kapcsolat biztonságos privát hivatkozást használ.

A VNet lévő alkalmazások a privát végponton keresztül csatlakozhatnak a konfigurációs tárolóhoz **ugyanazon kapcsolati karakterláncok és engedélyezési mechanizmusok használatával, amelyeket egyébként használni**fognak. A magánhálózati végpontok az alkalmazás-konfigurációs tároló által támogatott összes protokollal használhatók.

Noha az alkalmazás konfigurációja nem támogatja a szolgáltatási végpontokat, a magánhálózati végpontok olyan alhálózatokban hozhatók létre, amelyek [szolgáltatási végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md)használnak. Az alhálózatban lévő ügyfelek biztonságosan kapcsolódhatnak az alkalmazás konfigurációs tárolójához a privát végpont használatával, miközben a szolgáltatás-végpontokat használják mások eléréséhez.  

Amikor saját VNet hoz létre egy szolgáltatáshoz egy magánhálózati végpontot, a rendszer jóváhagyásra vonatkozó kérést küld a szolgáltatásfiók tulajdonosának. Ha a privát végpont létrehozását kérő felhasználó a fiók tulajdonosa is, a rendszer ezt a jóváhagyási kérést automatikusan jóváhagyja.

A szolgáltatásfiók tulajdonosai a `Private Endpoints` [Azure Portal](https://portal.azure.com)konfigurációs tárolójának lapján keresztül kezelhetik a belefoglalt kérelmeket és a privát végpontokat.

### <a name="private-endpoints-for-app-configuration"></a>Saját végpontok az alkalmazás konfigurálásához 

Privát végpont létrehozásakor meg kell adnia azt az alkalmazás-konfigurációs tárolót, amelyhez csatlakozik. Ha egy fiókban több alkalmazás-konfigurációs példány is található, minden egyes tárolóhoz külön privát végpontra van szükség.

### <a name="connecting-to-private-endpoints"></a>Csatlakozás privát végpontokhoz

Az Azure DNS-feloldásra támaszkodik, hogy a VNet és a konfigurációs tároló közötti kapcsolatokat egy privát kapcsolaton keresztül irányítsa. A Azure Portal található kapcsolati karakterláncok gyorsan megtalálhatók az alkalmazás konfigurációs tárolójának kiválasztásával, majd a **Beállítások**  >  **hozzáférési kulcsok lehetőség**kiválasztásával.  

> [!IMPORTANT]
> Ugyanazzal a kapcsolati karakterlánccal csatlakozhat az alkalmazás konfigurációs tárolójához privát végpontok használatával, ahogyan azt egy nyilvános végponthoz kívánja használni. Ne kapcsolódjon az áruházhoz az `privatelink` altartomány URL-címének használatával.

## <a name="dns-changes-for-private-endpoints"></a>A magánhálózati végpontok DNS-módosításai

Amikor létrehoz egy privát végpontot, a konfigurációs tárolóhoz tartozó DNS CNAME-erőforrásrekord az előtaggal rendelkező altartományban található aliasra frissül `privatelink` . Az Azure emellett létrehoz egy [saját DNS-zónát](../dns/private-dns-overview.md) is, amely az `privatelink` altartományhoz tartozik, és a DNS a saját végpontokhoz tartozó erőforrásrekordokat is.

Amikor a végponti URL-címet a privát végpontot futtató VNet oldja fel, a rendszer az áruház privát végpontját oldja fel. Ha a VNet kívülről oldódik meg, a végpont URL-címe feloldódik a nyilvános végpontra. Privát végpont létrehozásakor a nyilvános végpont le van tiltva.

Ha a hálózaton egyéni DNS-kiszolgálót használ, az ügyfeleknek képesnek kell lenniük a szolgáltatási végpont teljes tartománynevének (FQDN) feloldására a magánhálózati végpont IP-címére. Konfigurálja a DNS-kiszolgálót úgy, hogy delegálja a magánhálózati kapcsolat altartományát a VNet tartozó magánhálózati DNS-zónához, vagy konfigurálja a rekordokat a `AppConfigInstanceA.privatelink.azconfig.io` saját végpont IP-címével.

> [!TIP]
> Egyéni vagy helyszíni DNS-kiszolgáló használatakor a DNS-kiszolgálót úgy kell konfigurálni, hogy az altartományban lévő tároló nevét `privatelink` a magánhálózati végpont IP-címére oldja fel. Ezt úgy teheti meg, hogy delegálja az `privatelink` altartományt a VNet magánhálózati DNS-zónájához, vagy konfigurálja a DNS-zónát a DNS-kiszolgálón, és hozzáadja a DNS-rekordot.

## <a name="pricing"></a>Díjszabás

A privát végpontok engedélyezéséhez [standard szintű](https://azure.microsoft.com/pricing/details/app-configuration/) alkalmazás-konfigurációs tárolóra van szükség.  A privát hivatkozások díjszabásával kapcsolatos további információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>További lépések

További információ az alkalmazás-konfigurációs tároló privát végpontjának létrehozásáról:

- [Privát végpont létrehozása a Azure Portal privát kapcsolati központjának használatával](../private-link/create-private-endpoint-portal.md)
- [Privát végpont létrehozása az Azure CLI-vel](../private-link/create-private-endpoint-cli.md)
- [Privát végpont létrehozása Azure PowerShell használatával](../private-link/create-private-endpoint-powershell.md)

Ismerje meg, hogyan konfigurálhatja a DNS-kiszolgálót magánhálózati végpontokkal:

- [Azure virtuális hálózatokon található erőforrások névfeloldása](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [A magánhálózati végpontok DNS-konfigurációja](/azure/private-link/private-endpoint-overview#dns-configuration)
