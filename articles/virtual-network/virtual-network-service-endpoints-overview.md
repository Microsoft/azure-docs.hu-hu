---
title: Azure Virtual Network szolgáltatásbeli végpontok
titlesuffix: Azure Virtual Network
description: Tudnivalók Azure-erőforrások virtuális hálózatokról történő közvetlen elérésének engedélyezéséről szolgáltatásvégpontok használatával.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 5e97a35a272684a99e1ca21cbec9c8f3edef253c
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097422"
---
# <a name="virtual-network-service-endpoints"></a>Virtuális hálózati szolgáltatásvégpontok

A Virtual Network (VNet) szolgáltatás végpontja biztonságos és közvetlen kapcsolatot biztosít az Azure-szolgáltatásokkal az Azure-beli gerinces hálózaton keresztül optimalizált útvonalon. A végpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak a virtuális hálózatain legyenek elérhetőek. A szolgáltatási végpontok lehetővé teszik, hogy a VNet magánhálózati IP-címei elérjék az Azure-szolgáltatások végpontját anélkül, hogy a VNet nyilvános IP-címet kellene megadniuk.

Ez a funkció a következő Azure-szolgáltatásokhoz és-régiókhoz érhető el. A *Microsoft. \** Resource zárójelben van. Engedélyezze ezt az erőforrást az alhálózati oldalról a szolgáltatási végpontok szolgáltatáshoz való konfigurálása során:

**Általánosan elérhető**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft. Storage*): általánosan elérhető az összes Azure-régióban.
- **[Azure SQL Database](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): általánosan elérhető az összes Azure-régióban.
- **[Azure szinapszis Analytics](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): általánosan elérhető az összes Azure-régióban a dedikált SQL-készletek (korábban SQL DW) számára.
- **[Azure Database for PostgreSQL-kiszolgáló](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): általánosan elérhető az Azure-régiókban, ahol az adatbázis-szolgáltatás elérhető.
- **[Azure Database for MySQL-kiszolgáló](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): általánosan elérhető az Azure-régiókban, ahol az adatbázis-szolgáltatás elérhető.
- **[Azure Database for MariaDB](../mariadb/concepts-data-access-security-vnet.md)** (*Microsoft. SQL*): általánosan elérhető az Azure-régiókban, ahol az adatbázis-szolgáltatás elérhető.
- **[Azure Cosmos db](../cosmos-db/how-to-configure-vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. AzureCosmosDB*): általánosan elérhető az összes Azure-régióban.
- **[Azure Key Vault](../key-vault/general/overview-vnet-service-endpoints.md)** (*Microsoft.* kulcstartó): általánosan elérhető az összes Azure-régióban.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. ServiceBus*): általánosan elérhető az összes Azure-régióban.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. EventHub*): általánosan elérhető az összes Azure-régióban.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. AzureActiveDirectory*): általánosan elérhető minden olyan Azure-régióban, ahol ADLS Gen1 elérhető.
- **[Azure app Service](../app-service/app-service-ip-restrictions.md)** (*Microsoft. Web*): általánosan elérhető minden olyan Azure-régióban, ahol az App Service elérhető.
- **[Azure Cognitive Services](../cognitive-services/cognitive-services-virtual-networks.md?tabs=portal)** (*Microsoft. CognitiveServices*): általánosan elérhető minden olyan Azure-régióban, ahol a kognitív szolgáltatások elérhetők.

**Nyilvános előzetes verzió**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (*Microsoft. ContainerRegistry*): az előnézet korlátozott Azure-régiókban érhető el, ahol Azure Container Registry elérhető.

A legfrissebb értesítésekért tekintse meg az [Azure-beli virtuális hálózatok frissítéseinek](https://azure.microsoft.com/updates/?product=virtual-network) oldalát.

## <a name="key-benefits"></a>Főbb előnyök

A szolgáltatásvégpontok az alábbi előnyöket nyújtják:

- **Nagyobb biztonság az Azure-szolgáltatások erőforrásai számára: a** VNet privát címterület átfedésben van. Az átfedésben lévő szóközöket nem használhatja egyedi módon a VNet származó forgalom azonosítására. A szolgáltatási végpontok lehetővé teszik az Azure-szolgáltatások erőforrásainak a virtuális hálózatra való védelmét azáltal, hogy kiterjesztik a VNet-identitást a szolgáltatásra. Miután engedélyezte a szolgáltatási végpontokat a virtuális hálózaton, hozzáadhat egy virtuális hálózati szabályt az Azure-szolgáltatási erőforrások biztonságossá tételéhez a virtuális hálózaton. A szabály hozzáadása nagyobb biztonságot nyújt azáltal, hogy teljes mértékben eltávolítja a nyilvános internet-hozzáférést az erőforrásokhoz, és csak a virtuális hálózatról engedélyezi a forgalmat.
- **Optimális útválasztás az Azure-szolgáltatás forgalmához a virtuális hálózatról**: napjainkban a virtuális hálózat bármely olyan útvonala, amely a helyszíni és/vagy virtuális készülékekre irányuló internetes forgalmat kényszeríti, az Azure-szolgáltatás forgalmát is kényszeríti, hogy az internetes forgalmat megegyező útvonalat használja. A szolgáltatásvégpontok optimális útválasztást biztosítanak az Azure-forgalom számára. 

  A végpontok a szolgáltatás forgalmát a virtuális hálózatról közvetlenül az Azure-szolgáltatásra vezetik a Microsoft Azure gerinchálózatán át. Ha az Azure gerinchálózatán tartja az adatforgalmat, továbbra is naplózhatja és monitorozhatja a virtuális hálózatok kimenő internetforgalmát a kényszerített bújtatáson keresztül anélkül, hogy ez kihatna a szolgáltatás forgalmára. A felhasználó által meghatározott útvonalakkal és a kényszerített bújtatással kapcsolatos további információkért lásd: [Azure virtuális hálózati forgalom útválasztása](virtual-networks-udr-overview.md).
- **Egyszerű beállítás kevesebb felügyeleti igénnyel**: Többé nincs szükség lefoglalt nyilvános IP-címekre a virtuális hálózatokban az Azure-erőforrások IP-tűzfalon keresztül végzett biztosításához. A szolgáltatási végpontok beállításához nincs szükség hálózati címfordításra (NAT) vagy átjáró-eszközökre. A szolgáltatási végpontokat egy alhálózaton található egyszerű kattintással konfigurálhatja. A végpontok karbantartásához nincs további terhelés.

## <a name="limitations"></a>Korlátozások

- A szolgáltatás csak az Azure Resource Manager-alapú üzemi modellel üzembe helyezett virtuális hálózatokon érhető el.
- A végpontok az Azure-beli virtuális hálózatokon konfigurált alhálózatokon vannak engedélyezve. A végpontok nem használhatók a helyszíni és az Azure-szolgáltatások közötti adatforgalomhoz. További információ: az [Azure-szolgáltatások helyszíni hozzáférésének biztonságossá tétele](#secure-azure-services-to-virtual-networks)
- Az Azure SQL esetében a szolgáltatásvégpontok csak az adott virtuális hálózat régióján belül vannak hatással az Azure-szolgáltatások forgalmára. Az Azure Storage esetében a végpontok a párosított régiókra is kiterjednek, ahol a virtuális hálózatot a Read-Access Geo-Redundant Storage (RA-GRS) és a Geo-Redundant Storage (GRS) forgalmának támogatásához telepíti. További információ: [Azure párosított régiók](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- A Azure Data Lake Storage (ADLS) Gen 1 esetében a VNet integrációs képesség csak ugyanazon a régión belüli virtuális hálózatok esetében érhető el. Azt is vegye figyelembe, hogy a ADLS Gen1 virtuális hálózati integrációja a virtuális hálózati szolgáltatás végpontjának biztonságát használja a virtuális hálózat és a Azure Active Directory (Azure AD) között, hogy további biztonsági jogcímeket állítson elő a hozzáférési jogkivonatban. Ezután e jogcímek használatával hitelesíti a virtuális hálózatot az 1. generációs Data Lake Storage-fiókkal, és engedélyezi a hozzáférést. A szolgáltatások támogatása szolgáltatási végpontok területen felsorolt *Microsoft. AzureActiveDirectory* címkét csak az 1. generációs ADLS támogató szolgáltatási végpontok használják. Az Azure AD nem támogatja natív módon a szolgáltatási végpontokat. További Azure Data Lake Store információ az 1. generációs VNet-integrációról: [hálózati biztonság a Azure Data Lake Storage Gen1ban](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Azure-szolgáltatások biztonságossá tétele virtuális hálózatokhoz

- A virtuális hálózatok szolgáltatásvégpontjai adják meg a virtuális hálózatok identitását az Azure-szolgáltatás felé. Miután engedélyezte a szolgáltatási végpontokat a virtuális hálózaton, hozzáadhat egy virtuális hálózati szabályt az Azure-szolgáltatási erőforrások biztonságossá tételéhez a virtuális hálózaton.
- Manapság az Azure-szolgáltatások virtuális hálózatokról induló forgalma nyilvános IP-címeket használ forrás IP-ként. A szolgáltatásvégpontok használatakor a szolgáltatások forgalma átvált a virtuális hálózatok privát címeinek forrás IP-címekként való használatára a virtuális hálózatról az Azure-szolgáltatás felé irányuló forgalom esetén. Ez a váltás lehetővé teszi a szolgáltatások elérését anélkül, hogy szükség lenne az IP-tűzfalakban használt lefoglalt, nyilvános IP-címekre.

   >[!NOTE]
   > A szolgáltatásvégpontokkal a szolgáltatásforgalom alhálózatán lévő virtuális gépek forrás IP-címei a nyilvános IPv4-címek használatáról átváltanak magánhálózati IPv4-címek használatára. Az Azure-beli nyilvános IP-címeket használó Azure-szolgáltatáshoz tartozó meglévő tűzfalszabályok a váltás után nem fognak működni. A szolgáltatásvégpontok beállítása előtt győződjön meg arról, hogy az Azure-szolgáltatásokhoz tartozó tűzfalszabályok engedélyezik ezt a váltást. A szolgáltatásvégpontok konfigurálása során az alhálózatról érkező szolgáltatásforgalom átmenetileg megszakadhat. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Az Azure-szolgáltatások helyszíni hozzáférésének biztonságossá tétele

  Alapértelmezés szerint a virtuális hálózatokhoz biztosított Azure-szolgáltatási erőforrások nem érhetők el a helyszíni hálózatokból. Ha engedélyezni szeretné a forgalmat a helyszíni környezetből, engedélyeznie kell a nyilvános (jellemzően NAT) IP-címeket is a helyszíni vagy ExpressRoute. Ezeket az IP-címeket az Azure-szolgáltatás erőforrásainak IP-tűzfal-konfigurációja segítségével adhatja hozzá.

  ExpressRoute: Ha a [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nyilvános vagy Microsoft-partneri kapcsolatban használja a telephelyéről, azonosítania kell a használt NAT IP-címeket. A nyilvános társítás esetében minden ExpressRoute áramkör két NAT IP-címet használ, alapértelmezés szerint az Azure-szolgáltatás forgalmára vonatkozik, amikor a forgalom belép a Microsoft Azure hálózati gerincbe. Microsoft-partnerek esetén a NAT IP-címei vagy a szolgáltató által biztosított vagy biztosított ügyfél. A szolgáltatási erőforrások hozzáférésének engedélyezéséhez engedélyeznie kell ezeket a nyilvános IP-címeket az erőforrás IP-tűzfalának beállításai között. A nyilvános társviszony-létesítési ExpressRoute-kapcsolatcsoport IP-címeinek megkereséséhez [hozzon létre egy támogatási jegyet az ExpressRoute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon. A nyilvános és a Microsoft-ExpressRoute NAT-ról további információt a [EXPRESSROUTE NAT-követelmények](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)című témakörben talál.

![Azure-szolgáltatások biztosítása virtuális hálózatokhoz](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfiguráció

- Szolgáltatási végpontok konfigurálása egy virtuális hálózat alhálózatán. A végpontok bármilyen típusú számítási példányokkal működnek, ha azok az adott alhálózaton belül futnak.
- Az összes támogatott Azure-szolgáltatáshoz (például az Azure Storage-hoz vagy Azure SQL Database-hoz) több szolgáltatási végpont is konfigurálható egy alhálózaton.
- Az Azure SQL Database esetében a virtuális hálózatoknak és az Azure-szolgáltatási erőforrásnak ugyanabban a régióban kell lenniük. A GRS- és RA-GRS Azure Storage-fiókok használata esetén az elsődleges fióknak és a virtuális hálózatnak ugyanabban a régióban kell lennie. Minden más szolgáltatás esetében biztosíthatja az Azure-szolgáltatások erőforrásainak bármely régióban lévő virtuális hálózatokhoz való védelmét. 
- Az a virtuális hálózat, ahol a végpont konfigurálva van, lehet az Azure-szolgáltatási erőforrással egyazon előfizetésben, de egy másikban is. A végpontok beállításához és az Azure-szolgáltatások biztosításához szükséges engedélyekkel kapcsolatos további információkért lásd a [kiépítést](#provisioning) ismertető szakaszt.
- A támogatott szolgáltatások esetében a szolgáltatásvégpontokat használó virtuális hálózatokhoz biztosíthat új vagy meglévő erőforrásokat is.

### <a name="considerations"></a>Megfontolandó szempontok

- A szolgáltatási végpont engedélyezését követően a forrás IP-címek nyilvános IPv4-címekről a saját IPv4-címük használatára váltanak, amikor az adott alhálózat szolgáltatásával kommunikálnak. A váltás során bármilyen, a szolgáltatás felé nyitott fennálló TCP-kapcsolat bezárul. Győződjön meg arról, hogy semmilyen kritikus feladat nem fut, amikor egy alhálózaton engedélyez vagy letilt egy szolgáltatáshoz vezető szolgáltatásvégpontot. Továbbá győződjön meg arról is, hogy az alkalmazásai képesek automatikusan csatlakozni az Azure-szolgáltatásokhoz az IP-címváltást követően.

  Az IP-címváltás csak a virtuális hálózatról kimenő szolgáltatásforgalomra van hatással. Nincs hatással a virtuális gépekhez rendelt nyilvános IPv4-címekre vagy azokra irányuló egyéb forgalomra. Azure-szolgáltatások esetében, ha a meglévő tűzfalszabályok az Azure nyilvános IP-címeit használják, akkor a virtuális hálózat privát címeire történő váltást követően a szabályok nem működnek tovább.
- A szolgáltatási végpontok esetében az Azure-szolgáltatásokhoz tartozó DNS-bejegyzések a mai napon maradnak, és továbbra is az Azure-szolgáltatáshoz rendelt nyilvános IP-címekre lesznek feloldva.

- Hálózati biztonsági csoportok (NSG-k) szolgáltatásvégpontokkal:
  - Alapértelmezés szerint a NSG engedélyezi a kimenő internetes forgalmat, és lehetővé teszi, hogy a VNet érkező forgalmat az Azure-szolgáltatásokba is engedélyezzék. Ez a forgalom továbbra is együttműködik a szolgáltatás-végpontokkal. 
  - Ha meg szeretné tagadni az összes kimenő internetes forgalmat, és csak bizonyos Azure-szolgáltatásoknak engedélyezi a forgalmat, ezt a NSG használhatja a [szolgáltatási címkék](./network-security-groups-overview.md#service-tags) használatával. A NSG-szabályokban megadhatja a támogatott Azure-szolgáltatásokat célként, és az Azure biztosítja az egyes címkéken alapuló IP-címek karbantartását is. További információért lásd az [NSG-khez elérhető Azure-szolgáltatáscímkéket](./network-security-groups-overview.md#service-tags) ismertető szakaszt. 

### <a name="scenarios"></a>Forgatókönyvek

- **Társviszonyban álló, csatlakoztatott vagy többszörös virtuális hálózatok**: Ha egy vagy több virtuális hálózaton belüli több alhálózaton is szeretné biztosítani az Azure-szolgáltatásokat, akkor engedélyezze a szolgáltatásvégpontokat külön-külön mindegyik alhálózaton, hogy mindegyik számára biztosíthassa az Azure-szolgáltatások erőforrásait.
- **Virtuális hálózatról az Azure-szolgáltatásokba irányuló kimenő forgalom szűrése**: Ha az Azure-szolgáltatásnak egy virtuális hálózatról küldött forgalmat szeretné megvizsgálni vagy szűrni, üzembe helyezhet egy hálózati virtuális berendezést a virtuális hálózaton belül. Ezután szolgáltatásvégpontokat alkalmazhat azon az alhálózaton, ahol a hálózati virtuális készülék üzemel, hogy csak ennek az alhálózatnak biztosítsa az Azure-szolgáltatási erőforrásokat. Ez a forgatókönyv akkor lehet hasznos, ha a hálózati virtuális berendezés szűrését szeretné használni az Azure-szolgáltatás elérésének a virtuális hálózatról csak bizonyos Azure-erőforrásokra való korlátozásához. További információkért lásd a [kimenő forgalommal és a hálózati virtuális berendezésekkel](/azure/architecture/reference-architectures/dmz/nva-ha) foglalkozó témakört.
- **Azure-erőforrások biztonságossá tétele közvetlenül a virtuális hálózatokon üzembe helyezett szolgáltatásokban**: a különböző Azure-szolgáltatások közvetlenül üzembe helyezhetők a virtuális hálózatok egyes alhálózatai között. Az Azure-szolgáltatási erőforrások a [felügyelt szolgáltatási](virtual-network-for-azure-services.md) alhálózatokon egy szolgáltatásvégpont beállításával biztosíthatók.
- **Lemezes forgalom Azure-beli virtuális gépről**: a felügyelt és nem felügyelt lemezek virtuálisgép-forgalmát nem érinti az Azure Storage szolgáltatás-végpontok útválasztási módosításai. Ez a forgalom magában foglalja a Lemezforgalmát, valamint a csatlakoztatást és a leválasztást. Az oldal-Blobok REST-hozzáférését korlátozhatja a hálózatok kiválasztásához a szolgáltatási végpontokon és az [Azure Storage hálózati szabályain](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)keresztül. 

### <a name="logging-and-troubleshooting"></a>Naplózás és hibaelhárítás

Miután konfigurálta a szolgáltatási végpontokat egy adott szolgáltatáshoz, ellenőrizze, hogy a szolgáltatási végpont útvonala érvényben van-e: 
 
- Szolgáltatáskérések forrás IP-címeinek hitelesítése a szolgáltatási diagnosztikában. Minden szolgáltatásvégponttal rendelkező új kérés esetében a forrás IP-címnél a virtuális hálózat magánjellegű IP-címe jelenik meg, amely a virtuális hálózatról a hozzáférést kérvényező ügyfélhez van rendelve. A végpont nélkül ez a cím egy Azure-beli nyilvános IP-cím.
- Az érvényes útvonalak megtekintése az egyes alhálózatok hálózati adapterein. A szolgáltatásra mutató útvonal:
  - Egy pontosabban meghatározott alapértelmezett útvonalat jelent, az egyes szolgáltatások előtagtartományának megfelelően.
  - Az útvonal nextHopType típusa: *VirtualNetworkServiceEndpoint*
  - Azt jelzi, hogy a szolgáltatáshoz való közvetlen kapcsolódás a kényszerített bújtatási útvonalakhoz képest még nem történt meg

>[!NOTE]
> Azure-szolgáltatás használata esetén a szolgáltatásvégpont útvonalai felülbírálják a megfelelő címelőtag BGP- vagy UDR-útvonalait. További információ: [Hibaelhárítás hatékony útvonalakkal](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Kiépítés

A szolgáltatási végpontok a virtuális hálózatokon egy írási hozzáféréssel rendelkező felhasználótól függetlenül konfigurálhatók a virtuális hálózatokon. Az Azure-szolgáltatási erőforrások VNet való biztonságossá tételéhez a felhasználónak engedéllyel kell rendelkeznie a *Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/művelethez* a hozzáadott alhálózatokhoz. A beépített szolgáltatás-rendszergazdai szerepkörök alapértelmezés szerint tartalmazzák ezt az engedélyt. Az engedélyt egyéni szerepkörök létrehozásával módosíthatja.

További információ a beépített szerepkörökről: [Azure beépített szerepkörök](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). A konkrét engedélyek egyéni szerepkörökhöz való hozzárendelésével kapcsolatos további információkért lásd: [Egyéni Azure-szerepkörök](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak ugyanahhoz az előfizetéshez, de különböző előfizetésekhez is. Ha a virtuális hálózat és az Azure-szolgáltatási erőforrások különböző előfizetésekhez tartoznak, akkor az erőforrásoknak ugyanahhoz az Active Directory- (AD-) bérlőhöz kell tartozniuk. 

## <a name="pricing-and-limits"></a>Díjszabás és korlátok

A szolgáltatási végpontok használata nem díjköteles. Az Azure-szolgáltatások (Azure Storage, Azure SQL Database stb.) aktuális díjszabási modellje a következőre vonatkozik:.

A virtuális hálózatban lévő szolgáltatási végpontok száma korlátlan.

Bizonyos Azure-szolgáltatások, például az Azure Storage-fiókok korlátozhatják az erőforrás biztosításához használt alhálózatok számát. A részletekért tekintse meg a [következő lépések](#next-steps) című szakaszban található különféle szolgáltatások dokumentációját.

## <a name="vnet-service-endpoint-policies"></a>VNet szolgáltatás-végponti házirendek 

A VNet szolgáltatás-végponti házirendek lehetővé teszik a virtuális hálózati forgalom szűrését az Azure-szolgáltatásokra. Ez a szűrő csak bizonyos Azure szolgáltatásbeli erőforrásokat engedélyez a szolgáltatási végpontokon. A szolgáltatás-végponti szabályzatok részletes hozzáférés-vezérlést biztosítanak az Azure-szolgáltatásokhoz való virtuális hálózati forgalomhoz. További információ: [Virtual Network szolgáltatás-végponti szabályzatok](./virtual-network-service-endpoint-policies-overview.md).

## <a name="faqs"></a>Gyakori kérdések

A gyakori kérdésekért lásd: [Virtual Network szolgáltatási végponttal kapcsolatos gyakori kérdések](./virtual-networks-faq.md#virtual-network-service-endpoints).

## <a name="next-steps"></a>Következő lépések

- [Virtuális hálózati szolgáltatás-végpontok konfigurálása](tutorial-restrict-network-access-to-resources.md)
- [Azure Storage-fiók biztonságossá tétele virtuális hálózathoz](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure SQL Database biztonságossá tétele virtuális hálózathoz](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure szinapszis-elemzés biztonságossá tétele virtuális hálózathoz](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Azure-szolgáltatás integrációja virtuális hálózatokban](virtual-network-for-azure-services.md)
- [Virtual Network szolgáltatási végpont házirendjei](./virtual-network-service-endpoint-policies-overview.md)
- [Azure Resource Manager-sablon](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)