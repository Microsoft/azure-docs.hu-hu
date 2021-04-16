---
title: Az Azure API Management használata virtuális hálózatokkal
description: Megtudhatja, hogyan létesítsen kapcsolatot egy Azure-beli virtuális hálózattal API Management és hogyan férhet hozzá a webszolgáltatásokhoz.
services: api-management
author: vladvino
ms.service: api-management
ms.topic: how-to
ms.date: 04/12/2021
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: 5612da51c1896aaa40ff2a0fb90e3343f676de43
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531645"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Az Azure API Management használata virtuális hálózatokkal
Az Azure-beli virtuális hálózatokkal (VNET-ekkel) olyan nem internetalapú, irányítható hálózatokra helyezheti át Azure-erőforrásait, amelyekhez való hozzáférést Ön szabályozza. Ezek a hálózatok ezután különböző VPN-technológiák használatával csatlakoztathatóak a helyszíni hálózatokhoz. Az Azure-beli virtuális hálózatokkal kapcsolatos további információkért olvassa el az [Azure Virtual Network áttekintését.](../virtual-network/virtual-networks-overview.md)

Az Azure API Management üzembe helyezhető a virtuális hálózaton (VNET), így hozzáférhet a hálózaton belüli háttérszolgáltatásokhoz. A fejlesztői portál és az API-átjáró konfigurálható úgy, hogy az internetről vagy csak a virtuális hálózaton belül legyen elérhető.

> [!NOTE]
> Az API-importálási dokumentum URL-címét egy nyilvánosan elérhető internetcímen kell üzemeltetni.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések végrehajtásához a következővel kell lennie:

+ **Aktív Azure-előfizetés.**

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Egy API Management példány.** További információ: [Azure-beli virtuális API Management létrehozása.](get-started-create-service-instance.md)

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>VNET-kapcsolat engedélyezése

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Virtuális hálózati kapcsolat engedélyezése a Azure Portal

1. Az API [Management Azure Portal példány](https://portal.azure.com) megkereséhez keresse meg a következőt: . Keresse meg és válassza ki **a API Management szolgáltatásokat.**

1. Válassza ki a API Management-példányt.

1. Válassza **a Virtuális hálózat lehetőséget.**
1. Konfigurálja a API Management virtuális hálózaton belüli üzembe helyezésre.

    :::image type="content" source="media/api-management-using-with-vnet/api-management-menu-vnet.png" alt-text="Válassza a virtuális hálózat lehetőséget Azure Portal.":::
    
1. Válassza ki a kívánt hozzáférési típust:

    * **Off**: Ez az alapértelmezett beállítás. API Management virtuális hálózaton nincs telepítve.

    * **Külső:** A API Management átjáró és a fejlesztői portál egy külső terheléselosztáson keresztül érhető el a nyilvános internetről. Az átjáró hozzáférhet a virtuális hálózaton belüli erőforrásokhoz.

        ![Nyilvános társviszony-létesítés][api-management-vnet-public]

    * **Belső:** A API Management átjáró és a fejlesztői portál csak a virtuális hálózaton belülről érhető el egy belső terheléselosztáson keresztül. Az átjáró hozzáférhet a virtuális hálózaton belüli erőforrásokhoz.

        ![Magánhálózati társviszony-létesítés][api-management-vnet-private]

1. Ha a Külső **vagy** **a** Belső beállítást választotta, megjelenik az összes hely (régió) listája, ahol a API Management van kiépítve. Válasszon egy **Helyet,** majd válassza ki annak **virtuális hálózatát,** alhálózatát és **IP-címét.**  A virtuális hálózatok listája a konfigurált régióban Resource Manager Azure-előfizetésében elérhető virtuális hálózatokkal van feltöltve.


    :::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-select.png" alt-text="Virtuális hálózati beállítások a portálon.":::

    > [!IMPORTANT]
    > * Ha az ügyfél a **2020-12-01-es** vagy korábbi API-verziót használja egy Azure API Management-példány Resource Manager virtuális hálózatban való üzembe helyezéséhez, a szolgáltatásnak egy dedikált alhálózaton kell lennie, amely az Azure API Management-példányok kivételével egyetlen erőforrást sem tartalmaz. Ha egy Azure API Management-példányt próbálnak Resource Manager más erőforrásokat tartalmazó virtuális hálózat alhálózatán üzembe helyezni, az üzembe helyezés sikertelen lesz.
    > * Ha az ügyfél a **2021. 01. 01. 01.** vagy újabb API-verziót használja egy Azure API Management-példány virtuális hálózatban való üzembe helyezéséhez, csak egy Resource Manager virtuális hálózat támogatott. Emellett a használt alhálózat más erőforrásokat is tartalmazhat. Nem kell külön alhálózatot használnia a API Management számára. 

1. Kattintson az **Alkalmaz** gombra. A **virtuális hálózati** példány Virtuális hálózat API Management frissül az új virtuális hálózattal és alhálózati választási lehetőségekkel.

1. Folytassa a virtuális hálózati beállítások konfigurálását a virtuális gép többi API Management számára.

7. A felső navigációs sávon válassza a **Mentés,** majd a **Hálózati konfiguráció alkalmazása lehetőséget.**

    A példány frissítése 15–45 percet API Management percet.

> [!NOTE]
> A 2020-12-01-es vagy korábbi API-verziót használó ügyfelek esetében a API Management-példány VIRTUÁLIS IP-címe minden alkalommal megváltozik, amikor a virtuális hálózat engedélyezve van vagy le van tiltva. A virtuális IP-cím akkor is megváltozik,  API Management a külső virtuális hálózatról a belső virtuális hálózatra, vagy fordítva. 

> [!IMPORTANT]
> Ha eltávolítja API Management virtuális hálózatról, vagy módosítja azt, amelybe telepítve van, a korábban használt virtuális hálózat akár hat óráig is zárolva maradhat. Ebben az időszakban nem lehet törölni a VNET-et, vagy új erőforrást üzembe helyezni rajta. Ez a viselkedés a 2018. 01. 01. 01. és korábbi API-verziót használó ügyfelekre igaz. A 2019-01-01-es vagy újabb API-verziót használó ügyfelek a társított API Management törlésekor felszabadítják a virtuális hálózatokat.

### <a name="deploy-api-management-into-external-vnet"></a><a name="deploy-apim-external-vnet"> </a>Üzembe API Management virtuális hálózatban

A virtuális hálózati kapcsolatot az alábbi módszerekkel is engedélyezheti.

### <a name="api-version-2021-01-01-preview"></a>API-verzió: 2021. 01. 01. előzetes verzió

* Azure Resource Manager [sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-external-vnet-publicip)

     [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet-publicip%2Fazuredeploy.json)

### <a name="api-version-2020-12-01"></a>API-verzió: 2020-12-01

* Azure Resource Manager [sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-external-vnet)
    
     [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet%2Fazuredeploy.json)

* Azure PowerShell létrehozása – [Virtuális](/powershell/module/az.apimanagement/new-azapimanagement) [hálózatban](/powershell/module/az.apimanagement/update-azapimanagementregion) API Management példány létrehozása vagy frissítése

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>Csatlakozás virtuális hálózaton belül üzemeltetett webszolgáltatáshoz
Miután a API Management csatlakozott a VNET-hez, a rajta belüli háttérszolgáltatásokhoz való hozzáférés nem más, mint a nyilvános szolgáltatások elérése. Új API létrehozásakor vagy meglévő szerkesztésekor egyszerűen írja be a webszolgáltatás helyi IP-címét vagy állomásnevét (ha a virtuális hálózatra dns-kiszolgáló van konfigurálva) a **Webszolgáltatás URL-címe** mezőbe.

![API hozzáadása VPN-ről][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>Gyakori hálózati konfigurációs problémák
Az alábbi lista azokat a gyakori hibás konfigurációkat sorolja fel, amelyek a szolgáltatás API Management üzembe helyezésekor léphetnek Virtual Network.

* **Egyéni DNS-kiszolgáló beállítása:** A API Management szolgáltatás számos Azure-szolgáltatástól függ. Ha API Management DNS-kiszolgálóval üzemeltetett virtuális hálózatban található, fel kell oldania ezen Azure-szolgáltatások állomásnevét. Kövesse ezt [az útmutatót](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) az egyéni DNS beállításához. Referenciaként tekintse meg az alábbi portok táblázatát és az egyéb hálózati követelményeket.

> [!IMPORTANT]
> Ha egyéni DNS-kiszolgáló(k)t tervez használni a virtuális hálózathoz, akkor a virtuális hálózat üzembe helyezése előtt be kell állítania API Management szolgáltatást.  Ellenkező esetben frissítenie kell a API Management szolgáltatást minden alkalommal, amikor módosítja a DNS-kiszolgáló(k)t a Hálózati konfiguráció alkalmazása [művelet futtatásával.](/rest/api/apimanagement/2019-12-01/apimanagementservice/applynetworkconfigurationupdates)

* **Portok a API Management:** Az alhálózatra irányuló bejövő és kimenő forgalom, API Management hálózati biztonsági csoporttal [vezérelhető.][Network Security Group] Ha ezen portok bármelyike nem érhető el, API Management nem fog megfelelően működni, és elérhetetlenné válhat. Ha egy vagy több port blokkolva van, az egy másik gyakori hibás konfigurációval kapcsolatos probléma, API Management virtuális hálózattal használja.

<a name="required-ports"></a> Amikor egy API Management szolgáltatáspéldányt egy virtuális hálózatban üzemeltet, a rendszer az alábbi táblázatban található portokat használja.

| Forrás-/célport(ak) | Irány          | Átviteli protokoll |   [Szolgáltatáscímkék](../virtual-network/network-security-groups-overview.md#service-tags) <br> Forrás/cél   | Cél ( \* )                                                 | Virtual Network típusa |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | Bejövő            | TCP                | INTERNET/VIRTUAL_NETWORK            | Ügyfél-kommunikáció a API Management                      | Külső             |
| * / 3443                     | Bejövő            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Felügyeleti végpont az Azure Portal és a PowerShellhez         | Belső & külső  |
| * / 443                  | Kimenő           | TCP                | VIRTUAL_NETWORK / Storage             | **Függőség az Azure Storage-ban**                             | Belső & külső  |
| * / 443                  | Kimenő           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) Azure KeyVault-függőség                  | Belső & külső  |
| * / 1433                     | Kimenő           | TCP                | VIRTUAL_NETWORK / SQL                 | **Hozzáférés Azure SQL végponthoz**                           | Belső & külső  |
| * / 443                     | Kimenő           | TCP                | VIRTUAL_NETWORK / AzureKeyVault                 | **Hozzáférés az Azure KeyVaulthoz**                           | Belső & külső  |
| * / 5671, 5672, 443          | Kimenő           | TCP                | VIRTUAL_NETWORK / EventHub            | Függőség az [eseményközpontba való naplózás házirendjére és a](api-management-howto-log-event-hubs.md) monitorozási ügynökre | Belső & külső  |
| * / 445                      | Kimenő           | TCP                | VIRTUAL_NETWORK / Storage             | Függőség a GIT-hez tartozó [Azure-fájlmegosztástól](api-management-configuration-repository-git.md)                      | Belső & külső  |
| * / 443, 12000                     | Kimenő           | TCP                | VIRTUAL_NETWORK / AzureCloud            | Állapot- és monitorozási bővítmény         | Belső & külső  |
| * / 1886, 443                     | Kimenő           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Diagnosztikai [naplók és metrikák](api-management-howto-use-azure-monitor.md)közzététele, [Resource Health](../service-health/resource-health-overview.md) és [Application Insights](api-management-howto-app-insights.md)                   | Belső & külső  |
| * / 25, 587, 25028                       | Kimenő           | TCP                | VIRTUAL_NETWORK /INTERNET            | Csatlakozás AZ SMTP-továbbítóhoz e-mailek küldéhez                    | Belső & külső  |
| * / 6381 - 6383              | Bejövő & kimenő | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Redis Service for [Cache-szabályzatok](api-management-caching-policies.md) elérése gépek között         | Külső & belső  |
| * / 4290              | Bejövő & kimenő | UDP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Sebességkorlát-szabályzatok [szinkronizálási](api-management-access-restriction-policies.md#LimitCallRateByKey) számlálói gépek között         | Külső & belső  |
| * / *                        | Bejövő            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Azure Infrastructure Load Balancer                          | Külső & belső  |

>[!IMPORTANT]
> A portok,  amelyekhez a cél félkövér, kötelező API Management szolgáltatás sikeres üzembe helyezéséhez.  A többi port blokkolása azonban a futó szolgáltatás használatának és figyelését, valamint a lekötött **SLA-t biztosítanak.** 

+ **TLS-funkció:** A TLS/SSL tanúsítványlánc építésének és érvényesítésének engedélyezéséhez az API Management-szolgáltatásnak kimenő hálózati kapcsolatra van szüksége a ocsp.msocsp.com, mscrl.microsoft.com és crl.microsoft.com. Ez a függőség nem szükséges, ha a tanúsítványba API Management tanúsítvány tartalmazza a ca legfelső szintű hitelesítésszolgáltatóhoz való teljes láncot.

+ **DNS-hozzáférés:** A DNS-kiszolgálókkal való kommunikációhoz az 53-as porton kimenő hozzáférés szükséges. Ha létezik egyéni DNS-kiszolgáló egy VPN-átjáró másik végén, a DNS-kiszolgálónak elérhetőnek kell lennie az azt üzemeltető API Management.

+ **Metrikák és állapotfigyelés:** Kimenő hálózati kapcsolat az Azure Monitorozási végpontokkal, amelyek a következő tartományokban oldhatóak fel. Ahogy az a táblázatban is látható, ezek az URL-címek az AzureMonitor szolgáltatáscímkéje alatt jelennek meg a hálózati biztonsági csoportokkal való használathoz.

    | Azure-környezet | Végpontok                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>gcs.prod.monitoring.core.windows.net(**új**)</li><li>global.prod.microsoftmetrics.com(**új**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**új**)</li><li>prod3-black.prod.microsoftmetrics.com(**új**)</li><li>prod3-red.prod.microsoftmetrics.com(**új**)</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com(**új**)</li><li>shoebox2.prod.microsoftmetrics.com(**új**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**új**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com(**új**)</li><li>shoebox2.prod.microsoftmetrics.com(**új**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**új**)</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |

  >[!IMPORTANT]
  > A fenti fürtök **.nsatc.net** DNS-zónával **való módosítása .microsoftmetrics.com** többnyire DNS-módosítás. A fürt IP-címe nem változik.

+ Regionális szolgáltatáscímkék: A Storage, SQL és Event Hubs szolgáltatáscímkék kimenő kapcsolatát lehetővé tevő NSG-szabályok az API Management-példányt tartalmazó régiónak (például Storage.WestUS az USA nyugati régiójában található API Management-példányhoz) megfelelő regionális verzióit használják. Többrégió-környezetek esetén az egyes régiók NSG-inek engedélyezniük kell az adatforgalmat az egyes régiók és az elsődleges régió szolgáltatáscímkéire.

    > [!IMPORTANT]
    > Ha engedélyezni szeretné [](api-management-howto-developer-portal.md) a fejlesztői portál közzétételét egy API Management-példányhoz egy virtuális hálózaton, győződjön meg arról, hogy az USA nyugati régiójában is engedélyezi a blobtároló kimenő kapcsolatát. Használja például a **Storage.WestUS szolgáltatáscímkét** egy NSG-szabályban. Az USA nyugati régiójában lévő blobtárolókhoz való csatlakozásra jelenleg szükség van a fejlesztői portál közzétételéhez bármely API Management példányhoz.

+ **SMTP-továbbító:** Kimenő hálózati kapcsolat az SMTP-továbbítóhoz, amely a következő gazdagépen oldódik fel: `smtpi-co1.msn.com` `smtpi-ch1.msn.com` , , `smtpi-db3.msn.com` `smtpi-sin.msn.com` és `ies.global.microsoft.com`

+ **CAPTCHA fejlesztői** portál: Kimenő hálózati kapcsolat a fejlesztői portál CAPTCHA-jának, amely a és a gazdagépek alatt `client.hip.live.com` oldódik `partner.hip.live.com` fel.

+ **Azure Portal Diagnostics**: Ahhoz, hogy a Azure Portal-ból a API Management-bővítmény Virtual Network-on belüli használata esetén engedélyezze a diagnosztikai naplók áramlását, a `dc.services.visualstudio.com` 443-as porton kimenő hozzáférés szükséges. Ez segít elhárítani a bővítmény használata során esetleg előfordulható problémákat.

+ **Azure Load Balancer:** A Szolgáltatáscímke bejövő kérésének engedélyezésére nincs szükség `AZURE_LOAD_BALANCER` a termékváltozathoz, mivel csak egy számítási egységet helyezünk `Developer` üzembe mögötte. A [168.63.129.16-os](../virtual-network/what-is-ip-address-168-63-129-16.md) portról bejövő forgalom azonban kritikus fontosságúvá válik, amikor magasabb termékváltozatra (például ) skáláz, mivel a Load Balancer állapot-mintavételének hibája miatt meghiúsul `Premium` az üzembe helyezés.

+ **Application Insights:** Ha az [Azure Application Insights](api-management-howto-app-insights.md) monitorozása engedélyezve van az API Management-ban, engedélyeznie [](../azure-monitor/app/ip-addresses.md#outgoing-ports) kell a kimenő kapcsolatot a telemetriai végponthoz a Virtual Network. 

+ Forgalom helyszíni tűzfalra való bújtatásának kényszerítése Express **Route** vagy hálózati virtuális berendezés használatával: Gyakori ügyfélkonfiguráció a saját alapértelmezett útvonalának meghatározása (0.0.0.0/0), amely a API Management delegált alhálózatról származó összes forgalmat helyszíni tűzfalon vagy hálózati virtuális berendezésen keresztüli forgalomra kényszeríti. Ez a forgalom mindig megszakítja a kapcsolatot az Azure API Management-ral, mert a kimenő forgalom blokkolva van a helyszínen, vagy a NAT egy fel nem ismert címkészletre kerül, amely már nem működik a különböző Azure-végpontokkal. A megoldáshoz el kell látnia néhány dolgot:

  * Engedélyezze a szolgáltatásvégpontokat az alhálózaton, amelyen a API Management szolgáltatás üzembe van stb. [A szolgáltatásvégpontokat][ServiceEndpoints] engedélyezni kell az Azure Sql, az Azure Storage, az Azure EventHub és az Azure ServiceBus szolgáltatáshoz. Ha végpontokat engedélyez API Management delegált alhálózatból ezekbe a szolgáltatásokba, akkor a Microsoft Azure gerinchálózatot használják, amely optimális útválasztást biztosít a szolgáltatásforgalom számára. Ha szolgáltatásvégpontokat használ kényszerített bújtatású Api Managementhez, a fenti Azure-szolgáltatások forgalma nem kényszerített bújtatással történik. A másik API Management szolgáltatásfüggőség forgalma kényszerített bújtatással halad át, és nem veszhet el, különben a API Management szolgáltatás nem működne megfelelően.
    
  * Az internetről az API Management szolgáltatás felügyeleti végpontjára irányuló összes vezérlősík-forgalom a bejövő IP-k egy adott halmazán keresztül van irányítva, amelyet a API Management. A forgalom kényszerített bújtatása esetén a válaszok nem lesznek szimmetrikusan leképezve ezekre a bejövő forrás IP-ekre. A korlát leküzdése érdekében hozzá kell adnunk a következő, felhasználó által megadott útvonalakat[(UDR-ket),][UDRs]amelyek az Azure-ba irányítják a forgalmat a gazdaútvonalak célhelyének "Internet" beállításával. A vezérlősík forgalmához bejövő IP-címek készlete dokumentálva van a vezérlősík [IP-címeiről](#control-plane-ips)

  * Más, API Management kényszerített bújtatású szolgáltatásfüggőségek esetén meg kell oldani az állomásnevet, és el kell érni a végpontot. Ezek közé tartoznak a következők:
      - Metrikák és állapotfigyelés
      - Azure Portal diagnosztika
      - SMTP-továbbító
      - FEJLESZTŐI portál – CAPTCHA

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>Hibaelhárítás
* **Kezdeti beállítás:** Ha a API Management szolgáltatás kezdeti üzembe helyezése egy alhálózaton nem sikerül, javasoljuk, hogy először telepítsen egy virtuális gépet ugyanabban az alhálózatban. Ezután távoli asztalt a virtuális géphez, és ellenőrizze, hogy van-e kapcsolat az Alábbi erőforrások valamelyikével az Azure-előfizetésében
    * Azure Storage-blob
    * Azure SQL Database
    * Azure Storage-tábla

  > [!IMPORTANT]
  > A kapcsolat ellenőrzése után mindenképpen távolítsa el az alhálózatban üzembe helyezett összes erőforrást, mielőtt API Management az alhálózatba.

* **Hálózati kapcsolat állapotának** ellenőrzése: Miután üzembe API Management az alhálózatban, a portálon ellenőrizheti a példány és az olyan függőségek közötti kapcsolatot, mint az Azure Storage. A portál bal oldali menüjében, az Üzembe helyezés **és** infrastruktúra alatt válassza a **Hálózati kapcsolat állapota lehetőséget.**

   :::image type="content" source="media/api-management-using-with-vnet/verify-network-connectivity-status.png" alt-text="Hálózati kapcsolat állapotának ellenőrzése a portálon":::

    * Válassza **a Kötelező** lehetőséget a szükséges Azure-szolgáltatásokhoz való csatlakozás áttekintéséhez API Management. A hiba azt jelzi, hogy a példány nem tud alapvető műveleteket végrehajtani az API-k kezeléséhez.
    * Válassza **a Választható lehetőséget** a választható szolgáltatásokhoz való csatlakozás áttekintéséhez. A hibák csak azt jelzik, hogy az adott funkció (például SMTP) nem fog működni. Egy hiba a szolgáltatáspéldány használatának és monitorának teljesítménycsökkenését okozhatja, API Management biztosítja a lekötött SLA-t.

A csatlakozási problémák megoldásához tekintse át a gyakori hálózati konfigurációs [problémákat,](#network-configuration-issues) és javítsa ki a szükséges hálózati beállításokat.

* **Növekményes** frissítések: Ha módosítja a hálózatot, tekintse meg a [NetworkStatus](/rest/api/apimanagement/2019-12-01/networkstatus)API-t, és ellenőrizze, hogy az API Management szolgáltatás nem oszlott-e el a kritikus fontosságú erőforrásokhoz, amelyektől függ. A kapcsolati állapotot 15 percenként kell frissíteni.

* **Erőforrás-navigációs hivatkozások:** Az Resource Manager típusú vnet alhálózatban való üzembe helyezéskor a API Management lefoglalja az alhálózatot egy erőforrás-navigációs hivatkozás létrehozásával. Ha az alhálózat már tartalmaz egy másik szolgáltatótól származó erőforrást, az üzembe helyezés sikertelen **lesz.** Hasonlóképpen, ha áthelyez egy API Management egy másik alhálózatra, vagy törli azt, eltávolítjuk az erőforrás navigációs hivatkozását.

## <a name="subnet-size-requirement"></a><a name="subnet-size"></a> Alhálózat méretkövetelménye
Az Azure lefoglal néhány IP-címet az egyes alhálózatok számára, és ezek a címek nem használhatók. Az alhálózatok első és utolsó IP-címe a protokollnak való megfelelőség érdekében van fenntartva, valamint három további cím van használatban az Azure-szolgáltatásokhoz. További információ: Vannak korlátozások az IP-címek alhálózaton [belüli használatával kapcsolatban?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Az Azure VNET-infrastruktúra által használt IP-címeken kívül az alhálózat minden Api Management-példánya két IP-címet használ a Prémium termékváltozat egységenként vagy egy IP-címet a fejlesztői termékváltozathoz. Minden példány lefoglal egy további IP-címet a külső terheléselosztáshoz. A belső virtuális hálózatban való üzembe helyezéshez további IP-címre van szükség a belső terheléselosztáshoz.

Mivel a számítás az alhálózat minimális mérete felett van, az üzembe API Management a /29 érték, amely három használható IP-címet biztosít.

A további méretezési egységekhez API Management két további IP-cím szükséges.

## <a name="routing"></a><a name="routing"></a> Útválasztás
+ Egy elosztott terhelésű nyilvános IP-cím (VIP) lesz fenntartva, hogy hozzáférést biztosítson az összes szolgáltatásvégponthoz.
+ A rendszer egy alhálózati IP-címtartományból (DIP) származó IP-címmel fér hozzá a virtuális hálózaton belüli erőforrásokhoz, és egy nyilvános IP-címmel (VIP) fér hozzá a virtuális hálózaton kívüli erőforrásokhoz.
+ Az elosztott terhelésű nyilvános IP-cím az Áttekintés/Alapvető szolgáltatások panelen található a Azure Portal.

## <a name="limitations"></a><a name="limitations"> </a>Korlátozások
* A 2020-12-01-es vagy korábbi API-verziót használó ügyfelek esetében az API Management-példányokat tartalmazó alhálózat nem tartalmazhat más Azure-erőforrástípusokat.
* Az alhálózatnak és a API Management szolgáltatásnak ugyanabban az előfizetésben kell lennie.
* Az új példányokat API Management alhálózatot nem lehet áthelyezni az előfizetések között.
* A belső virtuális hálózati API Management konfigurált többrégiópontos környezetek esetében a felhasználók felelnek a terheléselosztás több régióban történő kezeléséért, mivel ők a felelősek az útválasztásért.
* Egy másik régióban található, globálisan virtuális társhálózatban található erőforrás és a belső API Management szolgáltatás közötti kapcsolat platformkorlátozás miatt nem fog működni. További információ: Az egyik virtuális hálózat erőforrásai nem tudnak kommunikálni az [Azure belső terheléselosztási szolgáltatásával a virtuális társhálózatban.](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"></a> Vezérlősík IP-címei

Az IP-címeket az **Azure-környezetek osztják el.** Ha engedélyezi a bejövő kérelmeket, a **Globális** jelölésű IP-címet a régióspecifikus IP-címmel együtt kell engedélyezettnek lennie. 

| **Azure-környezet**|   **Régió**|  **IP-cím**|
|-----------------|-------------------------|---------------|
| Azure Public| USA déli középső középső (globális)| 104.214.19.224|
| Azure Public| USA északi középső (globális)| 52.162.110.80|
| Azure Public| USA nyugati középső régiója| 52.253.135.58|
| Azure Public| Dél-Korea középső régiója| 40.82.157.167|
| Azure Public| Az Egyesült Királyság nyugati régiója| 51.137.136.0|
| Azure Public| Nyugat-Japán| 40.81.185.8|
| Azure Public| USA északi középső régiója| 40.81.47.216|
| Azure Public| Az Egyesült Királyság déli régiója| 51.145.56.125|
| Azure Public| Nyugat-India| 40.81.89.24|
| Azure Public| USA keleti régiója| 52.224.186.99|
| Azure Public| Nyugat-Európa| 51.145.179.78|
| Azure Public| Kelet-Japán| 52.140.238.179|
| Azure Public| Közép-Franciaország| 40.66.60.111|
| Azure Public| Kelet-Kanada| 52.139.80.117|
| Azure Public| Egyesült Arab Emírségek északi részén| 20.46.144.85|
| Azure Public| Dél-Brazília| 191.233.24.179|
| Azure Public| Délkelet-Brazília| 191.232.18.181|
| Azure Public| Délkelet-Ázsia| 40.90.185.46|
| Azure Public| Dél-Afrika északi régiója| 102.133.130.197|
| Azure Public| Közép-Kanada| 52.139.20.34|
| Azure Public| Dél-Korea déli régiója| 40.80.232.185|
| Azure Public| Közép-India| 13.71.49.1|
| Azure Public| USA nyugati régiója| 13.64.39.16|
| Azure Public| Délkelet-Ausztrália| 20.40.160.107|
| Azure Public| Ausztrália középső régiója| 20.37.52.67|
| Azure Public| Dél-India| 20.44.33.246|
| Azure Public| Az USA középső régiója| 13.86.102.66|
| Azure Public| Kelet-Ausztrália| 20.40.125.155|
| Azure Public| USA 2. nyugati régiója| 51.143.127.203|
| Azure Public| USA 2. keleti régiója – EUAP| 52.253.229.253|
| Azure Public| USA középső régiója – EUAP| 52.253.159.160|
| Azure Public| USA déli középső régiója| 20.188.77.119|
| Azure Public| USA 2. keleti régiója| 20.44.72.3|
| Azure Public| Észak-Európa| 52.142.95.35|
| Azure Public| Kelet-Ázsia| 52.139.152.27|
| Azure Public| Dél-Franciaország| 20.39.80.2|
| Azure Public| Nyugat-Svájc| 51.107.96.8|
| Azure Public| Ausztrália 2. középső régiója| 20.39.99.81|
| Azure Public| Egyesült Arab Emírségek középső| 20.37.81.41|
| Azure Public| Észak-Svájc| 51.107.0.91|
| Azure Public| Dél-Afrika nyugati régiója| 102.133.0.79|
| Azure Public| Nyugat-Németország – középső régió| 51.116.96.0|
| Azure Public| Észak-Németország| 51.116.0.0|
| Azure Public| Kelet-Kelet| 51.120.2.185|
| Azure Public| Nyugat-Nyugat-Németország| 51.120.130.134|
| Azure China 21Vianet| Észak-Kína (globális)| 139.217.51.16|
| Azure China 21Vianet| Kelet-Kína (globális)| 139.217.171.176|
| Azure China 21Vianet| Észak-Kína| 40.125.137.220|
| Azure China 21Vianet| Kelet-Kína| 40.126.120.30|
| Azure China 21Vianet| Kína 2. északi régiója| 40.73.41.178|
| Azure China 21Vianet| Kína 2. keleti régiója| 40.73.104.4|
| Azure Government| USGov Virginia (globális)| 52.127.42.160|
| Azure Government| USGov Texas (globális)| 52.127.34.192|
| Azure Government| USGov Virginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| USGov Arizona| 52.244.32.39|
| Azure Government| USGov Texas| 52.243.154.118|
| Azure Government| USDoD – középső| 52.182.32.132|
| Azure Government| USDoD – keleti régió| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>Kapcsolódó tartalom
* [Virtuális Virtual Network csatlakoztatása a háttérkiszolgálóhoz Vpn Gateway használatával](../vpn-gateway/design.md#s2smulti)
* [Különböző Virtual Network modellekből származó alkalmazás csatlakoztatása](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Az API Inspector használata az Azure-beli hívások API Management](api-management-howto-api-inspector.md)
* [Virtual Network gyakori kérdések](../virtual-network/virtual-networks-faq.md)
* [Szolgáltatáscímkék](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
