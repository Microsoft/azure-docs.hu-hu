---
title: fájl belefoglalása
description: fájl belefoglalása
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 3/26/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: b8842ab4bcaf16b7345b25fa9ac4998981d9c458
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804145"
---
### <a name="what-is-azure-firewall"></a>Mi az Azure Firewall?

Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Egy teljes körűen állapot-nyilvántartó tűzfal--szolgáltatásként beépített magas rendelkezésre állás és a korlátlan felhőbeli méretezhetőséget. Központilag hozhatja létre, érvényesítheti és naplózhatja az alkalmazás- és hálózatelérési szabályzatokat az előfizetésekre és a virtuális hálózatokra vonatkozóan.

### <a name="what-capabilities-are-supported-in-azure-firewall"></a>Azure tűzfal milyen funkciók támogatottak?

* Állapotalapú tűzfal mint szolgáltatás
* Beépített magas rendelkezésre állás korlátlan felhőbeli skálázással
* FQDN-szűrés
* FQDN-címkék
* Hálózati forgalomra vonatkozó szűrési szabályok
* Kimenő SNAT-támogatás
* Bejövő DNAT-támogatás
* Központilag létrehozása, érvényesítése, és jelentkezzen alkalmazás és a hálózati kapcsolat házirendek az Azure-előfizetések és a virtuális hálózatok között
* Teljes integráció az Azure Monitorral a naplózáshoz és az elemzéshez

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Mi az az Azure tűzfal a tipikus telepítési modell?

Azure tűzfal telepíthet minden olyan virtuális hálózaton, de az ügyfelek általában üzembe helyezni a központi virtuális hálózaton, és a Központ-küllő modellek hozzá más virtuális hálózatokat. Ezután beállíthatja az alapértelmezett útvonal, a társviszonyban álló virtuális hálózatba, mutasson a tűzfal központi virtuális hálózaton. Globális virtuális társhálózatok létesítésének támogatott, de nem ajánlott lehetséges teljesítményének és késési problémák miatt régiók között elosztva. A legjobb teljesítmény érdekében üzembe helyezése egy tűzfal régiónként.

Ez a modell előnye képesek központilag több küllő virtuális hálózatok különböző előfizetésekben vezérlőelem. Is találhatók költséget takaríthat meg, nem kell az egyes virtuális hálózatok tűzfal külön-külön telepíteni. A költségek csökkentését és a társítás társviszony-létesítési költséget, az ügyfél forgalmi minták alapján kell mérni.

### <a name="how-can-i-install-the-azure-firewall"></a>Hogyan telepíthetem az Azure-tűzfal?

Az Azure portal, PowerShell, REST API használatával, vagy sablonok használatával is beállíthatja az Azure-tűzfal. Lásd: [oktatóanyag: Telepítse és konfigurálja az Azure portal segítségével Azure tűzfal](../articles/firewall/tutorial-firewall-deploy-portal.md) részletes útmutatásait.

### <a name="what-are-some-azure-firewall-concepts"></a>Mik azok az Azure-tűzfal fogalmak?

Az Azure tűzfal szabályok és szabálygyűjtemények támogatja. Egy szabály gyűjteménye, amely egyforma sorrendben, valamint elsőbbségi szabályok. Szabálygyűjtemények prioritásuk szerinti sorrendben vannak végrehajtva. Hálózati szabálygyűjtemények magasabb prioritású, mint az alkalmazás szabálygyűjtemények, és az összes szabály is leáll.

Szabálygyűjtemény három típusa van:

* *Alkalmazás szabályok*: Állítsa be a teljes tartománynevek (FQDN), amely egy alhálózatról érhető el.
* *A hálózati szabályok*: Állítsa be a szabályt, amely tartalmazza a forrás-címek, protokollok, célportok és célcímek.
* *NAT-szabályok*: A bejövő kapcsolatok engedélyezéséhez DNAT-szabályok konfigurálása.

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure-tűzfal támogatja a bejövő forgalom szűrése?

Azure-tűzfalon támogatja a bejövő és kimenő szerinti szűrés konfigurálható. Bejövő protection egy nem HTTP/Https protokollokhoz. Például az RDP, SSH és FTP protokollt.

### <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Naplózás és elemzési szolgáltatásokat az Azure-tűzfal által támogatott?

Azure-tűzfalon megtekintésére és elemzésére tűzfalnaplók integrálva van az Azure Monitor szolgáltatással. Naplók a Log Analytics, az Azure Storage vagy az Event Hubs lehet küldeni. A Log Analytics vagy a különböző eszközök, például az Excel és a Power BI elemezhetők. További információkért lásd: [oktatóanyag: Azure tűzfalnaplók figyelése](../articles/firewall/tutorial-diagnostics.md).

### <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Azure-tűzfal működése eltér a meglévő szolgáltatások, például a piactéren az nva-k

Az Azure tűzfal egyszerű tűzfal szolgáltatása kezelheti az egyes felhasználói forgatókönyv. Valószínű, hogy külső nva-k és az Azure-tűzfal lesz. Jobban működnek együtt az alapvető prioritást.

### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Mi a különbség az Application Gateway WAF és az Azure-tűzfal?

A webalkalmazási tűzfal (WAF) az Application Gateway, amelyek a webalkalmazásoknak a gyakori támadások és biztonsági rések bejövő központi védelmet nyújt olyan szolgáltatása. Azure tűzfal bejövő védelmét nem HTTP/Https protokollok (például RDP, SSH, FTP), minden portok és protokollok a kimenő hálózati szintű védelmet és kimenő HTTP/s az alkalmazásszintű védelemhez biztosít

### <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Mi a különbség a hálózati biztonsági csoportok (NSG-k) és a tűzfal az Azure között?

Az Azure-tűzfal szolgáltatás egészíti ki a hálózati biztonsági csoport funkciót. Együtt biztosítanak a nagyobb "defense jellegű" hálózati biztonság. Hálózati biztonsági csoportok adja meg a hálózati réteg elosztott forgalom szűrése belül minden előfizetésben található virtuális hálózatok erőforrásokra irányuló forgalom korlátozásához. Az Azure tűzfal egy teljes mértékben állapotalapú, a központi hálózati tűzfal-szolgáltatásként, amely hálózati és az alkalmazás-szintű védelmet biztosít a különböző előfizetésekben találhatóak, és a virtuális hálózatok között.

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Hogyan állíthatok be Azure-tűzfal saját szolgáltatásvégpontokkal?

Biztonságos hozzáférés PaaS-szolgáltatások javasoljuk, hogy a Szolgáltatásvégpontok. Ha szeretné, engedélyezze a szolgáltatásvégpontokat az Azure-tűzfal alhálózat, és tiltsa le azokat a csatlakoztatott küllő virtuális hálózatokon. Ezzel a módszerrel, előnyös funkciók--service endpoint securityhez és a központi naplózás minden forgalom.

### <a name="what-is-the-pricing-for-azure-firewall"></a>Mi az Azure-tűzfal a díjszabása?

Azure tűzfal tartalmaz egy állandó költség + változó költség:

* Rögzített díj: $1.25/firewall/hour
* Változó díj: a tűzfal (bejövő vagy kimenő) által feldolgozott $ 0,03/GB

Nincsenek felszabadítva tűzfalak költségek.

További információkért lásd: [Azure tűzfal díjszabás](https://azure.microsoft.com/pricing/details/azure-firewall/).

### <a name="how-can-i-stop-and-start-azure-firewall"></a>Hogyan állítsa le és indítsa el az Azure-tűzfal?

Azure PowerShell használatával *felszabadítása* és *lefoglalni* módszereket.

Példa:

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
#Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Újra hozzá kell rendelnie egy tűzfal- és nyilvános IP-cím, az eredeti erőforráscsoportban és előfizetésben.

### <a name="what-are-the-known-service-limits"></a>Mik az ismert szolgáltatási korlátai?

A tűzfal az Azure-szolgáltatások korlátozásai, lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../articles/azure-subscription-service-limits.md#azure-firewall-limits)

### <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>A központi virtuális hálózaton előre Azure tűzfal is, és küllő virtuális hálózatok közötti hálózati forgalom szűrése?

Igen, az Azure tűzfal hub virtuális hálózatban két küllő virtuális hálózat közötti útvonal és a szűrő forgalom. Minden küllő virtuális hálózatok alhálózatokat megfelelően működjön az Azure-tűzfal mutató ebben a forgatókönyvben az alapértelmezett átjáróként UDR kell rendelkeznie.

### <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Azure-tűzfal előre is, és az azonos virtuális hálózatban vagy a társviszonyban álló virtuális hálózatba tartozó alhálózatok közötti hálózati forgalom szűrése?

Igen. Azonban ugyanazon virtuális Hálózatban lévő alhálózatok közötti forgalom átirányítása az udr-EK konfigurálása további figyelmet igényel. Használatakor a virtuális hálózati címtartományt, az UDR cél előtagjaként is elegendő, ez is továbbítja az egyik gépről egy másik gépre az Azure tűzfal-példány ugyanazon az alhálózaton minden forgalmat. Ennek elkerülése érdekében közé tartozik az alhálózat egy útvonalat az udr-t a következő ugrási típusú **VNET**. Ezeket az útvonalakat kezeléséhez gyakran fordul elő hiba, és nehézkes lehet. A belső hálózati szegmentálást ajánlott módszer, hogy hálózati biztonsági csoportok, amelyek nem igénylik az udr-EK.

### <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Kényszerítetten tunneling/láncolási egy hálózati virtuális berendezésre támogatott?

Igen.

Az Azure tűzfal közvetlen internetkapcsolattal kell rendelkeznie. Alapértelmezés szerint AzureFirewallSubnet egy 0.0.0.0/0 útvonalat rendelkezik a nexthoptype elem érték **Internet**.

Ha engedélyezi a kényszerített bújtatás ExpressRoute vagy VPN-átjáró használatával helyszíni, szükség lehet explicit módon egy 0.0.0.0/0 felhasználó által megadott útvonal (UDR) konfigurálja a nexthoptype elem értéke csoportot, az Internet, és társíthatja azt a AzureFirewallSubnet. Ez a művelet lehetséges alapértelmezett átjárót a helyszíni hálózat vissza a BGP hirdetésből. Ha a szervezet megköveteli, hogy az Azure-tűzfal alapértelmezett átjáró forgalmat a helyszíni hálózaton keresztül visszairányító kényszerített bújtatás, forduljon az ügyfélszolgálathoz. Azt is változatlan marad az előfizetés annak biztosítása érdekében az internetkapcsolat szükséges tűzfal engedélyezési listáján.

### <a name="are-there-any-firewall-resource-group-restrictions"></a>Vannak-e minden olyan tűzfal erőforrás-korlátozások csoportban?

Igen. A tűzfal, alhálózat, virtuális hálózat és a nyilvános IP-címet minden ugyanabban az erőforráscsoportban kell lennie.

### <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>A bejövő hálózati forgalom DNAT konfigurálásakor is szükséges e forgalom számára, hogy megfelelő hálózati szabály konfigurálása?

Nem. NAT-szabályok implicit módon adja hozzá a lefordított forgalmat engedélyező megfelelő hálózati szabály. Ezt a viselkedést felülírhatja, ha explicit módon hozzáad egy hálózatiszabály-készletet, amely megtagadja azokat a szabályokat, amelyek a lefordított adatforgalomhoz tartoznak. Az Azure Firewall szabályfeldolgozási logikájával kapcsolatos további információkért tekintse meg az [Azure Firewall szabályfeldolgozási logikájával](../articles/firewall/rule-processing.md) kapcsolatos cikket.

### <a name="how-to-wildcards-work-in-an-application-rule-target-fqdn"></a>A helyettesítő karakterek működése az egy alkalmazás szabály célja FQDN?

Ha konfigurálja ***. contoso.com**, lehetővé teszi *anyvalue*. contoso.com, de nem a contoso.com (domain pontjánál). Ha szeretné engedélyezni a domain pontjánál, explicit módon konfigurálnia kell azt FQDN célként.
