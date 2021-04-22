---
title: Csatlakozás Azure-beli virtuális hálózatokhoz ISE-val
description: Integrációs szolgáltatási környezet (ISE) létrehozása, amely az Azure-beli virtuális hálózatokhoz (VNET-ek) fér hozzá a Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: bfef9f2b5420ac9377cc369d7bf9a9bdac76743b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874224"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Csatlakozás Azure virtuális hálózatokhoz az Azure Logic Appsből integrációs szolgáltatási környezet (ISE) használatával

Olyan forgatókönyvek esetén, ahol a logikai alkalmazásoknak és az integrációs fiókoknak hozzá kell férni egy [Azure-beli](../virtual-network/virtual-networks-overview.md)virtuális hálózathoz, hozzon létre egy integrációs szolgáltatási környezetet [  (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) Az ISE egy dedikált környezet, amely dedikált tárterületet és egyéb, a „globális”, több-bérlős Logic Apps-szolgáltatástól elkülönített erőforrásokat használ. Ez az elkülönítés csökkenti az egyéb Azure-bérlőknek az alkalmazások teljesítményére gyakorolt esetleges hatását is. Egy ISE saját statikus IP-címeket is biztosít. Ezek az IP-címek elkülönülnek a nyilvános, több-bérlős szolgáltatás logikai alkalmazásai által megosztott statikus IP-címektől.

ISE létrehozásakor az *Azure* az ISE-t az Azure-beli virtuális hálózatba injektálja, amely aztán üzembe Logic Apps a virtuális hálózaton. Logikai alkalmazás vagy integrációs fiók létrehozásakor válassza ki az ISE-t helyként. A logikai alkalmazás vagy integrációs fiók ezután közvetlenül hozzáférhet a virtuális hálózatban lévő erőforrásokhoz, például virtuális gépekhez, kiszolgálókhoz, rendszerekhez és szolgáltatásokhoz.

![Integrációs szolgáltatási környezet kiválasztása](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Ahhoz, hogy a logikai alkalmazások és az integrációs fiókok együtt dolgoznak egy ISE-ban, mindkettőnek ugyanazt az *ISE-t kell használnia,* mint a helye.

Az ISE megnövelte a futtatás időtartamát, a tárterület-megőrzést, az átviteli sebességet, a HTTP-kérések és -válaszok időtúllépését, az üzenetméreteket és az egyéni összekötők kérését. További információ: [A](../logic-apps/logic-apps-limits-and-config.md)Azure Logic Apps. További információ az ISE-kről: Hozzáférés az [Azure Virtual Network-erőforrásokhoz a Azure Logic Apps.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

Ez a cikk bemutatja, hogyan végrehajtásához használja ezeket a feladatokat a Azure Portal:

* Engedélyezze a hozzáférést az ISE számára.
* Hozza létre az ISE-t.
* Adjon hozzá további kapacitást az ISE-hez.

IsE-t a minta Azure Resource Manager [sablonnal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) vagy a Logic Apps REST API használatával is létrehozhat, beleértve a felhasználó által kezelt kulcsok beállítását:

* [Integrációs szolgáltatási környezet (ISE) létrehozása a Logic Apps REST API-val](../logic-apps/create-integration-service-environment-rest-api.md)
* [Ügyfél által kezelt kulcsok beállítása az ISE-k által tárolt adatok titkosításához](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > A logikai alkalmazások, a beépített eseményindítók, a beépített műveletek és az ISE-ben futó összekötők a használatalapú díjszabási csomagtól eltérő díjszabást használnak. Az ISE-k díjszabásának és számlázásának a következő Logic Apps [meg:](../logic-apps/logic-apps-pricing.md#fixed-pricing). A díjszabást a [díjszabást Logic Apps meg.](../logic-apps/logic-apps-pricing.md)

* [Egy azure-beli](../virtual-network/virtual-networks-overview.md) virtuális hálózat, amely *négy* üres alhálózattal rendelkezik, amelyekre az ISE-erőforrások létrehozásához és üzembe helyezéséhez van szükség, és ezeket a belső és rejtett összetevők használják:

  * Logic Apps Compute
  * Belső App Service Environment (összekötők)
  * Belső API Management (összekötők)
  * Belső Redis gyorsítótárazáshoz és teljesítményhez
  
  Az alhálózatokat előre vagy az ISE létrehozásakor is létrehozhatja, így egyidejűleg hozhatja létre az alhálózatokat. Az alhálózatok létrehozása előtt azonban tekintse át az [alhálózatra vonatkozó követelményeket.](#create-subnet)

  * Győződjön meg arról, hogy a virtuális hálózat engedélyezi a hozzáférést az ISE számára, hogy az [ISE](#enable-access) megfelelően működjön, és elérhető maradjon.

  * Ha az [ExpressRoute-et](../expressroute/expressroute-introduction.md) és [](../firewall/forced-tunneling.md)a kényszerített bújtatást is használni szeretné, létre kell hoznia egy útvonaltáblát [a](../virtual-network/manage-route-table.md) következő útvonallal, és az útvonaltáblát az ISE által használt összes alhálózathoz hozzá kell csatolni:

    **Név:**<*útvonalnév*><br>
    **Címelőtag:** 0.0.0.0/0<br>
    **Következő ugrás:** Internet
    
    Erre az adott útvonaltáblára azért van szükség, hogy Logic Apps összetevők kommunikáljanak más függő Azure-szolgáltatásokkal, például az Azure Storage-ral és a Azure SQL DB-val. További információ erről az útvonalról: [0.0.0.0/0 címelőtag.](../virtual-network/virtual-networks-udr-overview.md#default-route) Ha nem használ kényszerített bújtatást az ExpressRoute-lal, nincs szüksége erre a konkrét útvonaltáblára.
    
    Az ExpressRoute segítségével kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe, és privát kapcsolaton keresztül csatlakozhat a Microsoft-felhőszolgáltatásokhoz, amelyet a kapcsolatszolgáltató biztosít. Az ExpressRoute egy virtuális magánhálózat, amely a forgalmat nem a nyilvános interneten, hanem magánhálózaton keresztül átirányítja. A logikai alkalmazások az ExpressRoute-on vagy egy virtuális magánhálózaton keresztül kapcsolódva csatlakozhatnak az azonos virtuális hálózaton található helyszíni erőforrásokhoz.
   
  * Ha hálózati virtuális [berendezést (NVA)](../virtual-network/virtual-networks-udr-overview.md#user-defined)használ, győződjön meg arról, hogy nem engedélyezi a TLS/SSL-leépítést, vagy nem módosítja a kimenő TLS/SSL-forgalmat. Győződjön meg arról is, hogy nem engedélyezi az ISE alhálózatának adatforgalmát. További információ: Virtuális hálózati [forgalom útválasztása.](../virtual-network/virtual-networks-udr-overview.md)

  * Ha egyéni DNS-kiszolgálókat szeretne használni az Azure-beli virtuális [hálózathoz,](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) ezeket a kiszolgálókat a következő lépésekkel állíthatja be, mielőtt üzembe helyezi az ISE-t a virtuális hálózaton. További információ a DNS-kiszolgáló beállításainak kezeléséről: Virtuális hálózat [létrehozása, módosítása vagy törlése.](../virtual-network/manage-virtual-network.md#change-dns-servers)

    > [!NOTE]
    > Ha módosítja a DNS-kiszolgáló vagy a DNS-kiszolgáló beállításait, újra kell indítania az ISE-t, hogy az ISE átveheti ezeket a módosításokat. További információkért lásd: [Restart your ISE (Az ISE újraindítása).](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE)

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>ISE-hez való hozzáférés engedélyezése

Ha ISE-t használ egy Azure-beli virtuális hálózattal, a beállítással kapcsolatos gyakori probléma egy vagy több blokkolt port. Az ISE és a célrendszerek közötti kapcsolatok létrehozásához használt összekötők saját portkövetelményekkel is lehetnek. Ha például az FTP-összekötővel kommunikál egy FTP-rendszerrel, az FTP-rendszeren használt portnak elérhetőnek kell lennie, például a 21-es portnak a parancsok küldése érdekében.

Annak érdekében, hogy az ISE elérhető legyen, és hogy az ISE logikai alkalmazásai kommunikáljanak a virtuális hálózat egyes alhálózataival, nyissa meg a táblázatban leírt portokat minden [alhálózathoz.](#network-ports-for-ise) Ha a szükséges portok nem érhetők el, az ISE nem fog megfelelően működni.

* Ha több ISE-példánya van, amelyek ip-korlátozásokkal rendelkező más végpontokhoz is hozzá kell férni, helyezzen üzembe egy [Azure Firewall-t](../firewall/overview.md) vagy egy hálózati virtuális berendezést [a](../virtual-network/virtual-networks-overview.md#filter-network-traffic) virtuális hálózatban, és iránytesse át a kimenő forgalmat a tűzfalon vagy a hálózati virtuális berendezésen keresztül. Ezután [egyetlen, kimenő, nyilvános,](connect-virtual-network-vnet-set-up-single-ip-address.md) statikus és kiszámítható IP-címet állíthat be, amely a virtuális hálózat összes ISE-példánya számára használható a célrendszerekkel való kommunikációhoz. Így nem kell külön tűzfal-megnyitásokat beállítania az egyes ISE-hez a célrendszereken.

   > [!NOTE]
   > Ezt a módszert akkor használhatja egyetlen ISE-hez, ha a forgatókönyvben korlátozni kell a hozzáférést igénylő IP-címek számát. Gondolja át, hogy a tűzfal vagy a virtuális hálózati berendezés többletköltsége megfelelő-e az adott forgatókönyvhöz. További információ a [Azure Firewall díjszabásról.](https://azure.microsoft.com/pricing/details/azure-firewall/)

* Ha korlátozások nélkül hozott létre új Azure-beli virtuális hálózatot és alhálózatokat, nem kell hálózati biztonsági csoportokat [(NSG-ket)](../virtual-network/network-security-groups-overview.md#network-security-groups) beállítania a virtuális hálózatban az alhálózatok közötti forgalom szabályozásához.

* Meglévő virtuális hálózat esetén  igény szerint hálózati biztonsági csoportokat [(NSG-ket)](../virtual-network/network-security-groups-overview.md#network-security-groups) is beállíthatja az alhálózatok közötti hálózati [forgalom szűréséhez.](../virtual-network/tutorial-filter-network-traffic.md) Ha ezt az útvonalat szeretné használni, vagy ha már használ NSG-ket, győződjön meg arról, hogy a táblázatban leírt portokat nyitotta meg ezekhez az NSG-khez. [](#network-ports-for-ise)

  Az [NSG](../virtual-network/network-security-groups-overview.md#security-rules)biztonsági szabályainak beállításakor  a **TCP** és az **UDP** protokollt is használnia kell, vagy választhatja a Bármely lehetőséget is, hogy ne külön szabályokat hozzon létre az egyes protokollokhoz.  Az NSG biztonsági szabályai azokat a portokat írják le, amelyek nyitva vannak az ip-címekhez, amelyekhez hozzá kell férni ezekhez a portokhoz. Győződjön meg arról, hogy a végpontok között meglévő tűzfalak, útválasztók és egyéb elemek is elérhetők maradnak ezekhez az IP-címekhez.

* Ha kényszerített bújtatást konfigurált a tűzfalon az internetes forgalom átirányítása érdekében, tekintse át a kényszerített [bújtatás követelményeit.](#forced-tunneling)

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Az ISE által használt hálózati portok

Ez a táblázat azokat a portokat ismerteti, amelyekhez az ISE-nek elérhetőnek kell lennie, valamint a portok célját. A biztonsági szabályok beállításakor a táblázat olyan [](../virtual-network/service-tags-overview.md) szolgáltatáscímkéket használ, amelyek egy adott Azure-szolgáltatás IP-címelőtag-csoportjait képviselik. Ahol feljegyezzük, *a belső ISE* és *a külső ISE* az ISE létrehozása során kiválasztott hozzáférési [végpontra vonatkozik.](connect-virtual-network-vnet-isolated-environment.md#create-environment) További információ: [Végpont-hozzáférés.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)

> [!IMPORTANT]
> Győződjön meg arról, hogy minden szabálynál a forrásportok beállítását adja meg, mert a `*` forrásportok rendszertelenek.

#### <a name="inbound-security-rules"></a>Bejövő biztonsági szabály

| Cél | Forrás szolgáltatáscímke vagy IP-címek | Forrásportok | Cél szolgáltatáscímke vagy IP-címek | Célportok | Jegyzetek |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Alhálózatok közötti kommunikáció a virtuális hálózaton belül | A virtuális hálózat címterülete ISE-alhálózatokkal | * | A virtuális hálózat címterülete ISE-alhálózatokkal | * | A virtuális hálózat  alhálózatai közötti forgalom áramlásához szükséges. <p><p>**Fontos:** Ahhoz, hogy  a forgalom az egyes alhálózatok összetevői között áramlikjon, győződjön meg arról, hogy minden alhálózaton belül az összes portot megnyitotta. |
| Mind: <p>Kommunikáció a logikai alkalmazással <p><p>Futtatáselőzmények a logikai alkalmazáshoz| Belső ISE: <br>**VirtualNetwork** <p><p>Külső ISE: **Internet vagy** lásd a **megjegyzéseket** | * | **VirtualNetwork** | 443 | Az Internet service **(Internetes** szolgáltatás) címke használata helyett megadhatja a forrás IP-címét az alábbi elemekhez: <p><p>– Az a számítógép vagy szolgáltatás, amely a logikai alkalmazásban bármilyen kérés-eseményindítót vagy webhookot hív meg <p>– Az a számítógép vagy szolgáltatás, amelyről hozzá szeretne férni a logikai alkalmazás futtatás előzményeihez <p><p>**Fontos:** A port bezárása vagy blokkolása megakadályozza a kérés-eseményindítókat vagy webhookokat kiváltó logikai alkalmazások hívásait. A futtatáselőzmények egyes lépéseinél a bemenetek és kimenetek elérésének megakadályozása is meg van akadályozva. Azonban nem akadályozza meg, hogy hozzáférjen a logikai alkalmazás futtatás előzményeihez.|
| Logic Apps tervező – dinamikus tulajdonságok | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | A kérések a Logic Apps hozzáférési végpont bejövő [IP-címeit](../logic-apps/logic-apps-limits-and-config.md#inbound) az adott régióhoz. <p><p>**Fontos:** Ha a felhőben Azure Government, a **LogicAppsManagement** szolgáltatáscímkéje nem fog működni. Ehelyett meg kell adnia a Logic Apps [IP-címeket](../logic-apps/logic-apps-limits-and-config.md#azure-government-inbound) a Azure Government. |
| Hálózati állapot ellenőrzése | **LogicApps** | * | **VirtualNetwork** | 454 | A kérések a Logic Apps a hozzáférési [](../logic-apps/logic-apps-limits-and-config.md#inbound) végpont bejövő IP-címeit és az adott régió kimenő IP-címeit. [](../logic-apps/logic-apps-limits-and-config.md#outbound) <p><p>**Fontos:** Ha a felhőben Azure Government, a **LogicApps** szolgáltatáscímkéje nem fog működni. Ehelyett meg kell adnia a bejövő IP-Logic Apps és a kimenő [IP-címeket](../logic-apps/logic-apps-limits-and-config.md#azure-government-inbound) is a Azure Government. [](../logic-apps/logic-apps-limits-and-config.md#azure-government-outbound) |
| Összekötő üzembe helyezése | **AzureConnectors** | * | **VirtualNetwork** | 454 | Az összekötők telepítéséhez és frissítéséhez szükséges. A port bezárásával vagy blokkolásával az ISE-telepítések meghiúsulnak, és megakadályozzák az összekötők frissítéseit és javításokat. <p><p>**Fontos:** Ha felhőalapú Azure Government dolgozik, az **AzureConnectors** szolgáltatáscímkéje nem fog működni. Ehelyett meg kell adnia a felügyelt összekötő [kimenő IP-címeit](../logic-apps/logic-apps-limits-and-config.md#azure-government-outbound) a Azure Government. |
| App Service Management függőség | **AppServiceManagement** | * | **VirtualNetwork** | 454, 455 ||
| Kommunikáció a Azure Traffic Manager | **AzureTrafficManager** | * | **VirtualNetwork** | Belső ISE: 454 <p><p>Külső ISE: 443 ||
| Mind: <p>Összekötő-szabályzat üzembe helyezése <p>API Management – felügyeleti végpont | **APIManagement** | * | **VirtualNetwork** | 3443 | Az összekötő-szabályzat üzembe helyezéséhez portelérés szükséges az összekötők telepítéséhez és frissítéséhez. Ha bezárja vagy blokkolja ezt a portot, az ISE-telepítések meghiúsulnak, és megakadályozza az összekötő frissítéseit és javításokat. |
| Hozzáférés Azure Cache for Redis-példányokhoz szerepkörpéldányok között | **VirtualNetwork** | * | **VirtualNetwork** | 6379 – 6383, plusz lásd: **Megjegyzések**| Ahhoz, hogy az ISE működjön a Azure Cache for Redis, meg kell nyitnia a gyakori kérdések által leírt kimenő [és Azure Cache for Redis portokat.](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements) |
|||||||

#### <a name="outbound-security-rules"></a>Kimenő biztonsági szabályok

| Cél | Forrás szolgáltatáscímke vagy IP-címek | Forrásportok | Cél szolgáltatáscímke vagy IP-címek | Célportok | Jegyzetek |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Alhálózatok közötti kommunikáció a virtuális hálózaton belül | A virtuális hálózat címterülete ISE-alhálózatokkal | * | A virtuális hálózat címterülete ISE-alhálózatokkal | * | A virtuális hálózat  alhálózatai közötti forgalom áramlásához szükséges. <p><p>**Fontos:** Ahhoz, hogy  a forgalom az egyes alhálózatok összetevői között áramlikjon, győződjön meg arról, hogy minden alhálózaton belül az összes portot megnyitotta. |
| Kommunikáció a logikai alkalmazásból | **VirtualNetwork** | * | A céltól függően változik | A céltól függően változik | A célportok azon külső szolgáltatások végpontjaitól függően változnak, amellyel a logikai alkalmazásnak kommunikálnia kell. <p><p>A célport például egy webszolgáltatáshoz a 443-as, az SMTP-szolgáltatás 25-ös portja, egy SFTP-szolgáltatás 22-es portja stb. |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80, 443 ||
| Azure Storage-függőség | **VirtualNetwork** | * | **Storage** | 80, 443, 445 ||
| Kapcsolatkezelés | **VirtualNetwork** | * | **AppService** | 443 ||
| Diagnosztikai naplók közzététele & metrikákhoz | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Azure SQL függőség | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure Resource Health | **VirtualNetwork** | * | **AzureMonitor** | 1886 | Az állapot közzétételéhez szükséges a Resource Health. |
| Függőség a naplóból az eseményközpontba szabályzat és a monitorozási ügynök között | **VirtualNetwork** | * | **EventHub** | 5672 ||
| Hozzáférés Azure Cache for Redis-példányokhoz szerepkörpéldányok között | **VirtualNetwork** | * | **VirtualNetwork** | 6379 – 6383, plusz lásd: **Megjegyzések**| Ahhoz, hogy az ISE működjön a Azure Cache for Redis, meg kell nyitnia a gyakori kérdések által leírt kimenő [és Azure Cache for Redis portokat.](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements) |
| DNS-névfeloldás | **VirtualNetwork** | * | IP-címek a virtuális hálózat bármely egyéni DNS-kiszolgálója számára | 53 | Csak akkor szükséges, ha egyéni DNS-kiszolgálókat használ a virtuális hálózaton |
|||||||

Emellett kimenő szabályokat kell hozzáadnia a App Service Environment [(ASE) számára:](../app-service/environment/intro.md)

* Ha az Azure Firewall-t használja, a tűzfalat az App Service Environment (ASE) teljes tartománynév [(FQDN)](../firewall/fqdn-tags.md#current-fqdn-tags)címkével kell beállítania, amely engedélyezi a kimenő hozzáférést az ASE-platform forgalmához.

* Ha nem tűzfalberendezést használ Azure Firewall, akkor a tűzfalat  a tűzfal integrációs [](../app-service/environment/firewall-integration.md#dependencies) függőségei között felsorolt összes olyan sszabályokkal kell beállítania, amelyek a App Service Environment.

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>Kényszerített bújtatás követelményei

Ha kényszerített bújtatást konfigurál vagy használ a tűzfalon keresztül, további külső függőségeket kell lehetővé tennie az ISE számára. [](../firewall/forced-tunneling.md) A kényszerített bújtatás lehetővé teszi, hogy az internetre irányuló forgalmat átirányítsa egy kijelölt következő ugrásra, például a virtuális magánhálózatra (VPN) vagy egy virtuális berendezésre az internet helyett, így megvizsgálhatja és naplólhatja a kimenő hálózati forgalmat.

Ha nem engedélyezi a hozzáférést ezekhez a függőségekhez, az ISE üzembe helyezése meghiúsul, és az üzembe helyezett ISE leáll.

* Felhasználó által megadott útvonalak

  Az aszimmetrikus útválasztás megakadályozásához meg kell határoznia egy útvonalat az alább  felsorolt minden egyes IP-címhez, következő ugrásként pedig az internetet.
  
  * [App Service Environment címek kezelése](../app-service/environment/management-addresses.md)
  * [Az ISE régióban található összekötők Azure IP-címei, amelyek ebben a letöltési fájlban érhetők el](https://www.microsoft.com/download/details.aspx?id=56519)
  * [Azure Traffic Manager címek kezelése](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)
  * [Logic Apps isE-régió bejövő és kimenő címeit](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)
  * [Az ISE régióban található összekötők Azure IP-címei, amelyek ebben a letöltési fájlban vannak](https://www.microsoft.com/download/details.aspx?id=56519)

* Szolgáltatásvégpontok

  Engedélyeznie kell a szolgáltatásvégpontokat a Azure SQL, a Storage, Service Bus, a KeyVault és a Event Hubs számára, mivel ezekhez a szolgáltatásokhoz nem küldhet forgalmat tűzfalon keresztül.

*  Egyéb bejövő és kimenő függőségek

   A tűzfalnak *engedélyeznie* kell a következő bejövő és kimenő függőségeket:
   
   * [Azure App Service függőségek](../app-service/environment/firewall-integration.md#deploying-your-ase-behind-a-firewall)
   * [Az Azure Cache Service függőségei](../azure-cache-for-redis/cache-how-to-premium-vnet.md#what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks)
   * [Az Azure API Management függőségei](../api-management/api-management-using-with-vnet.md#-common-network-configuration-issues)

<a name="create-environment"></a>

## <a name="create-your-ise"></a>ISE létrehozása

1. A [Azure Portal](https://portal.azure.com)a fő Azure-keresőmezőbe írja be szűrőként a következőt: , majd válassza az `integration service environments` **Integrációs szolgáltatási környezetek lehetőséget.**

   ![Keresse meg és válassza az "Integrációs szolgáltatási környezetek" lehetőséget](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Az **Integrációs szolgáltatási környezetek panelen** válassza a Hozzáadás **lehetőséget.**

   ![Az integrációs szolgáltatási környezet létrehozásához válassza a "Hozzáadás" lehetőséget](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Adja meg a környezet adatait, majd válassza az **Áttekintés + létrehozás** lehetőséget, például:

   ![A környezet részleteinek megszabadása](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Előfizetés** | Yes | <*Azure-előfizetés neve*> | A környezethez használt Azure-előfizetés |
   | **Erőforráscsoport** | Yes | <*Azure-erőforráscsoport neve*> | Egy új vagy meglévő Azure-erőforráscsoport, amelyben létre szeretné hozni a környezetét |
   | **Integrációs szolgáltatás környezetének neve** | Yes | <*környezet neve*> | Az ISE neve, amely csak betűket, számokat, kötőjeleket ( ), aláhúzásjeleket `-` ( `_` ), és pontokat ( ) `.` tartalmazhat. |
   | **Hely** | Yes | <*Azure-adatközpont régiója*> | Az Azure-adatközpont régiója, ahol üzembe helyezheti a környezetet |
   | **Termékváltozat** | Yes | **Prémium** vagy **Fejlesztő (SLA nélkül)** | A létrehozni és használni szükséges ISE termékváltozat. A fenti SKUS-k közötti különbségekért lásd: [ISE SKUs](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Fontos:** Ez a beállítás csak az ISE létrehozásakor érhető el, és később nem módosítható. |
   | **További kapacitás** | Prémium: <br>Yes <p><p>Fejlesztő: <br>Nem alkalmazható | Prémium: <br>0 és 10 között <p><p>Fejlesztő: <br>Nem alkalmazható | Az ISE-erőforráshoz használni szükséges további feldolgozóegységek száma. Kapacitás hozzáadásához a létrehozás után lásd: [ISE-kapacitás hozzáadása.](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) |
   | **Hozzáférési végpont** | Yes | **Belső** vagy **külső** | Az ISE-hez használni szükséges hozzáférési végpontok típusa. Ezek a végpontok határozzák meg, hogy az ISE logikai alkalmazásában a kérelem- vagy webhook-eseményindítók fogadhatnak-e hívásokat a virtuális hálózaton kívülről. <p><p>Ha például a következő webhook-alapú eseményindítókat szeretné használni, győződjön meg arról, hogy a Külső lehetőséget **választja:** <p><p>- Azure DevOps <br>– Azure Event Grid <br>- Common Data Service <br>- Office 365 <br>- SAP (ISE-verzió) <p><p>A kijelölés azt is befolyásolja, hogyan lehet megtekinteni és elérni a logikai alkalmazás futtatáselőzményeibe és kimeneteibe és kimeneteibe. További információ: [ISE-végpont elérése.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access) <p><p>**Fontos:** A hozzáférési végpontot csak az ISE létrehozása során választhatja ki, és ezt a beállítást később nem módosíthatja. |
   | **Virtuális hálózat** | Yes | <*Azure-virtual-network-name*> | Az Azure-beli virtuális hálózat, ahová be szeretné injektálni a környezetet, hogy az abban a környezetben lévő logikai alkalmazások hozzáférjenek a virtuális hálózathoz. Ha nincs hálózata, először hozzon létre [egy Azure-beli virtuális hálózatot.](../virtual-network/quick-create-portal.md) <p><p>**Fontos:** Ezt az injektálást csak az ISE létrehozásakor hajthatja végre.  |
   | **Alhálózatok** | Yes | <*subnet-resource-list*> | Az ISE  négy üres alhálózatot igényel, amelyekre szükség van az erőforrások létrehozásához és üzembe helyezéséhez az ISE-ben, és amelyeket belső Logic Apps-összetevők, például összekötők és a teljesítmény gyorsítótárazása használnak. <p>**Fontos:** Ellenőrizze, hogy áttekinti-e az alhálózatra vonatkozó követelményeket, mielőtt továbblépne a következő lépésekre [az alhálózatok létrehozásához.](#create-subnet) |
   |||||

   <a name="create-subnet"></a>

   **Alhálózatok létrehozása**

   Az ISE  négy üres alhálózatot igényel, amelyekre szükség van az erőforrások létrehozásához és üzembe helyezéséhez az ISE-ben, és amelyeket belső Logic Apps-összetevők, például összekötők és a teljesítmény gyorsítótárazása használnak. Ezeket *az alhálózati* címeket a környezet létrehozása után nem módosíthatja. Ha az ISE-t az Azure Portal használatával hozza létre és telepíti, győződjön meg arról, hogy ezeket az alhálózatokat nem delegálta semmilyen Azure-szolgáltatásba. Ha azonban az ISE-t REST API, Azure PowerShell vagy Azure Resource Manager-sablonnal hozza létre és telepíti, [](../virtual-network/manage-subnet-delegation.md) akkor egy üres alhálózatot kell delegálnia a `Microsoft.integrationServiceEnvironment` számára. További információ: [Alhálózat-delegálás hozzáadása.](../virtual-network/manage-subnet-delegation.md)

   Minden alhálózatnak meg kell felelnie a következő követelményeknek:

   * Olyan nevet használ, amely betűvel vagy aláhúzásjelkel (nincs szám) kezdődik, és nem használja a következő karaktereket: `<` , , , , , , `>` `%` `&` `\\` `?` `/` .

   * A Osztály nélküli [útválasztás Inter-Domain (CIDR) formátumot használja.](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)
   
     > [!IMPORTANT]
     >
     > Ne használja a következő IP-címtereket a virtuális hálózathoz vagy az alhálózathoz, mert azok nem feloldhatók a Azure Logic Apps:<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * A `/27` címtérben használ egy et, mert minden alhálózathoz 32 cím szükséges. A például 32 címmel rendelkezik, mert `10.0.0.0/27` a 2<sup>(32-27)</sup> 2<sup>5</sup> vagy 32. További címek nem biztosítanak további előnyöket. A címek kiszámításával kapcsolatos további információkért lásd: [IPv4 CIDR-blokkok.](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)

   * Az [ExpressRoute](../expressroute/expressroute-introduction.md)használata esetén létre kell hoznia egy útvonaltáblát, amely [a](../virtual-network/manage-route-table.md) következő útvonallal rendelkezik, és összekapcsolja a táblázatot az ISE által használt alhálózatokkal:

     **Név:**<*útvonalnév*><br>
     **Címelőtag:** 0.0.0.0/0<br>
     **Következő ugrás:** Internet

   1. Az **Alhálózatok listában** válassza az **Alhálózat konfigurációjának kezelése lehetőséget.**

      ![Alhálózat konfigurációjának kezelése](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. Az **Alhálózatok panelen** válassza az **Alhálózat lehetőséget.**

      ![Négy üres alhálózat hozzáadása](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. Az **Alhálózat hozzáadása panelen** adja meg ezt az információt.

      * **Név:** Az alhálózat neve
      * **Címtartomány (CIDR-blokk):** Az alhálózat tartománya a virtuális hálózatban és CIDR formátumban

      ![Alhálózat részleteinek hozzáadása](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Amikor elkészült, válassza az **OK** lehetőséget.

   1. Ismételje meg ezeket a lépéseket három további alhálózaton is.

      > [!NOTE]
      > Ha a létrehozni próbáló alhálózatok érvénytelenek, a Azure Portal üzenetet fog látni, de nem blokkolja a folyamat előrehaladását.

   További információ az alhálózatok létrehozásáról: [Virtuális hálózat alhálózatának hozzáadása.](../virtual-network/virtual-network-manage-subnet.md)

1. Miután az Azure sikeresen érvényesíti az ISE-adatokat, válassza a **Létrehozás** lehetőséget, például:

   ![A sikeres ellenőrzés után válassza a "Létrehozás" lehetőséget](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Az Azure megkezdi a környezet üzembe helyezését, amely általában két órán belül befejeződik. Az üzembe helyezés esetenként négy órát is igénybe vehet. Az üzembe helyezés állapotának ellenőrzéséhez az Azure eszköztáron válassza az értesítések ikont, amely megnyitja az Értesítések panelt.

   ![Az üzembe helyezés állapotának ellenőrzése](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Ha az üzembe helyezés sikeresen befejeződött, az Azure a következő értesítést jeleníti meg:

   ![Az üzembe helyezés sikeres](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Ellenkező esetben kövesse az Azure Portal hibaelhárítási útmutatóját.

   > [!NOTE]
   > Ha az üzembe helyezés sikertelen, vagy törli az ISE-t, az Alhálózatok felszabadítása előtt az Azure akár egy órát vagy akár tovább is tarthat. Ezért előfordulhat, hogy várnia kell, mielőtt újra felhasználhatja ezeket az alhálózatokat egy másik ISE-ban.
   >
   > Ha törli a virtuális hálózatot, az Azure általában két órát is igénybe vesz az alhálózatok felszabadítása előtt, de ez a művelet tovább is tarthat. 
   > Virtuális hálózatok törlésekor győződjön meg arról, hogy még nincsenek csatlakoztatva erőforrások. 
   > Lásd: [Virtuális hálózat törlése.](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)

1. A környezet megtekintéséhez válassza az **Erőforrás megtekintése** lehetőséget, ha az Azure nem indul el automatikusan a környezetbe az üzembe helyezés befejezése után.

1. Külső végponti hozzáféréssel ellátott ISE esetén létre kell hoznia egy hálózati biztonsági csoportot, ha még nem rendelkezik ilyensel, és hozzá kell adni egy bejövő biztonsági szabályt, amely engedélyezi a felügyelt összekötő kimenő IP-címeinek forgalmát.  A szabály beállítását az alábbi lépésekkel állíthatja be:

   1. Az ISE menü Beállítások **menüjében válassza** a Tulajdonságok **lehetőséget.**

   1. Az **Összekötő kimenő IP-címei** alatt másolja ki a nyilvános IP-címtartományokat, amelyek szintén megjelennek ebben a cikkben: Korlátok és konfiguráció [– Kimenő IP-címek.](../logic-apps/logic-apps-limits-and-config.md#outbound)

   1. Ha még nem rendelkezik hálózati biztonsági csoporttal, hozzon létre egyet.
   
   1. Az alábbi információk alapján adjon hozzá egy bejövő biztonsági szabályt a másolt nyilvános kimenő IP-címekhez. További információ: [Oktatóanyag: Hálózati](../virtual-network/tutorial-filter-network-traffic.md#create-a-network-security-group)forgalom szűrése hálózati biztonsági csoportokkal a Azure Portal.

      | Cél | Forrás szolgáltatáscímke vagy IP-címek | Forrásportok | Cél szolgáltatáscímke vagy IP-címek | Célportok | Jegyzetek |
      |---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
      | Az összekötő kimenő IP-címeinek forgalmának engedélye | <*connector-public-outbound-IP-addresses*> | * | A virtuális hálózat címterülete ISE-alhálózatokkal | * | |
      |||||||

1. Az ISE hálózati állapotának ellenőrzéséhez lásd: [Az integrációs szolgáltatási környezet kezelése.](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

   > [!CAUTION]
   > Ha az ISE hálózata nem megfelelő, az ISE által használt belső App Service Environment (ASE) szintén nem megfelelő lesz. Ha az ASE hét napnál tovább nem megfelelő, az ASE fel lesz függesztve. Az állapot megoldásához ellenőrizze a virtuális hálózat beállítását. Hárítsa el az esetlegesen talált problémákat, majd indítsa újra az ISE-t. Ellenkező esetben 90 nap után a felfüggesztett ASE törlődik, és az ISE használhatatlanná válik. Ezért győződjön meg arról, hogy az ISE kifogástalan állapotú, hogy lehetővé tegye a szükséges forgalmat.
   > 
   > További információt az alábbi témakörökben talál:
   >
   > * [Azure App Service diagnosztikai áttekintés](../app-service/overview-diagnostics.md)
   > * [Üzenetnaplózás Azure App Service Environment](../app-service/environment/using-an-ase.md#logging)

1. A logikai alkalmazások és más összetevők ISE-beli létrehozásához lásd: Erőforrások hozzáadása integrációs [szolgáltatási környezetekhez.](../logic-apps/add-artifacts-integration-service-environment-ise.md)

   > [!IMPORTANT]
   > Az ISE létrehozása után a felügyelt ISE-összekötők elérhetővé válnak, de nem jelennek meg automatikusan az összekötőválasztóban a Logikaialkalmazás-tervezőben. Az ISE-összekötők használata előtt manuálisan kell hozzáadnia és üzembe helyeznie ezeket az [összekötőket](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) az ISE-hez, hogy azok megjelenjenek a Logikaialkalmazás-tervezőben.

## <a name="next-steps"></a>Következő lépések

* [Erőforrás hozzáadása integrációs szolgáltatási környezetekhez](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Integrációs szolgáltatási környezetek kezelése](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* További információ az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Tudnivalók az [Azure-szolgáltatások virtuális hálózati integrációjáról](../virtual-network/virtual-network-for-azure-services.md)
