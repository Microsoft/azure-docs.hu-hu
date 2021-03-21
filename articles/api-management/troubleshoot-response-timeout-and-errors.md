---
title: Az ügyfél válaszára vonatkozó időtúllépések és hibák elhárítása API Management
description: Az időszakos kapcsolati hibák és az API Management kapcsolódó késési problémák elhárítása
author: vladvino
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: apimpm
ms.service: api-management
ms.openlocfilehash: 6cace4a02c8d45cacbbc34e9778b5c4a78ada27f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576523"
---
# <a name="troubleshooting-client-response-timeouts-and-errors-with-api-management"></a>Az ügyfél válaszára vonatkozó időtúllépések és hibák elhárítása API Management

Ebből a cikkből megtudhatja, hogy az [Azure API Management](./api-management-key-concepts.md)az időszakos kapcsolati hibákat és a kapcsolódó késési problémákat. Ez a cikk részletesen ismerteti a forrásoldali hálózati fordítási (SNAT) portok kimerítésének adatait és hibaelhárítását. Ha további segítségre van szüksége, forduljon az Azure- [Közösség támogatási](https://azure.microsoft.com/support/community/) szolgálatának Azure-szakértőihez, vagy nyújtson támogatási kérést az [Azure-támogatással](https://azure.microsoft.com/support/options/).

## <a name="symptoms"></a>Hibajelenségek

Az API-kat a API Management (APIM) szolgáltatáson keresztül hívó ügyfélalkalmazások a következő tünetek közül egyet vagy többet mutathatnak:

* Időszakos HTTP 500-hibák
* Időtúllépési hibaüzenetek

Ezek a tünetek a `BackendConnectionFailure` [Azure monitor erőforrás-naplóiban](../azure-monitor/essentials/resource-logs.md)lévő példányként jelennek meg.

## <a name="cause"></a>Ok

Ez a jelenség gyakran előfordul, mert a hálózati címfordítás (SNAT) portja korlátozza a APIM szolgáltatását.

Amikor egy ügyfél meghívja az egyik APIM API-t, az Azure API Management szolgáltatás megnyit egy SNAT-portot a háttérbeli API eléréséhez. Az Azure-beli [Kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md)esetében az Azure a forrás hálózati CÍMFORDÍTÁS (SNAT) és egy Load Balancer (az ügyfelek számára nem elérhető) használatával kommunikál az Azure-on kívüli végpontokkal a nyilvános IP-címeken, valamint az Azure-ban olyan végpontokat, amelyek nem használnak [Virtual Network szolgáltatási végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md). Ez a helyzet csak a nyilvános IP-címeken elérhető háttér-API-kra vonatkozik.

A API Management szolgáltatás minden példánya eredetileg előre lefoglalt számú SNAT-portot kap. Ez a korlát befolyásolja a kapcsolatok megnyitását ugyanahhoz a gazdagéphez és port kombinációhoz. A rendszer akkor használja a SNAT-portokat, ha ugyanazzal a címnek és portszámnak a hívásait megismétli. A SNAT-portok felszabadítása után a port igény szerint újra elérhetővé válik. Az Azure hálózati terheléselosztó a SNAT-portokat csak négy perc várakozás után visszaállítja a bezárt kapcsolatokból.

Az ügyfelek kéréseinek gyors egyidejű megtartása kimerítheti a SNAT-portok előre lefoglalt kvótáját, ha ezek a portok nem zárulnak le és nem állnak elég gyorsan, így megakadályozva, hogy a APIM szolgáltatás időben dolgozza fel az ügyfelek kéréseit.

## <a name="mitigations-and-solutions"></a>Enyhítések és megoldások

A SNAT-portok kimerülésével kapcsolatos probléma megoldásához először a háttér-szolgáltatások teljesítményének diagnosztizálására és optimalizálására van szükség.

A SNAT-portok kimerülésének mérséklésére vonatkozó általános stratégiákat a *Azure Load Balancer* dokumentációjában található [Kimenő kapcsolatok hibáinak elhárítása című témakörben](../load-balancer/troubleshoot-outbound-connection.md) talál. Ezen stratégiák közül a következők alkalmazhatók a API Managementra.

### <a name="scale-your-apim-instance"></a>A APIM-példány skálázása

Minden API Management-példány több SNAT-portot foglal le, APIM egységek alapján. További SNAT-portokat is lefoglalhat, ha a API Management-példányt további egységekkel szeretné méretezni. További információ: [API Management szolgáltatás skálázása](upgrade-and-scale.md#scale-your-api-management-service)

> [!NOTE]
> A SNAT-portok használata jelenleg nem érhető el metrikaként API Management egységek automatikus skálázásához.

### <a name="use-multiple-ips-for-your-backend-urls"></a>Több IP-cím használata a háttérbeli URL-címekhez

A APIM-példány és a háttérrendszer egy célként megadott IP-címe és célport közötti minden kapcsolódás egy SNAT portot fog használni a különböző adatforgalom fenntartása érdekében. Különböző SNAT-portok nélkül a háttérben futó szolgáltatástól érkező adatforgalomhoz a APIM nem lehet külön választ választani egymástól.

Mivel a SNAT-portok újra felhasználhatók, ha a cél IP-cím vagy a célport eltérő, a SNAT-portok kimerítésének másik módja, ha több IP-címet használ a háttérbeli szolgáltatás URL-címeihez.

További információ: [kimenő proxy Azure Load Balancer](../load-balancer/load-balancer-outbound-connections.md).

### <a name="place-your-apim-and-backend-service-in-the-same-vnet"></a>Helyezze el a APIM és a háttér-szolgáltatást ugyanabban a VNet

Ha a háttér-API egy olyan Azure-szolgáltatáson fut, amely támogatja az olyan *szolgáltatási végpontokat* , mint például a app Service, elkerülheti a portok kimerülésének SNAT, ha a APIM-példányt és a háttér-szolgáltatást ugyanabban a virtuális hálózaton helyezi el, és a [szolgáltatás-végpontokon](../virtual-network/virtual-network-service-endpoints-overview.md) vagy [privát végpontokon](../private-link/private-endpoint-overview.md)keresztül teszi közzé. Ha közös VNet használ, és az integrációs alhálózaton helyez el szolgáltatási végpontokat, az APIM-példányból kimenő forgalom megkerüli az internetet, így elkerülhető a SNAT-portok korlátozása. Hasonlóképpen, ha VNet és privát végpontokat használ, akkor nem lesz kimenő SNAT-portja a célhoz.

Részletekért lásd: az [azure API Management használata virtuális hálózatokkal](api-management-using-with-vnet.md) és a [app Service integrálása Azure-beli virtuális hálózattal](../app-service/web-sites-integrate-with-vnet.md).

### <a name="place-your-apim-in-a-virtual-network-and-route-outbound-calls-to-azure-firewall"></a>Helyezze a APIM egy virtuális hálózatba, és irányítsa a kimenő hívásokat Azure Firewall

A APIM és a háttér-szolgáltatások virtuális hálózatban való elhelyezéséhez hasonlóan Azure Firewall is alkalmazhat egy VNet a APIM szolgáltatással, majd átirányíthatja a kimenő APIM-hívásokat a Azure Firewall. A APIM és a Azure Firewall között (amelyek ugyanabban a VNet vannak), nincs szükség SNAT-portra. A háttér-szolgáltatásokhoz való SNAT a Azure Firewall 64 000 elérhető porttal rendelkezik, ami sokkal nagyobb mennyiségű, mint a APIM-példányok számára.

További információkért tekintse meg [Azure Firewall](../firewall/overview.md) dokumentációját.

### <a name="consider-response-caching-and-other-backend-performance-tuning"></a>A válasz gyorsítótárazásának és a háttérbeli teljesítmény finomhangolásának megfontolása

Egy másik lehetséges megoldás a háttérbeli API-k feldolgozási idejének javítása. Ennek egyik módja, ha bizonyos API-kat konfigurál a válasz gyorsítótárazásával, hogy csökkentse a késést az API-t és a APIM-háttérbeli terhelést igénylő ügyfélalkalmazások között.

További információ: a [gyorsítótárazás hozzáadása az Azure-API Management teljesítményének növelése](api-management-howto-cache.md)érdekében.

### <a name="consider-implementing-access-restriction-policies"></a>A hozzáférés-korlátozási szabályzatok megvalósításának megfontolása

Ha ésszerűnek érzi az üzleti forgatókönyvét, a API Management termékhez hozzáférési korlátozási szabályzatokat alkalmazhat. Például a szabályzat használatával `rate-limit-by-key` megakadályozható, hogy az API-használat a megadott időszakra korlátozza a hívások gyakoriságát.

További információért tekintse meg [API Management hozzáférés-korlátozási szabályzatokat](api-management-access-restriction-policies.md) .

## <a name="see-also"></a>Lásd még

* [Azure Load Balancer: a kimenő kapcsolatok hibáinak elhárítása](../load-balancer/troubleshoot-outbound-connection.md)
* [Azure App Service: időszakos kimenő kapcsolatok hibáinak elhárítása](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)
