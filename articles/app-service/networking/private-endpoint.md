---
title: Privát kapcsolat létrehozása egy Azure-webalkalmazáshoz privát végpont használatával
description: Privát kapcsolódás egy webalkalmazáshoz az Azure Private Endpoint használatával
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/16/2021
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 4de405e07a9ae9d1efb33f2cee3630a1eefdef33
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655903"
---
# <a name="using-private-endpoints-for-azure-web-app"></a>Az Azure-webalkalmazás privát végpontjainak használata

> [!IMPORTANT]
> A privát végpontok elérhetők a Windows-és Linux-webalkalmazásokhoz, amelyek tárolóban vannak, és nem az alábbi App Service-csomagokon futnak: **elkülönített**, **PremiumV2**, **PremiumV3**, **functions Premium** (más néven rugalmas Prémium csomag). 

Az Azure-webalkalmazás privát végpontjának használatával engedélyezheti a magánhálózaton található ügyfelek számára, hogy biztonságosan hozzáférjenek az alkalmazáshoz privát kapcsolaton keresztül. A privát végpont az Azure VNet IP-címét használja. A magánhálózaton lévő ügyfél és a webalkalmazás közötti hálózati forgalom a VNet és a Microsoft gerinc hálózatán található privát kapcsolaton keresztül történik, ami kiküszöböli a nyilvános internetről való kitettséget.

A webalkalmazás privát végpontjának használata lehetővé teszi a következőket:

- A webalkalmazás biztonságossá tételéhez konfigurálja a privát végpontot a nyilvános expozíció kiküszöbölése érdekében.
- Biztonságosan csatlakozhat a webalkalmazáshoz olyan helyszíni hálózatokból, amelyek VPN-vagy ExpressRoute-alapú privát kapcsolattal csatlakoznak a VNet.
- Kerülje a VNet származó adatok kiszűrése. 

Ha csak biztonságos kapcsolatra van szüksége a VNet és a webalkalmazás között, a legegyszerűbb megoldás a szolgáltatási végpont. Ha a webalkalmazást egy Azure-átjárón keresztül is el kell érnie, egy regionálisan felhasználható VNet vagy egy globálisan felhasználható VNet, a magánhálózati végpont a megoldás.  

További információ: szolgáltatás- [végpontok][serviceendpoint].

## <a name="conceptual-overview"></a>Fogalmi áttekintés

A privát végpont egy speciális hálózati adapter (NIC) az Azure-webalkalmazáshoz a Virtual Network (VNet) egyik alhálózatában.
Amikor létrehoz egy privát végpontot a webalkalmazáshoz, biztonságos kapcsolatot biztosít a magánhálózaton és a webalkalmazásban található ügyfelek között. A magánhálózati végpont IP-címet kap a VNet IP-címének tartományához.
A magánhálózati végpont és a webalkalmazás közötti kapcsolat biztonságos [privát hivatkozást][privatelink]használ. A magánhálózati végpont csak a webalkalmazásba érkező bejövő folyamatokhoz használható. A kimenő folyamatok nem fogják használni ezt a magánhálózati végpontot, de a kimenő folyamatokat egy másik alhálózaton is beinjektálhatja a [VNet-integrációs szolgáltatáson][vnetintegrationfeature]keresztül.

Az alhálózatot, amelyhez a privát végpontot csatlakoztatja, más erőforrásokkal is rendelkezhet, nincs szükség dedikált üres alhálózatra.
A privát végpontot a webalkalmazástól eltérő régióban is üzembe helyezheti. 

> [!Note]
>A VNet-integrációs szolgáltatás nem tudja használni a magánhálózati végponttal megegyező alhálózatot, ez a VNet-integrációs szolgáltatás korlátozása.

Biztonsági szempontból:

- Ha engedélyezi a privát végpontokat a webalkalmazáshoz, az összes nyilvános hozzáférés le van tiltva.
- Több privát végpontot is engedélyezhet más virtuális hálózatok és alhálózatokban, beleértve a más régiókban található virtuális hálózatok is.
- A magánhálózati végpont hálózati adapterének IP-címének dinamikusnak kell lennie, de addig marad, amíg nem törli a privát végpontot.
- A privát végpont hálózati adaptere nem rendelkezhet társított NSG.
- A privát végpontot futtató alhálózathoz NSG társítható, de le kell tiltania a hálózati házirendek kényszerítését a privát végponton: a [hálózati házirendek letiltása privát végpontok esetén][disablesecuritype]című témakört. Ennek eredményeképpen egyetlen NSG sem tudja szűrni a privát végponthoz való hozzáférést.
- Ha engedélyezi a privát végpontot a webalkalmazáshoz, a rendszer nem értékeli ki a webalkalmazás [hozzáférési korlátozásait][accessrestrictions] .
- Az adatok kiszűrése kockázatát a VNet távolíthatja el az összes olyan NSG-szabály eltávolításával, ahol a cél az Internet vagy az Azure-szolgáltatások címkéje. Ha privát végpontot telepít egy webalkalmazáshoz, akkor csak a privát végponton keresztül érheti el ezt az adott webalkalmazást. Ha van egy másik webalkalmazása, egy másik dedikált privát végpontot kell telepítenie ehhez a webalkalmazáshoz.

A webalkalmazás webes HTTP-naplóiban megtalálja az ügyfél forrásának IP-címét. Ez a funkció a TCP proxy protokoll használatával valósul meg, amely a-ügyfél IP-tulajdonságát továbbítja a webalkalmazásnak. További információ: a [kapcsolatok adatainak beszerzése a TCP proxy v2 használatával][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![A Web App Private Endpoint globális áttekintése](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Ha privát végpontot használ a webalkalmazáshoz, a kért URL-címnek meg kell egyeznie a webalkalmazás nevével. Alapértelmezés szerint mywebappname.azurewebsites.net.

Alapértelmezés szerint privát végpont nélkül a webalkalmazás nyilvános neve a fürt kanonikus neve.
A névfeloldás például a következő lesz:

|Név |Típus |Érték |
|-----|-----|------|
|mywebapp.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154| 


Privát végpont telepítésekor a DNS-bejegyzést úgy frissítjük, hogy a kanonikus név mywebapp.privatelink.azurewebsites.net mutasson.
A névfeloldás például a következő lesz:

|Név |Típus |Érték |Megjegyzés |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154|< – ez a nyilvános IP-cím nem az Ön privát végpontja, 403-as hibát fog kapni|

Egy privát DNS-kiszolgálót vagy egy Azure DNS privát zónát kell beállítania, hogy tesztelje a számítógép gazdagépének bejegyzését.
A létrehozandó DNS-zóna a következő: **privatelink.azurewebsites.net**. Regisztrálja a webalkalmazás rekordját egy rekorddal és a magánhálózati végpont IP-címével.
A névfeloldás például a következő lesz:

|Név |Típus |Érték |Megjegyzés |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|< – az Azure ezt a bejegyzést az Azure nyilvános DNS-ben hozza létre, hogy az App Service-t az privatelink irányítsa, és ezt az Egyesült Államok felügyeli|
|mywebapp.privatelink.azurewebsites.net|A|10.10.10.8|< – ezt a bejegyzést a DNS-rendszerben úgy kezelheti, hogy a magánhálózati végpont IP-címére mutasson.|

A DNS-konfiguráció után a webalkalmazást a mywebappname.azurewebsites.net alapértelmezett névvel együtt érheti el. Ezt a nevet kell használnia, mert a rendszer az alapértelmezett tanúsítványt adja ki a *. azurewebsites.net.


Ha egyéni DNS-nevet kell használnia, hozzá kell adnia az egyéni nevet a webalkalmazásban. Az egyéni nevet úgy kell érvényesíteni, mint bármely egyéni nevet, a nyilvános DNS-feloldást használva. További információ: [Egyéni DNS-ellenőrzés][dnsvalidation].

A kudu-konzol vagy a kudu REST API (az Azure DevOps saját üzemeltetésű ügynökökkel való üzembe helyezése) esetében két rekordot kell létrehoznia a Azure DNS saját zónájában vagy az egyéni DNS-kiszolgálón. 

| Név | Típus | Érték |
|-----|-----|-----|
| mywebapp.privatelink.azurewebsites.net | A | PrivateEndpointIP | 
| mywebapp.scm.privatelink.azurewebsites.net | A | PrivateEndpointIP | 



## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása][pricing].

## <a name="limitations"></a>Korlátozások

Ha az Azure-függvényt privát végponttal rendelkező rugalmas prémium szintű csomagban használja, a függvény futtatásához vagy végrehajtásához az Azure web Portalon közvetlen hálózati hozzáférésre van szükség, vagy HTTP 403-hibaüzenetet fog kapni. Más szóval a böngészőnek képesnek kell lennie arra, hogy elérje a privát végpontot, hogy végrehajtsa a függvényt az Azure web Portalon. 

Egy adott webalkalmazáshoz akár 100 privát végpontot is csatlakozhat.

A Távoli hibakeresési funkció nem érhető el, ha a magánhálózati végpont engedélyezve van a webalkalmazáshoz. A javaslat a kód üzembe helyezése egy tárolóhelyre és a távoli hibakeresés.

Az FTP-hozzáférés a bejövő nyilvános IP-címen keresztül érhető el. A magánhálózati végpont nem támogatja az FTP-hozzáférést a webalkalmazáshoz.

Rendszeresen fejlesztjük a privát kapcsolat funkciót és a privát végpontot, és a korlátozásokról a [jelen cikkben][pllimitations] tájékozódhat.

## <a name="next-steps"></a>Következő lépések

- Ha privát végpontot szeretne telepíteni a webalkalmazáshoz a portálon keresztül, tekintse meg a [webalkalmazásokhoz való privát kapcsolódás a portálon][howtoguide1] című témakört.
- Ha privát végpontot szeretne üzembe helyezni a webalkalmazáshoz az Azure CLI használatával, tekintse meg a [webalkalmazásokhoz való privát kapcsolódás az Azure CLI-vel][howtoguide2] című témakört.
- Privát végpont üzembe helyezése a webalkalmazáshoz a PowerShell használatával: [webalkalmazáshoz való privát kapcsolódás a PowerShell][howtoguide3] -lel
- Privát végpont üzembe helyezése a webalkalmazáshoz az Azure-sablonnal: [hogyan csatlakozhat egy webalkalmazáshoz az Azure template][howtoguide4] használatával.
- Végpontok közötti példa: előtér-webalkalmazások biztonságos háttér-webalkalmazáshoz való összekötése VNet-befecskendezéssel és saját végponttal [az ARM][howtoguide5] sablonnal
- Végpontok közötti példa: előtér-webalkalmazások biztonságos háttér-webalkalmazáshoz való összekötése VNet-befecskendezéssel és privát végponttal a Terraform-vel – lásd ez a [minta][howtoguide6]


<!--Links-->
[serviceendpoint]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[privatelink]: ../../private-link/private-link-overview.md
[vnetintegrationfeature]: ../web-sites-integrate-with-vnet.md
[disablesecuritype]: ../../private-link/disable-private-endpoint-network-policy.md
[accessrestrictions]: ../app-service-ip-restrictions.md
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: ../app-service-web-tutorial-custom-domain.md
[pllimitations]: ../../private-link/private-endpoint-overview.md#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide1]: ../../private-link/tutorial-private-endpoint-webapp-portal.md
[howtoguide2]: ../scripts/cli-deploy-privateendpoint.md
[howtoguide3]: ../scripts/powershell-deploy-private-endpoint.md
[howtoguide4]: ../scripts/template-deploy-private-endpoint.md
[howtoguide5]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-privateendpoint-vnet-injection
[howtoguide6]: ../scripts/terraform-secure-backend-frontend.md
