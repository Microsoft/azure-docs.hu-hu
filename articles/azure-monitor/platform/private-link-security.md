---
title: Hálózatok biztonságos csatlakoztatása az Azure Monitorhoz az Azure Private Linkkel
description: Hálózatok biztonságos csatlakoztatása az Azure Monitorhoz az Azure Private Linkkel
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.subservice: ''
ms.openlocfilehash: a7464216649d6b482893693a1f182af5cf6e77ac
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508999"
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

> [!NOTE]
> Egyetlen Azure Monitor erőforrás több AMPLSs is tartozhat, de egyetlen VNet nem csatlakoztatható egynél több AMPLS. 

### <a name="the-issue-of-dns-overrides"></a>A DNS-felülbírálások hibája
A Log Analytics és a Application Insights globális végpontokat használ egyes szolgáltatásaihoz, ami azt jelenti, hogy a rendszer minden munkaterületre/összetevőre irányuló kérelmeket szolgál ki. A Application Insights például globális végpontot használ a naplók betöltéséhez, és mindkét Application Insights és Log Analytics globális végpontot használ a lekérdezési kérelmekhez.

Amikor beállít egy magánhálózati kapcsolati kapcsolatot, a rendszer úgy frissíti a DNS-t, hogy Azure Monitor végpontokat a VNet IP-tartományában lévő magánhálózati IP-címekre képezze. Ez a változás felülbírálja a végpontok korábbi leképezéseit, amelyek a következőkben ismertetett hasznos következményekkel járhatnak. 

## <a name="planning-based-on-your-network-topology"></a>Tervezés a hálózati topológia alapján

A Azure Monitor privát kapcsolat beállításának beállítása előtt vegye figyelembe a hálózati topológiát és a DNS-útválasztási topológiát. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor privát hivatkozás az összes Azure Monitor erőforrásra vonatkozik – ez az összes vagy semmi
Mivel egyes Azure Monitor végpontok globálisak, nem lehet létrehozni egy adott összetevőhöz vagy munkaterülethez tartozó magánhálózati kapcsolati kapcsolatot. Ehelyett, amikor egyetlen Application Insights összetevőhöz állít be egy magánhálózati hivatkozást, a DNS-rekordok frissülnek az **összes** Application Insights összetevő esetében. Az összetevők betöltésére vagy lekérésére tett kísérletek a privát hivatkozáson keresztül próbálnak meghaladni, és valószínűleg sikertelenek lesznek. Hasonlóképpen, ha egyetlen munkaterületre vonatkozó privát hivatkozást állít be, az összes Log Analytics lekérdezés a privát kapcsolat lekérdezési végpontján halad át (de nem a betöltési kérelmeket, amelyek munkaterület-specifikus végpontokkal rendelkeznek).

![DNS-felülbírálások diagramja egyetlen VNet](./media/private-link-security/dns-overrides-single-vnet.png)

Ez nem csak egy adott VNet érvényes, hanem minden olyan virtuális hálózatok esetében, amely ugyanazt a DNS-kiszolgálót használja (lásd [a DNS-felülbírálások problémát](#the-issue-of-dns-overrides)). Így például a naplók betöltésére irányuló kéréseket a rendszer mindig a privát kapcsolat útvonalán keresztül küldi el a Application Insights-összetevőknek. A AMPLS nem kapcsolódó összetevők sikertelenek lesznek a privát kapcsolat érvényesítése, és nem haladnak át.

**Gyakorlatilag ez azt jelenti, hogy a hálózatban lévő összes Azure Monitor-erőforrást egy privát kapcsolathoz kell csatlakoztatnia (adja hozzá őket a AMPLS), vagy egyiket sem.**

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor privát hivatkozás a teljes hálózatra vonatkozik
Néhány hálózat több virtuális hálózatok áll. Ha ezek a virtuális hálózatok ugyanazt a DNS-kiszolgálót használják, akkor felülbírálják egymás DNS-leképezéseit, és esetleg megszakítják egymás kommunikációját Azure Monitor (lásd [a DNS-felülbírálások problémáját](#the-issue-of-dns-overrides)). Végső soron csak az utolsó VNet tud kommunikálni a Azure Monitorval, mivel a DNS az ettől a virtuális hálózatok-tartományból Azure Monitor végpontokat képez le a privát IP-címekhez (ami esetleg nem érhető el más virtuális hálózatok).

![DNS-felülbírálások diagramja több virtuális hálózatok](./media/private-link-security/dns-overrides-multiple-vnets.png)

A fenti ábrán a VNet 10.0.1. x először csatlakozik a AMPLS1-hez, és leképezi a Azure Monitor globális végpontokat az IP-címekre a tartományból. Később a VNet 10.0.2. x csatlakozik a AMPLS2-hoz, és felülbírálja *ugyanazokat a globális végpontokat* , amelyek IP-címei a tartományból származnak. Mivel ezek a virtuális hálózatok nincsenek összevonással, az első VNet most nem éri el ezeket a végpontokat.

**Az azonos DNS-t használó virtuális hálózatok-ket közvetlenül vagy hub-VNet keresztül kell összeállítani. A nem támogatott virtuális hálózatok más DNS-kiszolgálót, DNS-továbbítókat vagy más mechanizmust is használhatnak a DNS-ütközés elkerüléséhez.**

### <a name="hub-spoke-networks"></a>Sugaras hálózatok
A küllős topológiák elkerülhetik a DNS-felülbírálások hibáját azáltal, hogy egy privát hivatkozást állítanak be a központi (fő) VNet, ahelyett, hogy külön magánhálózati hivatkozást kellene beállítani az egyes VNet. Ez a beállítás különösen akkor hasznos, ha a küllő virtuális hálózatok által használt Azure Monitor erőforrások meg vannak osztva. 

![Sugaras – egyetlen PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Érdemes lehet külön privát hivatkozásokat létrehozni a küllős virtuális hálózatok, például hogy az egyes VNet a figyelési erőforrások korlátozott készletéhez férhessenek hozzá. Ilyen esetekben létrehozhat egy dedikált privát végpontot és AMPLS az egyes VNet, de azt is ellenőriznie kell, hogy nem ugyanazt a DNS-kiszolgálót használják-e a DNS-felülbírálások elkerülése érdekében.


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

2. Válassza ki az előfizetést, az erőforráscsoportot és a végpont nevét, valamint azt a régiót, amelyben élni kíván. A régiónak ugyanabban a régióban kell lennie, mint a virtuális hálózatnak, amelyhez csatlakozni fog.

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

    ![Képernyőkép a Select Private Endpoint2 létrehozásáról](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Most létrehozott egy új, ehhez a AMPLS csatlakozó privát végpontot.

## <a name="configure-log-analytics"></a>A Log Analytics konfigurálása

Nyissa meg az Azure Portalt. A Log Analytics munkaterület erőforrás menüjében található egy **hálózati elkülönítés** nevű elem a bal oldali oldalon. Ebben a menüben két különböző állapotot is megadhat.

![LA hálózati elkülönítés](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Csatlakoztatott Azure Monitor privát kapcsolati hatókörök
A munkaterülethez csatlakozó összes hatókör megjelenik ezen a képernyőn. A hatókörökhöz való csatlakozás (AMPLSs) lehetővé teszi, hogy az egyes AMPLS csatlakoztatott virtuális hálózatról érkező hálózati forgalom elérje ezt a munkaterületet. A kapcsolat létrehozása a következővel azonos hatással van a hatókörre, ahogy azt a [Azure monitor erőforrások csatlakoztatása](#connect-azure-monitor-resources)során hajtottuk végre. Új kapcsolat hozzáadásához válassza a **Hozzáadás** lehetőséget, majd válassza ki a Azure monitor privát hivatkozás hatókörét. A kapcsolódáshoz válassza az **alkalmaz** lehetőséget. Vegye figyelembe, hogy a munkaterületek 5 AMPLS-objektumhoz kapcsolódhatnak, ahogy azt a [korlátozásokban és korlátozásokban](#restrictions-and-limitations)is említettük. 

### <a name="access-from-outside-of-private-links-scopes"></a>Hozzáférés a privát hivatkozások hatókörén kívülről
A lap alsó részén lévő beállítások vezérlik a nyilvános hálózatokról való hozzáférést, azaz a fent felsorolt hatókörökön keresztül nem csatlakoztatott hálózatokat. A **nyilvános hálózati hozzáférés engedélyezése** a betöltéshez **nem** blokkolja a csatlakoztatott hatókörökön kívüli gépekről származó naplók betöltését. A **nyilvános hálózati hozzáférés engedélyezése a lekérdezésekhez** **nem** blokkolja a hatókörökön kívüli gépekről érkező lekérdezéseket. Ez magában foglalja a munkafüzetek, irányítópultok, API-alapú ügyfél-élmények, a Azure Portalon belüli bepillantások és egyéb funkciók használatával történő lekérdezéseket. A Azure Portalon kívül futó tapasztalatok, és a lekérdezés Log Analytics az adatnak is futnia kell a privát csatolt VNET.

### <a name="exceptions"></a>Kivételek
A fentiekben leírtak szerint a hozzáférés korlátozása nem vonatkozik a Azure Resource Managerra, ezért a következő korlátozásokkal rendelkezik:
* Hozzáférés az adatokhoz – a nyilvános hálózatokból érkező lekérdezések letiltása vagy engedélyezése a legtöbb Log Analyticsi tapasztalatra vonatkozik, és egyes funkciók a Azure Resource Manageron keresztül kérdezik le az adatlekérdezéseket, ezért nem lesznek képesek az adatok lekérdezésére, kivéve, ha a privát kapcsolat beállításait a Resource Manager is alkalmazza (a szolgáltatás hamarosan elérhető). Ez magában foglalja például a Azure Monitor megoldásokat, a munkafüzeteket és az adattartalmakat, valamint az LogicApp-összekötőt.
* A munkaterület-kezelés – a munkaterület beállítását és a konfiguráció módosításait (beleértve a hozzáférési beállítások be-és kikapcsolását) a Azure Resource Manager kezeli. Korlátozza a munkaterület-felügyelet elérését a megfelelő szerepkörök, engedélyek, hálózati vezérlők és naplózás használatával. További információ: [Azure monitor szerepkörök, engedélyek és biztonság](roles-permissions-security.md).

> [!NOTE]
> A munkaterületre a [diagnosztikai beállításokon](diagnostic-settings.md) keresztül feltöltött naplók és mérőszámok egy biztonságos privát Microsoft-csatornán haladnak át, és ezeket a beállításokat nem szabályozzák.

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

Másodszor, azt is szabályozhatja, hogy ez az erőforrás Hogyan érhető el a korábban felsorolt privát kapcsolati hatókörökön kívülről. Ha a **nyilvános hálózati hozzáférés engedélyezése** a **nem** értékre van állítva, akkor a csatlakoztatott hatókörökön kívüli gépek vagy SDK-k nem tölthetnek fel adatot ebbe az összetevőbe. Ha a **nyilvános hálózati hozzáférés engedélyezése a** **nem** értékre van állítva, akkor a hatókörön kívüli gépek nem férnek hozzá a Application Insights erőforrásban lévő adatforrásokhoz. Az adatok közé tartozik az APM-naplók, a metrikák és az élő metrikai stream elérésének, valamint a többek között a munkafüzetek, az irányítópultok, a lekérdezési API-alapú ügyfél-élmények, a Azure Portal elemzések és egyebek. 

Vegye figyelembe, hogy a portálon kívüli felhasználási élményeknek is futniuk kell a felügyelt munkaterheléseket tartalmazó privát csatolt VNET belül. 

A megfigyelt számítási feladatokat üzemeltető erőforrásokat a privát hivatkozáshoz kell hozzáadnia. Az alábbi [dokumentációból](../../app-service/networking/private-endpoint.md) megtudhatja, hogyan teheti meg ezt a app Services.

A hozzáférés ezen a módon való korlátozása csak a Application Insights erőforrásban lévő értékekre vonatkozik. A konfiguráció módosításait, beleértve a hozzáférési beállítások be-és kikapcsolását is, Azure Resource Manager kezeli. Ehelyett a megfelelő szerepkörök, engedélyek, hálózati vezérlők és naplózás használatával korlátozza a hozzáférést a Resource Managerhez. További információ: [Azure monitor szerepkörök, engedélyek és biztonság](roles-permissions-security.md).

> [!NOTE]
> A munkaterület-alapú Application Insights teljes biztonsága érdekében le kell zárnia a Application Insights erőforráshoz és a mögöttes Log Analytics munkaterülethez való hozzáférést.
>
> A kód szintű diagnosztika (Profiler/Debugger) esetében meg kell adnia a saját Storage-fiókját a privát kapcsolat támogatásához. Ehhez a [dokumentációban](../app/profiler-bring-your-own-storage.md) olvashat.

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

Tekintse [meg](#consider-limits) a határértékek részletesebb áttekintését, valamint azt, hogyan tervezze meg a privát kapcsolat beállítását.

### <a name="agents"></a>Ügynökök

A Windows-és Linux-ügynökök legújabb verzióit magánhálózati hálózatokon kell használni a Log Analytics munkaterületek biztonságos betöltésének lehetővé tételéhez. A régebbi verziók nem tölthetik fel a belső hálózaton lévő megfigyelési adatok feltöltését.

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

### <a name="programmatic-access"></a>Szoftveres hozzáférés

Ha a REST APIt, a [CLI](/cli/azure/monitor) -t vagy a PowerShellt a magánhálózaton lévő Azure monitor használatával szeretné használni, adja hozzá a **AzureActiveDirectory** és a **AzureResourceManager** [szolgáltatáshoz](../../virtual-network/service-tags-overview.md)a tűzfalhoz.  

A címkék hozzáadásával olyan műveleteket hajthat végre, mint például a naplózási adatok lekérdezése, Log Analytics munkaterületek és AI-összetevők létrehozása és kezelése.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights SDK-letöltések a Content Delivery Networkből

Csomagolja be a JavaScript-kódot a parancsfájlba, hogy a böngésző ne kísérelje meg a kód letöltését a CDN-ből. Példa a [githubon](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>Böngésző DNS-beállításai

Ha privát kapcsolaton keresztül csatlakozik a Azure Monitor-erőforrásokhoz, ezen erőforrások felé irányuló forgalomnak a hálózaton konfigurált privát végponton kell haladnia. A magánhálózati végpont engedélyezéséhez frissítse a DNS-beállításokat a [Kapcsolódás privát végponthoz](#connect-to-a-private-endpoint)című részben leírtak szerint. Egyes böngészők a beállított beállítások helyett a saját DNS-beállításait használják. Előfordulhat, hogy a böngésző megpróbál csatlakozni Azure Monitor nyilvános végpontokhoz, és teljesen megkerüli a privát hivatkozást. Győződjön meg arról, hogy a böngészők beállításai nem felülbírálják vagy gyorsítótárazzák a régi DNS-beállításokat. 

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [privát tárterületről](private-storage.md)