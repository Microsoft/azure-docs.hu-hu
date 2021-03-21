---
title: Azure-SSIS Integration Runtime csatlakoztatása virtuális hálózathoz
description: Ismerje meg, hogyan csatlakozhat Azure-SSIS integrációs futtatókörnyezethez egy Azure-beli virtuális hálózathoz.
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/02/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 9a82b305adec1385bf659987ea39df6bb953cd70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370971"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure-SSIS Integration Runtime csatlakoztatása virtuális hálózathoz

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ha SQL Server Integration Servicest (SSIS) használ a Azure Data Factoryban, akkor a következő esetekben csatlakoztatnia kell az Azure-SSIS Integration Runtime (IR)-t egy Azure-beli virtuális hálózathoz:

- A helyszíni adattárakhoz szeretne csatlakozni a Azure-SSIS IR futó SSIS-csomagokból, a saját üzemeltetésű integrációs modul konfigurálása vagy kezelése nélkül. 

- A SSIS katalógus-adatbázist (SSISDB) az IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy a privát végponttal rendelkező SQL felügyelt példányban szeretné üzemeltetni Azure SQL Database. 

- A virtuális hálózati szolgáltatás-végpontokkal konfigurált Azure-erőforrásokhoz szeretne csatlakozni a Azure-SSIS IRon futó SSIS-csomagokból.

- Az IP-tűzfalszabályok által konfigurált adattárakhoz/erőforrásokhoz szeretne csatlakozni a Azure-SSIS IRon futó SSIS-csomagokból.

Data Factory lehetővé teszi a Azure-SSIS IR csatlakoztatását a klasszikus üzemi modellen vagy a Azure Resource Manager üzemi modellen keresztül létrehozott virtuális hálózathoz.

> [!IMPORTANT]
> A klasszikus virtuális hálózat elavult, ezért inkább a Azure Resource Manager virtuális hálózatot használja.  Ha már használja a klasszikus virtuális hálózatot, a lehető leghamarabb váltson a Azure Resource Manager virtuális hálózatra.

Az [Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) konfigurálása egy virtuális hálózathoz való csatlakozáshoz](tutorial-deploy-ssis-virtual-network.md) oktatóanyag a minimális lépéseket mutatja be a Azure Portalon keresztül. Ez a cikk az oktatóanyagon alapul, és ismerteti az összes választható feladatot:

- Virtuális hálózat (klasszikus) használata esetén.
- Ha saját nyilvános IP-címeit hozza a Azure-SSIS IRhoz.
- Ha saját tartománynévrendszer-(DNS-) kiszolgálót használ.
- Ha hálózati biztonsági csoportot (NSG) használ az alhálózaton.
- Ha az Azure ExpressRoute vagy egy felhasználó által megadott útvonalat (UDR) használ.
- Ha testreszabott Azure-SSIS IR használ.
- Ha az Azure PowerShell üzembe helyezését használja.

## <a name="access-to-on-premises-data-stores"></a>Hozzáférés a helyszíni adattárakhoz

Ha a SSIS-csomagok hozzáférnek a helyszíni adattárakhoz, csatlakozhat a Azure-SSIS IR egy olyan virtuális hálózathoz, amely a helyszíni hálózathoz csatlakozik. Vagy konfigurálhatja és kezelheti a Azure-SSIS IR saját üzemeltetésű IR-proxyját. További információ: saját üzemeltetésű integrációs modul [beállítása proxyként egy Azure-SSIS IRhoz](./self-hosted-integration-runtime-proxy-ssis.md). 

A Azure-SSIS IR virtuális hálózathoz való csatlakoztatásakor jegyezze fel ezeket a fontos pontokat: 

- Ha nincs virtuális hálózat csatlakoztatva a helyszíni hálózathoz, először hozzon létre egy [Azure Resource Manager virtuális hálózatot](../virtual-network/quick-create-portal.md#create-a-virtual-network) a Azure-SSIS IRhoz való csatlakozáshoz. Ezután konfiguráljon egy helyek közötti [VPN Gateway-kapcsolat](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) vagy a virtuális hálózat [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) -kapcsolatát a helyszíni hálózatra. 

- Ha egy Azure Resource Manager virtuális hálózat már csatlakoztatva van a helyszíni hálózathoz ugyanazon a helyen, mint a Azure-SSIS IR, akkor csatlakoztathatja az IR-t a virtuális hálózathoz. 

- Ha egy klasszikus virtuális hálózat már csatlakoztatva van a helyszíni hálózathoz a Azure-SSIS IR egy másik helyen, létrehozhat egy [Azure Resource Manager virtuális hálózatot](../virtual-network/quick-create-portal.md#create-a-virtual-network) a Azure-SSIS IRhoz való csatlakozáshoz. Ezután konfiguráljon egy [klasszikus – Azure Resource Manager virtuális hálózati](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) kapcsolatokat. 
 
- Ha egy Azure Resource Manager virtuális hálózat már csatlakoztatva van a helyszíni hálózathoz a Azure-SSIS IR egy másik helyén, először hozzon létre egy [Azure Resource Manager virtuális hálózatot](../virtual-network/quick-create-portal.md#create-a-virtual-network) a Azure-SSIS IRhoz való csatlakozáshoz. Ezután konfiguráljon egy Azure Resource Manager – Azure Resource Manager virtuális hálózati kapcsolatokat. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>A SSIS-katalógus üzemeltetése SQL Database

Ha a SSIS-katalógust virtuális hálózati szolgáltatás-végpontokkal rendelkező Azure SQL Database üzemelteti, győződjön meg arról, hogy a Azure-SSIS IR ugyanahhoz a virtuális hálózathoz és alhálózathoz csatlakozik.

Ha a SSIS-katalógust az SQL felügyelt példányában privát végponttal futtatja, akkor győződjön meg arról, hogy a Azure-SSIS IR ugyanahhoz a virtuális hálózathoz csatlakozik, de a felügyelt példánytól eltérő alhálózatban. Ha a Azure-SSIS IRt egy másik virtuális hálózathoz szeretné csatlakoztatni, mint amennyit az SQL felügyelt példánya használ, javasoljuk, hogy a virtuális hálózat (amely ugyanahhoz a régióhoz van korlátozva), vagy virtuális hálózatról virtuális hálózatra való kapcsolódás. További információ: [az alkalmazás összekötése az Azure SQL felügyelt példányával](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="access-to-azure-services"></a>Hozzáférés az Azure-szolgáltatásokhoz

Ha a SSIS-csomagok hozzáférnek a [virtuális hálózati szolgáltatás-végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md) támogató Azure-erőforrásokhoz, és a Azure-SSIS IR-ból kívánja védeni ezeket az erőforrásokat, akkor csatlakoztathatja a Azure-SSIS IRt a virtuális hálózati szolgáltatás végpontokhoz konfigurált virtuális hálózati alhálózathoz, majd hozzáadhat egy virtuális hálózati szabályt a megfelelő Azure-erőforrásokhoz, hogy engedélyezze a hozzáférést ugyanahhoz az alhálózathoz.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Hozzáférés az IP-tűzfalszabály által védett adatforrásokhoz

Ha a SSIS-csomagok olyan adattárakhoz/erőforrásokhoz férnek hozzá, amelyek csak adott statikus nyilvános IP-címeket engedélyeznek, és az erőforrásokhoz való hozzáférést szeretne Azure-SSIS IR-ból, akkor a [nyilvános IP-címeket](../virtual-network/virtual-network-public-ip-address.md) társíthatja Azure-SSIS IRhoz, miközben csatlakoztatja azt egy virtuális hálózathoz, majd hozzáadhat egy IP-tűzfalszabály a megfelelő erőforrásokhoz, hogy engedélyezze a hozzáférést az adott IP-címekről. Ennek két alternatív módja van: 

- Azure-SSIS IR létrehozásakor saját nyilvános IP-címeit használhatja, és [Data Factory felhasználói felületen vagy SDK](#join-the-azure-ssis-ir-to-a-virtual-network)-n keresztül adhatja meg őket. Csak a Azure-SSIS IR kimenő internetkapcsolata fogja használni a megadott nyilvános IP-címeket, és az alhálózatban lévő más eszközök nem fogják használni őket.
- Az alhálózathoz [Virtual Network NAT](../virtual-network/nat-overview.md) -t is beállíthatja, amelyhez Azure-SSIS IR csatlakozni fog, és az alhálózat összes kimenő kapcsolata a megadott nyilvános IP-címeket fogja használni.

A virtuális hálózat minden esetben csak az Azure Resource Manager üzemi modellen keresztül helyezhető üzembe.

A következő szakaszokban további részleteket talál. 

## <a name="virtual-network-configuration"></a>Virtuális hálózati konfiguráció

Állítsa be a virtuális hálózatot a következő követelmények teljesítéséhez: 

- Győződjön meg arról, hogy a `Microsoft.Batch` regisztrált szolgáltató a Azure-SSIS IR üzemeltető virtuális hálózati alhálózat előfizetése alatt. Ha klasszikus virtuális hálózatot használ, a virtuális `MicrosoftAzureBatch` hálózat klasszikus virtuálisgép-közreműködői szerepköréhez is csatlakozhat. 

- Győződjön meg arról, hogy rendelkezik a szükséges engedélyekkel. További információ: [set up permissions (engedélyek beállítása](#perms)).

- Válassza ki a megfelelő alhálózatot a Azure-SSIS IR üzemeltetéséhez. További információ: [válassza ki az alhálózatot](#subnet). 

- Ha a Azure-SSIS IR saját nyilvános IP-címeit, tekintse meg [a statikus nyilvános IP-címek kiválasztása](#publicIP) című témakört.

- Ha a saját tartománynévrendszer-(DNS-) kiszolgálót használja a virtuális hálózaton, tekintse meg [a DNS-kiszolgáló beállítása](#dns_server)című témakört. 

- Ha hálózati biztonsági csoportot (NSG) használ az alhálózaton, tekintse meg a [NSG beállítása](#nsg)című témakört. 

- Ha az Azure ExpressRoute vagy egy felhasználó által megadott útvonalat (UDR) használ, tekintse meg az [Azure ExpressRoute vagy a UDR használatát](#route)ismertető témakört. 

- Győződjön meg arról, hogy a virtuális hálózat erőforráscsoport (vagy a nyilvános IP-címek erőforráscsoport, ha saját nyilvános IP-címeket hoz létre) létrehozhat és törölhet bizonyos Azure-hálózati erőforrásokat. További információ: [az erőforráscsoport beállítása](#resource-group). 

- Ha testreszabja a Azure-SSIS IR a [Azure-SSIS IR egyéni beállítása](./how-to-configure-azure-ssis-ir-custom-setup.md)című témakörben leírtak szerint, a csomópontok kezelésének belső folyamata privát IP-címeket fog használni a 172.16.0.0 előre meghatározott tartománya és a 172.31.255.255 között. Ezért győződjön meg arról, hogy a virtuális vagy a helyszíni hálózatok magánhálózati IP-címeinek tartománya nem ütközik ezzel a tartománnyal.

Ez az ábrán a Azure-SSIS IR szükséges kapcsolatok láthatók:

![A Azure-SSIS IRhoz szükséges kapcsolatokat bemutató diagram.](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a> Engedélyek beállítása

A Azure-SSIS IR létrehozó felhasználónak a következő engedélyekkel kell rendelkeznie:

- Ha a SSIS IR-t egy Azure Resource Manager virtuális hálózathoz csatlakoztatja, két lehetőség közül választhat:

  - Használja a beépített hálózati közreműködő szerepkört. Ehhez a szerepkörhöz a _Microsoft. Network/ \*_ engedély tartozik, amely a szükségesnél sokkal nagyobb hatókörű.

  - Hozzon létre egy egyéni szerepkört, amely csak a szükséges _Microsoft. Network/virtualNetworks/ \* /JOIN/Action_ engedélyt tartalmazza. Ha a Azure-SSIS IR saját nyilvános IP-címeit is szeretné csatlakoztatni egy Azure Resource Manager virtuális hálózathoz, akkor a szerepkörben a _Microsoft. Network/nyilvános IP/*/JOIN/Action_ engedély is szerepel.

- Ha a SSIS IR-t egy klasszikus virtuális hálózathoz csatlakoztatja, javasoljuk, hogy használja a klasszikus virtuális gépek beépített közreműködői szerepkörét. Ellenkező esetben meg kell határoznia egy egyéni szerepkört, amely magában foglalja a virtuális hálózathoz való csatlakozás engedélyét.

### <a name="select-the-subnet"></a><a name="subnet"></a> Válassza ki az alhálózatot

Amikor kiválaszt egy alhálózatot: 

- Ne válassza ki a GatewaySubnet egy Azure-SSIS IR telepítéséhez. A virtuális hálózati átjárók számára dedikált. 

- Győződjön meg arról, hogy a kiválasztott alhálózat rendelkezik-e elegendő szabad hellyel a Azure-SSIS IR használatához. Hagyjon elérhető IP-címeket legalább két alkalommal az IR-csomópont számának. Az Azure néhány IP-címet fenntart az egyes alhálózatokon belül. Ezeket a címeket nem lehet használni. Az alhálózatok első és utolsó IP-címe a protokollok megfelelőségére van fenntartva, és három további címet használ az Azure-szolgáltatásokhoz. További információ: az [IP-címek ezen alhálózatokon belüli használatára vonatkozó korlátozások?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Ne használjon olyan alhálózatot, amelyet kizárólag más Azure-szolgáltatások foglalnak magukban (például SQL Database SQL felügyelt példány, App Service stb.). 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>Statikus nyilvános IP-címek kiválasztása

Ha saját statikus nyilvános IP-címeit szeretné használni Azure-SSIS IRhoz, miközben csatlakoztatja azt egy virtuális hálózathoz, győződjön meg arról, hogy megfelelnek a következő követelményeknek:

- Pontosan két olyan nem használt, amely még nincs társítva más Azure-erőforráshoz. Ha rendszeresen frissítjük a Azure-SSIS IRt, a rendszer az extrat fogja használni. Vegye figyelembe, hogy egy nyilvános IP-cím nem osztható meg az aktív Azure-SSIS IRs között.

- Mindkettőnek a standard típusú statikusnak kell lennie. További részletekért tekintse meg a [nyilvános IP-cím SKU-](../virtual-network/public-ip-addresses.md#sku) t.

- Mindkettőnek rendelkeznie kell egy DNS-névvel. Ha nem adott meg DNS-nevet a létrehozásuk során, ezt megteheti Azure Portalon.

![Azure-SSIS integrációs modul](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- A virtuális hálózatnak ugyanabban az előfizetésben és ugyanabban a régióban kell lennie.

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a> A DNS-kiszolgáló beállítása 
Ha saját DNS-kiszolgálóját kell használnia a Azure-SSIS IR által csatlakoztatott virtuális hálózaton a privát állomásnév feloldásához, akkor győződjön meg róla, hogy a globális Azure-gazdagépek nevét (például egy nevű Azure Storage-blobot) is fel tudja oldani `<your storage account>.blob.core.windows.net` . 

Az egyik javasolt módszer a következő: 

-   Konfigurálja az egyéni DNS-t, hogy továbbítsa a kérelmeket Azure DNS. Feloldatlan DNS-rekordokat továbbíthat a saját DNS-kiszolgálón található Azure rekurzív resolvers (168.63.129.16) IP-címére. 

További információ: névfeloldás, [amely a saját DNS-kiszolgálóját használja](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

> [!NOTE]
> Használjon egy teljes tartománynevet (FQDN) a privát állomásnévhez (például a helyett használja a nevet `<your_private_server>.contoso.com` `<your_private_server>` ). Azt is megteheti, hogy a Azure-SSIS IR szabványos egyéni beállításával automatikusan hozzáfűzi a saját DNS-utótagját (például `contoso.com` ) a nem minősített egycímkés tartományhoz, és a DNS-lekérdezésekben való használat előtt kikapcsolja a teljes tartománynevet, és a [szabványos egyéni telepítési mintákat](./how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples)tartalmazza. 

### <a name="set-up-an-nsg"></a><a name="nsg"></a> NSG beállítása
Ha meg kell valósítania egy NSG a Azure-SSIS IR által használt alhálózathoz, engedélyezze a bejövő és kimenő forgalmat a következő portokon keresztül: 

-   **Azure-SSIS IR bejövő követelménye**

| Irány | Átviteli protokoll | Forrás | Forrásporttartomány | Cél | Célporttartomány | Megjegyzések |
|---|---|---|---|---|---|---|
| Bejövő | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (ha az IR-t egy Resource Manager-beli virtuális hálózathoz csatlakoztatja) <br/><br/>10100, 20100, 30100 (ha az IR-t egy klasszikus virtuális hálózathoz csatlakoztatja)| A Data Factory szolgáltatás ezeket a portokat használja a virtuális hálózatban lévő Azure-SSIS IR csomópontjaival való kommunikációhoz. <br/><br/> Azt határozza meg, hogy alhálózati NSG hoz-e létre, Data Factory mindig a Azure-SSIS IR futtató virtuális gépekhez csatlakoztatott hálózati adapterek (NIC-EK) szintjén konfigurálja a NSG. A hálózati adapter szintű NSG csak a megadott portok Data Factory IP-címeiről érkező bejövő forgalmat engedélyezi. Még ha a portok az alhálózat szintjén is megnyitják az internetes forgalmat, akkor a nem Data Factory IP-címekről érkező adatforgalmat a hálózati adapter szintjén blokkolja a rendszer. |
| Bejövő | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | Választható Ez a szabály csak akkor szükséges, ha a Microsoft támogatási szolgálata megkéri az ügyfelet, hogy nyissa meg a speciális hibaelhárítást, és a hibaelhárítás után azonnal lezárható legyen. A **CorpNetSaw** szolgáltatás címkéje csak a biztonságos hozzáférési munkaállomásokat engedélyezi a Microsoft vállalati hálózaton a távoli asztal használatához. Ezt a szolgáltatási címkét nem lehet kiválasztani a portálról, és csak Azure PowerShell vagy az Azure CLI-n keresztül érhető el. <br/><br/> A hálózati adapterek szintjének NSG a 3389-es port alapértelmezés szerint meg van nyitva, és lehetővé tesszük, hogy a 3389-es portot az alhálózat szintjén NSG, míg a Azure-SSIS IR az egyes IR-csomópontokon a Windows tűzfal szabályában nem engedélyezte a kimenő port 3389. |
||||||||

-   **A Azure-SSIS IR kimenő követelménye**

| Irány | Átviteli protokoll | Forrás | Forrásporttartomány | Cél | Célporttartomány | Megjegyzések |
|---|---|---|---|---|---|---|
| Kimenő | TCP | VirtualNetwork | * | AzureCloud | 443 | A virtuális hálózat Azure-SSIS IR csomópontjai ezt a portot használják az Azure-szolgáltatások, például az Azure Storage és az Azure Event Hubs eléréséhez. |
| Kimenő | TCP | VirtualNetwork | * | Internet | 80 | Választható A virtuális hálózat Azure-SSIS IR csomópontjai ezt a portot használják a visszavont tanúsítványok listájának internetről való letöltéséhez. Ha letiltja ezt a forgalmat, akkor a teljesítmény-visszalépést tapasztalhatja, ha elindítja az IR-t, és elveszti a tanúsítvány-visszavonási listát a tanúsítvány használatának ellenőrzése érdekében. Ha a célhelyet bizonyos FQDN-re szeretné szűkíteni, tekintse meg az **Azure ExpressRoute vagy a UDR használatát** ismertető szakaszt.|
| Kimenő | TCP | VirtualNetwork | * | SQL | 1433, 11000-11999 | Választható Ez a szabály csak akkor szükséges, ha a virtuális hálózatban lévő Azure-SSIS IR csomópontjai hozzáférnek a kiszolgáló által üzemeltetett SSISDB. Ha a kiszolgálói kapcsolódási házirend az **átirányítás** helyett **proxyra** van beállítva, akkor csak az 1433-es port szükséges. <br/><br/> Ez a kimenő biztonsági szabály nem alkalmazható a virtuális hálózatban lévő SQL felügyelt példány által üzemeltetett SSISDB, vagy a magánhálózati végponttal konfigurált SQL Database. |
| Kimenő | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | Választható Ez a szabály csak akkor szükséges, ha a virtuális hálózatban lévő Azure-SSIS IR csomópontjai hozzáférnek a virtuális hálózatban található SQL felügyelt példány által üzemeltetett SSISDB, vagy SQL Database privát végponttal konfigurálták. Ha a kiszolgálói kapcsolódási házirend az **átirányítás** helyett **proxyra** van beállítva, akkor csak az 1433-es port szükséges. |
| Kimenő | TCP | VirtualNetwork | * | Tárolás | 445 | Választható Ez a szabály csak akkor szükséges, ha a Azure Filesban tárolt SSIS-csomagot szeretné végrehajtani. |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a> Az Azure ExpressRoute vagy a UDR használata
Ha meg szeretné vizsgálni a kimenő forgalmat a Azure-SSIS IRről, átirányíthatja a Azure-SSIS IRról a helyszíni tűzfal-készülékre kezdeményezett forgalmat az [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) kényszerített bújtatásával (a BGP-útvonal, a 0.0.0.0/0, a virtuális hálózatra) vagy a hálózati virtuális berendezés (NVA) használatával tűzfalként vagy [Azure Firewallként](../firewall/index.yml) a [UDR](../virtual-network/virtual-networks-udr-overview.md)-on keresztül. 

![Azure-SSIS IR NVA forgatókönyve](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

A teljes forgatókönyv működéséhez az alábbi lépéseket kell elvégeznie
   -   A Azure Batch felügyeleti szolgáltatások és a Azure-SSIS IR közötti bejövő forgalom nem irányítható át a tűzfal készülékén keresztül.
   -   A tűzfal készüléknek engedélyeznie kell a Azure-SSIS IR által igényelt kimenő forgalmat.

Azure Batch felügyeleti szolgáltatások és a Azure-SSIS IR közötti bejövő forgalom nem irányítható át a tűzfal-berendezésbe, ellenkező esetben a forgalom aszimmetrikus útválasztási probléma miatt megszakad. A bejövő forgalomhoz meg kell adni az útvonalakat, így a forgalom ugyanúgy reagálhat, mint a szolgáltatás. Megadhat konkrét UDR, amelyekkel átirányíthatja a forgalmat Azure Batch felügyeleti szolgáltatások és a Azure-SSIS IR között a következő ugrási típusként az **Internet** lehetőséggel.

Ha például a Azure-SSIS IR a (z) helyen található, `UK South` és Azure Firewallon keresztül szeretné megvizsgálni a kimenő forgalmat, először a szolgáltatási címke IP-címtartomány `BatchNodeManagement.UKSouth` [letöltése hivatkozásra](https://www.microsoft.com/download/details.aspx?id=56519) vagy a Service [tag felderítési API](../virtual-network/service-tags-overview.md#service-tags-on-premises)-ra kell beolvasnia a szolgáltatás címkéjét. Ezt követően alkalmazza az alábbi UDR a kapcsolódó IP-címtartomány-útvonalakat a következő ugrási típussal az **Internet** mellett a 0.0.0.0/0 útvonalon, a következő ugrási típust pedig **virtuális készülékként**.

![Azure Batch UDR beállításai](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Ez a megközelítés További karbantartási költségekkel jár. Rendszeresen vizsgálja meg az IP-címtartományt, és vegyen fel új IP-tartományokat a UDR, hogy elkerülje a Azure-SSIS IR megszakítását. Azt javasoljuk, hogy havonta ellenőrizze az IP-címtartományt, mert ha az új IP-cím megjelenik a szolgáltatás címkéjén, az IP-cím egy másik hónapra lép életbe. 

A UDR-szabályok telepítésének megkönnyítéséhez futtassa a következő PowerShell-szkriptet a Azure Batch felügyeleti szolgáltatásokhoz tartozó UDR-szabályok hozzáadásához:
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

Ahhoz, hogy a tűzfal eszközön engedélyezve legyen a kimenő forgalom, engedélyeznie kell a kimenő forgalmat a lenti portokon a NSG kimenő szabályokban megkövetelt módon.
-   Az 443-es port az Azure Cloud Services szolgáltatással.

    Ha Azure Firewall használ, megadhatja a AzureCloud szolgáltatás címkével ellátott hálózati szabályt. A más típusú tűzfalak esetében egyszerűen engedélyezheti a célhelyet a 443-as porton, vagy az Azure-környezet típusa alapján engedélyezheti az alábbi FQDN-ket:

    | Azure-környezet | Végpontok                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li><b>Azure Data Factory (felügyelet)</b><ul><li>\*. frontend.clouddatahub.net</li></ul></li><li><b>Azure Storage (felügyelet)</b><ul><li>\*.blob.core.windows.net</li><li>\*. table.core.windows.net</li></ul></li><li><b>Azure Container Registry (egyéni telepítés)</b><ul><li>\*. azurecr.io</li></ul></li><li><b>Event hub (naplózás)</b><ul><li>\*. servicebus.windows.net</li></ul></li><li><b>Microsoft naplózási szolgáltatás (belső használat)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure Data Factory (felügyelet)</b><ul><li>\*. frontend.datamovement.azure.us</li></ul></li><li><b>Azure Storage (felügyelet)</b><ul><li>\*. blob.core.usgovcloudapi.net</li><li>\*. table.core.usgovcloudapi.net</li></ul></li><li><b>Azure Container Registry (egyéni telepítés)</b><ul><li>\*. azurecr.us</li></ul></li><li><b>Event hub (naplózás)</b><ul><li>\*. servicebus.usgovcloudapi.net</li></ul></li><li><b>Microsoft naplózási szolgáltatás (belső használat)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure China 21Vianet     | <ul><li><b>Azure Data Factory (felügyelet)</b><ul><li>\*. frontend.datamovement.azure.cn</li></ul></li><li><b>Azure Storage (felügyelet)</b><ul><li>\*. blob.core.chinacloudapi.cn</li><li>\*. table.core.chinacloudapi.cn</li></ul></li><li><b>Azure Container Registry (egyéni telepítés)</b><ul><li>\*. azurecr.cn</li></ul></li><li><b>Event hub (naplózás)</b><ul><li>\*. servicebus.chinacloudapi.cn</li></ul></li><li><b>Microsoft naplózási szolgáltatás (belső használat)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    Az Azure Storage, a Azure Container Registry és az Event hub teljes tartománynevéhez hasonlóan az alábbi szolgáltatási végpontokat is engedélyezheti a virtuális hálózat számára, hogy az ilyen végpontokra irányuló hálózati forgalom az Azure gerinces hálózaton haladjon át, ahelyett, hogy a tűzfal készülékére irányítja a rendszert:
    -  Microsoft.Storage
    -  Microsoft. ContainerRegistry
    -  Microsoft. EventHub


-   A 80-es port a CRL letöltési helyeinek célhelye.

    Az alábbi, a CRL-ként használt teljes tartománynevek (visszavont tanúsítványok listája) a tanúsítványok Azure-SSIS IR felügyeleti célból történő letöltését teszik lehetővé:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Ha eltérő CRL-t használó tanúsítványokat használ, azt javasoljuk, hogy vegye fel őket is. A [tanúsítvány-visszavonási listán szereplő](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx)további információk megismeréséhez olvassa el ezt a témakört.

    Ha letiltja ezt a forgalmat, a Azure-SSIS IR elindítása után a teljesítmény-visszalépést tapasztalhatja, és elveszítheti a tanúsítvány-visszavonási listát, amely a biztonsági szempontból nem ajánlott.

-   A 1433-es, 11000-11999-as port Azure SQL Database (csak akkor szükséges, ha a virtuális hálózatban lévő Azure-SSIS IR csomópontjai hozzáférnek a kiszolgáló által üzemeltetett SSISDB).

    Ha Azure Firewall használ, megadhatja az Azure SQL Service-címkével ellátott hálózati szabályt, ellenkező esetben előfordulhat, hogy adott Azure SQL URL-címként engedélyezi a célhelyet a tűzfal készülékben.

-   Az 445-es port az Azure Storage szolgáltatással (csak a Azure Files-ben tárolt SSIS-csomag végrehajtásakor szükséges).

    Ha Azure Firewall használ, megadhatja a Storage szolgáltatás címkével ellátott hálózati szabályt, ellenkező esetben előfordulhat, hogy a célhelyet meghatározott Azure file Storage URL-címként engedélyezi a tűzfal készülékben.

> [!NOTE]
> Az Azure SQL és a Storage esetében, ha Virtual Network szolgáltatási végpontokat állít be az alhálózaton, akkor az azonos régióban található Azure-SSIS IR és az Azure SQL közötti adatforgalom az azonos régióban lévő \ Azure Storage-ban vagy a párosított régiókban közvetlenül a tűzfal helyett a Microsoft Azure gerinc hálózatra lesz irányítva.

Ha nincs szüksége a Azure-SSIS IR kimenő forgalmának vizsgálatára, egyszerűen alkalmazhatja az útvonalat, hogy a következő ugrás típusú összes forgalmat kényszerítse az **internethez**:

-   Az Azure ExpressRoute-forgatókönyvekben a következő ugrási típussal rendelkező 0.0.0.0/0 útvonalakat alkalmazhatja  a Azure-SSIS IR futtató alhálózaton. 
-   Egy NVA-forgatókönyvben módosíthatja a meglévő 0.0.0.0/0 útvonalat azon az alhálózaton **, amely** a következő ugrási típusról az **internetre** helyezi a Azure-SSIS IR.

![Útvonal hozzáadása](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> Útvonal megadása a következő ugrás típusú **internettel** nem jelenti azt, hogy az összes forgalom az interneten keresztül fog haladni. Ha a cél címe az Azure egyik szolgáltatásához tartozik, az Azure a forgalmat közvetlenül az Azure gerinc hálózatán keresztül irányítja át, és nem irányítja át az internetre.

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a> Az erőforráscsoport beállítása

A Azure-SSIS IR létre kell hoznia bizonyos hálózati erőforrásokat a virtuális hálózattal azonos erőforráscsoporthoz. Ezek az erőforrások a következők:
- Egy Azure Load Balancer a name *\<Guid> -azurebatch-cloudserviceloadbalancer*.
- Egy Azure nyilvános IP-cím, amelynek neve *\<Guid> – azurebatch-cloudservicepublicip*.
- Egy hálózati munkahelyi biztonsági csoport, amelynek neve: *\<Guid> azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> Mostantól használhatja a saját statikus nyilvános IP-címeit a Azure-SSIS IRhoz. Ebben az esetben csak az Azure Load Balancer és a hálózati biztonsági csoport jön létre a virtuális hálózat helyett statikus nyilvános IP-címekkel.

Ezek az erőforrások a Azure-SSIS IR indításakor lesznek létrehozva. Ha a Azure-SSIS IR leáll, a rendszer törli őket. Ha a saját statikus nyilvános IP-címeit a Azure-SSIS IRhoz hozza, a saját statikus nyilvános IP-címei nem törlődnek, ha a Azure-SSIS IR leáll. Ha nem szeretné megakadályozni a Azure-SSIS IR leállítását, ne használja újra ezeket a hálózati erőforrásokat a többi erőforrásban.

Győződjön meg arról, hogy nincs erőforrás-zárolás azon az erőforráscsoport/előfizetés esetében, amelyhez a virtuális hálózat/a statikus nyilvános IP-címek tartoznak. Ha írásvédett/törlési zárolást konfigurál, a Azure-SSIS IR elindítása és leállítása sikertelen lesz, vagy a rendszer nem válaszol.

Győződjön meg arról, hogy nem rendelkezik Azure Policy-hozzárendeléssel, amely megakadályozza, hogy a következő erőforrások jöjjenek létre az erőforráscsoport/előfizetés alatt, amelyhez a virtuális hálózat/a statikus nyilvános IP-címek tartoznak: 
- Microsoft. Network/LoadBalancers 
- Microsoft. Network/NetworkSecurityGroups 
- Microsoft. Network/nyilvános IP 

Győződjön meg arról, hogy az előfizetés erőforrás-kvótája elegendő a fenti három hálózati erőforráshoz. A virtuális hálózatban létrehozott minden egyes Azure-SSIS IR esetében két ingyenes kvótát kell lefoglalni a fenti három hálózati erőforráshoz. Az extra egy kvóta akkor lesz használatban, amikor rendszeresen frissítjük a Azure-SSIS IR.

### <a name="faq"></a><a name="faq"></a> – GYAKORI kérdések

- Hogyan védhető meg a Azure-SSIS IR a bejövő kapcsolatok számára elérhető nyilvános IP-cím? Lehetséges a nyilvános IP-cím eltávolítása?
 
  Ekkor a rendszer automatikusan létrehoz egy nyilvános IP-címet, amikor a Azure-SSIS IR egy virtuális hálózathoz csatlakozik. A hálózati adapterek szintjének NSG csak Azure Batch felügyeleti szolgáltatásokat lehet beérkezőként csatlakozni a Azure-SSIS IRhoz. Megadhat egy alhálózati szintű NSG is a bejövő védelemhez.

  Ha nem szeretné, hogy a nyilvános IP-címek elérhetők legyenek, érdemes lehet saját üzemeltetésű integrációs modult [beállítani a Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md) ahelyett, hogy a Azure-SSIS IRt egy virtuális hálózathoz csatlakoztatná, ha ez a forgatókönyvre vonatkozik.
 
- Felvehetem a Azure-SSIS IR nyilvános IP-címét a tűzfal engedélyezési listájára az adatforrások esetében?

  Mostantól használhatja a saját statikus nyilvános IP-címeit a Azure-SSIS IRhoz. Ebben az esetben felveheti az IP-címeit a tűzfal engedélyezési listájába az adatforrásokhoz. Az alábbi lehetőségekkel is megtekintheti az adatok elérését az Azure-SSIS IR a forgatókönyvtől függően:

  - Ha az adatforrás a helyszínen található, miután csatlakoztatta a virtuális hálózatot a helyszíni hálózathoz, és csatlakoztatja a Azure-SSIS IR a virtuális hálózat alhálózatához, akkor az adott alhálózat magánhálózati IP-címének tartományát hozzáadhatja a tűzfal engedélyezési listájához az adatforráshoz.
  - Ha az adatforrás egy olyan Azure-szolgáltatás, amely támogatja a virtuális hálózati szolgáltatás végpontait, konfigurálhat egy virtuális hálózati szolgáltatás végpontját a virtuális hálózat alhálózatán, és csatlakozhat a Azure-SSIS IR az adott alhálózathoz. Ezután hozzáadhat egy virtuális hálózati szabályt az adott alhálózattal az adatforrás tűzfalához.
  - Ha az adatforrás egy nem Azure-beli felhőalapú szolgáltatás, akkor a UDR segítségével átirányíthatja a Azure-SSIS IR kimenő forgalmát egy NVA/Azure Firewall egy statikus nyilvános IP-címen keresztül. Ezután hozzáadhatja a NVA/Azure Firewall statikus nyilvános IP-címét az adatforrás tűzfalának engedélyezési listájához.
  - Ha a fenti lehetőségek egyike sem felel meg az igényeinek, érdemes lehet saját üzemeltetésű [IR-t konfigurálnia a Azure-SSIS IR számára](./self-hosted-integration-runtime-proxy-ssis.md). Ezután hozzáadhatja annak a számítógépnek a statikus nyilvános IP-címét, amely a saját üzemeltetésű integrációs modult üzemelteti az adatforráshoz tartozó tűzfal engedélyezési listájára.

- Miért van szükség két statikus nyilvános cím megadására, ha saját magam szeretném Azure-SSIS IR?

  A Azure-SSIS IR rendszeres időközönként automatikusan frissül. Az új csomópontok a verziófrissítés során jönnek létre, és a régieket is törli a rendszer. Az állásidő elkerülése érdekében azonban a régi csomópontok nem lesznek törölve, amíg az újak nem állnak készen. Így a régi csomópontok által használt első statikus nyilvános IP-cím nem szabadítható fel azonnal, és szükség van a második statikus nyilvános IP-címére az új csomópontok létrehozásához.

- Saját statikus nyilvános IP-címeket hoztam Azure-SSIS IR, de miért nem fér hozzá az adatforrásokhoz?

  - Győződjön meg arról, hogy a két statikus nyilvános IP-cím is hozzá van adva a tűzfal engedélyezési listájához az adatforrások számára. Minden alkalommal, amikor a Azure-SSIS IR frissült, a statikus nyilvános IP-címe az Ön által létrehozott két felhasználó között vált. Ha csak az egyiket adja hozzá az engedélyezési listához, akkor a Azure-SSIS IR adathozzáférése a frissítés után megszakad.
  - Ha az adatforrás egy Azure-szolgáltatás, ellenőrizze, hogy a virtuális hálózati szolgáltatás-végpontokkal van-e konfigurálva. Ha ez az eset áll fenn, az adatforráshoz Azure-SSIS IR adatforgalom az Azure-szolgáltatások által kezelt magánhálózati IP-címek használatára vált, és a saját statikus nyilvános IP-címeinek hozzáadása a tűzfal engedélyezési listájához az adatforrás esetében nem lép érvénybe.

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (Data Factory felhasználói felület)

Ez a szakasz bemutatja, hogyan csatlakozhat egy meglévő Azure-SSIS IR egy virtuális hálózathoz (klasszikus vagy Azure Resource Manager) az Azure Portal és Data Factory felhasználói felület használatával. 

Mielőtt csatlakoztatja a Azure-SSIS IRt a virtuális hálózathoz, megfelelően konfigurálnia kell a virtuális hálózatot. Kövesse a virtuális hálózat típusára vonatkozó szakasz lépéseit (klasszikus vagy Azure Resource Manager). Ezután kövesse a harmadik szakaszban található lépéseket a Azure-SSIS IR csatlakoztatásához a virtuális hálózathoz. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager virtuális hálózat konfigurálása

A portálon konfigurálhat egy Azure Resource Manager virtuális hálózatot, mielőtt csatlakozni próbál hozzá egy Azure-SSIS IRhoz.

1. Indítsa el a Microsoft Edge vagy a Google Chrome böngészőt. Jelenleg csak ezek a böngészők támogatják a Data Factory felhasználói felületet. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

1. Válassza a **További szolgáltatások** lehetőséget. A **virtuális hálózatok** szűrése és kiválasztása. 

1. A listában jelölje ki a virtuális hálózatot, és válassza ki azt. 

1. A **virtuális hálózat** lapon válassza a **Tulajdonságok** lehetőséget. 

1. Válassza a másolás gombot az **erőforrás-azonosítóhoz** , és másolja a virtuális hálózat erőforrás-azonosítóját a vágólapra. Mentse az azonosítót a vágólapról a OneNote vagy egy fájlból. 

1. A bal oldali menüben válassza az **alhálózatok** lehetőséget. Győződjön meg arról, hogy a rendelkezésre álló címek száma nagyobb, mint a Azure-SSIS IR csomópontjai. 

1. Ellenőrizze, hogy a Azure Batch szolgáltató regisztrálva van-e a virtuális hálózattal rendelkező Azure-előfizetésben. Vagy regisztrálja a Azure Batch szolgáltatót. Ha már rendelkezik Azure Batch-fiókkal az előfizetésben, az előfizetés regisztrálva lesz a Azure Batchhoz. (Ha a Azure-SSIS IR a Data Factory portálon hozza létre, a Azure Batch szolgáltató automatikusan regisztrálva lesz.) 

   1. A Azure Portal bal oldali menüjében válassza az **előfizetések** lehetőséget. 

   1. Válassza ki előfizetését. 

   1. A bal oldalon válassza az **erőforrás-szolgáltatók** lehetőséget, és győződjön meg arról, hogy **Microsoft.BatCH** egy regisztrált szolgáltató. 

   ![A "regisztrált" állapot megerősítése](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Ha nem látja **Microsoft.BatCH** -t a listában, a regisztráláshoz [hozzon létre egy üres Azure batch fiókot](../batch/batch-account-create-portal.md) az előfizetésében. Később is törölheti. 

### <a name="configure-a-classic-virtual-network"></a>Klasszikus virtuális hálózat konfigurálása

A portálon konfigurálhatja a klasszikus virtuális hálózatot, mielőtt csatlakoztatni próbál egy Azure-SSIS IR. 

1. Indítsa el a Microsoft Edge vagy a Google Chrome böngészőt. Jelenleg csak ezek a böngészők támogatják a Data Factory felhasználói felületet. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

1. Válassza a **További szolgáltatások** lehetőséget. Szűrje és válassza a **virtuális hálózatok (klasszikus)** lehetőséget. 

1. A listában jelölje ki a virtuális hálózatot, és válassza ki azt. 

1. A **virtuális hálózat (klasszikus)** lapon válassza a **Tulajdonságok** lehetőséget. 

   ![Klasszikus virtuális hálózati erőforrás-azonosító](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Válassza a másolás gombot az **erőforrás-azonosítóhoz** , és másolja a klasszikus hálózat erőforrás-azonosítóját a vágólapra. Mentse az azonosítót a vágólapról a OneNote vagy egy fájlból. 

1. A bal oldali menüben válassza az **alhálózatok** lehetőséget. Győződjön meg arról, hogy a rendelkezésre álló címek száma nagyobb, mint a Azure-SSIS IR csomópontjai. 

   ![A virtuális hálózatban elérhető címek száma](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Csatlakozzon a **MicrosoftAzureBatch** a virtuális hálózat **klasszikus virtuálisgép-közreműködői** szerepköréhez. 

   1. A bal oldali menüben válassza a **hozzáférés-vezérlés (iam)** lehetőséget, és válassza ki a **szerepkör-hozzárendelések** lapot. 

       !["Hozzáférés-vezérlés" és "Hozzáadás" gomb](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.

   1. A **szerepkör-hozzárendelés hozzáadása** lapon a **szerepkör** beállításnál válassza a **klasszikus virtuális gépek közreműködője** lehetőséget. A **Select (kiválasztás** ) mezőben illessze be a **ddbf3205-c6bd-46ae-8127-60eb93363864**, majd válassza a **Microsoft Azure batch** elemet a keresési eredmények listájából. 

       ![Keresési eredmények a "szerepkör-hozzárendelés hozzáadása" oldalon](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Válassza a **Mentés** lehetőséget a beállítások mentéséhez és az oldal bezárásához. 

       ![Hozzáférési beállítások mentése](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Ellenőrizze, hogy megjelenik-e **Microsoft Azure batch** a közreműködők listájában. 

       ![Azure Batch hozzáférésének megerősítése](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Ellenőrizze, hogy a Azure Batch szolgáltató regisztrálva van-e a virtuális hálózattal rendelkező Azure-előfizetésben. Vagy regisztrálja a Azure Batch szolgáltatót. Ha már rendelkezik Azure Batch-fiókkal az előfizetésben, az előfizetés regisztrálva lesz a Azure Batchhoz. (Ha a Azure-SSIS IR a Data Factory portálon hozza létre, a Azure Batch szolgáltató automatikusan regisztrálva lesz.) 

   1. A Azure Portal bal oldali menüjében válassza az **előfizetések** lehetőséget. 

   1. Válassza ki előfizetését. 

   1. A bal oldalon válassza az **erőforrás-szolgáltatók** lehetőséget, és győződjön meg arról, hogy **Microsoft.BatCH** egy regisztrált szolgáltató. 

   ![A "regisztrált" állapot megerősítése](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Ha nem látja **Microsoft.BatCH** -t a listában, a regisztráláshoz [hozzon létre egy üres Azure batch fiókot](../batch/batch-account-create-portal.md) az előfizetésében. Később is törölheti. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>A Azure-SSIS IR csatlakoztatása egy virtuális hálózathoz

Miután konfigurálta a Azure Resource Manager virtuális hálózatot vagy a klasszikus virtuális hálózatot, csatlakoztathatja a Azure-SSIS IRt a virtuális hálózathoz:

1. Indítsa el a Microsoft Edge vagy a Google Chrome böngészőt. Jelenleg csak ezek a böngészők támogatják a Data Factory felhasználói felületet. 

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza az **adatüzemek** lehetőséget. Ha a menüben nem látja az **adatok gyárait** , válassza a **További szolgáltatások** lehetőséget, majd az **intelligencia és elemzés** szakaszban válassza az **adatüzemek** lehetőséget. 

   ![Az adatüzemek listája](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Válassza ki az adatgyárat a listában szereplő Azure-SSIS IR. Megjelenik a saját adatelőállítójának kezdőlapja. Válassza ki a **szerző & figyelés** csempét. A Data Factory felhasználói felület külön lapon jelenik meg. 

   ![Data factory kezdőlap](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. A Data Factory felhasználói felületen váltson a **Szerkesztés** lapra, válassza a **kapcsolatok** lehetőséget, majd váltson az **Integration Runtimes (integrációs** modulok) lapra. 

   !["Integration Runtimes" lap](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Ha a Azure-SSIS IR fut, az **integrációs** modulok listájában, a **műveletek** oszlopban válassza a **Leállítás** gombot a Azure-SSIS IR. A Azure-SSIS IR addig nem szerkeszthető, amíg le nem állítja. 

   ![Az IR leállítása](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Az **integrációs** modulok listájának **műveletek** oszlopában válassza ki a Azure-SSIS IR **Szerkesztés** gombját. 

   ![Az Integration Runtime szerkesztése](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Az integrációs modul telepítése panelen az **általános beállítások** és az SQL- **Beállítások** szakaszban válassza a **tovább** gombot. 

1. A **Speciális beállítások** szakaszban: 

   1. Válassza ki a **VNet kiválasztása a Azure-SSIS Integration Runtimehoz való csatlakozáshoz lehetőséget, hogy az ADF bizonyos hálózati erőforrásokat hozzon létre, és igény szerint saját statikus nyilvános IP-címeket** használjon jelölőnégyzetet. 

   1. Az **előfizetés** mezőben válassza ki a virtuális hálózattal rendelkező Azure-előfizetést.

   1. A **hely** esetében az integrációs modul ugyanazon helye van kiválasztva.

   1. A **Típus mezőben** válassza ki a virtuális hálózat típusát: klasszikus vagy Azure Resource Manager. Javasoljuk, hogy válasszon ki egy Azure Resource Manager virtuális hálózatot, mert a klasszikus virtuális hálózatok hamarosan elavulttá válnak.

   1. A **VNet neve** mezőben válassza ki a virtuális hálózat nevét. A virtuális hálózati szolgáltatás-végpontokkal vagy az SQL felügyelt példányával, amely a SSISDB üzemeltetésére szolgál, ugyanazt a SQL Database használja. Vagy a helyszíni hálózathoz csatlakoztatva kell lennie. Ellenkező esetben bármely virtuális hálózat lehet a saját statikus nyilvános IP-címeinek használata Azure-SSIS IR számára.

   1. Az **alhálózat neve** mezőben válassza ki a virtuális hálózatához tartozó alhálózat nevét. A virtuális hálózati szolgáltatás-végpontokkal a SSISDB üzemeltetéséhez használt SQL Database-nek azonosnak kell lennie. Vagy egy másik alhálózatnak kell lennie, amely az SQL felügyelt példányának a privát végponttal való üzemeltetéséhez használt SSISDB. Ellenkező esetben bármilyen alhálózat lehet a saját statikus nyilvános IP-címeinek Azure-SSIS IR számára.

   1. Jelölje be a **statikus nyilvános IP-címek** bekapcsolása a Azure-SSIS Integration Runtime jelölőnégyzetet, és válassza ki, hogy a saját statikus nyilvános IP-címeit szeretné-e használni a Azure-SSIS IRhoz, így engedélyezheti azokat a tűzfalon az adatforrások számára.

      Ha bejelöli a jelölőnégyzetet, hajtsa végre a következő lépéseket.

      1. Az **első statikus nyilvános IP-cím** mezőben válassza ki az első statikus nyilvános IP-címet, amely [megfelel a Azure-SSIS IR követelményeinek](#publicIP) . Ha nincs, kattintson az új hivatkozás **létrehozása** lehetőségre statikus nyilvános IP-címek létrehozásához Azure Portalon, majd kattintson a frissítés gombra itt, így kiválaszthatja őket.
      
      1. A **második statikus nyilvános IP-cím** mezőben válassza ki a második statikus nyilvános IP-címet, amely [megfelel a Azure-SSIS IR követelményeinek](#publicIP) . Ha nincs, kattintson az új hivatkozás **létrehozása** lehetőségre statikus nyilvános IP-címek létrehozásához Azure Portalon, majd kattintson a frissítés gombra itt, így kiválaszthatja őket.

   1. Válassza a **VNet érvényesítése** lehetőséget. Ha az ellenőrzés sikeres, válassza a **Folytatás** lehetőséget. 

   ![Virtuális hálózat speciális beállításai](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Az **Összefoglalás** szakaszban tekintse át a Azure-SSIS IR összes beállítását. Ezután válassza a **frissítés** lehetőséget.

1. Indítsa el a Azure-SSIS IR a Azure-SSIS IR **műveletek** oszlop **Start** gombjának kiválasztásával. Körülbelül 20 – 30 percet vesz igénybe a virtuális hálózathoz csatlakozó Azure-SSIS IR elindítása. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>Változók definiálása

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>Virtuális hálózat konfigurálása

Ahhoz, hogy csatlakoztatni tudja a Azure-SSIS IRt egy virtuális hálózathoz, konfigurálnia kell a virtuális hálózatot. A virtuális hálózati engedélyek és beállítások automatikus konfigurálásához a Azure-SSIS IR a virtuális hálózathoz való csatlakozáshoz adja hozzá a következő parancsfájlt:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Azure-SSIS IR létrehozása és csatlakoztatása egy virtuális hálózathoz

Létrehozhat egy Azure-SSIS IR, és egy időben csatlakozhat egy virtuális hálózathoz. A teljes parancsfájl és utasításokért lásd: [Azure-SSIS IR létrehozása](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Meglévő Azure-SSIS IR csatlakoztatása egy virtuális hálózathoz

Az [Azure-SSIS IR létrehozása](create-azure-ssis-integration-runtime.md) cikkből megtudhatja, hogyan hozhat létre egy Azure-SSIS IR, és hogyan csatlakozhat egy virtuális hálózathoz ugyanabban a parancsfájlban. Ha már rendelkezik Azure-SSIS IRval, a következő lépésekkel csatlakozhat a virtuális hálózathoz: 
1. Állítsa le a Azure-SSIS IR. 
1. Konfigurálja a Azure-SSIS IR a virtuális hálózathoz való csatlakozáshoz. 
1. Indítsa el a Azure-SSIS IR. 

### <a name="stop-the-azure-ssis-ir"></a>A Azure-SSIS IR leállítása

A virtuális hálózathoz való csatlakoztatás előtt le kell állítania a Azure-SSIS IR. Ez a parancs kibocsátja az összes csomópontját, és leállítja a számlázást:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>A Azure-SSIS IR virtuális hálózati beállításainak konfigurálása a csatlakozáshoz

Az Azure-SSIS által csatlakoztatni kívánt virtuális hálózat beállításainak konfigurálásához használja a következő parancsfájlt: 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>A Azure-SSIS IR konfigurálása

Ha a Azure-SSIS IRt egy virtuális hálózathoz szeretné csatlakoztatni, futtassa a következő `Set-AzDataFactoryV2IntegrationRuntime` parancsot: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-azure-ssis-ir"></a>A Azure-SSIS IR elindítása

A Azure-SSIS IR elindításához futtassa a következő parancsot: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

A parancs futása 20 – 30 percet vesz igénybe.

## <a name="next-steps"></a>Következő lépések

A Azure-SSIS IRról a következő cikkekben talál további információt: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk az IRs-vel kapcsolatos általános információkat tartalmaz, beleértve a Azure-SSIS IRt is. 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-](./tutorial-deploy-ssis-packages-azure.md)ban. Ez az oktatóanyag részletes útmutatást nyújt a Azure-SSIS IR létrehozásához. A Azure SQL Database használja az SSIS-katalógus üzemeltetéséhez. 
- [Hozzon létre egy Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul. Útmutatást nyújt arról, hogyan használhatja Azure SQL Database virtuális hálózati szolgáltatás-végpontokkal vagy SQL felügyelt példánnyal egy virtuális hálózaton a SSIS-katalógus üzemeltetéséhez. Bemutatja, hogyan csatlakozhat a Azure-SSIS IR egy virtuális hálózathoz. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le adatokat a Azure-SSIS IR. A visszaadott információk állapotának leírását adja meg. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy törölni a Azure-SSIS IR. Azt is bemutatja, hogyan bővítheti a Azure-SSIS IR csomópontok hozzáadásával.
