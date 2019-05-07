---
title: Azure-tűzfalon – gyakori kérdések
description: Az Azure-tűzfal – gyakori kérdések
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 5/3/2019
ms.author: victorh
ms.openlocfilehash: 4c4a6776e3bb56026a48963ec83fe582380c68d0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145958"
---
# <a name="azure-firewall-faq"></a>Azure-tűzfalon – gyakori kérdések

## <a name="what-is-azure-firewall"></a>Mi az Azure Firewall?

Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Egy teljes körűen állapot-nyilvántartó tűzfal--szolgáltatásként beépített magas rendelkezésre állás és a korlátlan felhőbeli méretezhetőséget. Központilag hozhatja létre, érvényesítheti és naplózhatja az alkalmazás- és hálózatelérési szabályzatokat az előfizetésekre és a virtuális hálózatokra vonatkozóan.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Azure tűzfal milyen funkciók támogatottak?

* Állapotalapú tűzfal mint szolgáltatás
* Beépített magas rendelkezésre állás korlátlan felhőbeli skálázással
* FQDN-szűrés
* FQDN-címkék
* Hálózati forgalomra vonatkozó szűrési szabályok
* Kimenő SNAT-támogatás
* Bejövő DNAT-támogatás
* Központilag létrehozása, érvényesítése, és jelentkezzen alkalmazás és a hálózati kapcsolat házirendek az Azure-előfizetések és a virtuális hálózatok között
* Teljes integráció az Azure Monitorral a naplózáshoz és az elemzéshez

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Mi az az Azure tűzfal a tipikus telepítési modell?

Azure tűzfal telepíthet minden olyan virtuális hálózaton, de az ügyfelek általában üzembe helyezni a központi virtuális hálózaton, és a Központ-küllő modellek hozzá más virtuális hálózatokat. Ezután beállíthatja az alapértelmezett útvonal, a társviszonyban álló virtuális hálózatba, mutasson a tűzfal központi virtuális hálózaton. Globális virtuális társhálózatok létesítésének támogatott, de nem ajánlott lehetséges teljesítményének és késési problémák miatt régiók között elosztva. A legjobb teljesítmény érdekében üzembe helyezése egy tűzfal régiónként.

Ez a modell előnye képesek központilag több küllő virtuális hálózatok különböző előfizetésekben vezérlőelem. Is találhatók költséget takaríthat meg, nem kell az egyes virtuális hálózatok tűzfal külön-külön telepíteni. A költségek csökkentését és a társítás társviszony-létesítési költséget, az ügyfél forgalmi minták alapján kell mérni.

## <a name="how-can-i-install-the-azure-firewall"></a>Hogyan telepíthetem az Azure-tűzfal?

Az Azure portal, PowerShell, REST API használatával, vagy sablonok használatával is beállíthatja az Azure-tűzfal. Lásd: [oktatóanyag: Telepítse és konfigurálja az Azure portal segítségével Azure tűzfal](tutorial-firewall-deploy-portal.md) részletes útmutatásait.

## <a name="what-are-some-azure-firewall-concepts"></a>Mik azok az Azure-tűzfal fogalmak?

Az Azure tűzfal szabályok és szabálygyűjtemények támogatja. Egy szabály gyűjteménye, amely egyforma sorrendben, valamint elsőbbségi szabályok. Szabálygyűjtemények prioritásuk szerinti sorrendben vannak végrehajtva. Hálózati szabálygyűjtemények magasabb prioritású, mint az alkalmazás szabálygyűjtemények, és az összes szabály is leáll.

Szabálygyűjtemény három típusa van:

* *Alkalmazás szabályok*: Állítsa be a teljes tartománynevek (FQDN), amely egy alhálózatról érhető el.
* *A hálózati szabályok*: Állítsa be a szabályt, amely tartalmazza a forrás-címek, protokollok, célportok és célcímek.
* *NAT-szabályok*: A bejövő kapcsolatok engedélyezéséhez DNAT-szabályok konfigurálása.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure-tűzfal támogatja a bejövő forgalom szűrése?

Azure-tűzfalon támogatja a bejövő és kimenő szerinti szűrés konfigurálható. Bejövő protection egy nem HTTP/Https protokollokhoz. Például az RDP, SSH és FTP protokollt.

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Naplózás és elemzési szolgáltatásokat az Azure-tűzfal által támogatott?

Azure-tűzfalon megtekintésére és elemzésére tűzfalnaplók integrálva van az Azure Monitor szolgáltatással. Naplók a Log Analytics, az Azure Storage vagy az Event Hubs lehet küldeni. A Log Analytics vagy a különböző eszközök, például az Excel és a Power BI elemezhetők. További információkért lásd: [oktatóanyag: Azure tűzfalnaplók figyelése](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Azure-tűzfal működése eltér a meglévő szolgáltatások, például a piactéren az nva-k

Az Azure tűzfal egyszerű tűzfal szolgáltatása kezelheti az egyes felhasználói forgatókönyv. Valószínű, hogy külső nva-k és az Azure-tűzfal lesz. Jobban működnek együtt az alapvető prioritást.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Mi a különbség az Application Gateway WAF és az Azure-tűzfal?

A webalkalmazási tűzfal (WAF) az Application Gateway, amelyek a webalkalmazásoknak a gyakori támadások és biztonsági rések bejövő központi védelmet nyújt olyan szolgáltatása. Azure tűzfal bejövő védelmét nem HTTP/Https protokollok (például RDP, SSH, FTP), minden portok és protokollok a kimenő hálózati szintű védelmet és kimenő HTTP/s az alkalmazásszintű védelemhez biztosít

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Mi a különbség a hálózati biztonsági csoportok (NSG-k) és a tűzfal az Azure között?

Az Azure-tűzfal szolgáltatás egészíti ki a hálózati biztonsági csoport funkciót. Együtt biztosítanak a nagyobb "defense jellegű" hálózati biztonság. Hálózati biztonsági csoportok adja meg a hálózati réteg elosztott forgalom szűrése belül minden előfizetésben található virtuális hálózatok erőforrásokra irányuló forgalom korlátozásához. Az Azure tűzfal egy teljes mértékben állapotalapú, a központi hálózati tűzfal-szolgáltatásként, amely hálózati és az alkalmazás-szintű védelmet biztosít a különböző előfizetésekben találhatóak, és a virtuális hálózatok között.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Hogyan állíthatok be Azure-tűzfal saját szolgáltatásvégpontokkal?

Biztonságos hozzáférés PaaS-szolgáltatások javasoljuk, hogy a Szolgáltatásvégpontok. Ha szeretné, engedélyezze a szolgáltatásvégpontokat az Azure-tűzfal alhálózat, és tiltsa le azokat a csatlakoztatott küllő virtuális hálózatokon. Ezzel a módszerrel, előnyös funkciók--service endpoint securityhez és a központi naplózás minden forgalom.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Mi az Azure-tűzfal a díjszabása?

Lásd: [Azure tűzfal díjszabás](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Hogyan állítsa le és indítsa el az Azure-tűzfal?

Azure PowerShell használatával *felszabadítása* és *lefoglalni* módszereket.

Példa:

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Újra hozzá kell rendelnie egy tűzfal- és nyilvános IP-cím, az eredeti erőforráscsoportban és előfizetésben.

## <a name="what-are-the-known-service-limits"></a>Mik az ismert szolgáltatási korlátai?

A tűzfal az Azure-szolgáltatások korlátozásai, lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>A központi virtuális hálózaton előre Azure tűzfal is, és küllő virtuális hálózatok közötti hálózati forgalom szűrése?

Igen, az Azure tűzfal hub virtuális hálózatban két küllő virtuális hálózat közötti útvonal és a szűrő forgalom. Minden küllő virtuális hálózatok alhálózatokat megfelelően működjön az Azure-tűzfal mutató ebben a forgatókönyvben az alapértelmezett átjáróként UDR kell rendelkeznie.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Azure-tűzfal előre is, és az azonos virtuális hálózatban vagy a társviszonyban álló virtuális hálózatba tartozó alhálózatok közötti hálózati forgalom szűrése?

Igen. Azonban ugyanazon virtuális Hálózatban lévő alhálózatok közötti forgalom átirányítása az udr-EK konfigurálása további figyelmet igényel. Használatakor a virtuális hálózati címtartományt, az UDR cél előtagjaként is elegendő, ez is továbbítja az egyik gépről egy másik gépre az Azure tűzfal-példány ugyanazon az alhálózaton minden forgalmat. Ennek elkerülése érdekében közé tartozik az alhálózat egy útvonalat az udr-t a következő ugrási típusú **VNET**. Ezeket az útvonalakat kezeléséhez gyakran fordul elő hiba, és nehézkes lehet. A belső hálózati szegmentálást ajánlott módszer, hogy hálózati biztonsági csoportok, amelyek nem igénylik az udr-EK.

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Kényszerítetten tunneling/láncolási egy hálózati virtuális berendezésre támogatott?

Kényszerített bújtatás nem támogatott alapértelmezés szerint, de engedélyezhető támogatási segítségével.

Az Azure tűzfal közvetlen internetkapcsolattal kell rendelkeznie. A AzureFirewallSubnet megtanulja az alapértelmezett útvonalat a BGP-n keresztül a helyszíni hálózathoz, ha meg kell felülbírálja ezt az egy 0.0.0.0/0 UDR-a **NextHopType** értéket állítja be **Internet** közvetlen fenntartása Internetkapcsolat. Alapértelmezés szerint az Azure-tűzfal nem támogatja a kényszerített bújtatás a helyszíni hálózathoz.

Azonban ha a konfigurációt igényel a helyszíni hálózathoz a kényszerített bújtatás, a Microsoft támogatja eseti alapon. Úgy, hogy az eset, forduljon az ügyfélszolgálathoz. Ha elfogadja, hogy lesz engedélyezett az előfizetés, és biztosítják a szükséges internetkapcsolattal.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Vannak-e minden olyan tűzfal erőforrás-korlátozások csoportban?

Igen. A tűzfal, alhálózat, virtuális hálózat és a nyilvános IP-címet minden ugyanabban az erőforráscsoportban kell lennie.

## <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>A bejövő hálózati forgalom DNAT konfigurálásakor is szükséges e forgalom számára, hogy megfelelő hálózati szabály konfigurálása?

Nem. NAT-szabályok implicit módon adja hozzá a lefordított forgalmat engedélyező megfelelő hálózati szabály. Ezt a viselkedést felülírhatja, ha explicit módon hozzáad egy hálózatiszabály-készletet, amely megtagadja azokat a szabályokat, amelyek a lefordított adatforgalomhoz tartoznak. Az Azure Firewall szabályfeldolgozási logikájával kapcsolatos további információkért tekintse meg az [Azure Firewall szabályfeldolgozási logikájával](rule-processing.md) kapcsolatos cikket.

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Hogyan működnek a helyettesítő karakterek egy alkalmazás a szabály TARGET FQDN?

Ha konfigurálja ***. contoso.com**, lehetővé teszi *anyvalue*. contoso.com, de nem a contoso.com (domain pontjánál). Ha szeretné engedélyezni a domain pontjánál, explicit módon konfigurálnia kell azt FQDN célként.

## <a name="what-does-provisioning-state-failed-mean"></a>Mire *kiépítési állapota: Nem sikerült* mean?

Minden alkalommal, amikor egy konfigurációmódosítás van érvényben, Azure tűzfal megkísérli az alapul szolgáló háttérbeli példányok frissítése. Bizonyos ritkán előforduló esetekben ezek a háttér-példányok egyik lehetséges, hogy nem lehet frissíteni az új konfigurációval, és a frissítési folyamat leáll, az üzembe helyezési állapota sikertelen. Az Azure-tűzfal továbbra is működik, de az alkalmazott konfiguráció inkonzisztens állapotban, ahol néhány példány az előző konfigurációval rendelkezik, a frissített szabálykészlet mások. Ha ez történik, próbálja meg a konfiguráció még egyszer mindaddig, amíg a művelet sikeres, és a tűzfal frissítése egy *sikeres* üzembe helyezési állapota.
