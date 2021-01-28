---
title: Hálózatok biztonságos csatlakoztatása az Azure Monitorhoz az Azure Private Linkkel
description: Hálózatok biztonságos csatlakoztatása az Azure Monitorhoz az Azure Private Linkkel
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.subservice: ''
ms.openlocfilehash: 637e66956eadf57199d2e5191368d6355e2cd118
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941892"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Hálózatok biztonságos csatlakoztatása az Azure Monitorhoz az Azure Private Linkkel

Az [Azure Private link](../../private-link/private-link-overview.md) lehetővé teszi, hogy a privát végpontok használatával biztonságosan összekapcsolja az Azure Pásti-szolgáltatásokat a virtuális hálózattal. Számos szolgáltatás esetében csak egy végpontot állít be erőforrásként. A Azure Monitor azonban a különböző összekapcsolt szolgáltatások egyik konstellációja, amely együttműködik a számítási feladatok monitorozásával. Ennek eredményeképpen létrehozott egy Azure Monitor Private link (AMPLS) nevű erőforrást, amely lehetővé teszi a megfigyelési hálózat határainak meghatározását és a virtuális hálózathoz való csatlakozást. Ez a cikk azt ismerteti, hogy mikor kell használni és hogyan kell beállítani egy Azure Monitor privát hivatkozás hatókörét.

## <a name="advantages"></a>Előnyök

A privát hivatkozással a következőket teheti:

- A nyilvános hálózati hozzáférés megnyitása nélkül csatlakozhat a Azure Monitorhoz
- Győződjön meg arról, hogy a figyelési adatai csak a jogosult magánhálózatok használatával érhetők el
- A magánhálózati adatok kiszűrése megakadályozása a privát végponton keresztül kapcsolódó Azure Monitor erőforrások definiálásával
- A privát helyszíni hálózat biztonságos csatlakoztatása Azure Monitor ExpressRoute és privát kapcsolat használatával
- Az Microsoft Azure gerinc hálózatán belüli összes forgalom megőrzése

További információ:  [a privát hivatkozás legfontosabb előnyei](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Működés

Azure Monitor a privát hivatkozás hatóköre olyan csoportosítási erőforrás, amely egy vagy több privát végpont (és így a virtuális hálózatok) egy vagy több Azure Monitor erőforráshoz való csatlakoztatására szolgál. Az erőforrások közé tartoznak Log Analytics munkaterületek és Application Insights összetevők.

![Erőforrás-topológia ábrája](./media/private-link-security/private-link-topology-1.png)

> [!NOTE]
> Egyetlen Azure Monitor erőforrás több AMPLSs is tartozhat, de egyetlen VNet nem csatlakoztatható egynél több AMPLS. 

## <a name="planning-based-on-your-network"></a>Tervezés a hálózat alapján

A AMPLS-erőforrások beállítása előtt vegye figyelembe a hálózat elkülönítésének követelményeit. Értékelje ki a virtuális hálózatok hozzáférését a nyilvános internethez, valamint az egyes Azure Monitor erőforrások (azaz Application Insights összetevők és Log Analytics munkaterületek) hozzáférési korlátozásait.

> [!NOTE]
> A központilag küllő hálózatok vagy a több hálózattal rendelkező hálózatok bármely más topológiája beállíthat egy privát kapcsolatot a hub (fő) VNet és a kapcsolódó Azure Monitor erőforrások között, és nem kell minden egyes VNet privát hivatkozást beállítania. Ez különösen akkor hasznos, ha az ilyen hálózatok által használt Azure Monitor erőforrások meg vannak osztva. Ha azonban engedélyezni szeretné az egyes VNet számára a figyelési erőforrások különálló készletének elérését, hozzon létre egy privát hivatkozást az egyes hálózatok dedikált AMPLS.

### <a name="evaluate-which-virtual-networks-should-connect-to-a-private-link"></a>Annak kiértékelése, hogy mely virtuális hálózatok csatlakozzanak egy privát kapcsolathoz

Kezdje azzal, hogy kiértékeli, hogy a virtuális hálózatok (virtuális hálózatok-EK) közül melyiknek van korlátozott hozzáférése az internethez. Előfordulhat, hogy az ingyenes internettel rendelkező virtuális hálózatok nem igényelnek privát hivatkozást a Azure Monitor erőforrásainak eléréséhez. A virtuális hálózatok-hez csatlakozó figyelési erőforrások korlátozhatják a bejövő forgalmat, és privát kapcsolati kapcsolatot igényelnek (a napló betöltéséhez vagy lekérdezéséhez). Ilyen esetekben még a nyilvános internethez hozzáféréssel rendelkező VNet is csatlakoznia kell ezen erőforrásokhoz egy privát kapcsolaton keresztül, illetve egy AMPLS keresztül.

### <a name="evaluate-which-azure-monitor-resources-should-have-a-private-link"></a>Annak kiértékelése, hogy mely Azure Monitor erőforrásoknak legyen privát hivatkozása

Tekintse át a Azure Monitor összes erőforrását:

- Engedélyezi az erőforrás a naplók betöltését csak adott virtuális hálózatok található erőforrásokból?
- Csak bizonyos virtuális hálózatok lévő ügyfelek tudják lekérdezni az erőforrást?

Ha az ezekre a kérdésekre adott válasz igen, állítsa be a korlátozásokat a Log Analytics munkaterületek [konfigurálása](#configure-log-analytics) és a [Application Insights-összetevők konfigurálása](#configure-application-insights) és az erőforrások hozzárendelése egyetlen vagy több AMPLS. A figyelési erőforrásokhoz hozzáférő virtuális hálózatoknak rendelkeznie kell egy privát végponttal, amely csatlakozik a megfelelő AMPLS.
Ne feledje, hogy ugyanazokat a munkaterületeket vagy alkalmazásokat több AMPLS is összekapcsolhatjuk, hogy azok a különböző hálózatok számára is elérhetők legyenek.

### <a name="group-together-monitoring-resources-by-network-accessibility"></a>Erőforrások figyelése a hálózat kisegítő lehetőségeinek együttes használatával

Mivel minden VNet csak egy AMPLS-erőforráshoz tud csatlakozni, össze kell csoportosítani azokat a figyelési erőforrásokat, amelyeknek elérhetőnek kell lenniük ugyanazon hálózatok számára. A csoportosítás kezelésének legegyszerűbb módja, ha VNet egy AMPLS hoz létre, és kiválasztja a hálózathoz való kapcsolódáshoz szükséges erőforrásokat. Az erőforrások csökkentése és a kezelhetőség javítása érdekében azonban érdemes lehet egy AMPLS használni a hálózatokon keresztül.

Ha például a belső virtuális hálózatok VNet1 és VNet2 csatlakozniuk kell a munkaterületekhez, a Workspace1-t és a Workspace2-t, Application Insights Application Insights pedig a 3. összetevőt, akkor a három erőforrást ugyanahhoz a AMPLS kell rendelni. Ha a VNet3 csak a Workspace1 elérését szeretné elérni, hozzon létre egy másik AMPLS-erőforrást, rendeljen hozzá Workspace1, és kapcsolódjon a VNet3 az alábbi ábrán látható módon:

![Topológia AMPLS diagramja](./media/private-link-security/ampls-topology-a-1.png)

![B AMPLS-topológia ábrája](./media/private-link-security/ampls-topology-b-1.png)

### <a name="consider-limits"></a>Határértékek megfontolása

A magánhálózati kapcsolat beállításakor figyelembe kell venni néhány korlátozást:

* Egy VNet csak 1 AMPLS objektumhoz tud csatlakozni. Ez azt jelenti, hogy a AMPLS objektumnak hozzáférést kell biztosítania az összes olyan Azure Monitor erőforráshoz, amelyhez a VNet hozzáféréssel kell rendelkeznie.
* Egy Azure Monitor erőforrás (munkaterület vagy Application Insights összetevő) legfeljebb 5 AMPLSs tud csatlakozni.
* Egy AMPLS-objektum legfeljebb 50 Azure Monitor erőforráshoz tud csatlakozni.
* Egy AMPLS-objektum legfeljebb 10 privát végponthoz tud csatlakozni.

Az alábbi topológiában:
* Minden VNet csak **1** AMPLS objektumhoz csatlakozik.
* A B AMPLS két virtuális hálózatok (VNet2 és VNet3) privát végpontokhoz csatlakozik, 2/10 (20%) használatával a lehetséges privát végponti kapcsolataikat.
* A AMPLS két munkaterülethez és egy alkalmazás-Insight összetevőhöz csatlakozik, 3/50 (6%) használatával a lehetséges Azure Monitor erőforrások kapcsolatai.
* A Workspace2 az 2/5-es és A AMPLS-es AMPLS-hez csatlakozik, a (40%) használatával a lehetséges AMPLS-kapcsolatainak száma.

![AMPLS-korlátok diagramja](./media/private-link-security/ampls-limits.png)

> [!NOTE]
> Bizonyos hálózati topológiákban (főként a hub által küllő) a 10 virtuális hálózatok-korlátot gyorsan elérheti egyetlen AMPLS. Ilyen esetekben javasoljuk, hogy különálló kapcsolat helyett megosztott magánhálózati kapcsolatot használjon. Hozzon létre egyetlen privát végpontot a hub-hálózaton, kapcsolja össze a AMPLS, és a megfelelő hálózatokat a hub-hálózatra.

![Sugaras – egyetlen PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

## <a name="example-connection"></a>Példa a kapcsolatok

Első lépésként hozzon létre egy Azure Monitor privát hivatkozás hatókör-erőforrást.

1. Nyissa meg az **erőforrás létrehozása** a Azure Portalban, és keresse meg **Azure monitor privát hivatkozás hatókörét**.

   ![Azure Monitor privát hivatkozás hatókörének keresése](./media/private-link-security/ampls-find-1c.png)

2. Kattintson a **Létrehozás** gombra.
3. Válasszon egy előfizetést és egy erőforráscsoportot.
4. Adja meg a AMPLS nevét. Érdemes olyan nevet használni, amely törli a hatókört és a biztonsági határt, hogy valaki ne szakítsa meg véletlenül a hálózati biztonsági határokat. Például: "AppServerProdTelem".
5. Kattintson a **Felülvizsgálat + létrehozás** elemre. 

   ![Azure Monitor privát hivatkozás hatókörének létrehozása](./media/private-link-security/ampls-create-1d.png)

6. Hagyja meg az érvényesítési fázist, majd kattintson a **Létrehozás** gombra.

### <a name="connect-azure-monitor-resources"></a>Azure Monitor-erőforrások összekötése

Azure Monitor erőforrások (Log Analytics-munkaterületek és Application Insights-összetevők) összekapcsolhatók a AMPLS.

1. A Azure Monitor privát hivatkozás hatókörében kattintson a bal oldali menüben található **Azure monitor erőforrások** elemre. Kattintson a **Hozzáadás** gombra.
2. Adja hozzá a munkaterületet vagy összetevőt. A **Hozzáadás** gombra kattintva megjelenik egy párbeszédpanel, ahol kiválaszthatja Azure monitor erőforrásait. Böngészhet az előfizetések és az erőforráscsoportok között, vagy beírhatja a nevét a szűréshez. Válassza ki a munkaterületet vagy összetevőt, és kattintson az **alkalmaz** gombra a hatókörhöz való hozzáadásához.

    ![A hatókör kiválasztása UX képernyőkép](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Azure Monitor erőforrások törléséhez először le kell választania azokat minden olyan AMPLS-objektumról, amelyhez csatlakoznak. Nem lehet törölni egy AMPLS kapcsolódó erőforrásokat.

### <a name="connect-to-a-private-endpoint"></a>Kapcsolódás privát végponthoz

Most, hogy rendelkezik a AMPLS kapcsolódó erőforrásokkal, hozzon létre egy privát végpontot a hálózat csatlakoztatásához. Ezt a feladatot a [Azure Portal Private link Centerben](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)vagy a Azure monitor privát hivatkozás hatókörén belül hajthatja végre, ahogy az ebben a példában is látható.

1. A hatókör-erőforrásban kattintson a bal oldali erőforrás menüben található **privát végponti kapcsolatok** elemre. A végpont létrehozási folyamatának elindításához kattintson a **privát végpont** elemre. A privát kapcsolat központban elindított kapcsolatokat itt is jóváhagyhatja, ha kiválasztja őket, és a **jóváhagyás** gombra kattint.

    ![A Private Endpoint Connections UX képernyőképe](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Válassza ki az előfizetést, az erőforráscsoportot és a végpont nevét, valamint azt a régiót, amelyben élni kíván. A régiónak ugyanabban a régióban kell lennie, mint a virtuális hálózatnak, amelyhez csatlakozni fog.

3. Kattintson a **Tovább gombra: erőforrás**. 

4. Az erőforrás képernyőjén

   a. Válassza ki az **előfizetést** , amely tartalmazza a Azure monitor saját hatókörű erőforrását. 

   b. Az **erőforrástípus mezőben** válassza a **Microsoft. ininsights/privateLinkScopes** lehetőséget. 

   c. Az **erőforrás** legördülő menüben válassza ki a korábban létrehozott saját hivatkozás hatókörét. 

   d. Kattintson a **Tovább gombra: konfigurációs >**.
      ![Képernyőkép a privát végpont létrehozása elemről](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. A konfigurációs ablaktáblán

   a.    Válassza ki azt a **virtuális hálózatot** és **alhálózatot** , amelyhez csatlakozni szeretne a Azure monitor erőforrásaihoz. 
 
   b.    Válassza az **Igen** lehetőséget a **saját DNS-zónába való integráláshoz**, és hagyja, hogy automatikusan létrehozzon egy új saját DNS zónát. Előfordulhat, hogy a tényleges DNS-zónák eltérnek az alábbi képernyőképen láthatótól. 
   > [!NOTE]
   > Ha a **nem** lehetőséget választja, és manuálisan szeretné kezelni a DNS-rekordokat, először fejezze be a privát kapcsolat beállítását – beleértve ezt a privát végpontot és a AMPLS-konfigurációt. Ezután konfigurálja a DNS-t az [Azure-beli privát végpont DNS-konfigurálása](../../private-link/private-endpoint-dns.md) című dokumentumban leírt utasítások szerint. Ügyeljen arra, hogy ne hozzon létre üres rekordokat a privát kapcsolat beállításának előkészítése közben. Az Ön által létrehozott DNS-rekordok felülírják a meglévő beállításokat, és hatással vannak az Azure Monitorral létesített kapcsolatra.
 
   c.    Kattintson a **Felülvizsgálat + létrehozás** elemre.
 
   d.    Az érvényesítési fázis engedélyezése. 
 
   e.    Kattintson a **Létrehozás** lehetőségre. 

    ![Képernyőkép a Select Private Endpoint2 létrehozásáról](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Ezzel létrehozott egy új privát végpontot, amely ehhez a Azure Monitor privát kapcsolati hatókörhöz csatlakozik.

## <a name="configure-log-analytics"></a>A Log Analytics konfigurálása

Nyissa meg az Azure Portalt. A Log Analytics munkaterület-erőforrásban található a bal oldali menüpont **hálózati elkülönítése** . Ebben a menüben két különböző állapotot is megadhat.

![LA hálózati elkülönítés](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Csatlakoztatott Azure Monitor privát kapcsolati hatókörök
A munkaterülethez csatlakozó összes hatókör megjelenik ezen a képernyőn. A hatókörökhöz való csatlakozás (AMPLSs) lehetővé teszi, hogy az egyes AMPLS csatlakoztatott virtuális hálózatról érkező hálózati forgalom elérje ezt a munkaterületet. A kapcsolat létrehozása a következővel azonos hatással van a hatókörre, ahogy azt a [Azure monitor erőforrások csatlakoztatása](#connect-azure-monitor-resources)során hajtottuk végre. Új kapcsolat hozzáadásához kattintson a **Hozzáadás** gombra, és válassza ki a Azure monitor privát hivatkozás hatókörét. A kapcsolódáshoz kattintson az **alkalmaz** gombra. Vegye figyelembe, hogy a munkaterület 5 AMPLS objektumhoz tud csatlakozni, ahogy azt a [határértékek megfontolása](#consider-limits)is ismerteti. 

### <a name="access-from-outside-of-private-links-scopes"></a>Hozzáférés a privát hivatkozások hatókörén kívülről
A lap alsó részén lévő beállítások vezérlik a nyilvános hálózatokról való hozzáférést, azaz a fent felsorolt hatókörökön keresztül nem csatlakoztatott hálózatokat. Ha a **nem** értékre állítja a **nyilvános hálózati hozzáférés engedélyezése lehetőséget** , akkor a csatlakoztatott hatókörökön kívüli gépek nem tölthetnek fel adatot erre a munkaterületre. Ha a **nyilvános hálózati hozzáférés engedélyezése a** **nem** értékre van állítva, akkor a hatókörön kívüli gépek nem férhetnek hozzá a munkaterületen lévő adatforrásokhoz, ami azt jelenti, hogy nem fogja tudni lekérdezni a munkaterület adatforrásait. Ez magában foglalja a munkafüzetek, irányítópultok, API-alapú ügyfél-élmények, a Azure Portalban található bepillantások és egyéb funkciók lekérdezéseit. A Azure Portalon kívül futó tapasztalatok, és a lekérdezés Log Analytics az adatnak is futnia kell a privát csatolt VNET.

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

Nyissa meg az Azure Portalt. A Azure Monitor Application Insights összetevő-erőforrás a bal oldali menüpont **hálózati elkülönítése** . Ebben a menüben két különböző állapotot is megadhat.

![AI hálózati elkülönítés](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Először is csatlakoztathatja ezt a Application Insights-erőforrást, hogy Azure Monitor privát hivatkozás hatókörét, amelyhez hozzáféréssel rendelkezik. Kattintson a **Hozzáadás** gombra, és válassza ki a **Azure monitor privát hivatkozás hatókörét**. A kapcsolódáshoz kattintson az Alkalmaz gombra. Az összes csatlakoztatott hatókör megjelenik ezen a képernyőn. Ez a kapcsolat lehetővé teszi, hogy a csatlakoztatott virtuális hálózatok hálózati forgalma elérje ezt az összetevőt. A kapcsolat létrehozása ugyanaz, mint a [Azure monitor erőforrások csatlakoztatásakor](#connect-azure-monitor-resources)a hatókörhöz való csatlakozáskor. 

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

## <a name="collect-custom-logs-over-private-link"></a>Egyéni naplók gyűjtése privát kapcsolaton keresztül

A Storage-fiókok az egyéni naplók betöltési folyamatában használatosak. Alapértelmezés szerint a szolgáltatás által felügyelt Storage-fiókok használatosak. Az egyéni naplók privát hivatkozásokon való betöltéséhez azonban saját Storage-fiókokat kell használnia, és hozzá kell rendelnie őket Log Analytics munkaterülethez. Az ilyen fiókok [parancssorból](/cli/azure/monitor/log-analytics/workspace/linked-storage)történő beállításával kapcsolatos további részletekért tekintse meg a következő témakört:.

A saját Storage-fiók létrehozásával kapcsolatos további információkért lásd: [felhasználói tulajdonú Storage-fiókok a naplók](private-storage.md) betöltéséhez

## <a name="restrictions-and-limitations"></a>Korlátozások

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

Ha privát kapcsolaton keresztül csatlakozik a Azure Monitor-erőforrásokhoz, ezen erőforrás felé irányuló forgalomnak a hálózaton konfigurált privát végponton kell haladnia. A magánhálózati végpont engedélyezéséhez frissítse a DNS-beállításokat a [Kapcsolódás privát végponthoz](#connect-to-a-private-endpoint)című részben leírtak szerint. Egyes böngészők a beállított beállítások helyett a saját DNS-beállításait használják. Előfordulhat, hogy a böngésző megpróbál csatlakozni Azure Monitor nyilvános végpontokhoz, és teljesen megkerüli a privát hivatkozást. Győződjön meg arról, hogy a böngészők beállításai nem felülbírálják vagy gyorsítótárazzák a régi DNS-beállításokat. 

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [privát tárterületről](private-storage.md)
