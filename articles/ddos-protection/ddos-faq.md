---
title: Azure DDoS Protection standard gyakori kérdések
description: Gyakori kérdések a Azure DDoS Protection szabványról, amely segítséget nyújt a DDoS-támadások elleni védelemben.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: yitoh
ms.openlocfilehash: 89685d1b8c3a57fa142bbbfd1114f9aa0ff0c400
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98611117"
---
# <a name="azure-ddos-protection-standard-frequent-asked-questions"></a>Azure DDoS Protection standard gyakori kérdések

Ez a cikk a Azure DDoS Protection Szabványsal kapcsolatos gyakori kérdésekre ad választ. 

## <a name="what-is-a-distributed-denial-of-service-ddos-attack"></a>Mi az elosztott szolgáltatásmegtagadási (DDoS) támadás?
Az elosztott szolgáltatásmegtagadás vagy a DDoS olyan típusú támadás, amelyben a támadó több kérelmet küld az alkalmazásnak, mint amennyit az alkalmazás képes kezelni. Ennek eredményeként az erőforrások kimerítve lesznek, ami hatással van az alkalmazás rendelkezésre állására és az ügyfelek kiszolgálására. Az elmúlt néhány évben az iparág egyre nagyobb mennyiségű támadást észlelt, és a támadások egyre kifinomultabbak és nagyobb nagyságrendűek. A DDoS-támadások bármely olyan végpontra kiállíthatók, amely nyilvánosan elérhető az interneten keresztül.

## <a name="what-is-azure-ddos-protection-standard-service"></a>Mi az Azure DDoS Protection standard szolgáltatás?
A standard szintű, az alkalmazások kialakításával kapcsolatos ajánlott eljárásokkal kombinálva a DDoS-támadások elleni védelem érdekében fejlett DDoS-elhárítási funkciókat biztosít. Azure DDoS Protection A rendszer automatikusan hangolja az adott Azure-erőforrások védelmére egy virtuális hálózaton. A védelem egyszerűen engedélyezhető bármely új vagy meglévő virtuális hálózaton, és nem igényel alkalmazás-vagy erőforrás-módosítást. Az alapszintű szolgáltatás számos előnnyel jár, beleértve a naplózást, a riasztásokat és a telemetria is. További részletekért tekintse meg [Azure DDoS Protection standard áttekintést](ddos-protection-overview.md) . 

## <a name="how-does-pricing-work"></a>Hogyan működik a díjszabás?
A DDoS Protection-csomagok havi $2 944-os fix havi díja, amely akár 100 nyilvános IP-címet is magában foglalhat. A további erőforrások védelme havonta további $30 lesz. 

A bérlők egy DDoS Protection-tervet is használhatnak több előfizetésben, így nincs szükség több DDoS Protection-csomag létrehozására.

További részletekért tekintse meg a [Azure DDoS Protection standard díjszabását](https://azure.microsoft.com/pricing/details/ddos-protection/) .

## <a name="is-the-service-zone-resilient"></a>A szolgáltatási zóna rugalmas?
Igen. A Azure DDoS Protection alapértelmezés szerint rugalmas.

## <a name="how-do-i-configure-the-service-to-be-zone-resilient"></a>Hogyan konfigurálja a szolgáltatást zónákra rugalmasan?
A zóna rugalmasságának engedélyezéséhez nincs szükség ügyfél-konfigurációra. A Azure DDoS Protection erőforrások rugalmassága alapértelmezés szerint elérhető, és maga a szolgáltatás kezeli.

## <a name="what-about-protection-at-the-service-layer-layer-7"></a>Mi a védelem a szolgáltatási rétegben (7. réteg)?
Az ügyfelek a webalkalmazási tűzfallal (WAF) együtt használhatják a Azure DDoS Protection szolgáltatást a hálózati rétegben (3. és 4. réteg, Azure DDoS Protection standard által kínált) és az alkalmazási rétegben (7. réteg, WAF által felkínált). A WAF-ajánlatok közé tartoznak az Azure [Application Gateway WAF SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , valamint az [Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)elérhető, harmadik féltől származó webalkalmazási tűzfalak is.

## <a name="are-services-unsafe-in-azure-without-the-service"></a>A szolgáltatások nem biztonságosak az Azure-ban a szolgáltatás nélkül?
Az Azure-on futó szolgáltatásokat Azure DDoS Protection alapszintű védelemmel látja el, amely az Azure infrastruktúrájának védelmére van érvényben. Azonban az infrastruktúrát védő védelem jóval magasabb, mint a legtöbb alkalmazás kapacitása, és nem biztosít telemetria vagy riasztást, így ha a platform ártalmatlannak tekinti a forgalmi kötetet, akkor az azt fogadó alkalmazás számára is pusztító lehet. 

Az Azure DDoS Protection standard szolgáltatás bevezetésével az alkalmazás dedikált monitorozást kap a támadások és az alkalmazás-specifikus küszöbértékek észlelése érdekében. A szolgáltatás a várt adatforgalomhoz beállított profillal lesz védve, ami sokkal szigorúbb védelmet biztosít a DDoS-támadások ellen.

## <a name="what-are-the-supported-protected-resource-types"></a>Mik a támogatott védett erőforrástípusok?
Az ARM-alapú virtuális hálózatok lévő nyilvános IP-címek jelenleg az egyetlen védett erőforrás. A Pásti-szolgáltatások (több-bérlős) nem támogatottak a bemutatott időpontban. További részletekért tekintse meg [Azure DDoS Protection szabványos hivatkozási architektúrákat](ddos-protection-reference-architectures.md) .

## <a name="are-classicrdfe-protected-resources-supported"></a>Támogatottak-e a klasszikus/RDFE védett erőforrások?
Előzetes verzióban csak az ARM-alapú védett erőforrások támogatottak. A klasszikus/RDFE üzemelő példányokban nem támogatottak a virtuális gépek. A támogatás jelenleg nem tervezett a klasszikus/RDFE-erőforrásokhoz. További részletekért tekintse meg [Azure DDoS Protection szabványos hivatkozási architektúrákat](ddos-protection-reference-architectures.md) .

## <a name="can-i-protect-my-paas-resources-using-ddos-protection"></a>Biztosíthatom a saját Pásti-erőforrásait a DDoS Protection?
A több-bérlős rendszerhez csatolt nyilvános IP-címek jelenleg nem támogatottak. Nem támogatott erőforrások például a Storage VIP-címek, az Event hub VIP-címei és az alkalmazás-és Cloud Services alkalmazások. További részletekért tekintse meg [Azure DDoS Protection szabványos hivatkozási architektúrákat](ddos-protection-reference-architectures.md) .

## <a name="can-i-protect-my-on-premise-resources-using-ddos-protection"></a>Használhatom a helyszíni erőforrásokat a DDoS Protection használatával?
A DDoS-védelem engedélyezéséhez az Azure egyik VNet tartozó nyilvános végpontokat kell használnia a szolgáltatáshoz. A minták például a következők:
- Webhelyek (IaaS) az Azure-ban és a háttérbeli adatbázisokban a helyszíni adatközpontban. 
- Application Gateway az Azure-ban (a DDoS Protection az App Gateway/WAF esetében engedélyezve van) és a helyszíni adatközpontokban található webhelyeken.

További részletekért tekintse meg [Azure DDoS Protection szabványos hivatkozási architektúrákat](ddos-protection-reference-architectures.md) .

## <a name="can-i-register-a-domain-outside-of-azure-and-associate-that-to-a-protected-resource-like-vm-or-elb"></a>Regisztrálhatok egy tartományt az Azure-on kívül, és hogyan társíthatók egy védett erőforráshoz, például virtuális géphez vagy ELB?
A nyilvános IP-címek esetében DDoS Protection szolgáltatás minden alkalmazást támogat, függetlenül attól, hogy a társított tartomány hol van regisztrálva vagy üzemeltetve, amíg a társított nyilvános IP-cím az Azure-ban üzemel. 

## <a name="can-i-manually-configure-the-ddos-policy-applied-to-the-vnetspublic-ips"></a>Manuálisan konfigurálható a DDoS-szabályzat a virtuális hálózatok/nyilvános IP-címekre?
Nem, sajnos a szabályzatok testreszabása jelenleg nem érhető el.

## <a name="can-i-allowlistblocklist-specific-ip-addresses"></a>Adott IP-címeket is engedélyezési/Blocklist?
Nem, sajnos a manuális konfiguráció jelenleg nem érhető el.

## <a name="how-can-i-test-ddos-protection"></a>Hogyan lehet tesztelni a DDoS Protection?
Lásd: [tesztelés szimulációk használatával](test-through-simulations.md).

## <a name="how-long-does-it-take-for-the-metrics-to-load-on-portal"></a>Mennyi ideig tart a mérőszámok betöltése a portálon?
A metrikák 5 percen belül láthatók a portálon. Ha az erőforrás támadás alatt áll, a további metrikák a portálon 5-7 percen belül megjelennek. 

## <a name="does-the-service-store-customer-data"></a>A szolgáltatás tárolja az ügyféladatokat?
Nem, az Azure DDoS Protection nem tárolja az ügyféladatokat.
    
