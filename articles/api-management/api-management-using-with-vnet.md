---
title: Az Azure API Management használata virtuális hálózatokkal
description: Ismerje meg, hogyan lehet kapcsolatot létesíteni az Azure-beli virtuális hálózattal API Management és hogyan érheti el a webszolgáltatásokat.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/10/2020
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: c63b71ad00a5621babe07597720a1e9ea87f1e4a
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260248"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Az Azure API Management használata virtuális hálózatokkal
Az Azure-beli virtuális hálózatokkal (VNET-ekkel) olyan nem internetalapú, irányítható hálózatokra helyezheti át Azure-erőforrásait, amelyekhez való hozzáférést Ön szabályozza. Ezek a hálózatok ezután különböző VPN-technológiákkal csatlakozhatnak a helyszíni hálózatokhoz. Az Azure Virtual Networks szolgáltatással kapcsolatos további információkért tekintse meg az alábbi információkat: [azure Virtual Network – áttekintés](../virtual-network/virtual-networks-overview.md).

Az Azure API Management a virtuális hálózaton (VNET) belül üzembe helyezhető, így a hálózaton belül elérhetővé válik a háttér-szolgáltatások. A fejlesztői portál és az API-átjáró úgy konfigurálható, hogy az internetről vagy csak a virtuális hálózaton belül legyen elérhető.

> [!NOTE]
> Az API-importálási dokumentum URL-címét nyilvánosan elérhető internetes címen kell üzemeltetni.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következőket kell tennie:

+ Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Egy APIM-példány. További információ: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>VNET-kapcsolatok engedélyezése

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>VNET-kapcsolat engedélyezése a Azure Portal használatával

1. Az API Management-példány megkereséséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki **API Management szolgáltatásokat**.

2. Válassza ki API Management-példányát.

3. Válassza a **virtuális hálózat** lehetőséget.
4. Konfigurálja a API Management példányt egy virtuális hálózaton belül üzembe helyezéshez.

    :::image type="content" source="media/api-management-using-with-vnet/api-management-menu-vnet.png" alt-text="Válassza ki Azure Portal a virtuális hálózatot.":::
    
5. Válassza ki a kívánt hozzáférési típust:

    * **Kikapcsolva**: ez az alapértelmezett. API Management nincs üzembe helyezve virtuális hálózatban.

    * **Külső**: a API Management átjáró és a fejlesztői portál a nyilvános internetről külső terheléselosztó használatával érhető el. Az átjáró hozzáférhet a virtuális hálózaton belüli erőforrásokhoz.

        ![Nyilvános társviszony-létesítés][api-management-vnet-public]

    * **Belső**: a API Management átjáró és a fejlesztői portál csak a virtuális hálózaton belülről érhető el belső terheléselosztó használatával. Az átjáró hozzáférhet a virtuális hálózaton belüli erőforrásokhoz.

        ![Magánhálózati társviszony-létesítés][api-management-vnet-private]

6. Ha a **külső** vagy a **belső** lehetőséget választotta, megjelenik az összes olyan régió listája, ahol a API Management szolgáltatás kiépítve van. Válasszon egy **helyet**, majd válassza ki a **virtuális hálózatot** és az **alhálózatot**. A virtuális hálózatok listája a klasszikus és a Resource Manager virtuális hálózatokkal együtt érhető el az Ön által konfigurált régióban beállított Azure-előfizetésekben.

    > [!IMPORTANT]
    > Ha Azure API Management-példányt telepít egy Resource Manager-VNET, a szolgáltatásnak olyan dedikált alhálózaton kell lennie, amely nem tartalmaz más erőforrásokat, kivéve az Azure API Management-példányokat. Ha egy Azure API Management-példány üzembe helyezésére tett kísérletet egy olyan Resource Manager VNET-alhálózatra, amely más erőforrásokat is tartalmaz, a telepítés sikertelen lesz.

    Ezután válassza az **Alkalmaz** lehetőséget. A API Management példány **virtuális hálózat** lapja frissül az új virtuális hálózattal és az alhálózattal.

    :::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-select.png" alt-text="Virtuális hálózati beállítások a portálon.":::

7. A felső navigációs sávon válassza a **Mentés** lehetőséget, majd válassza a **hálózati konfiguráció alkalmazása** lehetőséget.

> [!NOTE]
> A API Management példány virtuális IP-címe minden alkalommal megváltozik, amikor a VNET engedélyezve vagy le van tiltva.
> A virtuális IP-cím akkor is megváltozik, ha a API Management **kívülről** **belsőre** kerül, vagy fordítva.
>

> [!IMPORTANT]
> Ha eltávolítja a API Management egy VNET, vagy megváltoztatja azt, akkor a korábban használt VNET akár hat óráig is zárolható maradhat. Ebben az időszakban nem lehet törölni a VNET, vagy új erőforrást telepíteni. Ez a viselkedés a 2018-01-01-es és korábbi API-verziót használó ügyfelek esetében igaz. A 2019-01-01-es és újabb verziójú API-t használó ügyfelek a társított API Management szolgáltatás törlése után azonnal felszabadul a VNET.

## <a name="deploy-api-management-into-external-vnet"></a><a name="deploy-apim-external-vnet"> </a>API Management üzembe helyezése külső VNET

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet%2Fazuredeploy.json)

* **API Management szolgáltatás létrehozása egy VNET belül**: a [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) parancsmaggal hozzon létre egy Azure API Management-szolgáltatást egy VNET belül.

* **Meglévő API Management-szolgáltatás üzembe helyezése egy VNET belül**: az [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) parancsmag használatával helyezzen át egy meglévő Azure API Management-szolgáltatást egy Virtual Network belül.

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>Kapcsolódás virtuális hálózaton belül üzemeltetett webszolgáltatáshoz
Miután a API Management szolgáltatás csatlakozott a VNET, a háttérbeli szolgáltatások elérése nem különbözik a nyilvános szolgáltatásokhoz való hozzáféréstől. Csak írja be a helyi IP-címet vagy az állomásnevet (ha a DNS-kiszolgáló a VNET van konfigurálva) a webszolgáltatás **URL-** címe MEZŐBE új API létrehozásakor vagy meglévő szerkesztésekor.

![API hozzáadása VPN-ből][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>Gyakori hálózati konfigurációs problémák
A következő lista felsorolja azokat a gyakori konfigurációs problémákat, amelyek a API Management szolgáltatás egy Virtual Network történő telepítésekor fordulnak elő.

* **Egyéni DNS-kiszolgáló beállítása**: a API Management szolgáltatás számos Azure-szolgáltatástól függ. Ha a API Management egy egyéni DNS-kiszolgálóval rendelkező VNET tárolja, az Azure-szolgáltatások állomásnevét fel kell oldania. Kérjük, kövesse [ezt az](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) útmutatót az egyéni DNS-telepítésre vonatkozóan. Tekintse meg az alábbi portok táblázatot, valamint az egyéb hálózati követelményeket a hivatkozáshoz.

> [!IMPORTANT]
> Ha egyéni DNS-kiszolgáló (ka) t szeretne használni a VNET, akkor a API Management szolgáltatás üzembe helyezése **előtt** állítsa be. Ellenkező esetben frissítenie kell a API Management szolgáltatást minden alkalommal, amikor módosítja a DNS-kiszolgáló (ka) t a [hálózati konfiguráció alkalmazása művelet](/rest/api/apimanagement/2019-12-01/apimanagementservice/applynetworkconfigurationupdates) futtatásával.

* A **API Managementhoz szükséges portok**: a bejövő és a kimenő forgalom abba az alhálózatba, amelyben a API Management telepítve van, a [hálózati biztonsági csoport][Network Security Group]használatával vezérelhető. Ha a portok bármelyike nem érhető el, előfordulhat, hogy API Management nem működik megfelelően, és elérhetetlenné válhat. Ha a portok közül egy vagy több le van tiltva, akkor a API Management VNET való használatakor a rendszer egy másik gyakori konfigurációs problémát is jelent.

<a name="required-ports"></a> Ha egy API Management Service-példány egy VNET üzemel, a rendszer a következő táblázatban található portokat használja.

| Forrás/cél port (ok) | Irány          | Átviteli protokoll |   [Szolgáltatás címkéi](../virtual-network/network-security-groups-overview.md#service-tags) <br> Forrás/cél   | Cél ( \* )                                                 | Virtual Network típusa |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| */[80], 443                  | Bejövő            | TCP                | INTERNET/VIRTUAL_NETWORK            | Ügyfél-kommunikáció API Management                      | Külső             |
| */3443                     | Bejövő            | TCP                | ApiManagement/VIRTUAL_NETWORK       | Felügyeleti végpont a Azure Portal és a PowerShell számára         | Külső & belső  |
| */443                  | Kimenő           | TCP                | VIRTUAL_NETWORK/tárterület             | **Függőség az Azure Storage-ban**                             | Külső & belső  |
| */443                  | Kimenő           | TCP                | VIRTUAL_NETWORK/AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) és Azure-beli kulcstartó-függőség                  | Külső & belső  |
| */1433                     | Kimenő           | TCP                | VIRTUAL_NETWORK/SQL                 | **Hozzáférés az Azure SQL-végpontokhoz**                           | Külső & belső  |
| */443                     | Kimenő           | TCP                | VIRTUAL_NETWORK/AzureKeyVault                 | **Hozzáférés az Azure kulcstartóhoz**                           | Külső & belső  |
| */5671, 5672, 443          | Kimenő           | TCP                | VIRTUAL_NETWORK/EventHub            | Az [Event hub-házirend](api-management-howto-log-event-hubs.md) és a figyelési ügynök közötti függőség | Külső & belső  |
| */445                      | Kimenő           | TCP                | VIRTUAL_NETWORK/tárterület             | Függőség a [git](api-management-configuration-repository-git.md) -hez készült Azure-fájlmegosztástól                      | Külső & belső  |
| */443, 12000                     | Kimenő           | TCP                | VIRTUAL_NETWORK/AzureCloud            | Állapot-és figyelési bővítmény         | Külső & belső  |
| */1886, 443                     | Kimenő           | TCP                | VIRTUAL_NETWORK/AzureMonitor         | [Diagnosztikai naplók és mérőszámok](api-management-howto-use-azure-monitor.md)közzététele, [Resource Health](../service-health/resource-health-overview.md) és [Application Insights](api-management-howto-app-insights.md)                   | Külső & belső  |
| */25, 587, 25028                       | Kimenő           | TCP                | VIRTUAL_NETWORK/INTERNET            | Kapcsolódás az SMTP Relayhez az e-mailek küldéséhez                    | Külső & belső  |
| */6381 – 6383              | Bejövő & kimenő | TCP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | A Redis szolgáltatás elérése a számítógépek közötti [gyorsítótárazási](api-management-caching-policies.md) házirendekhez         | Külső & belső  |
| */4290              | Bejövő & kimenő | UDP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | Számlálók szinkronizálása a számítógépek között a [díjszabási korlátozási](api-management-access-restriction-policies.md#LimitCallRateByKey) házirendekhez         | Külső & belső  |
| * / *                        | Bejövő            | TCP                | AZURE_LOAD_BALANCER/VIRTUAL_NETWORK | Azure-infrastruktúra Load Balancer                          | Külső & belső  |

>[!IMPORTANT]
> Azok a portok, amelyekhez a *cél* **félkövér** , szükségesek ahhoz, hogy API Management szolgáltatás üzembe helyezése sikeres legyen. A többi port blokkolása azonban **romlást** okoz a **futó szolgáltatás használatára és figyelésére, valamint az előjegyzett SLA biztosítására**.

+ **TLS-funkciók**: a TLS/SSL-tanúsítványlánc létrehozásának és ellenőrzésének engedélyezéséhez a API Management szolgáltatásnak kimenő hálózati kapcsolatra van szüksége a OCSP.msocsp.com, a mscrl.microsoft.com és a CRL.microsoft.com. Ez a függőség nem szükséges, ha a API Management feltöltött bármelyik tanúsítvány teljes láncot tartalmaz a HITELESÍTÉSSZOLGÁLTATÓ gyökeréhez.

+ **DNS-hozzáférés**: a 53-es porton kimenő hozzáférés szükséges a DNS-kiszolgálókkal folytatott kommunikációhoz. Ha egy VPN-átjáró másik végén létezik egy egyéni DNS-kiszolgáló, a DNS-kiszolgálónak elérhetőnek kell lennie a API Management üzemeltető alhálózaton.

+ **Metrikák és állapot-figyelés**: kimenő hálózati kapcsolat az Azure monitoring-végpontokkal, amelyek a következő tartományokban oldhatók fel. Ahogy az a táblázatban is látható, ezek az URL-címek a AzureMonitor szolgáltatás címkéje szerint jelennek meg a hálózati biztonsági csoportokkal való használatra.

    | Azure-környezet | Végpontok                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>gcs.prod.monitoring.core.windows.net (**új**)</li><li>prod.warmpath.msftcloudes.com (**elavultnak kell lennie**)</li><li>global.prod.microsoftmetrics.com (**új**)</li><li>global.metrics.nsatc.net (**elavultnak kell lennie**)</li><li>shoebox2.prod.microsoftmetrics.com (**új**)</li><li>shoebox2.metrics.nsatc.net (**elavultnak kell lennie**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com (**új**)</li><li>prod3.metrics.nsatc.net (**elavultnak kell lennie**)</li><li>prod3-black.prod.microsoftmetrics.com (**új**)</li><li>prod3-black.prod3.metrics.nsatc.net (**elavultnak kell lennie**)</li><li>prod3-red.prod.microsoftmetrics.com (**új**)</li><li>prod3-red.prod3.metrics.nsatc.net (**elavultnak kell lennie**)</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com (**új**)</li><li>global.metrics.nsatc.net (**elavultnak kell lennie**)</li><li>shoebox2.prod.microsoftmetrics.com (**új**)</li><li>shoebox2.metrics.nsatc.net (**elavultnak kell lennie**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com (**új**)</li><li>prod3.metrics.nsatc.net (**elavultnak kell lennie**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com (**új**)</li><li>global.metrics.nsatc.net (**elavultnak kell lennie**)</li><li>shoebox2.prod.microsoftmetrics.com (**új**)</li><li>shoebox2.metrics.nsatc.net (**elavultnak kell lennie**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com (**új**)</li><li>prod3.metrics.nsatc.net (**elavultnak kell lennie**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |

  >[!IMPORTANT]
  > A DNS-zónák felett lévő fürtök változása. a **nsatc.net** a **. microsoftmetrics.com** -re általában egy DNS-változás. A fürt IP-címe nem változik.

+ **Regionális szolgáltatás címkék**: a NSG-szabályok, amelyek engedélyezik a Storage, az SQL és a Event Hubs Service-címkék kimenő kapcsolatát, a API Management példányt tartalmazó régióhoz tartozó (például Storage. WestUS), az USA nyugati régiójában található API Management-példányhoz tartozó, az adott címkék regionális verzióit használhatják. A többrégiós környezetekben az egyes régiókban lévő NSG engedélyezni kell az adott régió és az elsődleges régió szolgáltatásbeli címkéi forgalmát.

    > [!IMPORTANT]
    > Ha engedélyezni szeretné a [fejlesztői portál](api-management-howto-developer-portal.md) közzétételét egy virtuális hálózat API Management példánya számára, győződjön meg arról, hogy az USA nyugati régiójában is engedélyezi a kimenő kapcsolatot a blob Storage-ban. Használja például a **Storage. WestUS** szolgáltatás címkéjét egy NSG-szabályban. Az USA nyugati régiójában lévő blob Storage-hoz való kapcsolódásra jelenleg minden API Management példányon közzé kell tenni a fejlesztői portált.

+ **SMTP-továbbító**: kimenő hálózati kapcsolat az SMTP-továbbítóhoz, amely a gazdagép, a `smtpi-co1.msn.com` , `smtpi-ch1.msn.com` `smtpi-db3.msn.com` `smtpi-sin.msn.com` és a `ies.global.microsoft.com`

+ **Fejlesztői portál CAPTCHA**: kimenő hálózati kapcsolat a fejlesztői portál CAPTCHA-vel, amely a gazdagépeken és a-ben oldható fel `client.hip.live.com` `partner.hip.live.com` .

+ **Azure Portal diagnosztika**: ahhoz, hogy a API Management bővítmény egy Virtual Networkon belülről való használatakor lehetővé váljon a diagnosztikai naplók áramlása Azure Portal, a 443-as porton kimenő hozzáférésre `dc.services.visualstudio.com` van szükség. Ez segít a bővítmények használata során felmerülő problémák elhárításában.

+ **Azure Load Balancer**: a szolgáltatási címke bejövő kérelmének engedélyezése `AZURE_LOAD_BALANCER` nem követelmény az SKU számára `Developer` , mivel csak egy egységet helyezünk üzembe a számítási feladatokból. A [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) bejövő állapota azonban kritikusra vált, ha a magasabb szintű SKU-ra, például a `Premium` Load Balancer állapotának meghibásodása miatt nem sikerül üzembe helyezést végrehajtani.

+ **Application Insights**: Ha az [Azure Application Insights](api-management-howto-app-insights.md) -figyelés engedélyezve van a API Managementon, akkor engedélyeznie kell a kimenő kapcsolatot a [telemetria-végpontnak](../azure-monitor/app/ip-addresses.md#outgoing-ports) a Virtual Network. 

+ A helyszíni **tűzfal felé irányuló forgalom kényszerítése az expressz útvonal vagy a hálózati virtuális berendezés használatával**: a közös ügyfél-konfiguráció a saját alapértelmezett útvonal (0.0.0.0/0) meghatározása, amely a API Management delegált alhálózatról a helyszíni tűzfalon vagy egy hálózati virtuális berendezésen keresztül áramlik át a forgalmat. Ez a forgalmi folyamat mindig megszakítja az Azure API Management kapcsolatát, mivel a kimenő forgalom vagy a helyszínen van letiltva, vagy a NAT-t olyan, a különböző Azure-végpontokkal már nem működő címekből álló halmazba kívánja felismerni. A megoldáshoz pár dolgot kell tennie:

  * Engedélyezze a szolgáltatási végpontokat azon az alhálózaton, amelyben a API Management szolgáltatás telepítve van. A [szolgáltatási végpontokat][ServiceEndpoints] engedélyezni kell az Azure SQL, az Azure Storage, az Azure EventHub és az Azure ServiceBus számára. A végpontok közvetlenül a API Management delegált alhálózatról ezekbe a szolgáltatásokba való engedélyezése lehetővé teszi, hogy a Microsoft Azure gerinc hálózatot használják, amely optimális útválasztást biztosít a szolgáltatás forgalmához. Ha kényszerített bújtatású API-felügyelettel rendelkező szolgáltatási végpontokat használ, a fenti Azure-szolgáltatások forgalma nem kényszerített bújtatással. A másik API Management szolgáltatás függőségi forgalma kényszerített bújtatású, és nem lehet elveszett, vagy a API Management szolgáltatás nem fog megfelelően működni.
    
  * Az internetről a API Management szolgáltatás felügyeleti végpontja felé irányuló összes vezérlési sík a API Management által üzemeltetett bejövő IP-címekre irányítja át a forgalmat. A forgalom kényszerített bújtatása esetén a válaszok nem lesznek szimmetrikusan leképezve a bejövő forrás IP-címekre. A korlátozás leküzdéséhez hozzá kell adnia a következő, felhasználó által megadott útvonalakat ([UDR][UDRs]) a forgalom Azure-ba történő visszairányításához. ehhez a gazdagép-útvonalak célhelyét az "Internet" értékre kell állítani. A vezérlési sík forgalmához tartozó bejövő IP-címek készlete dokumentálja a [vezérlési sík IP-címeit](#control-plane-ips)

  * A kényszerített bújtatással rendelkező más API Management szolgáltatási függőségek esetében az állomásnév feloldására és a végpont elérésére van lehetőség. Ilyenek például a következők:
      - Metrikák és állapot-figyelés
      - Azure Portal diagnosztika
      - SMTP-továbbító
      - Fejlesztői portál CAPTCHA

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>Hibaelhárítás
* **Kezdeti beállítás**: ha a API Management szolgáltatás központi telepítése egy alhálózatba nem sikerül, azt javasoljuk, hogy először helyezzen üzembe egy virtuális gépet ugyanahhoz az alhálózathoz. A következő Távoli asztalt a virtuális gépre, és ellenőrizze, hogy van-e kapcsolat az Azure-előfizetésében található egyes erőforrások valamelyikével
    * Azure Storage-blob
    * Azure SQL Database
    * Azure Storage-tábla

  > [!IMPORTANT]
  > A kapcsolat ellenőrzése után mindenképpen távolítsa el az alhálózaton üzembe helyezett összes erőforrást, mielőtt a API Management az alhálózatba telepítené.

* **Hálózati kapcsolat állapotának ellenőrzése**: a API Management az alhálózatba való telepítése után a portálon ellenőrizheti, hogy a példánya kapcsolódott-e a függőségekhez, például az Azure Storage szolgáltatáshoz. A portál bal oldali menüjében, az **üzembe helyezés és az infrastruktúra** területen válassza a **hálózati kapcsolat állapota** lehetőséget.

   :::image type="content" source="media/api-management-using-with-vnet/verify-network-connectivity-status.png" alt-text="Hálózati kapcsolat állapotának ellenőrzése a portálon":::

    * Válassza a **szükséges** lehetőséget a szükséges Azure-szolgáltatásokhoz való kapcsolódás ellenőrzéséhez API Managementhoz. A hiba azt jelzi, hogy a példány nem tudja végrehajtani az API-k kezelésére szolgáló alapvető műveleteket.
    * Válassza a **választható** lehetőséget a választható szolgáltatásokhoz való kapcsolódás ellenőrzéséhez. A hiba azt jelzi, hogy az adott funkció nem fog működni (például SMTP). A meghibásodás a API Management-példány használatára és figyelésére, valamint az előjegyzett SLA megadására is vezethet.

A kapcsolódási problémák megoldásához tekintse át az [általános hálózati konfigurációval kapcsolatos problémákat](#network-configuration-issues) , és javítsa a szükséges hálózati beállításokat.

* **Növekményes frissítések**: a hálózat módosításakor tekintse meg a [NetworkStatus API](/rest/api/apimanagement/2019-12-01/networkstatus)-t, és ellenőrizze, hogy a API Management szolgáltatás nem vesztett-e el semmilyen kritikus erőforráshoz való hozzáférést, amelytől függ. A kapcsolati állapotot 15 percenként kell frissíteni.

* **Erőforrás-navigációs hivatkozások**: Ha a üzembe helyezés Resource Manager stílusú vnet alhálózatba, API Management az alhálózatot az erőforrás-navigációs hivatkozás létrehozásával. Ha az alhálózat már tartalmaz egy másik szolgáltatótól származó erőforrást, akkor a telepítés **sikertelen** lesz. Hasonlóképpen, ha egy API Management szolgáltatást egy másik alhálózatra helyez át, vagy törli azt, akkor az erőforrás-navigációs hivatkozás el lesz távolítva.

## <a name="subnet-size-requirement"></a><a name="subnet-size"></a> Alhálózat méretének követelménye
Az Azure egyes alhálózatokon belül fenntart néhány IP-címet, és ezeket a címeket nem lehet használni. Az alhálózatok első és utolsó IP-címe a protokoll-megfelelőség számára van fenntartva, valamint az Azure-szolgáltatásokhoz használt három további címet. További információ: az [IP-címek ezen alhálózatokon belüli használatára vonatkozó korlátozások?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Az Azure VNET-infrastruktúra által használt IP-címeken kívül az alhálózat minden egyes API Management-példánya két IP-címet használ a prémium SKU vagy egy IP-cím alapján a fejlesztői SKU számára. Minden példány fenntart egy további IP-címet a külső terheléselosztó számára. Belső virtuális hálózatra való üzembe helyezéskor további IP-címet igényel a belső terheléselosztó számára.

Az alhálózat minimális mérete feletti számítás miatt, amelyben a API Management telepíthető a/29, amely három használható IP-címet biztosít.

API Management minden további méretezési egységéhez két további IP-cím szükséges.

## <a name="routing"></a><a name="routing"></a> Útválasztás
+ Az elosztott terhelésű nyilvános IP-cím (VIP) le lesz foglalva az összes szolgáltatási végpont elérésének biztosításához.
+ A rendszer az alhálózati IP-címtartomány (DIP) IP-címét fogja használni a vnet található erőforrások eléréséhez, és a nyilvános IP-címet (VIP) használja a vnet kívüli erőforrások elérésére.
+ Az elosztott terhelésű nyilvános IP-cím a Azure Portal áttekintés/Essentials paneljén található.

## <a name="limitations"></a><a name="limitations"> </a>Korlátozások
* API Management példányokat tartalmazó alhálózat nem tartalmazhat más Azure-erőforrás-típusokat.
* Az alhálózatnak és a API Management szolgáltatásnak ugyanahhoz az előfizetéshez kell tartoznia.
* API Management példányokat tartalmazó alhálózat nem helyezhető át az előfizetések között.
* A belső virtuális hálózati módban konfigurált többrégiós API Management üzemelő példányok esetében a felhasználók feladata, hogy a terheléselosztást több régión keresztül kezelhesse, ahogy az Útválasztás tulajdonosa.
* Ha egy másik régióban lévő, globálisan összekötött VNET lévő erőforrással kapcsolódik, API Management a szolgáltatás belső módban, a platform korlátozása miatt nem fog működni. További információ: az [egyik virtuális hálózat erőforrásai nem tudnak kommunikálni az Azure belső terheléselosztó szolgáltatásával a társ virtuális hálózatban](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) .

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"></a> Vezérlési sík IP-címei

Az IP-címeket az Azure- **környezet** osztja el. Ha a **globálisan** megjelölt IP-címet engedélyezi a bejövő kérelmek számára, a **régió** adott IP-címével együtt kell engedélyezni.

| **Azure-környezet**|   **Régió**|  **IP-cím**|
|-----------------|-------------------------|---------------|
| Azure Public| USA déli középső régiója (globális)| 104.214.19.224|
| Azure Public| USA északi középső régiója (globális)| 52.162.110.80|
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
| Azure Public| Észak-Egyesült Arab| 20.46.144.85|
| Azure Public| Dél-Brazília| 191.233.24.179|
| Azure Public| Dél-Brazília| 191.232.18.181|
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
| Azure Public| USA 2. keleti – EUAP| 52.253.229.253|
| Azure Public| USA középső – EUAP| 52.253.159.160|
| Azure Public| USA déli középső régiója| 20.188.77.119|
| Azure Public| USA 2. keleti régiója| 20.44.72.3|
| Azure Public| Észak-Európa| 52.142.95.35|
| Azure Public| Kelet-Ázsia| 52.139.152.27|
| Azure Public| Dél-Franciaország| 20.39.80.2|
| Azure Public| Nyugat-Svájc| 51.107.96.8|
| Azure Public| Ausztrália 2. középső régiója| 20.39.99.81|
| Azure Public| UAE középső régiója| 20.37.81.41|
| Azure Public| Észak-Svájc| 51.107.0.91|
| Azure Public| Dél-Afrika nyugati régiója| 102.133.0.79|
| Azure Public| Középnyugat-Németország| 51.116.96.0|
| Azure Public| Észak-Németország| 51.116.0.0|
| Azure Public| Kelet-Norvégia| 51.120.2.185|
| Azure Public| Norvégia nyugati régiója| 51.120.130.134|
| Azure China 21Vianet| Észak-Kína (globális)| 139.217.51.16|
| Azure China 21Vianet| Kelet-Kína (globális)| 139.217.171.176|
| Azure China 21Vianet| Észak-Kína| 40.125.137.220|
| Azure China 21Vianet| Kelet-Kína| 40.126.120.30|
| Azure China 21Vianet| Észak-Kína 2| 40.73.41.178|
| Azure China 21Vianet| Kelet-Kína 2| 40.73.104.4|
| Azure Government| USA Korm. Virginia (globális)| 52.127.42.160|
| Azure Government| USA Korm. Texas (globális)| 52.127.34.192|
| Azure Government| USGov Virginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| USGov Arizona| 52.244.32.39|
| Azure Government| USGov Texas| 52.243.154.118|
| Azure Government| USA középső régiója| 52.182.32.132|
| Azure Government| USA keleti régiója| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>Kapcsolódó tartalom
* [Virtual Network csatlakoztatása a háttérben a VPN Gateway használatával](../vpn-gateway/design.md#s2smulti)
* [Virtual Network csatlakoztatása különböző üzembe helyezési modellből](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Az API Inspector használata a hívások nyomkövetéséhez az Azure-ban API Management](api-management-howto-api-inspector.md)
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
