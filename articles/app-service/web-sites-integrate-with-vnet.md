---
title: Alkalmazás integrálása az Azure Virtual Network
description: Alkalmazás integrálása Azure App Service Azure-beli virtuális hálózatokkal.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 42391a073d7cb1d7e6850e298c2be32d550bb813
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832067"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Alkalmazás integrálása Azure-beli virtuális hálózattal

Ez a cikk a Azure App Service VNet-integráció szolgáltatást és annak beállítását ismerteti a [Azure App Service.](./overview.md) Az [Azure Virtual Network][VNETOverview] (VNet) használatával számos Azure-erőforrást egy nem internetről elérhető hálózatba is helyezzen. A VNet-integráció funkció lehetővé teszi, hogy az alkalmazások egy virtuális hálózatban vagy egy VNeten keresztül hozzáférjenek az erőforrásokhoz. A VNet-integráció nem teszi lehetővé az alkalmazások privát hozzáférését.

Azure App Service VNet-integráció funkciónak két változata van:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>VNet-integráció engedélyezése

1. A **portálon a Hálózat App Service** meg. A **VNet-integráció alatt** válassza a **Kattintson ide a konfiguráláshoz lehetőséget.**

1. Válassza a **VNet hozzáadása lehetőséget.**

   ![VNet-integráció kiválasztása][1]

1. A legördülő lista az előfizetésében Azure Resource Manager összes virtuális hálózatot tartalmazza ugyanabban a régióban. Ez alatt található a virtuális hálózatok Resource Manager összes többi régióban. Válassza ki azt a VNetet, amelybe integrálni szeretne.

   ![Válassza ki a VNetet][2]

   * Ha a virtuális hálózat ugyanabban a régióban van, hozzon létre egy új alhálózatot, vagy válasszon egy üres, már használt alhálózatot.
   * Egy másik régióban található virtuális hálózat kiválasztásához rendelkeznie kell egy olyan virtuális hálózati átjáróval, amely számára engedélyezve van a pont–hely kapcsolat.
   * Ha klasszikus virtuális hálózatba szeretne integrálni, a virtuális hálózat **Virtual Network** helyett válassza a Kattintson ide klasszikus virtuális hálózathoz való **csatlakozáshoz lehetőséget.** Válassza ki a kívánt klasszikus virtuális hálózatot. A cél virtuális hálózatnak már rendelkeznie kell Virtual Network pont–hely kapcsolattal kiépített átjáróval.

    ![Klasszikus virtuális hálózat kiválasztása][3]

Az integráció során az alkalmazás újraindul. Az integráció befejeztét a virtuális hálózat részleteiben láthatja, amelybe integrálva van.

## <a name="regional-vnet-integration"></a>Regionális VNet-integráció

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>A regionális VNet-integráció működése

A App Service alkalmazások feldolgozói szerepkörökben vannak üzemeltetve. Az alapszintű és magasabb tarifacsomagok olyan dedikált üzemeltetési csomagok, amelyekben nincsenek más ügyfelek számítási feladatai ugyanazon a dolgozókon. A regionális VNet-integráció a virtuális adapterek a delegált alhálózatban lévő címekkel való csatlakoztatásával működik. Mivel a cím a virtuális hálózatban található, a virtuális hálózatban vagy azon keresztül hozzáférhet a legtöbb dologhoz, mint a virtuális hálózat egyik virtuális gépe. A hálózat implementációja eltér a virtuális gépek virtuális hálózaton való futtatásától. Ezért egyes hálózati funkciók még nem érhetők el ehhez a funkcióhoz.

![A regionális VNet-integráció működése][5]

Ha a regionális VNet-integráció engedélyezve van, az alkalmazás a szokásos csatornákon keresztül kezdeményez kimenő hívásokat az internetre. Az alkalmazástulajdonságok portálján felsorolt kimenő címek azok a címek, amelyek továbbra is az alkalmazás által használtak. Az alkalmazás változásai a szolgáltatásvégpont által védett szolgáltatásokhoz vagy AZ RFC 1918-címekhez való hívások a virtuális hálózatba kerülnek. Ha WEBSITE_VNET_ROUTE_ALL 1-re van állítva, az összes kimenő forgalom elküldható a virtuális hálózatra.

> [!NOTE]
> `WEBSITE_VNET_ROUTE_ALL` A jelenleg nem támogatott a Windows-tárolókban.
> 

A funkció feldolgozónként csak egy virtuális felületet támogat. Feldolgozónként egy virtuális interfész egy regionális VNet-integrációt jelent App Service tervenként. Az azonos csomagba App Service összes alkalmazás használhatja ugyanazt a VNet-integrációt. Ha egy alkalmazásra van szüksége egy további virtuális hálózathoz való csatlakozáshoz, létre kell hoznia egy App Service tervet. A használt virtuális felület nem olyan erőforrás, amelyhez az ügyfelek közvetlen hozzáféréssel rendelkezik.

A technológia működése miatt a VNet-integrációval használt forgalom nem fog az Azure Network Watcher- vagy NSG-forgalom naplóiban adatokat mutatni.

## <a name="gateway-required-vnet-integration"></a>Átjáró által megkövetelt VNet-integráció

Az átjáró által megkövetelt VNet-integráció támogatja a csatlakozást egy másik régióban lévő virtuális hálózathoz vagy egy klasszikus virtuális hálózathoz. Átjáró által megkövetelt VNet-integráció:

* Lehetővé teszi, hogy egy alkalmazás egyszerre csak egy virtuális hálózathoz csatlakozzon.
* Legfeljebb öt virtuális hálózat integrálható egy App Service tervbe.
* Lehetővé teszi, hogy ugyanazt a VNetet több alkalmazás használja egy App Service-csomagon belül anélkül, hogy ez hatással lenne a virtuális hálózat által App Service számára. Ha hat alkalmazás használja ugyanazt a VNetet ugyanabban a App Service tervben, az egy használt virtuális hálózatnak számít.
* 99,9%-os SLA-t támogat az átjárón érvényes SLA miatt.
* Lehetővé teszi, hogy az alkalmazások azt a DNS-t használják, amellyel a virtuális hálózat konfigurálva van.
* Ehhez Virtual Network pont–hely VPN-sel konfigurált útvonalalapú átjáróra van szükség.

Nem használhatja az átjáró által megkövetelt VNet-integrációt:

* Virtuális hálózat csatlakoztatva Azure ExpressRoute.
* Linux-alkalmazásból.
* Egy [Windows-tárolóból.](quickstart-custom-container.md)
* A szolgáltatásvégpont által védett erőforrásokhoz való hozzáféréshez.
* Az expressRoute-et és a pont–hely vagy a hely–hely VPN-eket is támogató, 2012-es et támogató átjáróval.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Átjáró beállítása az Azure-beli virtuális hálózaton ###

Átjáró létrehozása:

1. [Hozzon létre egy átjáró-alhálózatot][creategatewaysubnet] a virtuális hálózatban.

1. [Hozza létre a VPN-átjárót.][creategateway] Válasszon útvonalalapú VPN-típust.

1. [Állítsa be a pont–hely címeket.][setp2saddresses] Ha az átjáró nincs az alapszintű termékváltozatban, akkor az IKEV2-t le kell tiltani a pont–hely konfigurációban, és ki kell választani az SSTP-t. A pont–hely címtérnek az RFC 1918 címblokkjában kell lennie: 10.0.0.0/8, 172.16.0.0/12 és 192.168.0.0/16.

Ha az átjárót virtuális hálózati App Service való használatra hozza létre, nem kell tanúsítványt feltöltenie. Az átjáró létrehozása 30 percet is igénybe vehet. Az alkalmazást nem tudja integrálni a VNettel, amíg az átjáró ki nincs építve.

### <a name="how-gateway-required-vnet-integration-works"></a>Az átjáró által megkövetelt VNet-integráció működése

Az átjáróhoz szükséges VNet-integráció pont–hely VPN technológiára épül. A pont–hely VPN-ek korlátozzák az alkalmazást tartalmazó virtuális gép hálózati hozzáférését. Az alkalmazások csak hibrid kapcsolatokon vagy VNet-integráción keresztül küldhetnek forgalmat az internetre. Ha az alkalmazás úgy van konfigurálva a portálon, hogy az átjáró által megkövetelt VNet-integrációt használjon, a rendszer egy összetett egyeztetést fog kezelni az Ön nevében, hogy tanúsítványokat hozzon létre és rendeljen hozzá az átjáró és az alkalmazás oldalán. Ennek eredményeképpen az alkalmazások gazdagépeként használt dolgozók közvetlenül csatlakozhatnak a kiválasztott virtuális hálózaton található virtuális hálózati átjáróhoz.

![Az átjáróhoz szükséges VNet-integráció működése][6]

### <a name="access-on-premises-resources"></a>Helyszíni erőforrások elérése

Az alkalmazások úgy férhetnek hozzá a helyszíni erőforrásokhoz, hogy integrálják a hely–hely kapcsolattal rendelkezik virtuális hálózatokkal. Ha átjáró által megkövetelt VNet-integrációt használ, frissítse a helyszíni VPN-átjáró útvonalait a pont–hely cím blokkokkal. A hely–hely VPN első beállításakor a konfigurálására használt szkriptnek megfelelően kell beállítania az útvonalakat. Ha a pont–hely címeket a hely–hely VPN létrehozása után adja hozzá, manuálisan kell frissítenie az útvonalakat. Ennek a módszernek a részletei átjárónként változnak, és itt nem ismertetjük őket. A BGP-t nem lehet hely–hely VPN-kapcsolattal konfigurálni.

Nincs szükség további konfigurációra ahhoz, hogy a regionális VNet-integrációs szolgáltatás a VNeten keresztül a helyszíni erőforrásokhoz érje el. Egyszerűen csatlakoztatnia kell a virtuális hálózatát a helyszíni erőforrásokhoz ExpressRoute vagy egy hely–hely VPN használatával.

> [!NOTE]
> Az átjáró által megkövetelt VNet-integrációs szolgáltatás nem integrál egy alkalmazást egy ExpressRoute-átjáróval rendelkezik virtuális hálózatba. A virtuális hálózat integrációja akkor [][VPNERCoex]sem működik, ha az ExpressRoute-átjáró együtt használható módban van konfigurálva. Ha ExpressRoute-kapcsolaton keresztül kell elérnie az erőforrásokat, használja a regionális VNet-integrációs szolgáltatást vagy egy [App Service Environment,][ASE]amely a virtuális hálózatán fut.
>
>

### <a name="peering"></a>Társviszony-létesítés

Ha társviszonyt használ a regionális VNet-integrációval, nem kell további konfigurációt használnia.

Ha átjáró által megkövetelt VNet-integrációt használ a társviszony-létesítéshez, néhány további elemet is konfigurálnia kell. Társviszony konfigurálása az alkalmazással való munkához:

1. Adjon hozzá egy társviszony-kapcsolatot a virtuális hálózathoz, amelyhez az alkalmazás csatlakozik. A társviszony-létesítés  hozzáadásakor engedélyezze a Virtuális hálózati hozzáférés engedélyezése, majd a **Továbbított** forgalom engedélyezése és az Átjárói átvitel **engedélyezése lehetőséget.**
1. Adjon hozzá egy társviszony-létesítés kapcsolatot a virtuális hálózathoz, amely ahhoz a virtuális hálózathoz van társviszonyban, amelyhez csatlakozik. Amikor hozzáadja a társviszony-kapcsolatot a  cél virtuális hálózaton, engedélyezze a Virtuális hálózati hozzáférés engedélyezése, majd a **Továbbított** forgalom engedélyezése és a Távoli átjárók **engedélyezése lehetőséget.**
1. A **portálon App Service**  >  **tervezze meg a**  >  **hálózattal kapcsolatos VNet-integráció** felhasználói felületét. Válassza ki azt a virtuális hálózat, amelyhez az alkalmazás csatlakozik. Az útválasztás szakaszban adja meg annak a virtuális hálózatnak a címtartományát, amely azzal a virtuális hálózatgal van társviszonyban, amelyhez az alkalmazás csatlakozik.

## <a name="manage-vnet-integration"></a>Virtuálishálózat-integráció kezelése

A virtuális hálózatokkal való csatlakozás és a kapcsolat bontása az alkalmazás szintjén van. A több alkalmazás közötti VNet-integrációt befolyásoló műveletek a App Service szinten vannak. A hálózat > VNet-integrációs portál alkalmazásból lekért részleteket   >   a virtuális hálózatról. Hasonló információkat a hálózat App Service   >    >  **VNet-integrációs** portál App Service csomagjának szintjén láthat.

A VNet-integrációs példány alkalmazásnézetében az egyetlen művelet az alkalmazás leválasztása az aktuálisan csatlakoztatott virtuális hálózatról. Ha le kell választania az alkalmazást egy virtuális hálózatról, válassza a **Leválasztás lehetőséget.** Az alkalmazás akkor indul újra, amikor leválasztja a virtuális hálózatról. A kapcsolat bontása nem módosítja a virtuális hálózatát. Az alhálózat vagy az átjáró nem törlődik. Ha ezután törölni szeretné a virtuális hálózatát, először válassza le az alkalmazást a virtuális hálózatról, és törölje az abban található erőforrásokat, például az átjárókat.

A App Service VNet-integrációs felhasználói felület megjeleníti a virtuális hálózat csomag alkalmazásai által használt összes VNet App Service integrációt. Az egyes virtuális hálózatok részleteinek megtekintése előtt válassza ki a kívánt virtuális hálózatokat. Itt két műveletet hajthat végre az átjáró által megkövetelt VNet-integrációhoz:

* **Szinkronizálási hálózat:** A szinkronizálási hálózati művelet csak az átjárótól függő VNet-integrációs funkcióhoz használatos. A szinkronizálási hálózati művelet biztosítja, hogy a tanúsítványok és a hálózati információk szinkronban legyenek. Ha hozzáadja vagy módosítja a virtuális hálózat DNS-ét, végezzen szinkronizálási hálózati műveletet. Ez a művelet újraindítja az összes olyan alkalmazást, amely ezt a VNetet használja. Ez a művelet nem fog működni, ha különböző előfizetések alkalmazásait és virtuális hálózatait használja.
* **Útvonalak hozzáadása:** Az útvonalak hozzáadása a kimenő forgalmat a virtuális hálózatra vezeti.

A példányhoz rendelt privát IP-címet a környezeti változó (a következő **WEBSITE_PRIVATE_IP.** A Kudu-konzol felhasználói felülete a webalkalmazás számára elérhető környezeti változók listáját is megjeleníti. Ezt az IP-címet az integrált alhálózat címtartománya alapján rendeli hozzá a rendszer. A regionális VNet-integráció esetén az WEBSITE_PRIVATE_IP értéke a delegált alhálózat címtartományának IP-címe, az átjáró által megkövetelt VNet-integráció esetén pedig az érték az Virtual Network-átjárón konfigurált pont–hely címkészlet címtartományának IP-címe. A webalkalmazás ezt az IP-címet fogja használni az erőforrásokhoz való csatlakozáshoz a Virtual Network. 

> [!NOTE]
> A WEBSITE_PRIVATE_IP megváltozhat. Ez azonban egy IP-cím lesz az integrációs alhálózat vagy a pont–hely címtartomány címtartományán belül, ezért a teljes címtartományból engedélyeznie kell a hozzáférést.
>

### <a name="gateway-required-vnet-integration-routing"></a>Átjáró által megkövetelt VNet-integrációs útválasztás
A virtuális hálózatban meghatározott útvonalak az alkalmazásból a virtuális hálózatra irányított forgalomra használhatók. Ha további kimenő forgalmat szeretne küldeni a virtuális hálózatnak, adja hozzá ezeket a címblokkokat itt. Ez a képesség csak az átjáró által megkövetelt VNet-integrációval működik. Az útvonaltáblák nem befolyásolják az alkalmazás forgalmát, ha az átjáró által megkövetelt VNet-integrációt használja, ahogyan a regionális VNet-integráció esetén.

### <a name="gateway-required-vnet-integration-certificates"></a>Átjáró által megkövetelt VNet-integrációs tanúsítványok
Ha az átjáró által megkövetelt VNet-integráció engedélyezve van, a kapcsolat biztonságának biztosításához tanúsítványcserére van szükség. A tanúsítványok mellett a DNS-konfiguráció, az útvonalak és a hálózatot leíró egyéb hasonló dolgok is vannak.

Ha a tanúsítványok vagy a hálózati adatok módosulnak, válassza a **Hálózat szinkronizálása lehetőséget.** Ha a Hálózat **szinkronizálása lehetőséget választja,** rövid ideig megszakad a kapcsolat az alkalmazás és a virtuális hálózat között. Bár az alkalmazás nem indul újra, a kapcsolat megszakadása miatt a hely nem fog megfelelően működni.

## <a name="pricing-details"></a>Díjszabás részletei
A regionális VNet-integrációs szolgáltatás használata nem számít fel további díjakat a App Service tarifacsomag díján túl.

Három díj az átjáró által megkövetelt VNet-integrációs funkció használatával kapcsolatos:

* **App Service tarifacsomag** díjai: Az alkalmazásoknak Standard, Premium, PremiumV2 vagy PremiumV3 vagy PrémiumV3 App Service kell lennie. További információ a költségekről: App Service [díjszabása.][ASPricing]
* **Adatátviteli költségek:** Az adatbeadási díj akkor is díjat számít fel, ha a virtuális hálózat ugyanabban az adatközpontban található. Ezeket a díjakat az Adatátvitel [díjszabása tartalmazza.][DataPricing]
* **VPN Gateway-költségek:** A pont–hely VPN-hez szükséges virtuális hálózati átjáró költsége van. További információ: [A VPN Gateway díjszabása.][VNETPricing]

## <a name="troubleshooting"></a>Hibaelhárítás

> [!NOTE]
> A VNET-integráció nem támogatott a docker compose-forgatókönyvekhez a App Service.
> Azure Functions a rendszer figyelmen kívül hagyja a hozzáférési korlátozásokat, ha privát végpont van jelen.
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

A cli-támogatás regionális VNet-integrációhoz érhető el. A következő parancsok eléréséhez telepítse [az Azure CLI-t.][installCLI]

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.

az appservice vnet-integration --help

Group
    az appservice vnet-integration : A method that lists the virtual network
    integrations used in an appservice plan.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    list : List the virtual network integrations used in an appservice plan.
```

A regionális VNet-integráció PowerShell-támogatása is elérhető, de létre kell hoznia egy általános erőforrást a resourceID alhálózat tulajdonságtömbje segítségével

```azurepowershell
# Parameters
$sitename = 'myWebApp'
$resourcegroupname = 'myRG'
$VNetname = 'myVNet'
$location = 'myRegion'
$integrationsubnetname = 'myIntegrationSubnet'
$subscriptionID = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'

#Property array with the SubnetID
$properties = @{
  subnetResourceId = "/subscriptions/$subscriptionID/resourceGroups/$resourcegroupname/providers/Microsoft.Network/virtualNetworks/$VNetname/subnets/$integrationsubnetname"
}

#Creation of the VNet integration
$vNetParams = @{
  ResourceName = "$sitename/VirtualNetwork"
  Location = $location
  ResourceGroupName = $resourcegroupname
  ResourceType = 'Microsoft.Web/sites/networkConfig'
  PropertyObject = $properties
}
New-AzResource @vNetParams
```


Az átjáró által megkövetelt VNet-integrációhoz a PowerShell App Service azure-beli virtuális hálózattal integrálhatja a virtuális hálózatokat. Futtatásra kész szkriptért lásd: Alkalmazás csatlakoztatása Azure App Service [Azure-beli virtuális hálózathoz.](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli
[privateendpoints]: networking/private-endpoint.md
