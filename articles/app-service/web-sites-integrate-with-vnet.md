---
title: Alkalmazás integrálása az Azure Virtual Network
description: Az alkalmazás integrálása Azure App Service Azure-beli virtuális hálózatokkal.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4666f03e529c568177ff56a2db84e226bc906132
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182910"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Alkalmazás integrálása Azure-beli virtuális hálózattal

Ez a cikk ismerteti a Azure App Service VNet integrációs funkciót, valamint azt, hogyan állíthatja be a [Azure app Service](./overview.md)alkalmazásokkal. Az [azure Virtual Network][VNETOverview] (virtuális hálózatok) használatával számos Azure-erőforrást elhelyezhet egy nem internetre irányítható hálózaton. A VNet integrációs funkciója lehetővé teszi, hogy alkalmazásai hozzáférjenek az erőforrásokhoz a vagy a VNet keresztül. A VNet-integráció nem teszi lehetővé, hogy az alkalmazások magánjellegű módon férhessenek hozzá.

Azure App Service két változattal rendelkezik a VNet-integrációs szolgáltatásban:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>VNet-integráció engedélyezése

1. Nyissa meg a **hálózati** felhasználói felületet a app Service portálon. A **VNet-integráció** területen válassza **a kattintson ide a konfiguráláshoz** lehetőséget.

1. Válassza a **VNet hozzáadása** lehetőséget.

   ![VNet-integráció kiválasztása][1]

1. A legördülő lista tartalmazza az előfizetésben található összes Azure Resource Manager virtuális hálózatot ugyanabban a régióban. Ez a lista az összes többi régióban található Resource Manager-alapú virtuális hálózatok listáját tartalmazza. Válassza ki a VNet, amelybe integrálni szeretné a-t.

   ![Válassza ki a VNet][2]

   * Ha a VNet ugyanabban a régióban található, hozzon létre egy új alhálózatot, vagy válasszon ki egy üres, már meglévő alhálózatot.
   * Egy másik régióban lévő VNet kiválasztásához rendelkeznie kell olyan VNet-átjáróval, amelynél engedélyezve van a pont – hely.
   * A klasszikus VNet való integráláshoz a **Virtual Network** legördülő lista kiválasztása helyett válassza a **kattintson ide a klasszikus VNet való kapcsolódáshoz** lehetőséget. Válassza ki a kívánt klasszikus virtuális hálózatot. A célként megadott VNet már rendelkeznie kell egy, a pont – hely kapcsolattal rendelkező Virtual Network átjáróval.

    ![Klasszikus VNet kiválasztása][3]

Az integráció során az alkalmazás újraindul. Az integráció befejezését követően megtekintheti a-nal integrált VNet részleteit.

## <a name="regional-vnet-integration"></a>Regionális VNet-integráció

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>A regionális VNet-integráció működése

A App Serviceban lévő alkalmazások a feldolgozói szerepkörökben vannak tárolva. Az alapszintű és a magasabb díjszabási csomagok olyan dedikált üzemeltetési csomagok, amelyeken nincs más, ugyanazon dolgozón futó ügyfél munkaterhelése. A regionális VNet-integráció úgy működik, hogy a virtuális adaptereket a meghatalmazott alhálózat címeivel csatlakoztatja. Mivel a feladó címe a VNet található, a VNet vagy azon keresztül érheti el a legtöbb dolgot, mint például egy virtuális gép a VNet. A hálózatkezelés megvalósítása eltér a virtuális gép VNet való futtatásának. Ezért nem érhető el néhány hálózatkezelési funkció ehhez a szolgáltatáshoz.

![A regionális VNet-integráció működése][5]

Ha a regionális VNet-integráció engedélyezve van, az alkalmazás a normál módon megegyező csatornákon keresztül teszi elérhetővé az internet felé irányuló hívásokat. Az alkalmazás-tulajdonságok portálon felsorolt kimenő címek az alkalmazás által még mindig használt címek. Az alkalmazásban a végpontok által biztosított szolgáltatásokra irányuló hívások, illetve az RFC 1918-címek bekerülnek a VNet. Ha WEBSITE_VNET_ROUTE_ALL értéke 1, a rendszer minden kimenő forgalmat elküldhet a VNet.

> [!NOTE]
> `WEBSITE_VNET_ROUTE_ALL` jelenleg nem támogatott Windows-tárolókban.
> 

A szolgáltatás csak egy virtuális felületet támogat egy feldolgozón. A munkavégzők egy virtuális felülete egy regionális VNet integrációt jelent App Service csomagon keresztül. Az azonos App Service tervben szereplő összes alkalmazás ugyanazt a VNet-integrációt használhatja. Ha egy alkalmazásnak egy további VNet való kapcsolódásra van szüksége, létre kell hoznia egy másik App Service csomagot. A használt virtuális felület nem erőforrás, amelyhez az ügyfeleknek közvetlen hozzáférésük van.

A technológia működésének jellegéből adódóan a VNet-integrációhoz használt forgalom nem jelenik meg az Azure Network Watcher vagy a NSG flow naplóiban.

## <a name="gateway-required-vnet-integration"></a>Átjáró – szükséges VNet-integráció

Az átjáróval megkövetelt VNet-integráció támogatja a csatlakozást egy másik régióban lévő VNet vagy egy klasszikus virtuális hálózathoz. Átjáró – kötelező VNet-integráció:

* Lehetővé teszi, hogy egy alkalmazás egyszerre csak egy VNet kapcsolódjon.
* Lehetővé teszi akár öt virtuális hálózatok integrálását egy App Service tervbe.
* Lehetővé teszi, hogy ugyanazt a VNet egy App Service-csomagban több alkalmazás is használja anélkül, hogy ez befolyásolná a App Service-csomag által használható teljes számot. Ha hat alkalmazása ugyanazt a VNet használja ugyanabban a App Service-csomagban, akkor a rendszer egyetlen használt VNet számít.
* A 99,9%-os SLA-t támogat az átjáró SLA-ja miatt.
* Lehetővé teszi, hogy az alkalmazások a VNet konfigurált DNS-t használják.
* Egy olyan Virtual Network útválasztási átjárót igényel, amely egy SSTP pont – hely VPN-kapcsolattal van konfigurálva ahhoz, hogy egy alkalmazáshoz lehessen csatlakozni.

Az átjáróhoz szükséges VNet-integráció nem használható:

* Az Azure ExpressRoute-vel összekapcsolt VNet.
* Linux-alkalmazásokból.
* Egy [Windows-tárolóból](quickstart-custom-container.md).
* A szolgáltatási végpont által védett erőforrások eléréséhez.
* Egy párhuzamos átjáróval, amely támogatja mind a ExpressRoute, mind a pont-hely vagy a helyek közötti VPN-eket.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Átjáró beállítása az Azure-beli virtuális hálózatban ###

Átjáró létrehozása:

1. [Hozzon létre egy átjáró-alhálózatot][creategatewaysubnet] a VNet.

1. [Hozza létre a VPN-átjárót][creategateway]. Válasszon egy Route-alapú VPN-típust.

1. [Állítsa be a pont – hely címeket][setp2saddresses]. Ha az átjáró nem az alapszintű SKU-ban található, akkor a IKEV2 le kell tiltani a pont – hely konfigurációban, és az SSTP-t ki kell választani. A pont – hely címtartomány az RFC 1918-es címek 10.0.0.0/8, 172.16.0.0/12 és 192.168.0.0/16 blokkjában kell, hogy legyen.

Ha az átjárót App Service VNet-integrációval való használatra hozza létre, nincs szükség a tanúsítvány feltöltésére. Az átjáró létrehozása akár 30 percet is igénybe vehet. Az alkalmazás nem integrálható a VNet, amíg az átjáró nincs kiépítve.

### <a name="how-gateway-required-vnet-integration-works"></a>Az átjáróhoz szükséges VNet-integráció működése

Az átjáróhoz szükséges VNet-integráció a pont – hely típusú VPN-technológiára épül. A pont – hely VPN-EK korlátozzák az alkalmazást futtató virtuális gép hálózati hozzáférését. Az alkalmazások csak Hibrid kapcsolatok vagy VNet-integráción keresztül küldhetnek forgalmat az internetre. Ha az alkalmazás az átjáróhoz szükséges VNet-integráció használatára van konfigurálva, akkor az Ön nevében egy összetett egyeztetést kell felügyelni az átjárón és az alkalmazás oldalán lévő tanúsítványok létrehozásához és hozzárendeléséhez. Ennek eredményeképpen az alkalmazások üzemeltetéséhez használt munkavégzők közvetlenül csatlakozhatnak a virtuális hálózati átjáróhoz a kiválasztott VNet.

![Az átjáróhoz szükséges VNet-integráció működése][6]

### <a name="access-on-premises-resources"></a>Helyszíni erőforrások elérése

Az alkalmazások a helyek közötti kapcsolatokkal rendelkező virtuális hálózatok integrálásával érhetik el a helyszíni erőforrásokat. Ha átjáróval megkövetelt VNet-integrációt használ, frissítse a helyszíni VPN Gateway-útvonalakat a pont – hely címekkel. Amikor először állítja be a helyek közötti VPN-t, a konfigurálásához használt parancsfájloknak megfelelően kell beállítania az útvonalakat. Ha a pont – hely címeket a helyek közötti VPN létrehozása után adja hozzá, akkor manuálisan kell frissítenie az útvonalakat. Az egyes átjárók működésével kapcsolatos részletekért lásd itt. A BGP-t nem lehet helyek közötti VPN-kapcsolattal konfigurálni.

Nincs szükség további konfigurációra ahhoz, hogy a regionális VNet integrációs szolgáltatás elérje a VNet a helyszíni erőforrásokra. A ExpressRoute vagy helyek közötti VPN használatával egyszerűen össze kell kapcsolnia a VNet a helyszíni erőforrásokhoz.

> [!NOTE]
> Az átjáróhoz szükséges VNet integrációs szolgáltatás nem integrálja az alkalmazást olyan VNet, amely ExpressRoute-átjáróval rendelkezik. A VNet-integráció nem működik, még akkor is, ha az ExpressRoute-átjáró [egyidejű módban][VPNERCoex]van konfigurálva. Ha egy ExpressRoute-kapcsolaton keresztül kell hozzáférni az erőforrásokhoz, használja a regionális VNet integrációs szolgáltatását, vagy egy [app Service Environment][ASE], amely a VNet fut.
>
>

### <a name="peering"></a>Társviszony-létesítés

Ha a regionális VNet-integrációval való társítást használ, nincs szükség további konfigurálásra.

Ha átjáróval megkövetelt VNet-integrációt használ a társításhoz, néhány további elemet is be kell állítania. Az alkalmazással való együttműködés konfigurálása:

1. Adjon hozzá egy egyenrangú kapcsolatot azon a VNet, amelyhez az alkalmazás csatlakozik. A társítási kapcsolat hozzáadásakor engedélyezze a **virtuális hálózati hozzáférés** engedélyezése beállítást, és válassza a **továbbított forgalom** engedélyezése és az **átjáró átvitelének** engedélyezése lehetőséget.
1. Hozzon létre egy egyenrangú kapcsolatot a VNet, amely a csatlakoztatott VNet van csatlakoztatva. Amikor hozzáadja a társítási kapcsolatot a cél VNet, engedélyezze a **virtuális hálózati hozzáférés** engedélyezése és a **továbbított forgalom** engedélyezése és a **távoli átjárók engedélyezése** beállítást.
1. Nyissa meg a **app Service terv**  >  **hálózatkezelés**  >  **VNet-integráció** felhasználói felületét a portálon. Válassza ki azt a VNet, amelyhez az alkalmazás csatlakozik. Az Útválasztás szakaszban adja meg annak a VNet a VNet, amelyhez az alkalmazás csatlakoztatva van.

## <a name="manage-vnet-integration"></a>Virtuálishálózat-integráció kezelése

A VNet való csatlakozás és a kapcsolat bontása alkalmazási szinten történik. A több alkalmazás közötti VNet-integrációt befolyásoló műveletek a App Servicei csomag szintjén találhatók. Az alkalmazás > **hálózatkezelés**  >  **VNet-integrációs** portálján megtekintheti a VNet adatait. A app Servicei terv szintjén hasonló információkat tekinthet meg a   >  **hálózatkezelés**  >  **VNet-integrációs** portál app Service megtervezése című témakörben.

Az egyetlen művelet, amelyet a VNet-integrációs példány alkalmazás nézetében elvégezhet, az alkalmazás leválasztása a VNet, amelyhez jelenleg csatlakozik. Az alkalmazás VNet való leválasztásához válassza a **Leválasztás** lehetőséget. Az alkalmazás újraindul, amikor leválaszt egy VNet. A kapcsolat bontása nem módosítja a VNet. Az alhálózat vagy átjáró nem törlődik. Ha ezt követően törölni szeretné a VNet, először válassza le az alkalmazást a VNet, és törölje az abban lévő erőforrásokat, például az átjárókat.

A App Service terv VNet-integrációs felhasználói felülete megjeleníti az App Service-csomagban lévő alkalmazások által használt összes VNet-integrációt. Az egyes VNet részleteinek megtekintéséhez válassza ki azt a VNet, amelyre kíváncsi. Az átjárók számára szükséges VNet-integráció két műveletet hajt végre:

* **Szinkronizáló hálózat**: a szinkronizálási hálózati művelet csak az átjárótól függő VNet-integrációs szolgáltatás esetében használatos. A szinkronizálási hálózati művelet végrehajtása biztosítja, hogy a tanúsítványok és a hálózati adatok szinkronban legyenek. Ha hozzáadja vagy megváltoztatja a VNet DNS-t, hajtson végre egy szinkronizálási hálózati műveletet. Ez a művelet újraindítja az összes olyan alkalmazást, amely ezt a VNet használja. Ez a művelet nem működik, ha egy alkalmazást és egy, a különböző előfizetésekhez tartozó vnet használ.
* **Útvonalak hozzáadása**: útvonalak hozzáadása a kimenő forgalmat a VNet.

A példányhoz rendelt magánhálózati IP-cím a ( **WEBSITE_PRIVATE_IP**) környezeti változón keresztül érhető el. A kudu-konzol felhasználói felületén a webalkalmazás számára elérhető környezeti változók listája is látható. Ezt az IP-címet az integrált alhálózat címtartományból rendeli hozzá a rendszer. A regionális VNet-integráció esetében a WEBSITE_PRIVATE_IP értéke a delegált alhálózat címtartományból származó IP-cím, az átjárók számára szükséges VNet-integráció esetében pedig az érték a Virtual Network átjárón konfigurált pont – hely címkészlet IP-címe. Ezt az IP-címet fogja használni a webalkalmazás, hogy az Virtual Networkon keresztül kapcsolódjon az erőforrásokhoz. 

> [!NOTE]
> A WEBSITE_PRIVATE_IP értéke a Change értékre van kötve. Azonban az integrációs alhálózat vagy a pont – hely címtartomány tartományán belüli IP-cím lesz, így engedélyezni kell a hozzáférést a teljes címtartományból.
>

### <a name="gateway-required-vnet-integration-routing"></a>Átjáró – kötelező VNet integrációs Útválasztás
A VNet meghatározott útvonalak az alkalmazásból a VNet irányuló forgalom irányítására szolgálnak. Ha további kimenő forgalmat szeretne elküldeni a VNet, adja hozzá ezeket a címeket. Ez a funkció csak az átjárók számára szükséges VNet-integrációval működik. Az útválasztási táblázatok nem érintik az alkalmazás adatforgalmát, ha átjáróval megkövetelt VNet-integrációt használ, ahogyan azt a regionális VNet-integrációval végzik.

### <a name="gateway-required-vnet-integration-certificates"></a>Átjáró – kötelező VNet integrációs tanúsítványok
Ha az átjáróval megkövetelt VNet-integráció engedélyezve van, a tanúsítványok biztonságának biztosításához szükség van a tanúsítványok cseréjére. A tanúsítványokkal együtt a DNS-konfiguráció, az útvonalak és más hasonló, a hálózatot leíró dolgok.

Ha a tanúsítványok vagy a hálózati adatok módosulnak, válassza a **szinkronizálás hálózat** lehetőséget. Ha a **szinkronizálási hálózat** lehetőséget választja, az alkalmazás és a VNet közötti kapcsolat rövid kimaradást okoz. Az alkalmazás nem indul újra, a kapcsolat elvesztése miatt előfordulhat, hogy a hely nem működik megfelelően.

## <a name="pricing-details"></a>Díjszabás részletei
A regionális VNet-integrációs szolgáltatás nem számít fel további díjat a App Service csomag díjszabási díjain túli használatért.

Az átjáróhoz szükséges VNet-integrációs funkció három díjat tartalmaz:

* **App Service csomag díjszabása**: az alkalmazásoknak standard, prémium, PremiumV2 vagy PremiumV3 app Service csomaggal kell rendelkezniük. További információ ezekről a költségekről: [app Service díjszabása][ASPricing].
* **Adatátviteli költségek**: a kimenő adatforgalomért akkor is díjat számítunk fel, ha a VNet ugyanabban az adatközpontban található. Ezeket a díjakat [adatátvitel díjszabása][DataPricing]ismerteti.
* **VPN Gateway – költségek**: a pont – hely típusú VPN-hez szükséges virtuális hálózati átjáró díja. További információ: a [VPN Gateway díjszabása][VNETPricing].

## <a name="troubleshooting"></a>Hibaelhárítás

> [!NOTE]
> A VNET-integráció nem támogatott a App Service Docker-összeállítási forgatókönyvek esetén.
> A Azure Functions hozzáférési korlátozásokat a rendszer figyelmen kívül hagyja, ha van privát végpont.
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

A CLI-támogatás a regionális VNet-integrációhoz érhető el. Az alábbi parancsok eléréséhez [telepítse az Azure CLI][installCLI]-t.

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

A regionális VNet-integráció PowerShell-támogatása is elérhető, de létre kell hoznia egy általános erőforrást az alhálózat resourceID

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


Az átjárók számára szükséges VNet-integrációhoz a PowerShell használatával integrálhatja App Serviceeit egy Azure-beli virtuális hálózattal. A használatra kész parancsfájlhoz lásd: [alkalmazás Összekötése Azure app Service egy Azure-beli virtuális hálózatban](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


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
