---
title: Hálózatok biztonságos csatlakoztatása az Azure Monitorhoz az Azure Private Linkkel
description: Hálózatok biztonságos csatlakoztatása az Azure Monitorhoz az Azure Private Linkkel
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 65af5810152034fd7b6014041edd07835eebd194
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101477"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Hálózatok biztonságos csatlakoztatása az Azure Monitorhoz az Azure Private Linkkel

Az [Azure Private link](../../private-link/private-link-overview.md) lehetővé teszi, hogy a privát végpontok használatával biztonságosan összekapcsolja az Azure Pásti-szolgáltatásokat a virtuális hálózattal. Számos szolgáltatás esetében csak egy végpontot állít be erőforrásként. A Azure Monitor azonban a különböző összekapcsolt szolgáltatások egyik konstellációja, amely együttműködik a számítási feladatok monitorozásával. Ennek eredményeképpen létrehozott egy Azure Monitor Private link-hatókört (AMPLS) nevű erőforrást. A AMPLS lehetővé teszi a megfigyelési hálózat határainak meghatározását és a virtuális hálózathoz való kapcsolódást. Ez a cikk azt ismerteti, hogy mikor kell használni és hogyan kell beállítani egy Azure Monitor privát hivatkozás hatókörét.

## <a name="advantages"></a>Előnyök

A privát hivatkozással a következőket teheti:

- A nyilvános hálózati hozzáférés megnyitása nélkül csatlakozhat a Azure Monitorhoz
- Győződjön meg arról, hogy a figyelési adatai csak a jogosult magánhálózatok használatával érhetők el
- A magánhálózati adatok kiszűrése megakadályozása a privát végponton keresztül kapcsolódó Azure Monitor erőforrások definiálásával
- A privát helyszíni hálózat biztonságos csatlakoztatása Azure Monitor ExpressRoute és privát kapcsolat használatával
- Az Microsoft Azure gerinc hálózatán belüli összes forgalom megőrzése

További információ:  [a privát hivatkozás legfontosabb előnyei](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Működés

Azure Monitor Private link scope (AMPLS) összekapcsolja a privát végpontokat (és a bennük található virtuális hálózatok) egy vagy több Azure Monitor erőforráshoz – Log Analytics munkaterületekhez és Application Insights összetevőkhöz.

![Alapszintű erőforrás-topológia ábrája](./media/private-link-security/private-link-basic-topology.png)

* A VNet lévő privát végpont lehetővé teszi, hogy a hálózati készletből származó magánhálózati IP-címeken keresztül elérje Azure Monitor végpontokat, ahelyett, hogy az adott végpontok nyilvános IP-címeire kellene használnia. Ez lehetővé teszi a Azure Monitor-erőforrások használatának fenntartását anélkül, hogy a VNet megnyitná a nem szükséges kimenő forgalomra. 
* A magánhálózati végpontról a Azure Monitor erőforrásaira irányuló forgalom a Microsoft Azure gerinc fölé kerül, és nem a nyilvános hálózatokra irányítja. 
* Az egyes munkaterületeket és összetevőket úgy is konfigurálhatja, hogy engedélyezze vagy megtagadja a nyilvános hálózatok betöltését és lekérdezéseit. Ez erőforrás-szintű védelmet biztosít, így adott erőforrásokra is vezérelheti a forgalmat.

> [!NOTE]
> Egyetlen Azure Monitor erőforrás több AMPLSs is tartozhat, de egyetlen VNet nem csatlakoztatható egynél több AMPLS. 

## <a name="planning-your-private-link-setup"></a>A privát kapcsolat beállításának megtervezése

A Azure Monitor privát kapcsolat beállításának beállítása előtt vegye figyelembe a hálózati topológiát és a DNS-útválasztási topológiát. 

### <a name="the-issue-of-dns-overrides"></a>A DNS-felülbírálások hibája
Egyes Azure Monitor szolgáltatások globális végpontokat használnak, ami azt jelenti, hogy a rendszer minden munkaterületre vagy összetevőre irányuló kérelmeket szolgál ki. Néhány példa a Application Insights betöltési végpontja, valamint a Application Insights és Log Analytics lekérdezési végpontja.

Amikor beállít egy magánhálózati kapcsolati kapcsolatot, a rendszer úgy frissíti a DNS-t, hogy Azure Monitor végpontokat a VNet IP-tartományában lévő magánhálózati IP-címekre képezze. Ez a változás felülbírálja a végpontok korábbi leképezéseit, amelyek a következőkben ismertetett hasznos következményekkel járhatnak. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor privát hivatkozás az összes Azure Monitor erőforrásra vonatkozik – ez az összes vagy semmi
Mivel egyes Azure Monitor végpontok globálisak, nem lehet létrehozni egy adott összetevőhöz vagy munkaterülethez tartozó magánhálózati kapcsolati kapcsolatot. Ehelyett, amikor egyetlen Application Insights összetevőhöz állít be egy magánhálózati hivatkozást, a DNS-rekordok frissülnek az **összes** Application Insights összetevő esetében. Az összetevők betöltésére vagy lekérdezésére tett kísérletek a privát kapcsolaton keresztül mennek keresztül, és valószínűleg sikertelenek lesznek. Hasonlóképpen, ha egyetlen munkaterületre vonatkozó privát hivatkozást állít be, az összes Log Analytics lekérdezés a privát kapcsolat lekérdezési végpontján halad át (de nem a betöltési kérelmeket, amelyek munkaterület-specifikus végpontokkal rendelkeznek).

![DNS-felülbírálások diagramja egyetlen VNet](./media/private-link-security/dns-overrides-single-vnet.png)

Ez nem csak egy adott VNet érvényes, hanem minden olyan virtuális hálózatok esetében, amely ugyanazt a DNS-kiszolgálót használja (lásd [a DNS-felülbírálások problémát](#the-issue-of-dns-overrides)). Így például a naplók betöltésére irányuló kéréseket a rendszer mindig a privát kapcsolat útvonalán keresztül küldi el a Application Insights-összetevőknek. A AMPLS nem kapcsolódó összetevők sikertelenek lesznek a privát kapcsolat érvényesítése, és nem haladnak át.

> [!NOTE]
> Megkötés: Ha a beállítás egy önálló erőforráshoz való magánhálózati kapcsolattal rendelkezik, a hálózat összes Azure Monitor erőforrására vonatkozik – ez mind vagy semmi. Ez gyakorlatilag azt jelenti, hogy a hálózatban lévő összes Azure Monitor erőforrást fel kell vennie a AMPLS, vagy egyiket sem.

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor privát hivatkozás a teljes hálózatra vonatkozik
Néhány hálózat több virtuális hálózatok áll. Ha a virtuális hálózatok ugyanazt a DNS-kiszolgálót használja, a rendszer felülírja egymás DNS-leképezéseit, és esetleg megszakítja egymás kommunikációját a Azure Monitorsal (lásd [a DNS-felülbírálások problémáját](#the-issue-of-dns-overrides)). Végső soron csak az utolsó VNet tud kommunikálni a Azure Monitorval, mivel a DNS az ettől a virtuális hálózatok-tartományból Azure Monitor végpontokat képez le a privát IP-címekhez (ami esetleg nem érhető el más virtuális hálózatok).

![DNS-felülbírálások diagramja több virtuális hálózatok](./media/private-link-security/dns-overrides-multiple-vnets.png)

A fenti ábrán a VNet 10.0.1. x először csatlakozik a AMPLS1-hez, és leképezi a Azure Monitor globális végpontokat az IP-címekre a tartományból. Később a VNet 10.0.2. x csatlakozik a AMPLS2-hoz, és felülbírálja *ugyanazokat a globális végpontokat* , amelyek IP-címei a tartományból származnak. Mivel ezek a virtuális hálózatok nincsenek összevonással, az első VNet most nem éri el ezeket a végpontokat.

> [!NOTE]
> A befejezéshez: a AMPLS telepítője minden olyan hálózatot érint, amely ugyanazokat a DNS-zónákat használja. Az egymás DNS-végpontok leképezésének felülbírálásának elkerülése érdekében érdemes egyetlen privát végpontot beállítani egy összekapcsolt hálózaton (például egy központi VNet), vagy a DNS szintjén elkülöníteni a hálózatokat (az ellenség példa DNS-továbbítókkal vagy különálló DNS-kiszolgálók használatával).

### <a name="hub-spoke-networks"></a>Sugaras hálózatok
A küllős topológiák elkerülhetik a DNS-felülbírálások hibáját azáltal, hogy egy privát hivatkozást állítanak be a központi (fő) VNet, ahelyett, hogy külön magánhálózati hivatkozást kellene beállítani az egyes VNet. Ez a beállítás különösen akkor hasznos, ha a küllő virtuális hálózatok által használt Azure Monitor erőforrások meg vannak osztva. 

![Sugaras – egyetlen PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Érdemes lehet külön privát hivatkozásokat létrehozni a küllős virtuális hálózatok, például hogy az egyes VNet a figyelési erőforrások korlátozott készletéhez férhessenek hozzá. Ilyen esetekben létrehozhat egy dedikált privát végpontot és AMPLS az egyes VNet, de azt is ellenőriznie kell, hogy nem ugyanazokat a DNS-zónákat használják-e a DNS-felülbírálások elkerülése érdekében.


### <a name="consider-limits"></a>Határértékek megfontolása

A [korlátozások és korlátozások](#restrictions-and-limitations)listájában felsoroltak szerint a AMPLS objektumnak számos korlátja van, amely az alábbi topológiában látható:
* Minden VNet csak **1** AMPLS objektumhoz csatlakozik.
* A "B" AMPLS két virtuális hálózatok (VNet2 és VNet3) privát végpontokhoz csatlakozik, a 10 lehetséges privát végpont-kapcsolat közül 2 használatával.
* A AMPLS két munkaterülethez és egy alkalmazás-betekintési összetevőhöz csatlakozik, az 50 lehetséges Azure Monitor erőforrás-kapcsolatok 3. használatával.
* A Workspace2 az 5 lehetséges AMPLS-kapcsolat közül 2-AMPLS csatlakozik az A és A AMPLS B szolgáltatáshoz.

![AMPLS-korlátok diagramja](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>Példa a kapcsolatok

Első lépésként hozzon létre egy Azure Monitor privát hivatkozás hatókör-erőforrást.

1. Nyissa meg az **erőforrás létrehozása** a Azure Portalban, és keresse meg **Azure monitor privát hivatkozás hatókörét**.

   ![Azure Monitor privát hivatkozás hatókörének keresése](./media/private-link-security/ampls-find-1c.png)

2. Válassza a **Létrehozás** lehetőséget.
3. Válasszon egy előfizetést és egy erőforráscsoportot.
4. Adja meg a AMPLS nevét. Érdemes lehet értelmes és egyértelmű nevet használni, például "AppServerProdTelem".
5. Válassza a **Felülvizsgálat és létrehozás** lehetőséget. 

   ![Azure Monitor privát hivatkozás hatókörének létrehozása](./media/private-link-security/ampls-create-1d.png)

6. Hagyja meg az érvényesítési fázist, majd válassza a **Létrehozás** lehetőséget.

### <a name="connect-azure-monitor-resources"></a>Azure Monitor-erőforrások összekötése

Azure Monitor erőforrások (Log Analytics-munkaterületek és Application Insights-összetevők) összekapcsolhatók a AMPLS.

1. A Azure Monitor privát hivatkozás hatókörében válassza ki **Azure monitor erőforrásokat** a bal oldali menüben. Kattintson a **Hozzáadás** gombra.
2. Adja hozzá a munkaterületet vagy összetevőt. A **Hozzáadás** gombra kattintva megjelenik egy párbeszédpanel, ahol kiválaszthatja Azure monitor erőforrásait. Böngészhet az előfizetések és az erőforráscsoportok között, vagy beírhatja a nevét a szűréshez. Válassza ki a munkaterületet vagy összetevőt, majd válassza az **alkalmaz** lehetőséget a hatókörhöz való hozzáadáshoz.

    ![A hatókör kiválasztása UX képernyőkép](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Azure Monitor erőforrások törléséhez először le kell választania azokat minden olyan AMPLS-objektumról, amelyhez csatlakoznak. Nem lehet törölni egy AMPLS kapcsolódó erőforrásokat.

### <a name="connect-to-a-private-endpoint"></a>Kapcsolódás privát végponthoz

Most, hogy rendelkezik a AMPLS kapcsolódó erőforrásokkal, hozzon létre egy privát végpontot a hálózat csatlakoztatásához. Ezt a feladatot a [Azure Portal Private link Centerben](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)vagy a Azure monitor privát hivatkozás hatókörén belül hajthatja végre, ahogy az ebben a példában is látható.

1. A hatókör-erőforrás területen válassza a **privát végponti kapcsolatok** elemet a bal oldali erőforrás menüben. Válassza a **privát végpont** lehetőséget a végpont létrehozási folyamatának elindításához. A privát kapcsolat központban elindított kapcsolatokat itt is jóváhagyhatja, ha kiválasztja őket, és kiválasztja a **jóváhagyás** lehetőséget.

    ![A Private Endpoint Connections UX képernyőképe](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Válassza ki az előfizetést, az erőforráscsoportot és a végpont nevét, valamint azt a régiót, amelyben élni kíván. A régiónak ugyanabban a régióban kell lennie, mint a VNet, amelyhez csatlakozik.

3. Válassza a **Tovább: erőforrás** elemet. 

4. Az erőforrás képernyőjén

   a. Válassza ki az **előfizetést** , amely tartalmazza a Azure monitor saját hatókörű erőforrását. 

   b. Az **erőforrástípus mezőben** válassza a **Microsoft. ininsights/privateLinkScopes** lehetőséget. 

   c. Az **erőforrás** legördülő menüben válassza ki a korábban létrehozott saját hivatkozás hatókörét. 

   d. Válassza a Next (tovább) lehetőséget **: Configuration >**.
      ![Képernyőkép a privát végpont létrehozása elemről](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. A konfigurációs ablaktáblán

   a.    Válassza ki azt a **virtuális hálózatot** és **alhálózatot** , amelyhez csatlakozni szeretne a Azure monitor erőforrásaihoz. 
 
   b.    Válassza az **Igen** lehetőséget a **saját DNS-zónába való integráláshoz**, és hagyja, hogy automatikusan létrehozzon egy új saját DNS zónát. Előfordulhat, hogy a tényleges DNS-zónák eltérnek az alábbi képernyőképen láthatótól. 
   > [!NOTE]
   > Ha a **nem** lehetőséget választja, és manuálisan szeretné kezelni a DNS-rekordokat, először fejezze be a privát kapcsolat beállítását – beleértve ezt a privát végpontot és a AMPLS-konfigurációt. Ezután konfigurálja a DNS-t az [Azure-beli privát végpont DNS-konfigurálása](../../private-link/private-endpoint-dns.md) című dokumentumban leírt utasítások szerint. Ügyeljen arra, hogy ne hozzon létre üres rekordokat a privát kapcsolat beállításának előkészítése közben. Az Ön által létrehozott DNS-rekordok felülírják a meglévő beállításokat, és hatással vannak az Azure Monitorral létesített kapcsolatra.
 
   c.    Válassza az **Áttekintés + létrehozás** lehetőséget.
 
   d.    Az érvényesítési fázis engedélyezése. 
 
   e.    Válassza a **Létrehozás** lehetőséget. 

    ![Képernyőkép a privát végpont részleteinek kiválasztásáról.](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Most létrehozott egy új, ehhez a AMPLS csatlakozó privát végpontot.

## <a name="review-and-validate-your-private-link-setup"></a>A privát hivatkozás beállításainak áttekintése és ellenőrzése

### <a name="reviewing-your-endpoints-dns-settings"></a>A végpont DNS-beállításainak áttekintése
A létrehozott privát végpontnak most már négy DNS-zónával kell rendelkeznie:

[![Képernyőkép a privát végpont DNS-zónái.](./media/private-link-security/private-endpoint-dns-zones.png)](./media/private-link-security/private-endpoint-dns-zones-expanded.png#lightbox)

* privatelink-monitor-Azure-com
* privatelink-OMS-opinsights-Azure-com
* privatelink-ODS-opinsights-Azure-com
* privatelink-agentsvc-Azure-Automation-net

> [!NOTE]
> Ezen zónák mindegyike adott Azure Monitor végpontokat képez le a VNet IP-címeiből származó magánhálózati IP-címekre. Az alábbi képen látható IP-címek csak példák. A konfigurációnak Ehelyett privát IP-címeket kell megjelenítenie a saját hálózatáról.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-Azure-com
Ez a zóna a Azure Monitor által használt globális végpontokat fedi le, ami azt jelenti, hogy ezek a végpontok olyan kérelmeket szolgálnak ki, amelyek az összes erőforrást figyelembe veszik. Ehhez a zónához a következőhöz hozzárendelt végpontoknak kell szerepelniük:
* `in.ai` -(Application Insights betöltési végpont, globális és regionális bejegyzést fog látni
* `api` -Application Insights és Log Analytics API-végpont
* `live` -Application Insights élő metrikák végpontja
* `profiler` -Application Insights Profiler-végpont
* `snapshot`-Application Insights Pillanatképek végpont [ ![ képernyőképe saját DNS Zone monitor – Azure-com.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-OMS-opinsights-Azure-com
Ez a zóna a OMS-végpontokra vonatkozó munkaterület-specifikus hozzárendeléseket ismerteti. Minden munkaterülethez meg kell jelennie egy olyan bejegyzésnek, amely az ehhez a privát végponthoz kapcsolódó AMPLS van társítva.
[![Képernyőkép a saját DNS Zone OMS-opinsights-Azure-com.](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ODS-opinsights-Azure-com
Ez a zóna az ODS-végpontokra vonatkozó munkaterület-specifikus hozzárendelést, Log Analytics betöltési végpontját fedi le. Minden munkaterülethez meg kell jelennie egy olyan bejegyzésnek, amely az ehhez a privát végponthoz kapcsolódó AMPLS van társítva.
[![Képernyőkép a saját DNS Zone ODS-opinsights-Azure-com-ről.](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-Azure-Automation-net
Ez a zóna az ügynök szolgáltatás-automatizálási végpontokra vonatkozó munkaterület-specifikus hozzárendeléseket ismerteti. Minden munkaterülethez meg kell jelennie egy olyan bejegyzésnek, amely az ehhez a privát végponthoz kapcsolódó AMPLS van társítva.
[![A saját DNS Zone Agent SVC-Azure-Automation-net képernyőképe.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

### <a name="validating-you-are-communicating-over-a-private-link"></a>Annak ellenőrzése, hogy privát kapcsolaton keresztül kommunikál-e
* Annak ellenőrzéséhez, hogy a rendszer elküldte a kéréseket a privát végponton és a magánhálózati IP-címekre leképezett végpontokon, áttekintheti azokat egy hálózati követéssel az eszközökön, vagy akár a böngészőben is. Például a munkaterület vagy alkalmazás lekérdezésére tett kísérlet során győződjön meg arról, hogy a kérést az API-végponthoz rendelt magánhálózati IP-címre küldi, ebben a példában ez a *172.17.0.9*.

    Megjegyzés: egyes böngészők más DNS-beállításokat is használhatnak (lásd a [böngésző DNS-beállításait](#browser-dns-settings)). Ellenőrizze, hogy a DNS-beállítások érvényesek-e.

* Annak biztosításához, hogy a munkaterület vagy az összetevő ne kapjon nyilvános hálózatokról érkező kéréseket (a AMPLS-on keresztül nem csatlakozik), állítsa be az erőforrás nyilvános betöltését és a lekérdezési jelzőket a *nem* értékre a [privát hivatkozások hatókörén kívüli hozzáférés kezelése](#manage-access-from-outside-of-private-links-scopes)részben leírtaknak

* A védett hálózat egyik ügyfelétől a `nslookup` DNS-zónákban felsorolt végpontok bármelyikére használhatja. A DNS-kiszolgáló az alapértelmezés szerint használt nyilvános IP-címek helyett a hozzárendelt magánhálózati IP-címekre kell feloldania.


## <a name="configure-log-analytics"></a>A Log Analytics konfigurálása

Nyissa meg az Azure Portalt. A Log Analytics munkaterület erőforrás menüjében található egy **hálózati elkülönítés** nevű elem a bal oldali oldalon. Ebben a menüben két különböző állapotot is megadhat.

![LA hálózati elkülönítés](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Csatlakoztatott Azure Monitor privát kapcsolati hatókörök
A munkaterülethez csatlakozó összes hatókör megjelenik ezen a képernyőn. A hatókörökhöz való csatlakozás (AMPLSs) lehetővé teszi, hogy az egyes AMPLS csatlakoztatott virtuális hálózatról érkező hálózati forgalom elérje ezt a munkaterületet. A kapcsolat létrehozása a következővel azonos hatással van a hatókörre, ahogy azt a [Azure monitor erőforrások csatlakoztatása](#connect-azure-monitor-resources)során hajtottuk végre. Új kapcsolat hozzáadásához válassza a **Hozzáadás** lehetőséget, majd válassza ki a Azure monitor privát hivatkozás hatókörét. A kapcsolódáshoz válassza az **alkalmaz** lehetőséget. Vegye figyelembe, hogy a munkaterületek 5 AMPLS-objektumhoz kapcsolódhatnak, ahogy azt a [korlátozásokban és korlátozásokban](#restrictions-and-limitations)is említettük. 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>A privát hivatkozások hatókörén kívüli hozzáférés kezelése
A lap alsó részén lévő beállítások vezérlik a nyilvános hálózatokról való hozzáférést, azaz a fent felsorolt hatókörökön keresztül nem csatlakoztatott hálózatokat. A **nyilvános hálózati hozzáférés engedélyezése** a betöltéshez **nem** blokkolja a csatlakoztatott hatókörökön kívüli gépekről származó naplók betöltését. A **nyilvános hálózati hozzáférés engedélyezése a lekérdezésekhez** **nem** blokkolja a hatókörökön kívüli gépekről érkező lekérdezéseket. Ez magában foglalja a munkafüzetek, irányítópultok, API-alapú ügyfél-élmények, a Azure Portalon belüli bepillantások és egyéb funkciók használatával történő lekérdezéseket. A Azure Portalon kívül futó tapasztalatok, és a lekérdezés Log Analytics az adatnak is futnia kell a privát csatolt VNET.

### <a name="exceptions"></a>Kivételek
A fentiekben leírtak szerint a hozzáférés korlátozása nem vonatkozik a Azure Resource Managerra, ezért a következő korlátozásokkal rendelkezik:
* Hozzáférés az adatokhoz – a nyilvános hálózatokból érkező lekérdezések letiltása vagy engedélyezése a legtöbb Log Analyticsi tapasztalatra vonatkozik, és egyes funkciók a Azure Resource Manageron keresztül kérdezik le az adatlekérdezéseket, ezért nem lesznek képesek az adatok lekérdezésére, kivéve, ha a privát kapcsolat beállításait a Resource Manager is alkalmazza (a szolgáltatás hamarosan elérhető). Ilyenek például a következők: Azure Monitor megoldások, munkafüzetek és bepillantások, valamint az LogicApp-összekötő.
* A munkaterület-kezelés – a munkaterület beállítását és a konfiguráció módosításait (beleértve a hozzáférési beállítások be-és kikapcsolását) a Azure Resource Manager kezeli. Korlátozza a munkaterület-felügyelet elérését a megfelelő szerepkörök, engedélyek, hálózati vezérlők és naplózás használatával. További információ: [Azure monitor szerepkörök, engedélyek és biztonság](../roles-permissions-security.md).

> [!NOTE]
> A munkaterületre a [diagnosztikai beállításokon](../essentials/diagnostic-settings.md) keresztül feltöltött naplók és mérőszámok egy biztonságos privát Microsoft-csatornán haladnak át, és ezeket a beállításokat nem szabályozzák.

### <a name="log-analytics-solution-packs-download"></a>Log Analytics a megoldás csomagjainak letöltése

Ha engedélyezni szeretné, hogy a Log Analytics ügynök letöltse a megoldási csomagokat, adja hozzá a megfelelő teljes tartományneveket a tűzfal engedélyezési listájához. 


| Felhőalapú környezet | Ügynök erőforrása | Portok | Irány |
|:--|:--|:--|:--|
|Azure Public     | scadvisorcontent.blob.core.windows.net         | 443 | Kimenő
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Kimenő
|Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Kimenő

## <a name="configure-application-insights"></a>Az Application Insights konfigurálása

Nyissa meg az Azure Portalt. A Azure Monitor Application Insights összetevő-erőforrásban a bal oldali menüpont **hálózati elkülönítése** . Ebben a menüben két különböző állapotot is megadhat.

![AI hálózati elkülönítés](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Először is csatlakoztathatja ezt a Application Insights-erőforrást, hogy Azure Monitor privát hivatkozás hatókörét, amelyhez hozzáféréssel rendelkezik. Válassza a **Hozzáadás** lehetőséget, majd válassza ki a **Azure monitor privát hivatkozás hatókörét**. A kapcsolódáshoz válassza az alkalmaz lehetőséget. Az összes csatlakoztatott hatókör megjelenik ezen a képernyőn. Ez a kapcsolat lehetővé teszi, hogy a csatlakoztatott virtuális hálózatok hálózati forgalma elérje ezt az összetevőt, és ugyanaz a hatása, mint ahogy a [Azure monitor erőforrások csatlakoztatása](#connect-azure-monitor-resources)során a hatókörhöz csatlakoztatta. 

Másodszor, azt is szabályozhatja, hogy ez az erőforrás Hogyan érhető el a korábban felsorolt privát kapcsolati hatókörökön (AMPLS) kívülről. Ha a **nyilvános hálózati hozzáférés engedélyezése** a **nem** értékre van állítva, akkor a csatlakoztatott hatókörökön kívüli gépek vagy SDK-k nem tölthetnek fel adatot ebbe az összetevőbe. Ha a **nyilvános hálózati hozzáférés engedélyezése a** **nem** értékre van állítva, akkor a hatókörökön kívüli gépek nem férnek hozzá a Application Insights erőforrásban található adatforrásokhoz. Az adatok közé tartozik az APM-naplók, a metrikák és az élő metrikai stream elérésének, valamint a többek között a munkafüzetek, az irányítópultok, a lekérdezési API-alapú ügyfél-élmények, a Azure Portal elemzések és egyebek. 

> [!NOTE]
> A portálon kívüli felhasználási tapasztalatokat a felügyelt munkaterheléseket tartalmazó privát csatolt VNET is futtatni kell.

A megfigyelt számítási feladatokat üzemeltető erőforrásokat a privát hivatkozáshoz kell hozzáadnia. Lásd például: [privát végpontok használata az Azure Web App](../../app-service/networking/private-endpoint.md)szolgáltatáshoz.

A hozzáférés ezen a módon való korlátozása csak a Application Insights erőforrásban lévő értékekre vonatkozik. Azonban a konfiguráció módosításait, például a hozzáférési beállítások be-és kikapcsolását, a Azure Resource Manager kezeli. Ezért a megfelelő szerepkörök, engedélyek, hálózati vezérlők és naplózás használatával korlátozhatja a hozzáférést a Resource Managerhez. További információ: [Azure monitor szerepkörök, engedélyek és biztonság](../roles-permissions-security.md).

> [!NOTE]
> A munkaterület-alapú Application Insights teljes biztonsága érdekében le kell zárnia a Application Insights erőforráshoz és a mögöttes Log Analytics munkaterülethez való hozzáférést.
>
> A kód szintű diagnosztika (Profiler/Debugger) esetében meg kell [adnia a saját Storage-fiókját](../app/profiler-bring-your-own-storage.md) a privát kapcsolat támogatásához.

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>A privát hivatkozások összes vagy semmi természetének kezelését
Ahogy azt a [saját hivatkozásának megtervezése](#planning-your-private-link-setup)című részben leírtak szerint, egy privát hivatkozás beállítása akár egyetlen erőforrásra is hatással van az adott hálózat összes Azure monitor erőforrására, valamint más, azonos DNS-t használó hálózatokra. Ez a viselkedés a bevezetési folyamat számára is kihívást jelenthet. Vegye figyelembe a következő lehetőségeket:

* A legegyszerűbb és legbiztonságosabb megközelítése az összes Application Insights-összetevő hozzáadása a AMPLS. Azokhoz az összetevőkhöz, amelyeken más hálózatokból is el szeretné érni a hozzáférést, ne hagyja az Igen értékre a "nyilvános internet-hozzáférés engedélyezése a betöltéshez/lekérdezéshez" jelzőt (az alapértelmezett érték).
* Hálózatok elkülönítése – ha a küllős virtuális hálózatok használatával (vagy a szolgáltatáshoz is igazodik), kövesse az [Azure-beli sugaras hálózati topológia](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)útmutatását. Ezután állítsa be a privát kapcsolat beállításait a megfelelő küllős virtuális hálózatok. Ügyeljen arra, hogy a DNS-zónák elkülönítése is megtörténjen, mivel a DNS-zónák más küllős hálózatokkal való megosztása a [DNS-felülbírálásokat](#the-issue-of-dns-overrides)eredményezi.
* Egyéni DNS-zónák használata adott alkalmazásokhoz – ez a megoldás lehetővé teszi, hogy a privát kapcsolaton keresztül válassza ki Application Insights összetevőket, miközben az összes többi forgalmat a nyilvános útvonalakon tartja.
    - Hozzon létre egy [Egyéni DNS-zónát](../../private-link/private-endpoint-dns.md), és adjon meg egy egyedi nevet, például Internal.monitor.Azure.com
    - Hozzon létre egy AMPLS és egy privát végpontot, és válassza a **nem** lehetőséget az automatikus integrálásra a magánhálózati DNS-sel
    - Nyissa meg a magánhálózati végpont-> DNS-konfigurációt, és tekintse át a teljes tartománynevek javasolt leképezését.
    - Válassza a konfiguráció hozzáadása lehetőséget, és válassza ki az imént létrehozott internal.monitor.azure.com zónát
    - Rekordok hozzáadása a ![ konfigurált DNS-zóna fenti képernyőképéhez](./media/private-link-security/private-endpoint-global-dns-zone.png)
    - Nyissa meg a Application Insights összetevőt, és másolja a [kapcsolati karakterláncát](../app/sdk-connection-string.md).
    - Azokat az alkalmazásokat vagy parancsfájlokat, amelyek privát hivatkozáson keresztül Szeretnék meghívni ezt az összetevőt, a kapcsolati karakterláncot a EndpointSuffix = Internal. monitor. Azure. com használatával kell használniuk.
* Végpontok leképezése a gazdagépeken a DNS helyett, hogy csak egy adott számítógépről/virtuális gépről férhessen hozzá a saját hálózatán:
    - Állítson be egy AMPLS és egy privát végpontot, és válassza ki, hogy **ne** legyen automatikus integráció a magánhálózati DNS-sel 
    - A fenti A rekordok konfigurálása olyan gépen, amelyen az alkalmazás fut a gazdagépek fájlban


## <a name="use-apis-and-command-line"></a>API-k és parancssor használata

A korábban ismertetett folyamatot Azure Resource Manager sablonok, REST és parancssori felületek használatával automatizálhatja.

A privát hivatkozások hatókörének létrehozásához és kezeléséhez használja a [REST API](/rest/api/monitor/private%20link%20scopes%20(preview)) vagy az [Azure CLI-t (az monitor Private-link-scope)](/cli/azure/monitor/private-link-scope).

A hálózati hozzáférés kezeléséhez használja a jelzőket `[--ingestion-access {Disabled, Enabled}]` és `[--query-access {Disabled, Enabled}]` [log Analytics munkaterületeket](/cli/azure/monitor/log-analytics/workspace) , vagy [Application Insights összetevőket](/cli/azure/ext/application-insights/monitor/app-insights/component).

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Egyéni naplók és IIS-napló gyűjtése privát kapcsolaton keresztül

A Storage-fiókok az egyéni naplók betöltési folyamatában használatosak. Alapértelmezés szerint a szolgáltatás által felügyelt Storage-fiókok használatosak. Az egyéni naplók privát hivatkozásokon való betöltéséhez azonban saját Storage-fiókokat kell használnia, és hozzá kell rendelnie őket Log Analytics munkaterülethez. Az ilyen fiókok [parancssorból](/cli/azure/monitor/log-analytics/workspace/linked-storage)történő beállításával kapcsolatos további részletekért tekintse meg a következő témakört:.

A saját Storage-fiók létrehozásával kapcsolatos további információkért lásd: [felhasználói tulajdonú Storage-fiókok a naplók](private-storage.md) betöltéséhez

## <a name="restrictions-and-limitations"></a>Korlátozások

### <a name="ampls"></a>AMPLS
A AMPLS objektum több korlátozást is figyelembe kell vennie, amikor megtervezi a privát kapcsolat beállítását:

* Egy VNet csak 1 AMPLS objektumhoz tud csatlakozni. Ez azt jelenti, hogy a AMPLS objektumnak hozzáférést kell biztosítania az összes olyan Azure Monitor erőforráshoz, amelyhez a VNet hozzáféréssel kell rendelkeznie.
* Egy Azure Monitor erőforrás (munkaterület vagy Application Insights összetevő) legfeljebb 5 AMPLSs tud csatlakozni.
* Egy AMPLS-objektum legfeljebb 50 Azure Monitor erőforráshoz tud csatlakozni.
* Egy AMPLS-objektum legfeljebb 10 privát végponthoz tud csatlakozni.

Tekintse [meg](#consider-limits) a határértékek részletesebb áttekintését.

### <a name="agents"></a>Ügynökök

A Windows-és Linux-ügynökök legújabb verzióit kell használni a Log Analytics munkaterületek biztonságos betöltésének támogatásához. A régebbi verziók nem tudják feltölteni a figyelési adatok privát hálózaton keresztüli feltöltését.

**Log Analytics Windows-ügynök**

Használja a Log Analytics Agent 10.20.18038.0 vagy újabb verzióját.

**A Log Analytics Linux-ügynöke**

Használja az ügynök 1.12.25 vagy újabb verzióját. Ha nem tudja, futtassa a következő parancsokat a virtuális gépen.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure Portal

A Azure Monitor-portál használatának, például a Application Insights és a Log Analytics használatához engedélyeznie kell a Azure Portal és a Azure Monitor bővítmények számára a magánhálózatok elérését. Vegye fel a **AzureActiveDirectory**, a **AzureResourceManager**, a **AzureFrontDoor. FirstParty** és a **AzureFrontDoor. frontend** [szolgáltatás címkéit](../../firewall/service-tags.md) a hálózati biztonsági csoportba.

### <a name="querying-data"></a>Adatok lekérdezése
Az [ `externaldata` operátor](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) nem támogatott privát kapcsolaton keresztül, mert beolvassa az adatokat a Storage-fiókokból, de nem garantálja, hogy a tárterületet privát módon éri el.

### <a name="programmatic-access"></a>Szoftveres hozzáférés

Ha a REST APIt, a [CLI](/cli/azure/monitor) -t vagy a PowerShellt a magánhálózaton lévő Azure monitor használatával szeretné használni, adja hozzá a **AzureActiveDirectory** és a **AzureResourceManager** [szolgáltatáshoz](../../virtual-network/service-tags-overview.md)a tűzfalhoz.  

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights SDK-letöltések a Content Delivery Networkből

Csomagolja be a JavaScript-kódot a parancsfájlba, hogy a böngésző ne kísérelje meg a kód letöltését a CDN-ből. Példa a [githubon](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>Böngésző DNS-beállításai

Ha privát kapcsolaton keresztül csatlakozik a Azure Monitor-erőforrásokhoz, ezen erőforrások felé irányuló forgalomnak a hálózaton konfigurált privát végponton kell haladnia. A magánhálózati végpont engedélyezéséhez frissítse a DNS-beállításokat a [Kapcsolódás privát végponthoz](#connect-to-a-private-endpoint)című részben leírtak szerint. Egyes böngészők a beállított beállítások helyett a saját DNS-beállításait használják. Előfordulhat, hogy a böngésző megpróbál csatlakozni Azure Monitor nyilvános végpontokhoz, és teljesen megkerüli a privát hivatkozást. Győződjön meg arról, hogy a böngészők beállításai nem felülbírálják vagy gyorsítótárazzák a régi DNS-beállításokat. 

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [privát tárterületről](private-storage.md)