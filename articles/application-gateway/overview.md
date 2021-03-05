---
title: Mi az Azure Application Gateway?
description: Megtudhatja, hogyan kezelheti az alkalmazása webes forgalmát az Azure Application Gateway segítségével.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 4344cd38d9a58eec27c6202e81b8ef678a510681
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176008"
---
# <a name="what-is-azure-application-gateway"></a>Mi az Azure Application Gateway?

Az Azure Application Gateway egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát. A hagyományos terheléselosztók az átviteli rétegen működnek (OSI 4. réteg – TCP és UDP), és a forrás IP-címe és portja alapján irányítják a forgalmat a célállomás IP-címére és portjára.

Application Gateway a HTTP-kérések további attribútumain alapuló útválasztási döntéseket hozhat létre, például az URI elérési út vagy a gazdagép fejléceit. A forgalmat például a bejövő URL-cím alapján irányíthatja. Tehát ha a bejövő URL-cím a `/images` kifejezést tartalmazza, a forgalmat adott, képekre konfigurált kiszolgálókra irányíthatja (azaz egy készletbe). Ha `/video` a értéke az URL-cím, akkor a rendszer átirányítja a forgalmat egy másik, videókra optimalizált készletbe.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Ezt a fajta útválasztást alkalmazásrétegbeli (OSI 7. réteg) terheléselosztásnak nevezzük. Az Azure Application Gateway URL-alapú és egyéb útválasztásra is képes.

>[!NOTE]
> Az Azure teljeskörűen felügyelt terheléselosztási megoldások együttesét biztosítja a különböző forgatókönyvekre. 
> * Ha DNS-alapú globális útválasztást szeretne végezni, és **nem** rendelkezik a TRANSPORT Layer Security (TLS) protokoll leállítására ("SSL-kiszervezés"), a HTTP/HTTPS-kérelemre vagy az alkalmazás-réteg feldolgozására vonatkozó követelményekkel, tekintse át a [Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
> * Ha optimalizálni szeretné a webes forgalom globális útválasztását, és a gyors globális feladatátvételsel optimalizálja a legfelső szintű végfelhasználói teljesítményt és megbízhatóságot, tekintse meg a [bejárati ajtót](../frontdoor/front-door-overview.md).
> * A hálózati réteg terheléselosztásának elvégzéséhez tekintse át [Load Balancer](../load-balancer/load-balancer-overview.md). 
> 
> A végpontok közötti forgatókönyvek igény szerint összekapcsolják ezeket a megoldásokat.
> Az Azure terheléselosztási lehetőségeinek összehasonlítását lásd: [Az Azure terheléselosztási lehetőségeinek áttekintése](/azure/architecture/guide/technology-choices/load-balancing-overview).


## <a name="features"></a>Funkciók

Application Gateway szolgáltatásokkal kapcsolatos további tudnivalókért lásd az [Azure Application Gateway funkcióit](features.md)ismertető témakört.

## <a name="pricing-and-sla"></a>Díjszabás és SLA

Application Gateway díjszabási információkért lásd: [Application Gateway díjszabása](https://azure.microsoft.com/pricing/details/application-gateway/).

Application Gateway SLA-ra vonatkozó információkért lásd: [Application Gateway SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/).

## <a name="whats-new"></a>Újdonságok

Az Azure Application Gateway újdonságait az [Azure Updates](https://azure.microsoft.com/updates/?category=networking&query=Application%20Gateway)című témakör ismerteti.

## <a name="next-steps"></a>Következő lépések

A követelményektől és a környezettől függően létrehozhat egy tesztelési Application Gateway a Azure Portal, a Azure PowerShell vagy az Azure CLI használatával.

- [Rövid útmutató: Webes forgalom irányítása az Azure Application Gatewayjel – Azure Portal](quick-create-portal.md)
- [Rövid útmutató: Webes forgalom irányítása az Azure Application Gatewayjel – Azure PowerShell](quick-create-powershell.md)
- [Első lépések – A webes forgalom irányítása az Azure Application Gateway szolgáltatással – Azure CLI](quick-create-cli.md)