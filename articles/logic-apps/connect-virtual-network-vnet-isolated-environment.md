---
title: Kapcsolódás Azure-beli virtuális hálózatokhoz ISE-vel
description: Hozzon létre egy integrációs szolgáltatási környezetet (ISE), amely hozzáférhet az Azure Virtual Networks (virtuális hálózatok) szolgáltatáshoz Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 03/30/2021
ms.openlocfilehash: a56a41b704b12da08cf86b450ac1c734409c8032
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219314"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Csatlakozás Azure-beli virtuális hálózatokhoz Azure Logic Appsból integrációs szolgáltatási környezet (ISE) használatával

Olyan esetekben, amikor a Logic apps és az integrációs fiókoknak hozzáférésre van szüksége egy Azure-beli [virtuális hálózathoz](../virtual-network/virtual-networks-overview.md), hozzon létre egy [ *integrációs szolgáltatási környezetet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Az ISE egy dedikált környezet, amely dedikált tárterületet és egyéb, a „globális”, több-bérlős Logic Apps-szolgáltatástól elkülönített erőforrásokat használ. Ez a elkülönítés azt is csökkenti, hogy más Azure-bérlők milyen hatással lehetnek az alkalmazások teljesítményére. Egy ISE saját statikus IP-címeket is biztosít. Ezek az IP-címek a nyilvános, több-bérlős szolgáltatásban a logikai alkalmazások által megosztott statikus IP-címektől eltérnek.

Ha ISE-t hoz létre, az Azure *befecskendezi* az ISE-t az Azure-beli virtuális hálózatba, amely ezután telepíti a Logic Apps szolgáltatást a virtuális hálózatba. Logikai alkalmazás vagy integrációs fiók létrehozásakor válassza ki az ISE helyét. A logikai alkalmazás vagy integrációs fiók ezután közvetlenül hozzáférhet az erőforrásokhoz, például a virtuális gépekhez, a kiszolgálókhoz, a rendszerekhez és a szolgáltatásokhoz a virtuális hálózaton.

![Integrációs szolgáltatási környezet kiválasztása](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Ahhoz, hogy a Logic apps és az integrációs fiókok együtt működjenek az ISE-ben, mindkettőnek *ugyanazt az ISE* -t kell használnia, mint a helyük.

Az ISE megnövelte a futtatási időtartamot, a tárterület megőrzését, az átviteli sebességet, a HTTP-kérést és a válasz időtúllépését, az üzenetek méretét és az egyéni összekötői kérelmeket. További információ: [Azure Logic apps korlátai és konfigurálása](../logic-apps/logic-apps-limits-and-config.md). További információ a ISEs: [Azure Virtual Network-erőforrások elérése Azure Logic Appsból](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Ez a cikk bemutatja, hogyan hajthatja végre ezeket a feladatokat a Azure Portal használatával:

* Engedélyezze az ISE hozzáférését.
* Hozza létre az ISE-t.
* Extra kapacitás hozzáadása az ISE-hez.

Létrehozhat egy ISE-t is a [minta Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) a gyors üzembe helyezési sablonnal vagy a Logic apps REST API használatával, beleértve az ügyfél által felügyelt kulcsok beállítását:

* [Integrációs szolgáltatási környezet (ISE) létrehozása a Logic Apps REST API-val](../logic-apps/create-integration-service-environment-rest-api.md)
* [Ügyfél által felügyelt kulcsok beállítása a ISEs tárolt adatok titkosításához](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > A Logic apps, a beépített triggerek, a beépített műveletek és az ISE-ben futó összekötők a fogyasztáson alapuló díjszabási csomagtól eltérő díjszabási csomagot használnak. A ISEs díjszabásának és számlázásának megismeréséhez tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md#fixed-pricing). A díjszabással kapcsolatban lásd: [Logic apps díjszabása](../logic-apps/logic-apps-pricing.md).

* Olyan [Azure-beli virtuális hálózat](../virtual-network/virtual-networks-overview.md) , amely négy *üres* alhálózattal rendelkezik, amelyek szükségesek az ISE-beli erőforrások létrehozásához és üzembe helyezéséhez, és ezeket a belső és rejtett összetevők használják:

  * Logic Apps számítás
  * Belső App Service Environment (összekötők)
  * Belső API Management (összekötők)
  * Belső Redis gyorsítótárazáshoz és teljesítményhez
  
  Az alhálózatokat előre vagy az ISE létrehozásakor is létrehozhatja, hogy egyszerre lehessen létrehozni az alhálózatokat. Az alhálózatok létrehozása előtt azonban ellenőrizze, hogy megtekinti-e az alhálózatra [vonatkozó követelményeket](#create-subnet).

  * Győződjön meg arról, hogy a virtuális hálózat [lehetővé teszi az ISE hozzáférését](#enable-access) , hogy az ISE megfelelően működjön, és elérhető maradjon.

  * Ha a használatával vagy a [kényszerített bújtatással](../firewall/forced-tunneling.md)együtt kívánja használni a [ExpressRoute](../expressroute/expressroute-introduction.md) -t, akkor [létre kell hoznia egy útválasztási táblázatot](../virtual-network/manage-route-table.md) a következő adott útvonalon, és össze kell kapcsolnia az útválasztási táblázatot az ISE által használt összes alhálózathoz:

    **Név**: <*útvonal neve*><br>
    **Címzési előtag**: 0.0.0.0/0<br>
    **Következő ugrás**: Internet
    
    Erre az útválasztási táblázatra azért van szükség, hogy Logic Apps-összetevők kommunikáljanak más függő Azure-szolgáltatásokkal, például az Azure Storage és az Azure SQL DB használatával. További információ erről az útvonalról: [0.0.0.0/0 címek előtagja](../virtual-network/virtual-networks-udr-overview.md#default-route). Ha nem kényszerített bújtatást használ a ExpressRoute, nincs szüksége erre az útválasztási táblázatra.
    
    A ExpressRoute segítségével kiterjesztheti a helyszíni hálózatait a Microsoft-felhőbe, és csatlakozhat a Microsoft Cloud Serviceshez egy olyan privát kapcsolaton keresztül, amelyet a kapcsolódási szolgáltató megkönnyít. A ExpressRoute olyan virtuális magánhálózat, amely nem a nyilvános interneten keresztül irányítja át a forgalmat egy privát hálózaton keresztül. A logikai alkalmazások olyan helyszíni erőforrásokhoz kapcsolódhatnak, amelyek ugyanabban a virtuális hálózatban találhatók, amikor ExpressRoute vagy virtuális magánhálózaton keresztül csatlakoznak.
   
  * Ha [hálózati virtuális berendezést (NVA)](../virtual-network/virtual-networks-udr-overview.md#user-defined)használ, győződjön meg arról, hogy nem engedélyezi a TLS/SSL-megszakítást, vagy módosítsa a kimenő TLS/SSL-forgalmat. Ügyeljen arra is, hogy ne engedélyezze az ISE alhálózatáról származó forgalom vizsgálatát. További információ: [virtuális hálózati forgalom útválasztása](../virtual-network/virtual-networks-udr-overview.md).

  * Ha egyéni DNS-kiszolgálókat szeretne használni az Azure-beli virtuális hálózathoz, [ezeket a lépéseket követve állítsa be ezeket a kiszolgálókat](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) , mielőtt TELEPÍTENÉ az ISE-t a virtuális hálózatra. A DNS-kiszolgáló beállításainak kezelésével kapcsolatos további információkért lásd: [virtuális hálózat létrehozása, módosítása vagy törlése](../virtual-network/manage-virtual-network.md#change-dns-servers).

    > [!NOTE]
    > Ha módosítja a DNS-kiszolgáló vagy a DNS-kiszolgáló beállításait, újra kell indítania az ISE-t, hogy az ISE fel tudja venni a módosításokat. További információ: [az ISE újraindítása](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>ISE-hez való hozzáférés engedélyezése

Ha egy Azure-beli virtuális hálózattal rendelkező ISE-t használ, egy gyakori telepítési probléma egy vagy több letiltott porttal rendelkezik. Az ISE és a célként megadott rendszerek közötti kapcsolatok létrehozásához használt összekötők rendelkezhetnek saját portra vonatkozó követelményekkel is. Ha például az FTP-összekötővel kommunikál egy FTP-rendszerrel, akkor az FTP-rendszeren használt portnak elérhetőnek kell lennie, például a 21-es porton keresztül a parancsok küldéséhez.

Győződjön meg arról, hogy az ISE elérhető, és hogy az ISE logikai alkalmazásai kommunikálhatnak a virtuális hálózat minden alhálózatán, majd [nyissa meg az ebben a táblázatban ismertetett portokat az egyes alhálózatokhoz](#network-ports-for-ise). Ha bármelyik szükséges port nem érhető el, az ISE nem fog megfelelően működni.

* Ha több ISE-példánnyal rendelkezik, amelyek IP-korlátozásokkal rendelkező más végpontokhoz férnek hozzá, helyezzen üzembe egy [Azure Firewall](../firewall/overview.md) vagy egy [hálózati virtuális berendezést](../virtual-network/virtual-networks-overview.md#filter-network-traffic) a virtuális hálózatban, és irányítsa át a kimenő forgalmat a tűzfalon vagy a hálózati virtuális berendezésen keresztül. Ezután [beállíthat egy, a kimenő, nyilvános, statikus és kiszámítható IP-címet](connect-virtual-network-vnet-set-up-single-ip-address.md) , amelyet a virtuális hálózat összes ISE-példánya használhat a célszámítógépeken való kommunikációhoz. Így nem kell további tűzfal-megnyitásokat beállítania a célszámítógépeken az egyes ISE-rendszerek esetében.

   > [!NOTE]
   > Ezt a módszert egyetlen ISE esetében is használhatja, ha a forgatókönyv megköveteli a hozzáférést igénylő IP-címek számának korlátozását. Gondolja át, hogy a tűzfal vagy a virtuális hálózati berendezés további költségei ésszerűek-e a forgatókönyvhöz. További információ a [Azure Firewall díjszabásáról](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Ha új Azure-beli virtuális hálózatot és alhálózatokat hozott létre korlátozás nélkül, nem kell beállítania [hálózati biztonsági csoportokat (NSG)](../virtual-network/network-security-groups-overview.md#network-security-groups) a virtuális hálózatban az alhálózatok közötti adatforgalom szabályozásához.

* Egy meglévő virtuális hálózat esetében *opcionálisan* beállíthat [hálózati biztonsági csoportokat (NSG)](../virtual-network/network-security-groups-overview.md#network-security-groups) az alhálózatok [közötti hálózati forgalom szűrésére](../virtual-network/tutorial-filter-network-traffic.md). Ha ezt az útvonalat szeretné használni, vagy ha már használja a NSG-t, győződjön meg arról, hogy [megnyitotta az ebben a táblázatban ismertetett portokat](#network-ports-for-ise) a NSG.

  A [NSG biztonsági szabályainak](../virtual-network/network-security-groups-overview.md#security-rules)beállításakor a **TCP** -és **UDP** -protokollokat is használni kell, **vagy választhatja** azt, hogy nem kell külön szabályokat létrehoznia *az egyes* protokollokhoz. A NSG biztonsági szabályai azokat a portokat írják le, amelyeknek meg kell nyitni azokat az IP-címeket, amelyeknek hozzá kell férniük a portokhoz. Győződjön meg arról, hogy a végpontok között található tűzfalak, útválasztók vagy egyéb elemek is megőrzik ezeket a portokat az IP-címek számára.

* Ha a tűzfalon keresztül kényszerített bújtatást állít be az internetes forgalom átirányítására, tekintse át a [kényszerített bújtatási követelményeket](#forced-tunneling).

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Az ISE által használt hálózati portok

Ez a táblázat azokat a portokat ismerteti, amelyeknek az ISE számára elérhetőnek kell lennie, valamint a portok célját. A biztonsági szabályok beállítása során a bonyolultság csökkentése érdekében a táblázat olyan [szolgáltatási címkéket](../virtual-network/service-tags-overview.md) használ, amelyek az IP-cím előtagjainak egy adott Azure-szolgáltatáshoz tartozó csoportjait jelölik. Ahol a megjegyezte, a *belső ISE* és a *külső ISE* az [ISE létrehozásakor kiválasztott hozzáférési végpontra](connect-virtual-network-vnet-isolated-environment.md#create-environment)hivatkozik. További információ: [végponti hozzáférés](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access).

> [!IMPORTANT]
> Az összes szabály esetében ügyeljen arra, hogy a forrás portjait állítsa be, `*` mert a forrásoldali portok elmúlóak.

#### <a name="inbound-security-rules"></a>Bejövő biztonsági szabály

| Cél | Forrásként szolgáló szolgáltatás címkéje vagy IP-címe | Forrásportok | Cél szolgáltatás címkéje vagy IP-címe | Célportok | Jegyzetek |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Alhálózati kommunikáció a virtuális hálózaton belül | Az ISE-alhálózatokkal rendelkező virtuális hálózat címterület | * | Az ISE-alhálózatokkal rendelkező virtuális hálózat címterület | * | A virtuális hálózat alhálózatai *közötti* adatforgalomhoz szükséges. <p><p>**Fontos**: az egyes alhálózatokban található *összetevők* közötti adatforgalom esetén győződjön meg arról, hogy az egyes alhálózatokon belül minden portot megnyit. |
| Mind <p>Kommunikáció a logikai alkalmazással <p><p>A logikai alkalmazás előzményeinek futtatása| Belső ISE: <br>**VirtualNetwork** <p><p>Külső ISE: **Internet** vagy lásd a **megjegyzéseket** | * | **VirtualNetwork** | 443 | Az **Internet** Service címke használata helyett megadhatja az alábbi elemek forrás IP-címét: <p><p>– Az a számítógép vagy szolgáltatás, amely meghívja a logikai alkalmazásban megjelenő kérelem-eseményindítókat vagy webhookokat <p>– Az a számítógép vagy szolgáltatás, ahonnan el szeretné érni a logikai alkalmazás futtatási előzményeit <p><p>**Fontos**: a port bezárása vagy blokkolása megakadályozza a meghívást az eseményindítókkal vagy webhookokkal rendelkező logikai alkalmazásoknak. Azt is megakadályozhatja, hogy a futtatási előzményekben szereplő egyes lépéseknél hozzáférjenek a bemenetekhez és a kimenetekhez. Azonban nem akadályozza meg a logikai alkalmazás futtatási előzményeinek elérését.|
| Logic Apps Designer – dinamikus tulajdonságok | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | A kérések az adott régióhoz tartozó Logic Apps hozzáférési végpont [bejövő IP-címeiből](../logic-apps/logic-apps-limits-and-config.md#inbound) származnak. <p><p>**Fontos**: Ha Azure Government felhővel dolgozik, a **LogicAppsManagement** szolgáltatás címkéje nem fog működni. Ehelyett meg kell adnia a Azure Government Logic Apps [bejövő IP-címeit](../logic-apps/logic-apps-limits-and-config.md#azure-government-inbound) . |
| Hálózati állapot-ellenőrzési | **LogicApps** | * | **VirtualNetwork** | 454 | A kérelmek a Logic Apps hozzáférési végpont [bejövő IP-címeiből](../logic-apps/logic-apps-limits-and-config.md#inbound) és a régió [kimenő IP-címeiből](../logic-apps/logic-apps-limits-and-config.md#outbound) származnak. <p><p>**Fontos**: Ha Azure Government felhővel dolgozik, a **LogicApps** szolgáltatás címkéje nem fog működni. Ehelyett Logic Apps a [bejövő IP-címeket](../logic-apps/logic-apps-limits-and-config.md#azure-government-inbound) és a Azure Government [kimenő IP-címeit](../logic-apps/logic-apps-limits-and-config.md#azure-government-outbound) is meg kell adnia. |
| Összekötő üzembe helyezése | **AzureConnectors** | * | **VirtualNetwork** | 454 | Összekötők telepítéséhez és frissítéséhez szükséges. A port bezárása vagy blokkolása esetén az ISE-telepítések sikertelenek lesznek, és meggátolják az összekötők frissítését és javítását. <p><p>**Fontos**: Ha Azure Government felhővel dolgozik, a **AzureConnectors** szolgáltatás címkéje nem fog működni. Ehelyett meg kell adnia a [felügyelt összekötő kimenő IP-címeit](../logic-apps/logic-apps-limits-and-config.md#azure-government-outbound) a Azure Government számára. |
| App Service felügyeleti függőség | **AppServiceManagement** | * | **VirtualNetwork** | 454, 455 ||
| Kommunikáció az Azure Traffic Manager | **AzureTrafficManager** | * | **VirtualNetwork** | Belső ISE: 454 <p><p>Külső ISE: 443 ||
| Mind <p>Összekötő-házirend üzembe helyezése <p>API Management felügyeleti végpont | **APIManagement** | * | **VirtualNetwork** | 3443 | Az összekötő-házirend üzembe helyezéséhez a port hozzáférés szükséges az összekötők telepítéséhez és frissítéséhez. A port bezárása vagy blokkolása esetén az ISE-telepítések sikertelenek lesznek, és meggátolják az összekötők frissítését és javítását. |
| Azure cache elérése Redis-példányok között szerepkör-példányok között | **VirtualNetwork** | * | **VirtualNetwork** | 6379 – 6383, plusz lásd: **Megjegyzések**| Ahhoz, hogy az ISE működjön az Azure cache-sel az Redis-hez, meg kell nyitnia ezeket [a kimenő és bejövő portokat, amelyeket az Azure cache ismertet a REDIS GYIK](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)-ban. |
|||||||

#### <a name="outbound-security-rules"></a>Kimenő biztonsági szabályok

| Cél | Forrásként szolgáló szolgáltatás címkéje vagy IP-címe | Forrásportok | Cél szolgáltatás címkéje vagy IP-címe | Célportok | Jegyzetek |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Alhálózati kommunikáció a virtuális hálózaton belül | Az ISE-alhálózatokkal rendelkező virtuális hálózat címterület | * | Az ISE-alhálózatokkal rendelkező virtuális hálózat címterület | * | A virtuális hálózat alhálózatai *közötti* adatforgalomhoz szükséges. <p><p>**Fontos**: az egyes alhálózatokban található *összetevők* közötti adatforgalom esetén győződjön meg arról, hogy az egyes alhálózatokon belül minden portot megnyit. |
| Kommunikáció a logikai alkalmazásból | **VirtualNetwork** | * | A célhelytől függően változik | 80, 443 | A cél a külső szolgáltatáshoz tartozó végpontok alapján változik, amelyekkel a logikai alkalmazásnak kommunikálnia kell. |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80, 443 ||
| Azure Storage-függőség | **VirtualNetwork** | * | **Storage** | 80, 443, 445 ||
| Kapcsolatok kezelése | **VirtualNetwork** | * | **AppService** | 443 ||
| Diagnosztikai naplók közzététele & metrikák | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Azure SQL-függőség | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure Resource Health | **VirtualNetwork** | * | **AzureMonitor** | 1886 | A Resource Health állapotának közzétételéhez szükséges. |
| Függőség a naplótól az Event hub-házirendbe és a figyelési ügynökbe | **VirtualNetwork** | * | **EventHub** | 5672 ||
| Azure cache elérése Redis-példányok között szerepkör-példányok között | **VirtualNetwork** | * | **VirtualNetwork** | 6379 – 6383, plusz lásd: **Megjegyzések**| Ahhoz, hogy az ISE működjön az Azure cache-sel az Redis-hez, meg kell nyitnia ezeket [a kimenő és bejövő portokat, amelyeket az Azure cache ismertet a REDIS GYIK](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)-ban. |
| DNS-névfeloldás | **VirtualNetwork** | * | A virtuális hálózaton található bármely egyéni tartománynévrendszer-(DNS-) kiszolgáló IP-címei | 53 | Csak akkor szükséges, ha egyéni DNS-kiszolgálókat használ a virtuális hálózaton |
|||||||

Emellett a [app Service Environment (](../app-service/environment/intro.md)beadási) kimenő szabályait is hozzá kell adnia:

* Ha Azure Firewall használ, be kell állítania a tűzfalat a App Service Environment ( [teljes tartománynév) címkével](../firewall/fqdn-tags.md#current-fqdn-tags), amely lehetővé teszi a kimenő hozzáférést a szolgáltatói platform forgalmához.

* Ha nem Azure Firewall tűzfalat használ, be kell állítania a tűzfalat a App Service Environmenthoz szükséges tűzfal  - [integrációs függőségekben](../app-service/environment/firewall-integration.md#dependencies) felsorolt szabályokkal.

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>Kényszerített bújtatási követelmények

Ha a tűzfalon keresztül [kényszerített bújtatást](../firewall/forced-tunneling.md) állít be vagy használ, engedélyeznie kell a további külső függőségeket az ISE-hez. A kényszerített bújtatás lehetővé teszi, hogy az interneten keresztüli forgalmat a következő ugrásra, például a virtuális magánhálózati (VPN) vagy egy virtuális készülékre irányítsa át, nem pedig az internetre, így ellenőrizheti és naplózhatja a kimenő hálózati forgalmat.

Ha nem engedélyezi a hozzáférést ezekhez a függőségekhez, az ISE-telepítés meghiúsul, és a telepített ISE működése leáll.

* Felhasználó által megadott útvonalak

  Az aszimmetrikus útválasztás megakadályozása érdekében meg kell határoznia az összes alább felsorolt IP-cím útvonalát a következő ugrásként elérhető **internettel** .
  
  * [Felügyeleti címek App Service Environment](../app-service/environment/management-addresses.md)
  * [Az ISE régióban található összekötők Azure IP-címei az ebben a letöltési fájlban elérhetők](https://www.microsoft.com/download/details.aspx?id=56519)
  * [Azure Traffic Manager felügyeleti címek](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)
  * [Az ISE-régió bejövő és kimenő címeinek Logic Apps](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)
  * [Az ebben a letöltési fájlban található, az ISE régióban található összekötők Azure IP-címei](https://www.microsoft.com/download/details.aspx?id=56519)

* Szolgáltatásvégpontok

  Engedélyeznie kell a szolgáltatási végpontokat az Azure SQL, a Storage, a Service Bus, a kulcstartó és a Event Hubs számára, mivel nem küldhet forgalmat tűzfalon keresztül ezen szolgáltatások számára.

*  Egyéb bejövő és kimenő függőségek

   A tűzfalnak *engedélyeznie kell* a következő bejövő és kimenő függőségeket:
   
   * [Azure App Service függőségek](../app-service/environment/firewall-integration.md#deploying-your-ase-behind-a-firewall)
   * [Azure-Cache Service függőségei](../azure-cache-for-redis/cache-how-to-premium-vnet.md#what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks)
   * [Azure-API Management függőségei](../api-management/api-management-using-with-vnet.md#-common-network-configuration-issues)

<a name="create-environment"></a>

## <a name="create-your-ise"></a>ISE létrehozása

1. A [Azure Portal](https://portal.azure.com)a fő Azure-keresés mezőben adja meg a `integration service environments` szűrőt, és válassza az **integrációs szolgáltatási környezetek** lehetőséget.

   ![Az "integrációs szolgáltatási környezetek" megkeresése és kiválasztása](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Az **integrációs szolgáltatási környezetek** ablaktáblán válassza a **Hozzáadás** lehetőséget.

   ![Integrációs szolgáltatási környezet létrehozásához válassza a Hozzáadás lehetőséget.](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Adja meg ezeket az adatokat a környezetében, majd válassza a **felülvizsgálat + létrehozás** lehetőséget, például:

   ![Adja meg a környezet részleteit](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Előfizetés** | Igen | <*Azure-előfizetés – név*> | A környezetéhez használni kívánt Azure-előfizetés |
   | **Erőforráscsoport** | Igen | <*Azure-Erőforrás-csoport-név*> | Új vagy meglévő Azure-erőforráscsoport, amelyben létre szeretné hozni a környezetet |
   | **Integrációs szolgáltatási környezet neve** | Igen | <*környezet – név*> | Az ISE neve, amely csak betűket, számokat, kötőjeleket () `-` , aláhúzásokat ( `_` ) és pontokat ( `.` ) tartalmazhat. |
   | **Hely** | Igen | <*Azure-Datacenter – régió*> | Az Azure-adatközpont régiója, ahol üzembe helyezheti a környezetet |
   | **Termékváltozat** | Igen | **Prémium** vagy **fejlesztői (SLA nélkül)** | A létrehozandó és használandó ISE SKU. Az adatsku-változatok közötti különbségekért lásd: [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)-i. <p><p>**Fontos**: Ez a beállítás csak az ISE létrehozásakor érhető el, és később nem módosítható. |
   | **További kapacitás** | Prémium: <br>Igen <p><p>Fejlesztő <br>Nem alkalmazható | Prémium: <br>0 – 10 <p><p>Fejlesztő <br>Nem alkalmazható | Az ISE-erőforráshoz használandó extra feldolgozási egységek száma. A kapacitás létrehozás utáni hozzáadásával kapcsolatban lásd: [ISE-kapacitás hozzáadása](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Hozzáférési végpont** | Igen | **Belső** vagy **külső** | Az ISE számára használandó hozzáférési végpontok típusa. Ezek a végpontok határozzák meg, hogy az ISE-beli logikai alkalmazásokban a kérelmek vagy a webhook-eseményindítók fogadhatnak-e hívásokat a virtuális hálózaton kívülről. <p><p>Ha például a következő webhook-alapú eseményindítókat szeretné használni, győződjön meg arról, hogy a **külső** elemet választotta: <p><p>– Azure DevOps <br>– Azure Event Grid <br>– Common Data Service <br>– Office 365 <br>-SAP (ISE-verzió) <p><p>A kiválasztott módszer azt is befolyásolja, hogyan lehet megtekinteni és elérni a logikai alkalmazás futtatási előzményeinek bemeneteit és kimeneteit. További információ: [ISE Endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Fontos**: a hozzáférési végpontot csak az ISE létrehozásakor választhatja ki, és később nem módosíthatja ezt a beállítást. |
   | **Virtuális hálózat** | Igen | <*Azure-Virtual-Network-name*> | Az Azure-beli virtuális hálózat, ahová be szeretné szúrni a környezetét, hogy a környezetében a logikai alkalmazások hozzáférhessenek a virtuális hálózathoz. Ha nem rendelkezik hálózattal, [először hozzon létre egy Azure-beli virtuális hálózatot](../virtual-network/quick-create-portal.md). <p><p>**Fontos**: ezt az injekciót *csak* akkor hajthatja végre, ha létrehozza az ISE-t. |
   | **Alhálózatok** | Igen | <*alhálózat – erőforrás-lista*> | Az ISE négy *üres* alhálózatot igényel, amelyek szükségesek az ISE-erőforrások létrehozásához és üzembe helyezéséhez, és amelyeket a belső Logic apps-összetevők, például összekötők és a teljesítmény gyorsítótárazása használ. <p>**Fontos**: Ügyeljen arra, hogy [az alhálózatok létrehozásához tekintse át az alhálózat követelményeit, mielőtt folytatná ezeket a lépéseket](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Alhálózatok létrehozása**

   Az ISE négy *üres* alhálózatot igényel, amelyek szükségesek az ISE-erőforrások létrehozásához és üzembe helyezéséhez, és a belső Logic apps-összetevők, például összekötők és a teljesítmény gyorsítótárazásához használják. A környezet létrehozása után ezeket az alhálózati címeket *nem* módosíthatja. Ha az ISE-t a Azure Portalon hozza létre és telepíti, győződjön meg arról, hogy nem delegálja ezeket az alhálózatokat bármely Azure-szolgáltatáshoz. Ha azonban a REST APIon, Azure PowerShellon vagy Azure Resource Manager sablonon keresztül hozza létre és telepíti az ISE-t, akkor egy üres alhálózatot kell [delegálnia](../virtual-network/manage-subnet-delegation.md) `Microsoft.integrationServiceEnvironment` . További információ: [alhálózati delegálás hozzáadása](../virtual-network/manage-subnet-delegation.md).

   Minden alhálózatnak meg kell felelnie a következő követelményeknek:

   * Olyan nevet használ, amely alfabetikus karakterrel vagy aláhúzással (No Numbers) kezdődik, és nem használja a következő karaktereket:,,,,,, `<` `>` `%` `&` `\\` `?` `/` .

   * Az [osztály nélküli Inter-Domain útválasztási (CIDR) formátumot](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)használja.
   
     > [!IMPORTANT]
     >
     > Ne használja a következő IP-címeket a virtuális hálózathoz vagy alhálózatokhoz, mert Azure Logic Apps nem oldhatók fel:<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * A `/27` címtartomány használata, mert minden egyes alhálózathoz 32 cím szükséges. Például 32- `10.0.0.0/27` címmel rendelkezik, mert 2<sup>(32-27)</sup> 2<sup>5</sup> vagy 32. További címek nem biztosítanak további előnyöket. A címek kiszámításával kapcsolatos további tudnivalókért lásd: [IPv4-CIDR blokkok](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Ha a [ExpressRoute](../expressroute/expressroute-introduction.md)-t használja, [létre kell hoznia egy útválasztási táblázatot](../virtual-network/manage-route-table.md) , amely a következő útvonalat tartalmazza, és csatolja a táblát az ISE által használt összes alhálózathoz:

     **Név**: <*útvonal neve*><br>
     **Címzési előtag**: 0.0.0.0/0<br>
     **Következő ugrás**: Internet

   1. Az **alhálózatok** listában válassza az **alhálózat konfigurációjának kezelése** lehetőséget.

      ![Alhálózat konfigurációjának kezelése](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. Az **alhálózatok** ablaktáblán válassza az **alhálózat** lehetőséget.

      ![Négy üres alhálózat hozzáadása](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. Az **alhálózat hozzáadása** panelen adja meg ezt az információt.

      * **Name (név**): az alhálózat neve
      * **Címtartomány (CIDR blokk)**: az alhálózat tartománya a virtuális hálózatban és CIDR formátumban

      ![Alhálózat adatainak hozzáadása](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Amikor elkészült, válassza az **OK** lehetőséget.

   1. Ismételje meg ezeket a lépéseket három további alhálózatra.

      > [!NOTE]
      > Ha a létrehozni kívánt alhálózatok nem érvényesek, a Azure Portal egy üzenetet jelenít meg, de nem blokkolja a folyamat állapotát.

   Az alhálózatok létrehozásával kapcsolatos további információkért lásd: [virtuális hálózati alhálózat hozzáadása](../virtual-network/virtual-network-manage-subnet.md).

1. Miután az Azure sikeresen ellenőrizte az ISE-információkat, válassza a **Létrehozás** lehetőséget, például:

   ![A sikeres ellenőrzés után válassza a létrehozás lehetőséget.](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Az Azure elindítja a környezet üzembe helyezését, amely általában két órán belül befejeződik. Alkalmanként az üzembe helyezés akár négy órát is igénybe vehet. A központi telepítés állapotának megtekintéséhez az Azure eszköztárán válassza az értesítések ikont, amely megnyitja az értesítések panelt.

   ![Központi telepítés állapotának keresése](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Ha a telepítés sikeresen befejeződött, az Azure megjeleníti ezt az értesítést:

   ![Az üzembe helyezés sikerült](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Ellenkező esetben kövesse az üzembe helyezés hibaelhárításához Azure Portal utasításokat.

   > [!NOTE]
   > Ha az üzembe helyezés sikertelen, vagy törli az ISE-t, az Azure akár egy órát is igénybe vehet, vagy ritkán fordulhat elő az alhálózatok kiadása előtt. Ezért előfordulhat, hogy várnia kell, amíg újra fel nem használja ezeket az alhálózatokat egy másik ISE-ben.
   >
   > Ha törli a virtuális hálózatot, az Azure általában akár két órával az alhálózatok felszabadítása előtt is eltarthat, de ez a művelet hosszabb időt is igénybe vehet. 
   > A virtuális hálózatok törlésekor győződjön meg arról, hogy egyetlen erőforrás sincs még csatlakoztatva. 
   > Lásd: [virtuális hálózat törlése](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Ha szeretné megtekinteni a környezetet, válassza az **Ugrás az erőforráshoz** lehetőséget, ha az Azure nem automatikusan a környezetbe lép a telepítés befejeződése után.

1. Ha egy ISE *külső* végponti hozzáféréssel rendelkezik, létre kell hoznia egy hálózati biztonsági csoportot, ha még nem rendelkezik ilyennel, és hozzá kell adnia egy bejövő biztonsági szabályt, amely engedélyezi a forgalmat a felügyelt összekötő kimenő IP-címeiről. A szabály beállításához kövesse az alábbi lépéseket:

   1. Az ISE menü **Beállítások** területén válassza a **Tulajdonságok** elemet.

   1. Az **összekötő kimenő IP-címei** területen másolja a nyilvános IP-címtartományok, amelyek szintén megjelennek ebben a cikkben, [korlátok és konfiguráció – kimenő IP-címek](../logic-apps/logic-apps-limits-and-config.md#outbound).

   1. Hozzon létre egy hálózati biztonsági csoportot, ha még nem rendelkezik ilyennel.
   
   1. A következő információk alapján adjon hozzá egy bejövő biztonsági szabályt a másolt nyilvános kimenő IP-címekhez. További információ: [oktatóanyag: hálózati forgalom szűrése hálózati biztonsági csoporttal a Azure Portal használatával](../virtual-network/tutorial-filter-network-traffic.md#create-a-network-security-group).

      | Cél | Forrásként szolgáló szolgáltatás címkéje vagy IP-címe | Forrásportok | Cél szolgáltatás címkéje vagy IP-címe | Célportok | Jegyzetek |
      |---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
      | Az összekötő kimenő IP-címeiről érkező forgalom engedélyezése | <*összekötő – nyilvános-kimenő-IP-címek*> | * | Az ISE-alhálózatokkal rendelkező virtuális hálózat címterület | * | |
      |||||||

1. Az ISE hálózati állapotának ellenõrzéséhez tekintse meg az [integrációs szolgáltatási környezet kezelése](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)című témakört.

   > [!CAUTION]
   > Ha az ISE hálózata sérült állapotba kerül, az ISE által használt belső App Service Environment (bevezető) nem Kifogástalan állapotba kerül. Ha a benyújtó nem kifogástalan állapotú, mint hét napja, a kiegészítő szolgáltatás fel van függesztve. Az állapot megoldásához keresse meg a virtuális hálózat beállításait. Javítsa ki a megtalált problémákat, majd indítsa újra az ISE-t. Ellenkező esetben, 90 nap elteltével a rendszer törli a felfüggesztett bekötést, és az ISE használhatatlanná válik. Ezért ügyeljen arra, hogy az ISE Kifogástalan állapotba kerüljön, hogy engedélyezze a szükséges forgalmat.
   > 
   > További információt az alábbi témakörökben talál:
   >
   > * [Azure App Service diagnosztika áttekintése](../app-service/overview-diagnostics.md)
   > * [Azure App Service Environment üzenetek naplózása](../app-service/environment/using-an-ase.md#logging)

1. A Logic apps és más összetevők az ISE-ben való létrehozásának megkezdéséhez lásd: [erőforrások hozzáadása az integrációs szolgáltatási környezetekhez](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > Miután létrehozta az ISE-t, a felügyelt ISE-összekötők elérhetővé válnak a használatra, de nem jelennek meg automatikusan a Logic app Designer összekötő-választójában. Az ISE-összekötők használata előtt [ezeket az összekötőket manuálisan kell hozzáadnia és telepítenie az ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) -ben, hogy azok megjelenjenek a Logic app Designerben.

## <a name="next-steps"></a>Következő lépések

* [Erőforrás hozzáadása integrációs szolgáltatási környezetekhez](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Integrációs szolgáltatási környezetek kezelése](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* További információ az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Ismerje meg az [Azure-szolgáltatások virtuális hálózati integrációját](../virtual-network/virtual-network-for-azure-services.md)
