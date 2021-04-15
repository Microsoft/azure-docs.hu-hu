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
ms.openlocfilehash: 2c1cf6e1d47f9bb78349e0846f624e1d6a484669
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386761"
---
### <a name="is-azure-virtual-wan-in-ga"></a>A Azure Virtual WAN ga ga?

Igen, Azure Virtual WAN általánosan elérhető. A Virtual WAN azonban számos funkcióból és forgatókönyvből áll. Vannak olyan funkciók vagy forgatókönyvek a Virtual WAN ahol a Microsoft az Előzetes verzió címkét alkalmazza. Ilyen esetekben az adott funkció vagy maga a forgatókönyv előzetes verzióban érhető el. Ha nem használ egy adott előzetes verziójú funkciót, a normál általánosan elérhető verzió támogatása érvényes. További információ az előzetes verziók támogatásról: Kiegészítő használati [feltételek a Microsoft Azure előzetes verziókhoz.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Szüksége van-e a felhasználónak küllős küllős kapcsolatra SD-WAN-/VPN-eszközökkel a Azure Virtual WAN?

A Virtual WAN számos funkciót kínál, amelyek egyetlen ablaktáblába vannak beépítve, például a helyek közötti VPN-kapcsolat, a felhasználó/P2S-kapcsolat, az ExpressRoute-kapcsolat, az Virtual Network-kapcsolat, a VPN ExpressRoute-összekapcsolás, a virtuális hálózatok közötti tranzitív kapcsolatok, a központosított útválasztás, a Azure Firewall és az Firewall Manager-biztonság, a monitorozás, az ExpressRoute-titkosítás és számos egyéb képesség. Nem kell az összes ilyen esetre szükség ahhoz, hogy használatba Virtual WAN. Az első lépésekhez csak egy használatot kell használnia.

Az Virtual WAN architektúra egy küllős architektúra, amelybe beépített skálázható és teljesítménnyel rendelkezik, amelyben az ágak (VPN/SD-WAN-eszközök), a felhasználók (Azure VPN-ügyfelek, openVPN- vagy IKEv2-ügyfelek), az ExpressRoute-kapcsolati körök és a virtuális hálózatok a virtuális központok küllőiként szolgálnak. Minden hub teljes hálóban van csatlakoztatva egy Standard Virtual WAN így a felhasználó könnyedén használhatja a Microsoft gerinchálózatát a bármely csomópontok (bármely küllők) kapcsolataihoz. Az SD-WAN/VPN-eszközökkel rendelkező küllős kapcsolatok esetében a felhasználók manuálisan állíthatják be azt az Azure Virtual WAN portálon, vagy a Virtual WAN Partner CPE (SD-WAN/VPN) használatával állíthatják be az Azure-ral való kapcsolatot.

Virtual WAN-partnerek automatizálást biztosítanak a kapcsolathoz, amely lehetővé teszi az eszközadatok Azure-ba való exportálását, az Azure-konfiguráció letöltését és az Azure Virtual WAN létesíteni. A pont–hely/felhasználói VPN-kapcsolat esetén támogatjuk az Azure VPN-ügyfelet, az OpenVPN-t vagy az IKEv2-ügyfelet. [](https://go.microsoft.com/fwlink/?linkid=2117554)

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>Le lehet tiltani a teljes hálós hubokat egy Virtual WAN?

Virtual WAN kétféle változatban lehet: Alapszintű és Standard. Az Alapszintű Virtual WAN a hubok nincsenek hálóban. A Standard Virtual WAN a hubok hálóval vannak összekapcsolva, és automatikusan csatlakoztatva vannak a virtuális WAN első beállításakor. A felhasználónak nincs szüksége konkrét beavatkozásra. A felhasználónak emellett nem kell letiltania vagy engedélyeznie a funkciót a hálós hubok beszerzéséhez. Virtual WAN számos útválasztási lehetőséget kínál a küllők (VNet, VPN vagy ExpressRoute) közötti forgalom irányítására. Megkönnyíti a teljes hálós hubok, valamint a forgalom igény szerint való útválasztásának rugalmasságát.

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>Hogyan kezelik Availability Zones és a rugalmasságot a Virtual WAN?

Virtual WAN a központon belül elérhetővé tett központok és szolgáltatások gyűjteménye. A felhasználónak annyi Virtual WAN lehet, amennyire szüksége van. Egy Virtual WAN központban több szolgáltatás is van, például a VPN, az ExpressRoute stb. Ezen szolgáltatások mindegyikét (a Azure Firewall kivételével) egy Availability Zones, azaz ha a régió támogatja a Availability Zones. Ha egy régió a központ kezdeti üzembe helyezése után rendelkezésre állási zónává válik, a felhasználó újra létrehozhatja az átjárókat, ami kiváltja a rendelkezésre állási zóna üzembe helyezését. Minden átjáró aktív-aktívként van kiépítve egy központban, ami azt jelenti, hogy a központon belül rugalmasság van beépítve. A felhasználók több központhoz is csatlakozhatnak, ha rugalmasságot szeretne a régiók között.

Bár a Virtual WAN fogalma globális, a Virtual WAN erőforrás Resource Manager, és regionálisan van üzembe stb. Ha maga a virtuális WAN-régió problémába tolódik, a virtuális WAN-ban lévő összes központ továbbra is működőképes marad, de a felhasználó nem fog tudni új központokat létrehozni, amíg a virtuális WAN-régió elérhetővé nem válik.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Melyik ügyfelet Azure Virtual WAN (pont–hely) felhasználói VPN?

Virtual WAN Támogatja az [Azure VPN-ügyfelet,](https://go.microsoft.com/fwlink/?linkid=2117554)az OpenVPN-ügyfelet vagy bármely IKEv2-ügyfelet. Az Azure AD-hitelesítést az Azure VPN-ügyfél támogatja.Legalább Windows 10 17763.0-s vagy újabb verziójú ügyfél operációs rendszer szükséges.  Az OpenVPN-ügyfél(ek) támogatják a tanúsítványalapú hitelesítést. Miután kiválasztotta a tanúsítványalapú hitelesítést az átjárón, látni fogja az eszközre letölteni kívánt.ovpn* fájlt. Az IKEv2 a tanúsítvány- és RADIUS-hitelesítést is támogatja. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Felhasználói VPN (pont–hely) esetében – Miért van a P2S-ügyfélkészlet két útvonalra osztva?

Minden átjáró két példányból áll, a felosztás azért történik, hogy az egyes átjárópéldányok egymástól függetlenül lefoglalják az ügyfél IP-eket a csatlakoztatott ügyfelek számára, és a virtuális hálózatról származó forgalmat visszairányítjuk a megfelelő átjárópéldányra, hogy elkerülhető legyen az átjárópéldányok közötti ugrás.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Hogyan DNS-kiszolgálókat aD hozzá a P2S-ügyfelekhez?

A P2S-ügyfelekhez két DNS-kiszolgálót adhat hozzá. Az első módszer ajánlott, mivel az ügyfél helyett az egyéni DNS-kiszolgálókat az átjáróhoz ad hozzá.

1. Az egyéni DNS-kiszolgálók hozzáadásához használja a következő PowerShell-szkriptet. Cserélje le a környezet értékeit.

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
2. Ha az Azure VPN-ügyfelet használja a Windows 10, az importálás előtt módosíthatja a **\<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers>** letöltött profil XML-fájlját, és hozzáadhatja a címkéket.

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

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>Felhasználói VPN (pont–hely) esetén – hány ügyfél támogatott?

Minden felhasználó VPN P2S-átjárója két példányból áll. Minden példány legfeljebb egy bizonyos számú kapcsolatot támogat a skálás egység változásának hatékonyban. Az 1–3. skálás egység 500 kapcsolatot támogat, a 4–6. skálás egység 1000 kapcsolatot, a 7–12. skálás egység 5000 kapcsolatot, a 13–18. skálás egység pedig legfeljebb 10 000 kapcsolatot támogat.

Tegyük fel például, hogy a felhasználó az 1 skálás egységet választja. Minden skálázható egység egy aktív-aktív üzembe helyezett átjárót jelent, és az egyes példányok (ebben az esetben a 2) legfeljebb 500 kapcsolatot támogatnak. Mivel átjárónként 500 kapcsolatot * 2 létesíthet, ez nem jelenti azt, hogy az 500 helyett 1000-et tervez használni ehhez a skáláz egységhez. Előfordulhat, hogy a példányokat szervizelni kell, amelyek során a további 500-as kapcsolat megszakadhat, ha meghaladja a javasolt kapcsolatszámot. Mindenképpen tervezze meg az állásidőt arra az esetre, ha a skálázható egységen fel- vagy leskálán dönt, vagy módosítsa a pont–hely konfigurációt a VPN-átjárón.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Mi a különbség az Azure-beli virtuális hálózati átjáró (VPN Gateway) és a Azure Virtual WAN VPN-átjáró között?

A Virtual WAN nagy léptékben biztosít helyek közötti kapcsolatokat, és kifejlesztése során elsősorban az átviteli sebességet, a méretezhetőséget és a könnyű használatot tartották szem előtt. Ha egy helyet csatlakoztat egy Virtual WAN VPN-átjáróhoz, az eltér a "VPN" átjárótípust használó normál virtuális hálózati átjáróktól. Hasonlóképpen, amikor egy ExpressRoute-kapcsolatcsoportot csatlakoztat egy Virtual WAN-központhoz, az egy másik erőforrást használ az ExpressRoute-átjáróhoz, mint az "ExpressRoute" átjárótípust használó normál virtuális hálózati átjáró. 

Virtual WAN 20 Gbit/s összesített átviteli sebességet támogat a VPN és az ExpressRoute esetében is. Virtual WAN a CPE-ág eszközpartnereivel való kapcsolat automatizálását is biztosítja. A CPE-ágeszközök beépített automatizálással vannak, amely automatikusan kiépíti és csatlakozik a Azure Virtual WAN. Ezek az eszközök az SD-WAN- és a VPN-partnerek egyre bővülő körétől szerezhetők be. Lásd az [előnyben részesített partnerek listáját.](../articles/virtual-wan/virtual-wan-locations-partners.md)

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Miben Virtual WAN különbözik az Azure-beli virtuális hálózati átjárótól?

A virtuális hálózati átjáró VPN-hálózata legfeljebb 30 alagútból áll. Kapcsolatokhoz nagy mennyiségű VPN-forgalmat bonyolító Virtual WAN használata javasolt. Régiónként (virtuális központonként) legfeljebb 1000 ágkapcsolatot csatlakoztathat, központonként 20 Gb/s-os összesítéssel. A kapcsolatok aktív-aktív alagútnak minősülnek a helyszíni VPN-eszköz és a virtuális központ között. Régiónként egy központot is lehet, ami azt jelenti, hogy több mint 1000 ágat csatlakoztathat a központok között.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Mi az a Virtual WAN-méretezési egység?

A skálázható egység egy olyan egység, amely egy átjáró összesített átviteli sebességét határozza meg a Virtuális központban. 1 vpn skálázegység = 500 Mbps. Az ExpressRoute 1 skálázegysége = 2 Gb/s. Példa: 10 VPN-méretezési egység 500 Mbps * 10 = 5 Gbps

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Mely eszközszolgáltatók (Virtual WAN partnerek) támogatottak?

Mostanra már számos partner támogatja a teljesen automatizált Virtual WAN-t. További információ: [Virtual WAN-partnerek](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Melyek a Virtual WAN-partnerek automatizálásának lépései?

A partnerek automatizálásának lépéseiért tekintse át [a Virtual WAN-partnerek automatizálásával](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) foglalkozó részt.

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Kötelező egy adott partner eszközét használnom?

Nem. Bármely olyan VPN-kompatibilis eszközt használhat, amely megfelel az Azure IKEv2/IKEv1 IPsec-támogatásra vonatkozó követelményeinek. Virtual WAN CPE-partnermegoldásokkal is rendelkezik, amelyek automatizálják a Azure Virtual WAN és megkönnyítik az IPsec VPN-kapcsolatok nagy léptékű beállítását.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hogyan csatlakozhatnak a Virtual WAN-partnerek automatikusan az Azure Virtual WAN-hoz?

A szoftveralapú csatlakozási megoldások jellemzően vezérlővel vagy eszközkiépítési központtal kezelik a kompatibilis eszközöket. A vezérlők Azure API-kat is használhatnak az Azure Virtual WAN-hoz való automatikus csatlakozáshoz. Az automatizálás magában foglalja az áginformációk feltöltését, az Azure-konfiguráció letöltését, az IPsec-alagutak Azure Virtual Hubsba való beállítását, valamint az ágeszköz kapcsolatának automatikus beállítását a Azure Virtual WAN. Ha több száz ága van, az Virtual WAN CPE-partnerek használatával könnyű csatlakozni, mivel a beállítási folyamat nem szükséges nagy léptékű IPsec-kapcsolatot beállítani, konfigurálni és kezelni. További információ: Virtual WAN [automatizálása.](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Mi történik, ha egy általom használt eszköz nem szerepel a Virtual WAN listájában? Továbbra is csatlakozhatok vele egy vpn Azure Virtual WAN hálózathoz?

Igen, ha az eszköz támogatja az IPsec IKEv1 vagy az IKEv2 protokollt. Virtual WAN a partnerek automatizálják az eszköz és az Azure VPN-végpontok kapcsolatát. Ez olyan lépések automatizálását jelenti, mint például az "áginformációk feltöltése", az "IPsec és konfiguráció" és a "kapcsolat". Mivel az eszköz nem egy Virtual WAN-partneri ökoszisztémából van, önnek kell elvégeznie a legtöbb munkát, hogy manuálisan használja az Azure-konfigurációt, és frissítse az eszközt az IPsec-kapcsolat beállításának érdekében.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hogyan készülhetnek elő a szolgáltatásra a meglévő partnerek listáján még nem szereplő új partnerek?

Minden virtual WAN API nyitott API. A technikai megvalósíthatóság felmérése [érdekében Virtual WAN partnerautomatizálás](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) dokumentációját. Ideális esetben a partner olyan eszközzel rendelkezik, amely támogatja az IKEv1 vagy az IKEv2 IPsec-kapcsolatot. Miután a vállalat befejezte a CPE-eszköz automatizálási munkáját a fent megadott automatizálási irányelvek alapján, felveheti a kapcsolatot, hogy felveje a kapcsolatot a azurevirtualwan@microsoft.com [partnereken keresztül.]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners) Ha Ön olyan ügyfél, aki azt szeretné, hogy egy adott vállalati megoldás Virtual WAN-partnerként legyen felsorolva, a vállalat lépjen kapcsolatba a Virtual WAN e-mailben a azurevirtualwan@microsoft.com címre.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Hogyan támogatja Virtual WAN SD-WAN-eszközöket?

Virtual WAN automatizálják az IPsec-kapcsolatot az Azure VPN-végpontokkal. Ha a Virtual WAN SD-WAN szolgáltató, akkor az SD-WAN vezérlő kezeli az Automatizálást és az IPsec-kapcsolatot az Azure VPN-végpontokkal. Ha az SD-WAN eszköznek saját végpontra van szüksége Azure VPN helyett bármely saját SD-WAN-funkcióhoz, az SD-WAN végpontot üzembe helyezheti egy Azure-beli virtuális hálózatban, és használhatja az Azure Virtual WAN.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Hány VPN-eszköz csatlakozhat egyetlen központhoz?

Virtuális központonként legfeljebb 1000 kapcsolat támogatott. Minden kapcsolat négy kapcsolatból áll, és mindegyik kapcsolat két aktív-aktív konfigurációban lévő alagutat támogat. Az alagutak egy Azure-beli virtuális központ VPN-átjáróban végződnek. A hivatkozások a fiókirodában/VPN-eszközön található fizikai internetszolgáltatói hivatkozást képviselik.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Mi az ágkapcsolat a Azure Virtual WAN?

Az ágak vagy VPN-eszközök és a Azure Virtual WAN közötti kapcsolat olyan VPN-kapcsolat, amely virtuális központban gyakorlatilag a VPN-helyet és az Azure VPN Gateway kapcsolatot létesít.

### <a name="what-happens-if-the-on-premises-vpn-device-only-has-1-tunnel-to-an-azure-virtual-wan-vpn-gateway"></a>Mi történik, ha a helyszíni VPN-eszköz csak 1 alagúttal rendelkezik egy Azure Virtual WAN VPN-átjáróhoz?

A Azure Virtual WAN kapcsolat 2 alagútból áll. A Virtual WAN VPN-átjáró aktív-aktív módban van üzembe helyezni egy virtuális központban, ami azt jelenti, hogy külön alagutak vannak a különálló példányok számára lezáró helyszíni eszközöktől. Ez az összes felhasználóra vonatkozó javaslat. Ha azonban a felhasználó úgy dönt, hogy csak 1 alagutat használ az Virtual WAN VPN Gateway-példányhoz, és bármilyen okból (karbantartás, javítások stb.) az átjárópéldány offline állapotba lép, az alagút a másodlagos aktív példányra lesz áthelyezve, és a felhasználó újracsatlakozhat. A BGP-munkamenetek nem lesznek áthelyezve a példányok között.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Csatlakozhat a helyszíni VPN-eszköz több központhoz?

Igen. Az építéskor a forgalom a helyszíni eszköztől a legközelebbi Microsoft hálózati peremhálózatig, majd a virtuális központig áramlik.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Elérhetővé válnak új Resource Manager-erőforrások a Virtual WAN-hoz?
  
Igen, Virtual WAN új erőforrásokkal Resource Manager rendelkezik. További információ: [Áttekintés](../articles/virtual-wan/virtual-wan-about.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Az Azure Virtual WAN-on is üzembe helyezhetem és használhatom kedvenc hálózati virtuális készülékemet (NVA virtuális hálózaton)?

Igen, csatlakoztathatja kedvenc hálózati virtuális berendezése (NVA-ja) virtuális hálózatát az Azure Virtual WAN-hoz.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Létrehozhatok hálózati virtuális berendezést a virtuális központban?

Hálózati virtuális berendezés (NVA) nem helyezhető üzembe egy virtuális központban. Létrehozhatja azonban egy küllő virtuális hálózatban, amely a virtuális központhoz csatlakozik, és lehetővé teszi a megfelelő útválasztást a forgalom igény szerint történő átirányításához.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>A küllő virtuális hálózatnak lehet virtuális hálózati átjárója?

Nem. A küllő virtuális hálózat nem lehet virtuális hálózati átjáró, ha a virtuális központhoz csatlakozik.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>Támogatja a BGP a VPN-kapcsolatot?

Igen, a BGP támogatott. VPN-hely létrehozásakor meg lehet adni benne a BGP-paramétereket. Ez azt jelenti, hogy az Adott helyhez az Azure-ban létrehozott kapcsolatok engedélyezve lesznek a BGP-hez.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Milyen licenc- vagy díjszabási információ érhető el a Virtual WAN-ról?

Igen. Lásd a [Díjszabás](https://azure.microsoft.com/pricing/details/virtual-wan/) oldalt.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Lehetséges Azure Virtual WAN létrehozása Resource Manager-sablon használatával?

Egy gyorsindítási sablonnal Virtual WAN egy központtal és egy vpnsite-sel rendelkező adatbázis egyszerű [konfigurációja.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network) Virtual WAN szolgáltatás elsősorban REST- vagy portálalapú szolgáltatás.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Kommunikálhatnak egymással a virtuális központhoz csatlakozó küllő virtuális hálózatok (V2V átvitel)?

Igen. A standard Virtual WAN támogatja a virtuális hálózatok között tranzitív kapcsolatot az Virtual WAN hubon keresztül, amelyhez a virtuális hálózatok csatlakoznak. Virtual WAN Terminológiában ezeket az elérési utakat "helyi Virtual WAN VNet-átvitelnek" hívjuk az egyetlen régión belüli Virtual Wan-központhoz csatlakoztatott virtuális hálózatok esetében, illetve "globális Virtual WAN VNet-átvitelnek" a két vagy több régióban több Virtual WAN hubon keresztül csatlakoztatott virtuális hálózatok esetében.

Bizonyos esetekben a küllő virtuális hálózatok közvetlenül is létesíthetőek társviszonyban egymással virtuális hálózatok közötti társviszony-létesítés használatával a helyi vagy globális virtuális hálózatok Virtual WAN mellett. [](../articles/virtual-network/virtual-network-peering-overview.md) Ebben az esetben a virtuális hálózatok közötti társviszony elsőbbséget élvez a virtuális központon keresztüli tranzitív Virtual WAN felett.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A Virtual WAN-ban engedélyezett az ágak közötti kapcsolat?

Igen, az ágak közötti kapcsolat elérhető a Virtual WAN-ban. Az ág fogalmilag alkalmazható VPN-hely, ExpressRoute-kapcsolatcsoport vagy pont–hely/felhasználói VPN-felhasználók számára. Az ágak és ágak között alapértelmezés szerint engedélyezve van, és a WAN konfigurációs **beállításai között található.** Ez lehetővé teszi, hogy a VPN-ágak/felhasználók más VPN-ágakhoz csatlakozznak, és a VPN- és az ExpressRoute-felhasználók között is engedélyezve van az átmenő kapcsolat.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Az ágak között áthaladó forgalom áthalad a Azure Virtual WAN?

Igen. Az ágak között áthaladó forgalom áthalad a Azure Virtual WAN.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Minden Virtual WAN szükség van az ExpressRoute-ra?

Nem. Virtual WAN az ExpressRoute nem szükséges minden oldalról. Előfordulhat, hogy a helyek ExpressRoute-kapcsolatcsoporttal csatlakoznak a szolgáltatói hálózathoz. Az ExpressRoute-ot egy virtuális központhoz és IPsec VPN-t ugyanannak a központnak az expressroute-kapcsolattal csatlakozó helyek esetén a virtuális központ tranzit kapcsolatot biztosít a VPN- és az ExpressRoute-felhasználó között.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Van hálózati átviteli sebesség vagy kapcsolati korlát a hálózati Azure Virtual WAN?

A hálózati átviteli sebesség szolgáltatásonkénti egy virtuális WAN-központban. Bár annyi virtuális WAN-ja lehet, amennyit csak szeretne, minden Virtual WAN régiónként 1 központot tesz lehetővé. Az egyes központokban a VPN összesített átviteli sebesség akár 20 Gb/s, az ExpressRoute összesített átviteli sebesség akár 20 Gb/s, a felhasználói VPN/pont–hely VPN összesített átviteli sebesség pedig akár 20 Gb/s is lehet. A virtuális központban lévő útválasztó legfeljebb 50 Gb/s sebességet támogat a virtuális hálózatok közötti adatforgalom esetében, és összesen 2000 virtuálisgép-számítási feladatot feltételez az egyetlen virtuális központhoz csatlakoztatott összes virtuális hálózatban.

Amikor a VPN-helyek csatlakoznak egy központhoz, azt a kapcsolatokkal is meg tudják tenni. Virtual WAN virtuális központonként legfeljebb 1000 kapcsolatot vagy 2000 IPsec-alagutat támogat. Amikor a távoli felhasználók csatlakoznak a virtuális központhoz, a P2S VPN-átjáróhoz csatlakoznak, amely a virtuális központban a P2S VPN-átjáróhoz kiválasztott skálázható egységtől (sávszélességtől) függően akár 10 000 felhasználót is támogat.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Mekkora a VPN-alagút és a kapcsolat teljes VPN-átviteli sebessége?

A hubok vpn-átviteli sebességének teljes mérete a VPN-átjáró kiválasztott skálázható egysége alapján akár 20 Gb/s is lehet. Az átviteli sebességet az összes meglévő kapcsolat megosztja. Egy kapcsolat minden alagútja legfeljebb 1 Gb/s-os sebességet támogat.

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>Használhatom a NAT-T-t a VPN-kapcsolatomon?

Igen, a NAT-bejárás (NAT-T) támogatott. A Virtual WAN VPN-átjáró NEM fog NAT-hez hasonló funkciót végrehajtani az IPsec-alagutak belső csomagjaion. Ebben a konfigurációban győződjön meg arról, hogy a helyszíni eszköz elindítja az IPsec-alagutat.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>Nem látom a virtuális központ 20 Gb/s-os beállítását a portálon. Hogyan konfigurálni?

Navigáljon a VPN-átjáróhoz a portál egyik központján belül, majd kattintson a skálázható egységre a megfelelő beállításra való váltáshoz.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Lehetővé Virtual WAN, hogy a helyszíni eszköz párhuzamosan több ISP-t is használjon, vagy mindig egyetlen VPN-alagutat használ?

A helyszíni eszközmegoldások forgalmi szabályzatokkal irányítják a forgalmat a Azure Virtual WAN hubba (a virtuális központban található VPN-átjáróba).

### <a name="what-is-global-transit-architecture"></a>Mi az a globális tranzit architektúra?

További információ a globális tranzit architektúráról: Globális tranzit [hálózati architektúra és Virtual WAN.](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md)

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Hogyan történik a forgalom átirányítása az Azure gerinchálózatára?

A forgalom a következő mintát követi: branch device ->ISP->Microsoft network edge->Microsoft DC (hub VNet)->Microsoft network edge->ISP->branch device

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Ebben a modellben mire van szükség az egyes helyek esetében? Csupán internetkapcsolatra?

Igen. Internetkapcsolat és az IPsec-et támogató fizikai eszköz, lehetőleg az integrált [Virtual WAN segítségével.](../articles/virtual-wan/virtual-wan-locations-partners.md) Igény szerint manuálisan is kezelheti a konfigurációt és az Azure-hoz való kapcsolódást a kívánt eszközről.

### <a name="how-do-i-enable-default-route-00000-for-a-connection-vpn-expressroute-or-virtual-network"></a>Hogyan engedélyezni az alapértelmezett útvonalat (0.0.0.0/0) egy kapcsolathoz (VPN, ExpressRoute vagy Virtual Network)?

A virtuális központ propagálhat egy megtanult alapértelmezett útvonalat egy virtuális hálózatra/hely–hely VPN-/ExpressRoute-kapcsolatra, ha a kapcsolat jelzője Engedélyezve. Ez a jelző akkor látható, ha a felhasználó virtuális hálózati kapcsolatot, VPN-kapcsolatot vagy ExpressRoute-kapcsolatot szerkeszt. Ez a jelző alapértelmezés szerint le van tiltva, ha egy hely vagy Egy ExpressRoute-kapcsolatcsoport csatlakozik egy központhoz. Ez alapértelmezés szerint engedélyezve van, amikor hozzáad egy virtuális hálózati kapcsolatot egy virtuális hálózat virtuális központhoz való csatlakoztatásához.

Az alapértelmezett útvonal nem a központi Virtual WAN származik; az alapértelmezett útvonal propagálva lesz, ha az Virtual WAN hub már megtanulta azt egy tűzfal központi telepítésének eredményeként, vagy ha egy másik csatlakoztatott helyen engedélyezve van a kényszerített bújtatás. Az alapértelmezett útvonalak nem propagálnak a központok között (központok között).

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Hogyan választja ki a virtuális WAN virtuális központja a legjobb útvonalat egy útvonalhoz több központból?

Ha egy virtuális központ több távoli központból tanulja meg ugyanazt az útvonalat, a következő sorrendben dönt:

1. A leghosszabb előtag-egyezés.
1. Helyi útvonalak a központon keresztül (a virtuális központ a 65520-65520-as kiosztást rendeli hozzá a központközi AS-hez).
1. Statikus útvonalak A BGP-ben: Ez a virtuális központ útválasztója által hozott döntés kontextusában van. Ha azonban a döntéshozó az a VPN-átjáró, ahol egy hely BGP-n keresztül hirdet útvonalakat, vagy statikus címelőtagokat biztosít, a statikus útvonalak előnyben részesíthetőek a BGP-útvonalakkal.
1. ExpressRoute (ER) VPN-en keresztül: A VPN-hez előnyben részesítik az ER-t, ha a környezet helyi központ. Az ExpressRoute-kapcsolatkapcsolatok közötti tranzit kapcsolat csak a Global Reach. Ezért az olyan forgatókönyvekben, ahol az ExpressRoute-kapcsolatkapcsolat egy központhoz csatlakozik, és van egy másik, VPN-kapcsolattal rendelkező központhoz csatlakoztatott ExpressRoute-kapcsolat kapcsolat, a központok közötti forgatókönyvek esetében a VPN használata javasolt.
1. AS elérési út hossza.

### <a name="does-the-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>Engedélyezi a Virtual WAN az ExpressRoute-kapcsolatkapcsolatok közötti kapcsolatot?

Az ER-ről ER-be történő átvitel mindig a Global Reachen keresztül érhető el. A virtuálisközpont-átjárók DC vagy Azure-régiókban vannak telepítve. Ha két ExpressRoute-kapcsolatkör a Global Reachen keresztül csatlakozik, nincs szükség arra, hogy a forgalom a peremhálózati útválasztóktól a virtuális központ tartományvezérlőjéig minden úton jöjjön.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-expressroute-circuits-or-vpn-connections"></a>Van-e súlyozási fogalom az ExpressRoute-kapcsolat Azure Virtual WAN VPN-kapcsolatokban?

Ha több ExpressRoute-kapcsolatcsoport csatlakozik egy virtuális központhoz, a kapcsolat útválasztási súlyozása olyan mechanizmust biztosít, amely lehetővé teszi a virtuális központban lévő ExpressRoute számára, hogy előnyben részesítsen egy kapcsolatcsoportot a másikkal helyett. Nincs olyan mechanizmus, amely súlyozást állítson be a VPN-kapcsolatokhoz. Az Azure mindig az ExpressRoute-kapcsolatot részesíti előnyben egy VPN-kapcsolat helyett egyetlen központon belül.

### <a name="does-virtual-wan-prefer-expressroute-over-vpn-for-traffic-egressing-azure"></a>Az Virtual WAN az ExpressRoute-et részesíti előnyben a VPN-hez az Azure-ba bejövő forgalomhoz?

Igen. Virtual WAN az ExpressRoute-et részesíti előnyben a VPN-hez az Azure-ba bejövő forgalomhoz.

### <a name="when-a-virtual-wan-hub-has-an-expressroute-circuit-and-a-vpn-site-connected-to-it-what-would-cause-a-vpn-connection-route-to-be-preferred-over-expressroute"></a>Ha egy Virtual WAN-központhoz ExpressRoute-kapcsolatcsoport és vpn-hely csatlakozik, mi okozhatja a VPN-kapcsolat útvonalának előnyben részesítését az ExpressRoute-hoz?

Amikor egy ExpressRoute-kapcsolatkör csatlakozik a virtuális központhoz, a Microsoft peremhálózati útválasztói az első csomópontok a helyszíni rendszer és az Azure közötti kommunikációhoz. Ezek a peremhálózati útválasztók az Virtual WAN ExpressRoute-átjárókval kommunikálnak, amelyek pedig az átjárók közötti összes útvonalat vezérlő virtuálisközpont-útválasztótól tanulnak Virtual WAN. A Microsoft peremhálózati útválasztói a helyszínen megtanult útvonalakkal magasabb prioritással feldolgoznak egy virtuális központ ExpressRoute-útvonalait.

Ha a VPN-kapcsolat lesz az elsődleges eszköz, amelyből a virtuális központ megtanulja az útvonalakat (például az ExpressRoute és a VPN közötti feladatátvételi forgatókönyveket), kivéve, ha a VPN-hely hosszabb AS elérési úttal rendelkezik, a virtuális központ továbbra is meg fogja osztani a VPN által megtanult útvonalakat az ExpressRoute-átjáróval. Ez azt okozza, hogy a Microsoft peremhálózati útválasztói a VPN-útvonalakat részesítik előnyben a helyszíni útvonalakkal.

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>Ha két hub (az 1. és a 2. központ) csatlakoztatva van, és mindkét hubhoz egy ExpressRoute-kapcsolat kapcsolat van csatlakoztatva, milyen útvonalon éri el az 1. központhoz csatlakozó virtuális hálózat a 2. központban csatlakoztatott virtuális hálózatokat?

A jelenlegi viselkedés az ExpressRoute-kapcsolat kapcsolati útvonalának előnyben részesítését részesíti előnyben a virtuális hálózatok és a virtuális hálózatok kapcsolatának központ–hub kapcsolattal való előnyben részesítésével. Ez azonban nem ajánlott virtuális WAN-környezetben. A Virtual WAN csapat egy javításon dolgozik, hogy lehetővé tegye a központ és központ előnyben részesítését az ExpressRoute-útvonalon keresztül. Azt javasoljuk, hogy több ExpressRoute-kapcsolatkör (különböző szolgáltatók) csatlakozzon az egyik központhoz, és használja az Virtual WAN által biztosított központközi kapcsolatot a régiók közötti forgalomhoz.

### <a name="can-hubs-be-created-in-different-resource-group-in-virtual-wan"></a>Létre lehet-e hozva hubok különböző erőforráscsoportban a Virtual WAN?

Igen. Ez a lehetőség jelenleg csak a PowerShellen keresztül érhető el. A Virtual WAN portál megköveteli, hogy a központok ugyanabban az erőforráscsoportban vannak, mint maga Virtual WAN erőforrás.

### <a name="what-is-the-recommended-hub-address-space-during-hub-creation"></a>Mi az ajánlott központcímtér a központ létrehozása során?

Az ajánlott Virtual WAN a /23. Virtual WAN hub különböző átjárókhoz rendel alhálózatokat (ExpressRoute, helyek közötti VPN, pont–hely TÍPUSÚ VPN, Azure Firewall-útválasztó). Olyan forgatókönyvek esetén, ahol az NVA-k egy virtuális központban vannak telepítve, az NVA-példányok általában /28-at tartalmaznak. Ha azonban a felhasználó több NVA-t is kiépít, hozzárendelhet egy /27 alhálózatot. Ezért a jövőbeli architektúrát szem előtt tartva, bár a Virtual WAN hubok /24-es minimális mérettel vannak üzembe tartva, a felhasználók számára a létrehozáskor javasolt hubcímtér a /23.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Támogatott az IPv6 a Virtual WAN?

Az IPv6 nem támogatott a Virtual WAN és átjáróiban. Ha olyan virtuális hálózattal rendelkezik, amely IPv4- és IPv6-támogatással rendelkezik, és csatlakoztatni szeretné a virtuális Virtual WAN hálózathoz, ez a forgatókönyv jelenleg nem támogatott.

Az Azure Firewall-n keresztüli internet-megszakító pont–hely felhasználói VPN-forgatókönyv esetében valószínűleg ki kell kapcsolnia az IPv6-kapcsolatot az ügyféleszközön, hogy kényszeríteni tudja a forgalmat az Virtual WAN hubra. Ennek az az oka, hogy a modern eszközök alapértelmezés szerint IPv6-címeket használnak.

### <a name="what-is-the-recommended-api-version-to-be-used-by-scripts-automating-various-virtual-wan-functionalities"></a>Mi az ajánlott API-verzió, amelyet a különböző funkciók automatizálását Virtual WAN szkriptek használni?

A 05-01-2020 (2020. május 1.) minimális verzió szükséges.

### <a name="are-there-any-virtual-wan-limits"></a>Vannak a Virtual WAN korlátai?

Tekintse meg [Virtual WAN előfizetési](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#virtual-wan-limits) és szolgáltatási korlátok lap Virtual WAN korlátok című szakaszát.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Mi a különbség a két típus Virtual WAN (Alapszintű és Standard)?

Lásd: [Alapszintű és Standard virtuális WAN-ok.](../articles/virtual-wan/virtual-wan-about.md#basicstandard) A díjszabást a Díjszabás [oldalon](https://azure.microsoft.com/pricing/details/virtual-wan/) láthatja.

### <a name="does-virtual-wan-store-customer-data"></a>Tárolnak Virtual WAN ügyféladatokat?

Nem. Virtual WAN nem tárol ügyféladatokat.

### <a name="are-there-any-managed-service-providers-that-can-manage-virtual-wan-for-users-as-a-service"></a>Vannak olyan felügyelt szolgáltatók, akik Virtual WAN szolgáltatásként kezelik a felhasználókra vonatkozó 3D-eket?

Igen. Az MSP-partnerek által Azure Marketplace felügyelt szolgáltatói (MSP-) Azure Marketplace lásd: Azure-hálózatkezelés [ajánlatok.](../articles/networking/networking-partners-msp.md#msp)

### <a name="how-does-virtual-wan-hub-routing-differ-from-azure-route-server-in-a-vnet"></a>Miben különbözik Virtual WAN hub útválasztása a virtuális hálózatban található Azure Route Servertől?

Az Azure Route Server egy Border Gateway Protocol (BGP) társviszony-létesítő szolgáltatást biztosít, amelyet az NVA-k (hálózati virtuális berendezés) használhatnak a DIY hub virtuális hálózatban lévő útvonalkiszolgáló útvonalának megtanulására. Az Virtual WAN-útválasztás több képességet biztosít, beleértve a virtuális hálózatok közötti tranzit útválasztást, az egyéni útválasztást, az egyéni útvonal-társítást és -propagálást, valamint egy érintés nélküli, teljes hálóval rendelkező központi szolgáltatást, valamint az ExpressRoute, a hely VPN, a távoli felhasználó/nagy léptékű P2S VPN és a biztonságos központ (Azure Firewall) képességeit. Amikor BGP-társviszonyt létesít az NVA és az Azure Route Server között, meghirdethet IP-címeket az NVA-ból a virtuális hálózatba. Minden fejlett útválasztási képességhez, például a tranzit útválasztáshoz, az egyéni útválasztáshoz stb., használhatja a Virtual WAN útválasztást.
