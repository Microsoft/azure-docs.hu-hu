---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b7f79bebce5a086b268f4fc1080c33517555fb39
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431540"
---
### <a name="is-azure-virtual-wan-in-ga"></a>Az Azure Virtual WAN a GA-ban található?

Igen, az Azure Virtual WAN általánosan elérhető (GA). A virtuális WAN azonban számos funkciót és forgatókönyvet tartalmaz. A virtuális WAN-on belül olyan funkciók vagy forgatókönyvek találhatók, amelyekben a Microsoft az előnézet címkét alkalmazza. Ezekben az esetekben az adott funkció vagy maga a forgatókönyv előzetes verzióban érhető el. Ha nem használ konkrét előzetes verziójú szolgáltatást, a rendszer a normál GA-támogatást alkalmazza. Az előzetes verzió támogatásával kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Szükség van a felhasználónak a hub és az SD-WAN/VPN-eszközök használatára az Azure Virtual WAN használatához?

A Virtual WAN számos funkciót kínál az olyan üvegek számára, mint például a hely/helyek közötti VPN-kapcsolat, a felhasználó/P2S kapcsolat, a ExpressRoute-kapcsolat, a Virtual Network kapcsolat, a VPN-ExpressRoute összekapcsolása, a VNet-VNet tranzitív kapcsolat, a központosított útválasztás, a Azure Firewall és a tűzfal-kezelő biztonság, a monitorozás, a ExpressRoute-titkosítás és sok más képesség. A virtuális WAN használatának megkezdéséhez nem szükséges az összes ilyen használati eset. Első lépésként csak egy használati esetet érhet el.

A virtuális WAN-architektúra egy olyan sugaras és küllős architektúra, amely az ágak (VPN/SD-WAN-eszközök), a felhasználók (Azure VPN-ügyfelek, openVPN vagy IKEv2-ügyfelek), a ExpressRoute-áramkörök, a virtuális hálózatok pedig küllőként szolgálnak a virtuális központ (ok) hoz. Az összes hub egy szabványos virtuális WAN-kapcsolaton keresztül csatlakozik a teljes hálóhoz, így a felhasználó könnyedén használhatja a Microsoft-gerincet bármilyen (küllős) kapcsolathoz. A hub és az SD-WAN/VPN-eszközök használata esetén a felhasználók manuálisan is beállíthatják az Azure-beli virtuális WAN-portálon, vagy a virtuális WAN-partner CPE (SD-WAN/VPN) használatával állíthatnak be kapcsolatot az Azure-ral.

A virtuális WAN-partnerek automatizálást biztosítanak a kapcsolódáshoz, amely lehetővé teszi az eszköz adatainak az Azure-ba való exportálását, az Azure-konfiguráció letöltését és az Azure Virtual WAN hub-kapcsolat létesítését. Pont – hely/felhasználó VPN-kapcsolat esetén az [Azure VPN Client](https://go.microsoft.com/fwlink/?linkid=2117554), az OpenVPN vagy a IKEv2-ügyfél támogatott.

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>Le tudja tiltani a teljes mértékben Rácsvonalas hubokat egy virtuális WAN-ban?

A Virtual WAN két változatban érhető el: alapszintű és standard. Alapszintű virtuális WAN esetén a hubok nincsenek rácsvonalak. A standard szintű virtuális WAN-ban a hubok a virtuális WAN első beállításakor vannak elfoglalva és automatikusan csatlakoztatva. A felhasználónak semmit nem kell tennie. A felhasználónak emellett nem kell letiltania vagy engedélyeznie a hálós hubok beszerzéséhez szükséges funkciót. A Virtual WAN számos útválasztási lehetőséget biztosít a bármilyen küllős (VNet, VPN vagy ExpressRoute) közötti forgalom irányítására. A szolgáltatás egyszerűvé teszi a teljes szembőségű hubokat, valamint az igényeinek megfelelő útválasztási forgalom rugalmasságát is.

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>Hogyan kezelik a Availability Zones és a rugalmasságot a virtuális WAN-ban?

A virtuális WAN az elosztón belül elérhetővé tett hubok és szolgáltatások gyűjteménye. A felhasználónak tetszőleges számú virtuális WAN-ra van szüksége. Egy virtuális WAN-központban több szolgáltatás, például VPN, ExpressRoute stb. Az egyes szolgáltatások (kivéve a Azure Firewall) egy Availability Zones régióban vannak telepítve, vagyis ha a régió támogatja a Availability Zones. Ha egy régió elérhetővé válik egy rendelkezésre állási zónában a központi telepítés után, akkor a felhasználó újra létrehozhatja az átjárókat, amelyek kiváltják a rendelkezésre állási zónák központi telepítését. Az összes átjáró aktív-aktívként van kiépítve egy hubhoz, ami azt jelenti, hogy a csomóponton belül van egy rugalmasság. A felhasználók több hubhoz is csatlakozhatnak, ha rugalmasságot szeretnének a régiók között.

Habár a virtuális WAN koncepciója globális, a tényleges virtuális WAN-erőforrás Resource Manager-alapú, és regionálisan van üzembe helyezve. Ha maga a virtuális WAN-régióval kapcsolatos probléma merült fel, a virtuális WAN összes központja továbbra is működni fog, de a felhasználó nem fog tudni új hubokat létrehozni, amíg a virtuális WAN-régió elérhetővé nem válik.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Milyen ügyfelet támogat az Azure Virtual WAN felhasználói VPN (pont – hely)?

A Virtual WAN támogatja az [Azure VPN-ügyfelet](https://go.microsoft.com/fwlink/?linkid=2117554), az OpenVPN-ügyfelet vagy bármely IKEv2-ügyfelet. Az Azure AD-hitelesítés támogatott az Azure VPN-ügyfélen. legalább a Windows 10 ügyfél operációs rendszer 17763,0-es vagy újabb verziójára van szükség.  Az OpenVPN-ügyfél képes támogatni a tanúsítványalapú hitelesítést. Miután kiválasztotta a tanúsítvány alapú hitelesítést az átjárón, megjelenik az eszközre letölteni kívánt. ovpn * fájl. A IKEv2 mind a tanúsítvány-, mind a RADIUS-hitelesítést támogatja. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Felhasználói VPN (pont – hely) esetén – miért oszlik két útvonalra a P2S-ügyfél készlete?

Minden átjáró két példánnyal rendelkezik, a felosztás úgy történik, hogy az egyes átjáró-példányok egymástól függetlenül lefoglalhatják az ügyfél IP-címeit a csatlakoztatott ügyfelek számára, és a virtuális hálózatról érkező forgalom a megfelelő átjáró-példányra kerül vissza, hogy elkerülje az átjárók közötti példányok ugrását.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Hogyan DNS-kiszolgálókat hozzáadni a P2S-ügyfelekhez?

A P2S-ügyfelekhez két lehetőség van a DNS-kiszolgálók hozzáadására. Az első módszer a javasolt, mivel az ügyfél helyett az egyéni DNS-kiszolgálókat adja hozzá az átjáróhoz.

1. Használja az alábbi PowerShell-szkriptet az egyéni DNS-kiszolgálók hozzáadásához. Cserélje le a környezete értékeit.

   ```powershell
   // Define variables
   $rgName = "testRG1"
   $virtualHubName = "virtualHub1"
   $P2SvpnGatewayName = "testP2SVpnGateway1"
   $vpnClientAddressSpaces = 
   $vpnServerConfiguration1Name = "vpnServerConfig1"
   $vpnClientAddressSpaces = New-Object string[] 2
   $vpnClientAddressSpaces[0] = "192.168.2.0/24"
   $vpnClientAddressSpaces[1] = "192.168.3.0/24"
   $customDnsServers = New-Object string[] 2
   $customDnsServers[0] = "7.7.7.7"
   $customDnsServers[1] = "8.8.8.8"
   $virtualHub = $virtualHub = Get-AzVirtualHub -ResourceGroupName $rgName -Name $virtualHubName
   $vpnServerConfig1 = Get-AzVpnServerConfiguration -ResourceGroupName $rgName -Name $vpnServerConfiguration1Name

   // Specify custom dns servers for P2SVpnGateway VirtualHub while creating gateway
   createdP2SVpnGateway = New-AzP2sVpnGateway -ResourceGroupName $rgname -Name $P2SvpnGatewayName -VirtualHub $virtualHub -VpnGatewayScaleUnit 1 -VpnClientAddressPool $vpnClientAddressSpaces -VpnServerConfiguration $vpnServerConfig1 -CustomDnsServer $customDnsServers

   // Specify custom dns servers for P2SVpnGateway VirtualHub while updating existing gateway
   $P2SVpnGateway = Get-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName
   $updatedP2SVpnGateway = Update-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName  -CustomDnsServer $customDnsServers 

   // Re-generate Vpn profile either from PS/Portal for Vpn clients to have the specified dns servers
   ```
2. Ha a Windows 10 rendszerhez készült Azure VPN-ügyfelet használja, akkor a letöltött profil XML-fájlját is módosíthatja, **\<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers>** és az importálás előtt hozzáadhatja a címkéket.

   ```powershell
      <azvpnprofile>
      <clientconfig>

          <dnsservers>
              <dnsserver>x.x.x.x</dnsserver>
              <dnsserver>y.y.y.y</dnsserver>
          </dnsservers>
    
      </clientconfig>
      </azvpnprofile>
   ```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>Felhasználói VPN (pont – hely) esetén – hány ügyfél támogatott?

Minden felhasználói VPN P2S-átjáró két példánnyal rendelkezik. Az egyes példányok bizonyos számú kapcsolatot támogatnak a skálázási egység változásainál. A 1-3-es méretezési egység támogatja a 500-kapcsolatokat, a skálázási egység 4-6 támogatja a 1000 kapcsolatokat, a Scale Unit 7-12 támogatja az 5000-kapcsolatokat, és a Scale Unit 13-18 támogatja a legfeljebb 10 000 kapcsolatot.

Tegyük fel például, hogy a felhasználó 1 méretezési egységet választ. Mindegyik skálázási egység egy aktív-aktív átjáró üzembe helyezését jelentené, és a példányok mindegyike (ebben az esetben 2) legfeljebb 500 kapcsolatot támogat. Mivel az 500-os kapcsolatok * 2 átjárón keresztül, nem jelenti azt, hogy az 500 helyett 1000-et tervez ehhez a skálázási egységhez. Előfordulhat, hogy olyan példányokat kell kiszolgálni, amelyekben az extra 500-kapcsolat megszakad, ha túllépi az ajánlott kapcsolatok számát. Ügyeljen arra is, hogy az állásidőt úgy tervezze meg, hogy a méretezési egység vertikális fel-vagy leskálázását választja, vagy ha módosítani szeretné a pont – hely konfigurációt a VPN-átjárón.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Mi a különbség az Azure Virtual Network Gateway (VPN Gateway) és egy Azure virtuális WAN VPN Gateway között?

A Virtual WAN nagy léptékben biztosít helyek közötti kapcsolatokat, és kifejlesztése során elsősorban az átviteli sebességet, a méretezhetőséget és a könnyű használatot tartották szem előtt. Ha egy helyet egy virtuális WAN VPN-átjáróhoz kapcsolódik, az különbözik a VPN-átjárót használó normál virtuális hálózati átjárótól. Hasonlóképpen, amikor egy ExpressRoute-áramkört csatlakoztat egy virtuális WAN-központhoz, az ExpressRoute-átjáróhoz egy másik erőforrást használ, mint a "ExpressRoute" típusú átjárót használó normál virtuális hálózati átjáró. 

A virtuális WAN a VPN-és a ExpressRoute esetében akár 20 GB/s összesített átviteli sebességet is támogat. A Virtual WAN a CPE ág-eszközökhöz tartozó partnerekkel való kapcsolat automatizálásával is rendelkezik. A CPE ág eszközei beépített automatizálással rendelkeznek, amely automatikusan kiépíti és csatlakozik az Azure Virtual WAN-hoz. Ezek az eszközök az SD-WAN- és a VPN-partnerek egyre bővülő körétől szerezhetők be. Tekintse meg az [előnyben részesített partnerek listáját](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Miben különbözik a virtuális WAN egy Azure-beli virtuális hálózati átjárótól?

A virtuális hálózati átjáró VPN-je legfeljebb 30 alagútra van korlátozva. Kapcsolatokhoz nagy mennyiségű VPN-forgalmat bonyolító Virtual WAN használata javasolt. Régiónként (Virtual hub) akár 1 000 ág-kapcsolatot is csatlakoztathat, és a központ 20 GB/s-os összesítéssel rendelkezik. A kapcsolatok aktív-aktív alagútnak minősülnek a helyszíni VPN-eszköz és a virtuális központ között. Régiónként egy hubhoz is rendelkezhet, ami azt jelenti, hogy több mint 1 000 ágat tud csatlakoztatni a hubok között.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Mi az a virtuális WAN Gateway skálázási egység?

A méretezési egység egy olyan egység, amely egy átjáró összesített átviteli sebességének kiválasztására van beállítva a virtuális központban. 1 a VPN = 500 Mbps méretezési egysége. 1 skálázási egység, ExpressRoute = 2 GB/s. Például: a VPN 10 skálázási egysége 500 Mbps * 10 = 5 GB/s értéket jelentene.

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Mely szolgáltatók (virtuális WAN-partnerek) támogatottak?

Mostanra már számos partner támogatja a teljesen automatizált Virtual WAN-t. További információ: [Virtual WAN-partnerek](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Melyek a Virtual WAN-partnerek automatizálásának lépései?

A partnerek automatizálásának lépéseiért tekintse át [a Virtual WAN-partnerek automatizálásával](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) foglalkozó részt.

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Kötelező egy adott partner eszközét használnom?

Nem. Bármely olyan VPN-kompatibilis eszközt használhat, amely megfelel az Azure IKEv2/IKEv1 IPsec-támogatásra vonatkozó követelményeinek. A Virtual WAN olyan CPE-partneri megoldásokat is tartalmaz, amelyek automatizálják az Azure Virtual WAN-kapcsolatait, így könnyebben állíthatók be az IPsec VPN-kapcsolatok a skálán.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hogyan csatlakozhatnak a Virtual WAN-partnerek automatikusan az Azure Virtual WAN-hoz?

A szoftveralapú csatlakozási megoldások jellemzően vezérlővel vagy eszközkiépítési központtal kezelik a kompatibilis eszközöket. A vezérlők Azure API-kat is használhatnak az Azure Virtual WAN-hoz való automatikus csatlakozáshoz. Az Automation magában foglalja a fiókirodák feltöltését, az Azure-konfiguráció letöltését, az IPsec-alagutak Azure-beli virtuális hubokba való beállítását, valamint a kapcsolat automatikus beállítását az Azure Virtual WAN-hoz. Ha több száz ága van, a virtuális WAN CPE-partnerekkel való csatlakozás egyszerű, mivel a bevezetési élmény eltartja a nagyméretű IPsec-kapcsolatok beállításának, konfigurálásának és felügyeletének szükségességét. További információ: [Virtual WAN partner Automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Mi a teendő, ha a virtuális WAN-partnerek listáján nem szerepel a használni kívánt eszköz? Használhatom továbbra is az Azure Virtual WAN VPN-hez való csatlakozáshoz?

Igen, amíg az eszköz támogatja az IPsec-IKEv1 vagy a IKEv2. A virtuális WAN-partnerek automatizálják az eszközről az Azure VPN-végpontokhoz való kapcsolódást. Ez olyan lépéseket tesz szükségessé, mint például az "ág-adatok feltöltése", az "IPsec és a konfiguráció" és a "kapcsolat". Mivel az eszköz nem virtuális WAN-partneri ökoszisztémából származik, az Azure-konfiguráció manuális megkezdése és az eszköz az IPsec-kapcsolat beállításához való frissítése után kell megtennie a nagy terhelést.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hogyan készülhetnek elő a szolgáltatásra a meglévő partnerek listáján még nem szereplő új partnerek?

Minden virtuális WAN API nyitott API. A technikai megvalósíthatóság értékeléséhez áttekintheti a [Virtual WAN partner Automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) dokumentációját. Ideális esetben a partner olyan eszközzel rendelkezik, amely támogatja az IKEv1 vagy az IKEv2 IPsec-kapcsolatot. Miután a vállalat befejezte a CPE-eszközre vonatkozó automatizálási munkát a fent ismertetett automatizálási irányelvek alapján, elérheti a azurevirtualwan@microsoft.com [partnereken keresztüli kapcsolatait]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners). Ha Ön olyan ügyfél, amely szeretné, hogy egy adott vállalati megoldás virtuális WAN-partnerként jelenjen meg, a vállalat a következő e-mail címre küldi a kapcsolatot a virtuális HÁLÓZATtal: azurevirtualwan@microsoft.com .

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Hogyan támogatja a virtuális WAN az SD-WAN-eszközöket?

A virtuális WAN-partnerek automatizálják az IPsec-kapcsolatot az Azure VPN-végpontokkal. Ha a virtuális WAN-partner egy SD-WAN-szolgáltató, akkor azt feltételezi, hogy az SD-WAN-vezérlő az Azure VPN-végpontok automatizálását és IPsec-kapcsolatait kezeli. Ha az SD-WAN-eszköz saját végpontot igényel az Azure VPN helyett a tulajdonosi SD-WAN-funkciókhoz, akkor az SD-WAN végpontot üzembe helyezheti egy Azure-VNet, és az Azure Virtual WAN-ral együtt is létezhet.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Hány VPN-eszköz tud csatlakozni egyetlen hubhoz?

A virtuális központ legfeljebb 1 000 kapcsolatot támogat. Minden kapcsolat négy kapcsolatból áll, és az egyes kapcsolati kapcsolatok két, aktív-aktív konfigurációban lévő alagutat támogatnak. Az alagutak egy Azure-beli virtuális központ VPN-átjárójában leállnak. A hivatkozások az ág/VPN-eszköz fizikai ISP-hivatkozását jelölik.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Mi az az Azure Virtual WAN-hoz kapcsolódó fiókiroda?

Egy fiókirodából vagy VPN-eszközről az Azure Virtual WAN-ba létesített kapcsolat egy VPN-kapcsolat, amely gyakorlatilag a VPN-helyet és az Azure-VPN Gateway csatlakoztatja egy virtuális hubhoz.

### <a name="what-happens-if-the-on-premises-vpn-device-only-has-1-tunnel-to-an-azure-virtual-wan-vpn-gateway"></a>Mi történik, ha a helyszíni VPN-eszköznek csak 1 alagútja van egy Azure-beli virtuális WAN VPN-átjáróhoz?

Az Azure-beli virtuális WAN-kapcsolat 2 alagútból áll. Egy virtuális WAN VPN-átjáró üzembe helyezése aktív-aktív módban lévő virtuális központban történik, ami azt jelenti, hogy külön alagutak találhatók a helyszíni eszközöktől a különálló példányokon. Ez az összes felhasználóra vonatkozó javaslat. Ha azonban a felhasználó úgy dönt, hogy csak 1 alagúttal rendelkezik az egyik virtuális WAN VPN Gateway-példányhoz, ha valamilyen okból kifolyólag (karbantartás, javítás stb.) az átjáró példánya offline állapotba kerül, az alagút át lesz helyezve a másodlagos aktív példányra, és a felhasználó újracsatlakozást tapasztalhat. A BGP-munkamenetek nem kerülnek át a példányokra.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Csatlakozhat a helyszíni VPN-eszköz több hubhoz is?

Igen. A forgalom a megkezdése után a helyszíni eszközről a legközelebbi Microsoft hálózati szélre, majd a virtuális hubhoz kerül.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Elérhetővé válnak új Resource Manager-erőforrások a Virtual WAN-hoz?
  
Igen, a Virtual WAN új Resource Manager-erőforrásokkal rendelkezik. További információ: [Áttekintés](../articles/virtual-wan/virtual-wan-about.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Az Azure Virtual WAN-on is üzembe helyezhetem és használhatom kedvenc hálózati virtuális készülékemet (NVA virtuális hálózaton)?

Igen, csatlakoztathatja kedvenc hálózati virtuális berendezése (NVA-ja) virtuális hálózatát az Azure Virtual WAN-hoz.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Létrehozhatok hálózati virtuális készüléket a virtuális központban?

Egy hálózati virtuális berendezés (NVA) nem helyezhető üzembe egy virtuális központon belül. Azonban létrehozhatja azt egy küllős VNet, amely a virtuális hubhoz csatlakozik, és lehetővé teszi a megfelelő útválasztást az igényeinek megfelelő közvetlen forgalomhoz.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Lehet küllős VNet virtuális hálózati átjáróval?

Nem. A küllős VNet nem rendelkezhet virtuális hálózati átjáróval, ha a virtuális hubhoz csatlakozik.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>Támogatja a BGP-t a VPN-kapcsolaton?

Igen, a BGP támogatott. VPN-hely létrehozásakor megadhatja a BGP-paramétereket. Ez azt jelenti, hogy az Azure-ban az adott helyhez létrehozott összes kapcsolat engedélyezve lesz a BGP-hez.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Milyen licenc- vagy díjszabási információ érhető el a Virtual WAN-ról?

Igen. Lásd a [Díjszabás](https://azure.microsoft.com/pricing/details/virtual-wan/) oldalt.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Lehetséges Azure Virtual WAN létrehozása Resource Manager-sablon használatával?

Egy virtuális WAN egyszerű konfigurációja egy hubhoz, egy vpnsite pedig egy rövid útmutató [sablon](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network)használatával hozható létre. A Virtual WAN elsősorban a REST vagy a portálon alapuló szolgáltatás.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Megbeszélheti a virtuális hálózatok csatlakoztatott virtuális hubhoz való kommunikációt (V2V-átvitel)?

Igen. A standard szintű virtuális WAN támogatja a VNet-VNet tranzitív kapcsolatot a virtuális WAN-hubhoz, amelyhez a virtuális hálózatok csatlakozik. A virtuális WAN-terminológiában ezeket az útvonalakat "helyi virtuális WAN VNet Transit"-ként tekintjük át egyetlen régióban lévő virtuális WAN-központhoz csatlakoztatott virtuális hálózatok, és a "globális virtuális WAN VNet Transit" a több virtuális WAN-hubhoz csatlakoztatott virtuális hálózatok több, mint két régióban.

Bizonyos helyzetekben a küllős virtuális hálózatok a helyi vagy globális virtuális WAN VNet-átvitelen kívül a [virtuális hálózatok](../articles/virtual-network/virtual-network-peering-overview.md) közötti kapcsolattal is elvégezhető. Ebben az esetben a VNet-társítás a virtuális WAN-központon keresztül elsőbbséget élvez a tranzitív kapcsolattal szemben.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A Virtual WAN-ban engedélyezett az ágak közötti kapcsolat?

Igen, az ágak közötti kapcsolat elérhető a Virtual WAN-ban. Az ág fogalmilag alkalmazható a VPN-webhelyekre, a ExpressRoute áramkörökre, illetve a pont – hely/felhasználó VPN-felhasználókra. A ág – ág engedélyezése alapértelmezés szerint engedélyezve van, és a WAN- **konfigurációs** beállításokban található. Ez lehetővé teszi, hogy a VPN-ágak/felhasználók csatlakozzanak más VPN-ágakhoz, és az átviteli kapcsolat is engedélyezve legyen a VPN-és ExpressRoute-felhasználók között.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Az ág-ág forgalom áthalad az Azure Virtual WAN-on keresztül?

Igen. A fiókirodai forgalom az Azure Virtual WAN használatával halad át.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Szükséges a virtuális WAN ExpressRoute az egyes helyekről?

Nem. A virtuális WAN nem igényel ExpressRoute az egyes helyekről. Előfordulhat, hogy a helyek ExpressRoute-kapcsolatcsoporttal csatlakoznak a szolgáltatói hálózathoz. Azon helyek esetében, amelyek a ExpressRoute használatával csatlakoznak egy virtuális hubhoz és az IPsec VPN-hez ugyanahhoz a központhoz, a virtuális központ átviteli kapcsolatot biztosít a VPN és a ExpressRoute felhasználó között.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Van hálózati átviteli sebesség vagy kapcsolati korlát az Azure Virtual WAN használata esetén?

A hálózati átviteli sebesség szolgáltatásként egy virtuális WAN-központban történik. Habár több virtuális WAN-t is használhat, minden egyes virtuális WAN régiónként 1 hub-t tesz lehetővé. Az egyes központokban a VPN aggregált átviteli sebessége akár 20 GB/s is lehet, a ExpressRoute összesített átviteli sebessége akár 20 GB/s is lehet, és a felhasználó VPN/pont – hely közötti VPN aggregált átviteli sebessége akár 20 GB/s is lehet. A Virtual hub útválasztója akár 50 GB/s-ot is támogat a VNet forgalom VNet, és összesen 2000 virtuális gép számítási feladatát feltételezi az összes olyan virtuális hálózatok, amely egyetlen virtuális hubhoz csatlakozik.

Amikor a VPN-helyek egy központhoz csatlakoznak, a kapcsolatokkal. A Virtual WAN a virtuális hubhoz legfeljebb 1000 kapcsolatot vagy 2000 IPsec-alagutat támogat. Amikor a távoli felhasználók csatlakoznak a virtuális központhoz, csatlakoznak a P2S VPN-átjáróhoz, amely legfeljebb 10 000 felhasználót támogat, attól függően, hogy milyen méretezési egység (sávszélesség) van kiválasztva a virtuális központ P2S VPN-átjárója számára.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Mi a VPN-alagút és a kapcsolat teljes VPN-átviteli sebessége?

A hub teljes VPN-átviteli sebessége a VPN-átjáró kiválasztott méretezési egysége alapján akár 20 GB-ig is elsajátítható. Az átviteli sebességet az összes meglévő kapcsolat megosztja. A kapcsolatok mindegyik alagútja akár 1 GB/s-t is képes támogatni.

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>Használhatom a NAT-T-T a VPN-kapcsolatokon?

Igen, a NAT-bejárás (NAT-T) támogatott. A virtuális WAN VPN Gateway nem hajtja végre a NAT-hoz hasonló funkciókat a belső csomagokon az IPsec-alagutak között. Ebben a konfigurációban győződjön meg arról, hogy a helyszíni eszköz kezdeményezi az IPsec-alagutat.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>Nem látom a 20 GB/s beállítást a virtuális hubhoz a portálon. Hogyan konfigurálja?

Navigáljon a VPN-átjáróhoz a portálon belül, majd a méretezési egységre kattintva módosítsa a megfelelő beállítást.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>A virtuális WAN lehetővé teszi, hogy a helyszíni eszköz párhuzamosan több internetszolgáltatót is használjon, vagy mindig egyetlen VPN-alagút?

A helyszíni eszközökön a forgalmi szabályzatok alkalmazhatók arra, hogy több alagúton keresztül irányítsák a forgalmat az Azure virtuális WAN-hubhoz (a virtuális központ VPN-átjárója).

### <a name="what-is-global-transit-architecture"></a>Mi az a globális átviteli architektúra?

A globális átviteli architektúrával kapcsolatos információkért lásd: a [globális tranzit hálózati architektúra és a virtuális WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Hogyan történik a forgalom átirányítása az Azure gerinchálózatára?

A forgalom a következő mintát követi: ág Device->ISP->Microsoft Network Edge->Microsoft DC (hub VNet) – >Microsoft Network Edge->ISP->ág eszköz

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Ebben a modellben mire van szükség az egyes helyek esetében? Csupán internetkapcsolatra?

Igen. Internetkapcsolat és fizikai eszköz, amely támogatja az IPsec használatát, lehetőleg az integrált [virtuális WAN-partnereinktől](../articles/virtual-wan/virtual-wan-locations-partners.md). Szükség esetén manuálisan is kezelheti a konfigurációt és a kapcsolatot az Azure-hoz a kívánt eszközről.

### <a name="how-do-i-enable-default-route-00000-for-a-connection-vpn-expressroute-or-virtual-network"></a>Hogyan az alapértelmezett útvonal (0.0.0.0/0) engedélyezése egy kapcsolathoz (VPN, ExpressRoute vagy Virtual Network)?

Egy virtuális központ propagálhatja a megtanult alapértelmezett útvonalat egy virtuális hálózat/helyek közötti VPN/ExpressRoute kapcsolatra, ha a jelző "enabled" (engedélyezve) van a kapcsolaton. Ez a jelző akkor látható, ha a felhasználó szerkeszt egy virtuális hálózati kapcsolat, egy VPN-kapcsolat vagy egy ExpressRoute-kapcsolat. Alapértelmezés szerint ez a jelző le van tiltva, ha egy hely vagy egy ExpressRoute áramkör egy hubhoz van csatlakoztatva. Alapértelmezés szerint engedélyezve van, ha egy virtuális hálózati kapcsolat hozzáadásával csatlakozik egy VNet egy virtuális hubhoz.

Az alapértelmezett útvonal nem a virtuális WAN-hubhoz származik; a rendszer az alapértelmezett útvonalat propagálja, ha a virtuális WAN-központ már megtanulta a tűzfal központi telepítésének eredményeképpen, vagy ha egy másik csatlakoztatott hely kényszerített bújtatást engedélyez. Az alapértelmezett útvonal nem terjed ki A hubok (Inter-hub) között.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Hogyan jelöli ki a virtuális hálózat virtuális központja a különböző hubok útvonalának legjobb elérési útját?

Ha egy virtuális központ ugyanazokat az útvonalakat tanulja meg több távoli hubokból, a következőképpen határozhatja meg a sorrendet:

1. A leghosszabb előtag egyezése.
1. Helyi útvonalak a interhub-en keresztül (a virtuális központ 65520-65520-et rendel hozzá a interhub-hoz).
1. Statikus útvonalak BGP-kapcsolaton keresztül: Ez a Virtual hub-útválasztó által hozott döntés kontextusában van. Ha azonban a döntéshozó az a VPN-átjáró, ahol a hely a BGP-n keresztül hirdeti az útvonalakat, vagy statikus címek előtagjait biztosít, a statikus útvonalak előnyben részesítettek a BGP-útvonalakon.
1. ExpressRoute (ER) VPN-en keresztül: az ABB előnyben részesített VPN-en, ha a környezet helyi központ. A ExpressRoute-áramkörök közötti átviteli kapcsolat csak Global Reachon keresztül érhető el. Ezért olyan helyzetekben, amikor a ExpressRoute áramkör egy hubhoz csatlakozik, és egy másik, VPN-kapcsolattal rendelkező központhoz csatlakoztatott ExpressRoute áramkör van, akkor a VPN-t előnyben részesítettük a csomópontok közötti forgatókönyvek esetében.
1. Elérési út hossza

### <a name="does-the-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>Engedélyezi a virtuális WAN hub a ExpressRoute-áramkörök közötti kapcsolatot?

Az ER-to-ER továbbítása mindig globális elérhetőséggel történik. A Virtual hub-átjárók tartományvezérlő-vagy Azure-régiókban vannak telepítve. Ha két ExpressRoute-áramkör a globális elérhetőségen keresztül csatlakozik, nincs szükség arra, hogy a forgalom egészen a peremhálózati útválasztók és a virtuális központ TARTOMÁNYVEZÉRLŐje között ne legyenek elérhetők.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-expressroute-circuits-or-vpn-connections"></a>Létezik-e súlyozás az Azure Virtual WAN ExpressRoute-áramkörökben vagy a VPN-kapcsolatokban

Ha több ExpressRoute-áramkör csatlakozik egy virtuális központhoz, a kapcsolat útválasztási súlyozása olyan mechanizmust biztosít a virtuális központban lévő ExpressRoute számára, amely előnyben részesíti az egyik áramkört a másikon. Nincs olyan mechanizmus, amellyel a súlyozást beállíthatja a VPN-kapcsolaton. Az Azure mindig egy ExpressRoute-kapcsolaton keresztül VPN-kapcsolaton keresztül csatlakozik egy adott központon belül.

### <a name="does-virtual-wan-prefer-expressroute-over-vpn-for-traffic-egressing-azure"></a>A Virtual WAN előnyben részesített VPN-en keresztüli ExpressRoute az Azure-beli forgalom egressing

Igen. A Virtual WAN inkább a VPN-en keresztül ExpressRoute az Azure-egressing.

### <a name="when-a-virtual-wan-hub-has-an-expressroute-circuit-and-a-vpn-site-connected-to-it-what-would-cause-a-vpn-connection-route-to-be-preferred-over-expressroute"></a>Ha egy virtuális WAN-hubhoz ExpressRoute-áramkör és VPN-hely van csatlakoztatva, mi okozhatja a VPN-kapcsolat útvonalát a ExpressRoute-en keresztül?

Ha egy ExpressRoute áramkör csatlakozik a virtuális központhoz, a Microsoft Edge útválasztók jelentik az első csomópontot a helyszíni és az Azure közötti kommunikációhoz. Ezek a peremhálózati útválasztók a virtuális WAN ExpressRoute-átjárókkal kommunikálnak, amelyek a virtuális HÁLÓZATban található átjárók közötti összes útvonalat vezérlik. A Microsoft Edge útválasztók feldolgozzák a virtuális központ ExpressRoute útvonalait magasabb prioritással a helyszínen megszerzett útvonalaknál.

Bármilyen okból, ha a VPN-kapcsolat a virtuális Központ elsődleges médiuma lesz, hogy megismerje az útvonalakat (például a ExpressRoute és a VPN közötti feladatátvételi forgatókönyvek között), kivéve, ha a VPN-hely hosszabb, mint az elérési út hossza, a virtuális központ továbbra is megoszthatja a VPN által megismert útvonalakat a ExpressRoute Ez azt eredményezi, hogy a Microsoft Edge-útválasztók inkább a VPN-útvonalakat részesítik előnyben a helyszíni útvonalakon.

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>Ha két hub (1. és 2. hub) csatlakoztatva van, és egy ExpressRoute áramkör van csatlakoztatva mindkét hubhoz, mi az az elérési út az 1. hubhoz csatlakozó VNet, hogy elérjen egy, a 2. hubhoz csatlakozó VNet?

A jelenlegi viselkedés az, hogy a ExpressRoute áramköri útvonalat a VNet és a VNet közötti kapcsolathoz részesíti előnyben. Ez azonban nem javasolt a virtuális WAN-telepítésben. A virtuális WAN-csapat olyan javításon dolgozik, amely lehetővé teszi a hub – hub elérését a ExpressRoute útvonalon. A javaslat több ExpressRoute-áramkörre (különböző szolgáltatóra) vonatkozik az egyik hubhoz való csatlakozáshoz, és a virtuális WAN által a régiók közötti adatforgalomhoz biztosított, a központ és a központ közötti kapcsolat használatát.

### <a name="can-hubs-be-created-in-different-resource-group-in-virtual-wan"></a>Létrehozhatók-e a virtuális WAN különböző erőforráscsoporthoz tartozó hubok?

Igen. Ez a beállítás jelenleg csak a PowerShellen keresztül érhető el. A virtuális WAN-portál megköveteli, hogy a hubok ugyanabban az erőforráscsoporthoz legyenek, mint a virtuális WAN-erőforrás.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Támogatja az IPv6-ot a virtuális WAN-ban?

A virtuális WAN-központ és az átjárók nem támogatják az IPv6 protokollt. Ha olyan VNet rendelkezik, amely IPv4-és IPv6-támogatással rendelkezik, és szeretné, hogy a VNet a virtuális WAN-hoz kapcsolódjon, ez a forgatókönyv jelenleg nem támogatott.

Ha a pont – hely felhasználói VPN-forgatókönyvet az Internet breakout használatával Azure Firewall, valószínűleg ki kell kapcsolni az IPv6-kapcsolatot az ügyfélszámítógépen, hogy a forgalmat a virtuális WAN-hubhoz irányítsa. Ennek az az oka, hogy a modern eszközök alapértelmezés szerint IPv6-címeket használnak.

### <a name="what-is-the-recommended-api-version-to-be-used-by-scripts-automating-various-virtual-wan-functionalities"></a>Mi a javasolt API-verzió, amelyet a parancsfájlok a különböző virtuális WAN-funkciók automatizálására használnak?

A 05-01-2020 (május 1 2020) minimális verzióját kötelező megadni.

### <a name="are-there-any-virtual-wan-limits"></a>Vannak virtuális WAN-korlátok?

Tekintse meg a [virtuális WAN-korlátok](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#virtual-wan-limits) szakaszt az előfizetés és a szolgáltatás korlátai lapon.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Mi a különbség a virtuális WAN-típusok (Basic és standard) között?

Lásd: [alapszintű és standard szintű virtuális WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard). A díjszabással kapcsolatban tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/virtual-wan/) oldalt.

### <a name="does-virtual-wan-store-customer-data"></a>A virtuális WAN tárolja az ügyféladatokat?

Nem. A virtuális WAN nem tárolja az ügyféladatokat.

### <a name="are-there-any-managed-service-providers-that-can-manage-virtual-wan-for-users-as-a-service"></a>Vannak olyan felügyelt szolgáltatók, amelyek felügyelik a virtuális WAN szolgáltatást a felhasználók számára szolgáltatásként?

Igen. Az Azure Marketplace-en keresztül engedélyezett felügyelt szolgáltatói (MSP) megoldások listáját az Azure [Marketplace-ajánlatok az Azure hálózati MSP-partnerek](../articles/networking/networking-partners-msp.md#msp)számára című témakörben találja.

### <a name="how-does-virtual-wan-hub-routing-differ-from-azure-route-server-in-a-vnet"></a>Miben különbözik a virtuális WAN hub útválasztása az Azure Route Server VNet?

Az Azure Route Server egy Border Gateway Protocol (BGP) társ-szolgáltatási szolgáltatást biztosít, amelyet a NVA (hálózati virtuális berendezés) használhat az útválasztási kiszolgálótól érkező útvonalak megismeréséhez egy DIY hub-VNet. A virtuális WAN-útválasztás több képességet biztosít, többek között a VNet, az egyéni útválasztást, az egyéni útvonal-társítást és a propagálást, valamint egy nulla érintéses, teljes mértékben szembőségű hub-szolgáltatást a ExpressRoute, a VNet, a távoli felhasználó/nagy léptékű P2S VPN és a Secure hub (Azure Firewall) képességeivel együtt. Ha a NVA és az Azure Route Server közötti BGP-társat hoz létre, akkor a NVA származó IP-címeket a virtuális hálózatra is meghirdetheti. Az összes olyan speciális útválasztási funkcióhoz, mint például az árutovábbítási útválasztás, az egyéni útválasztás stb., a virtuális WAN-útválasztást használhatja.
