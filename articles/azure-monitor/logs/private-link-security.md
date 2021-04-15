---
title: Hálózatok biztonságos csatlakoztatása az Azure Monitorhoz az Azure Private Linkkel
description: Hálózatok biztonságos csatlakoztatása az Azure Monitorhoz az Azure Private Linkkel
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 43707a99792ae3c4d817f47d770629287b8a774b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374335"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Hálózatok biztonságos csatlakoztatása az Azure Monitorhoz az Azure Private Linkkel

[Azure Private Link](../../private-link/private-link-overview.md) lehetővé teszi az Azure PaaS-szolgáltatások biztonságos csatolását a virtuális hálózathoz privát végpontok használatával. Sok szolgáltatáshoz csak be kell állítania egy végpontot erőforrásonként. A Azure Monitor azonban a különböző összekapcsolt szolgáltatások egyik fontos része, amelyek együtt figyelik a számítási feladatokat. Ennek eredményeképpen kiépítettünk egy Azure Monitor Private Link (AMPLS) nevű erőforrást. Az AMPLS segítségével meghatározhatja a monitorozási hálózat határait, és csatlakozhat a virtuális hálózathoz. Ez a cikk bemutatja, mikor használható, és hogyan állíthat be Azure Monitor Private Link hatókört.

## <a name="advantages"></a>Előnyök

A Private Link a következőt tudja:

- Privát csatlakozás Azure Monitor nyilvános hálózati hozzáférés megnyitása nélkül
- Győződjön meg arról, hogy a monitorozási adatok csak engedélyezett magánhálózaton keresztül érhetők el
- A magánhálózatok adatszivárgásának megakadályozása a privát végponton keresztül Azure Monitor erőforrások meghatározásával
- A helyszíni magánhálózat biztonságos csatlakoztatása Azure Monitor ExpressRoute és Private Link
- Minden forgalom a gerinchálózaton Microsoft Azure belül marad

További információ:  [A](../../private-link/private-link-overview.md#key-benefits)Private Link.

## <a name="how-it-works"></a>Működés

Azure Monitor Private Link hatókör (AMPLS) privát végpontokat (és az őket tartalmazó virtuális hálózatokat) csatlakoztat egy vagy több Azure Monitor-erőforráshoz – Log Analytics-munkaterületek és Application Insights összetevők.

![Alapszintű erőforrás-topológia ábrája](./media/private-link-security/private-link-basic-topology.png)

* A virtuális hálózat privát végpontja lehetővé teszi, hogy Azure Monitor végpontokat magánhálózati IP-címekkel érje el a hálózat készletéből ahelyett, hogy a végpontok nyilvános IP-címeit használná. Ez lehetővé teszi, hogy továbbra is használja a Azure Monitor anélkül, hogy megnyitja a virtuális hálózatát a szükséges kimenő forgalom számára. 
* A privát végpontról a Azure Monitor felé az adatforgalom a Microsoft Azure lesz, és nem nyilvános hálózatokra lesz irányítva. 
* Minden munkaterületet vagy összetevőt konfigurálhat úgy, hogy engedélyezze vagy megtagadja a nyilvános hálózatokról származó be- és lekérdezéseket. Ez erőforrásszintű védelmet biztosít, hogy szabályozni tudja az adott erőforrásokhoz való forgalmat.

> [!NOTE]
> Egyetlen Azure Monitor erőforrás több AMPLS-hez is tartozhat, de egyetlen virtuális hálózat nem csatlakoztatható több AMPLS-hez. 

## <a name="planning-your-private-link-setup"></a>A Private Link megtervezése

A hálózati Azure Monitor Private Link beállítása előtt gondolja át a hálózati topológiát, és különösen a DNS útválasztási topológiáját. 

### <a name="the-issue-of-dns-overrides"></a>A DNS-felülbírálások problémája
Egyes Azure Monitor szolgáltatások globális végpontokat használnak, ami azt jelenti, hogy bármely munkaterületre/összetevőre vonatkozó kéréseket szolgálnak ki. Néhány példa a Application Insights végpontja, valamint a Application Insights Log Analytics lekérdezési végpontja.

Amikor beállít egy Private Link kapcsolatot, a RENDSZER frissíti a DNS-t, hogy Azure Monitor végpontokat magánhálózati IP-címekre leképezve a virtuális hálózat IP-címtartományában. Ez a módosítás felülírja ezen végpontok korábbi leképezését, ami jelentős következményekkel járhat– erről alább olvashat. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor Private Link összes erőforrásra Azure Monitor vonatkozik – mind vagy semmi
Mivel egyes Azure Monitor végpontok globálisak, lehetetlen létrehozni Private Link kapcsolatot egy adott összetevő vagy munkaterület számára. Ehelyett ha egyetlen Private Link vagy Log Analytics-munkaterületre Application Insights, a DNS-rekordok az összes  Application Insights frissülnek. Az összetevők be- vagy lekérdezésére tett minden kísérlet a Private Link sikertelen lesz. A Log Analytics esetében a be- és konfigurációs végpontok munkaterület-specifikusak, ami azt jelenti, hogy a privát kapcsolat beállítása csak a megadott munkaterületeken érvényes. Más munkaterületek be- és konfigurációja az alapértelmezett nyilvános Log Analytics-végpontokra lesz irányítva.

![Egyetlen virtuális hálózat DNS-felülbírálásokat ábrázoló ábrája](./media/private-link-security/dns-overrides-single-vnet.png)

Ez nem csak egy adott virtuális hálózatra igaz, hanem az összes olyan virtuális hálózatra is, amely ugyanazokkal a DNS-kiszolgálóval osztozik (lásd: [A DNS-felülbírálások problémája).](#the-issue-of-dns-overrides) Így például a naplók bármely összetevőnek való Application Insights mindig az útvonalon keresztül lesz Private Link kérés. Az AMPLS-hez nem kapcsolódó összetevők nem Private Link ellenőrzést, és nem mennek végig.

> [!NOTE]
> Zárásképpen: Miután a Private Link kapcsolatot hoz létre egyetlen erőforrással, az a hálózaton Azure Monitor erőforrásokra is vonatkozik. A Application Insights az "Összes vagy Semmi" lehet. Ez gyakorlatilag azt jelenti, hogy a Application Insights összes erőforrását hozzá kell adni az AMPLS-hez, vagy egyiket sem.
> 
> Az adatkiszivárgási kockázatok kezelése érdekében azt javasoljuk, hogy adja hozzá az összes Application Insights- és Log Analytics-erőforrást az AMPLS-hez, és a lehető legnagyobb mértékben blokkolja a hálózatokból bejövő forgalmat.

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor Private Link a teljes hálózatra vonatkozik
Egyes hálózatok több virtuális hálózatból állnak. Ha a virtuális hálózatok ugyanazt a DNS-kiszolgálót használják, felülírják egymás DNS-leképezéseit, és esetleg megszakítják egymás és a Azure Monitor közötti kommunikációt (lásd: A DNS-felülbírálások [problémája).](#the-issue-of-dns-overrides) Végső soron csak az utolsó virtuális hálózat fog tudni kommunikálni az Azure Monitor-val, mivel Azure Monitor DNS Azure Monitor-végpontokat leképez magánhálózati IP-címekkel erről a VNet-tartományról (amely más virtuális hálózatokról nem mindig lesz elérhető).

![Több virtuális hálózat DNS-felülbírálásokat ábrázoló diagramja](./media/private-link-security/dns-overrides-multiple-vnets.png)

A fenti ábrán a 10.0.1.x virtuális hálózat először csatlakozik az AMPLS1-hez, és leképezi a Azure Monitor globális végpontokat IP-címekre a tartományból. Később a 10.0.2.x virtuális hálózat csatlakozik az AMPLS2-hez, és felülbírálja ugyanazon globális végpontok DNS-leképezését ip-címekkel a tartományból.  Mivel ezek a virtuális hálózatok nincsenek társviszonyban, az első virtuális hálózat most nem éri el ezeket a végpontokat.

> [!NOTE]
> Zárásképpen: Az AMPLS beállítása az összes olyan hálózatra hatással van, amely azonos DNS-zónával osztozik. Annak érdekében, hogy ne bírálják felül egymás DNS-végpontleképezéseit, ajánlott egyetlen privát végpontot beállítani egy társhálózaton (például egy központi virtuális hálózaton), vagy elkülöníti a hálózatokat a DNS szintjén (például DNS-továbbítók vagy teljesen különálló DNS-kiszolgálók használatával).

### <a name="hub-spoke-networks"></a>Küllős hálózatok
A küllős topológiák elkerülik a DNS-felülbírálások kérdését, ha egy Private Link-et a központi (fő) virtuális hálózatra be kell írni ahelyett, hogy külön-külön Private Link külön-külön egy virtuális hálózathoz. Ez a beállítás különösen akkor hasznos, Azure Monitor küllő virtuális hálózatok által használt összes erőforrás meg van osztva. 

![Küllős-egyszeres PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Előfordulhat, hogy szándékosan inkább különálló privát kapcsolatokat szeretne létrehozni a küllő virtuális hálózatokhoz, például hogy az egyes virtuális hálózatok hozzáférjenek a monitorozási erőforrások korlátozott készlete számára. Ilyen esetekben dedikált privát végpontot és AMPLS-t hozhat létre az egyes virtuális hálózatokhoz, de azt is ellenőriznie kell, hogy nem osztoznak-e ugyanazokkal a DNS-zónákkal a DNS-felülbírálások elkerülése érdekében.


### <a name="consider-limits"></a>Korlátok figyelembe venni

Ahogy az a [Korlátozások és korlátozások](#restrictions-and-limitations)alatt szerepel, az AMPLS objektumnak több korlátja is van, amelyek az alábbi topológiában láthatók:
* Minden virtuális hálózat csak **1** AMPLS-objektumhoz csatlakozik.
* Az AMPLS B két virtuális hálózat (VNet2 és VNet3) privát végpontjaihoz csatlakozik a 10 lehetséges privát végponti kapcsolat közül 2 használatával.
* Az AMPLS A két munkaterülethez és egy Application Insight-összetevőhez csatlakozik, az 50 lehetséges erőforrás-kapcsolat Azure Monitor használatával.
* A 2. munkaterület az 5 lehetséges AMPLS-kapcsolatból 2 használatával csatlakozik az AMPLS A-hez és az AMPLS B-hez.

![AmpLS-korlátok ábrája](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>Példa kapcsolatra

Először hozzon létre egy Azure Monitor Private Link Scope erőforrást.

1. Az **erőforráscsoportban keresse meg az** Erőforrás létrehozása Azure Portal, és keressen rá a **Azure Monitor Private Link gombra.**

   ![Hatókör Azure Monitor Private Link megkeresve](./media/private-link-security/ampls-find-1c.png)

2. Válassza a **Létrehozás** lehetőséget.
3. Válasszon ki egy előfizetést és egy erőforráscsoportot.
4. Nevezze el az AMPLS-t. A legjobb, ha egy kifejező és egyértelmű nevet használ, például az "AppServerProdTelem" nevet.
5. Válassza a **Felülvizsgálat és létrehozás** lehetőséget. 

   ![Hatókör Azure Monitor Private Link létrehozása](./media/private-link-security/ampls-create-1d.png)

6. Hagyja, hogy az érvényesítés le legyen edzve, majd válassza a **Létrehozás lehetőséget.**

### <a name="connect-azure-monitor-resources"></a>Csatlakozás Azure Monitor erőforrásokhoz

Az Azure Monitor (Log Analytics-munkaterületek és Application Insights összetevők) csatlakoztatása az AMPLS-hez.

1. A Azure Monitor Private Link a bal **oldali Azure Monitor válassza** az Erőforrások elemet. Válassza a **Hozzáadás** gombot.
2. Adja hozzá a munkaterületet vagy összetevőt. A Hozzáadás **gombra** kattintva megjelenik egy párbeszédpanel, ahol kiválaszthatja Azure Monitor erőforrásokat. Tallózhat az előfizetések és az erőforráscsoportok között, vagy begépelheti a nevüket a szűréshez. Válassza ki a munkaterületet vagy összetevőt, és válassza az **Alkalmaz** lehetőséget, hogy hozzáadja őket a hatóköréhez.

    ![Képernyőkép a hatókör kiválasztásáról felhasználói felületről](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> A Azure Monitor törléséhez először le kell bontani azokat az AMPLS-objektumokról, amelyekhez csatlakoznak. Az AMPLS-hez csatlakoztatott erőforrások nem törölhetők.

### <a name="connect-to-a-private-endpoint"></a>Csatlakozás privát végponthoz

Most, hogy az erőforrások csatlakoztatva vannak az AMPLS-hez, hozzon létre egy privát végpontot a hálózat csatlakoztatásához. Ezt a feladatot a [](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)következő Azure Portal Private Link használhatja, Azure Monitor Private Link hatókörben, ahogyan ebben a példában is tette.

1. A hatókör-erőforrás bal **oldali** erőforrásmenüjében válassza a Privát végponti kapcsolatok lehetőséget. Válassza **a Privát végpont** lehetőséget a végpont létrehozási folyamatának elkezdődjön. A következő központban elindított kapcsolatokat is jóváhagyhatja: jelölje ki Private Link, majd válassza a Jóváhagyás **et.**

    ![Privát végpontkapcsolatok felhasználói felületének képernyőképe](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Válassza ki az előfizetést, az erőforráscsoportot és a végpont nevét, valamint azt a régiót, ahol a végpontnak lennie kell. A régiónak ugyanannak a régiónak kell lennie, mint a virtuális hálózatnak, amelyhez csatlakoztatja.

3. Válassza **a Tovább: Erőforrás lehetőséget.** 

4. Az Erőforrás képernyőn:

   a. Válassza ki **azt az** előfizetést, amely a Azure Monitor a Privát hatókör erőforrást. 

   b. Az **erőforrástípushoz válassza** a **Microsoft.insights/privateLinkScopes lehetőséget.** 

   c. Az erőforrás **legördülő** menüből válassza ki a Private Link korábban létrehozott hatókört. 

   d. Válassza **a Tovább: Konfigurációs >** lehetőséget.
      ![A Privát végpont létrehozása kiválasztásának képernyőképe](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. A konfigurációs panelen:

   a.    Válassza ki azt  **a virtuális** hálózatot és alhálózatot, amelyről csatlakozni szeretne a Azure Monitor erőforrásaihoz. 
 
   b.    Válassza **az Igen** lehetőséget a Privát **DNS-zónával való integrációhoz,** és hagyja, hogy az automatikusan hozzon létre egy új saját DNS zónában. A tényleges DNS-zónák különbözhetnek az alábbi képernyőképen láthatótól. 
   > [!NOTE]
   > Ha a Nem **lehetőséget** választja, és inkább manuálisan szeretné kezelni a DNS-rekordokat, először Private Link a privát végpontot és az AMPLS-konfigurációt is beleértve. Ezután konfigurálja a DNS-t az [Azure-beli privát végpont DNS-konfigurálása](../../private-link/private-endpoint-dns.md) című dokumentumban leírt utasítások szerint. Ügyeljen arra, hogy ne hozzon létre üres rekordokat a privát kapcsolat beállításának előkészítése közben. Az Ön által létrehozott DNS-rekordok felülírják a meglévő beállításokat, és hatással vannak az Azure Monitorral létesített kapcsolatra.
 
   c.    Válassza az **Áttekintés + létrehozás** lehetőséget.
 
   d.    Hagyja, hogy az érvényesítés haladjon. 
 
   e.    Válassza a **Létrehozás** lehetőséget. 

    ![A Privát végpont részletei kiválasztásának képernyőképe.](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Ezzel létrehozott egy új privát végpontot, amely ehhez az AMPLS-hez csatlakozik.

## <a name="review-and-validate-your-private-link-setup"></a>A beállítások áttekintése és Private Link ellenőrzése

### <a name="reviewing-your-endpoints-dns-settings"></a>A végpont DNS-beállításainak áttekintése
A létrehozott privát végponton most már négy DNS-zónának kell konfigurálva lennie:

[![Privát végponti DNS-zónák képernyőképe.](./media/private-link-security/private-endpoint-dns-zones.png)](./media/private-link-security/private-endpoint-dns-zones-expanded.png#lightbox)

* privatelink-monitor-azure-com
* privatelink-oms-opinsights-azure-com
* privatelink-ods-opinsights-azure-com
* privatelink-agentsvc-azure-automation-net

> [!NOTE]
> Ezek a zónák a virtuális hálózat IP Azure Monitor készletében lévő privát IP-címekkel leképezik a végpontokat. Az alábbi képeken látható IP-címek csak példák. A konfigurációnak ehelyett a saját hálózatán lévő magánhálózati IP-eket kell mutatnia.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-azure-com
Ez a zóna lefedi a Azure Monitor által használt globális végpontokat, ami azt jelenti, hogy ezek a végpontok az összes erőforrást figyelembe véve szolgálják ki a kéréseket, nem pedig egy adott végpontot. Ebben a zónában a következő végpontok vannak leképezve:
* `in.ai` – (Application Insights végpontot, egy globális és egy regionális bejegyzést fog látni
* `api` - Application Insights Log Analytics API-végpont
* `live` - Application Insights élő metrikavégpont
* `profiler` - Application Insights Profiler-végpont
* `snapshot`- Application Insights pillanatképek végpont képernyőképe [ ![ saját DNS zone monitor-azure-com virtuális hálózatról.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-oms-opinsights-azure-com
Ez a zóna az OMS-végpontokra való munkaterület-specifikus leképezést fedi le. A privát végponttal összekapcsolt AMPLS-hez csatolt munkaterületek mindegyikéhez meg kell lennie egy bejegyzésnek.
[![Képernyőkép saját DNS oms-opinsights-azure-com zónáról.](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ods-opinsights-azure-com
Ez a zóna a munkaterület-specifikus leképezést fedi le ODS-végpontokra , azaz a Log Analytics adatbeedési végpontjára. A privát végponthoz csatlakoztatott AMPLS-hez csatolt munkaterületek mindegyikéhez meg kell lennie egy bejegyzésnek.
[![Képernyőkép saját DNS ods-opinsights-azure-com zónáról.](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-azure-automation-net
Ez a zóna az ügynökszolgáltatás-automatizálási végpontokra való munkaterület-specifikus leképezést fedi le. A privát végponthoz csatlakoztatott AMPLS-hez csatolt munkaterületek mindegyikéhez meg kell lennie egy bejegyzésnek.
[![Képernyőkép saját DNS svc-azure-automation-net zónaügynökről.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

### <a name="validating-you-are-communicating-over-a-private-link"></a>A kommunikáció egy adott Private Link
* Annak ellenőrzéséhez, hogy a rendszer a privát végponton keresztül, illetve a magánhálózati IP-leképezett végpontokra küldi-e a kéréseket, áttekintheti őket egy hálózatkövetéssel az eszközök vagy akár a böngésző számára. Amikor például a munkaterületet vagy alkalmazást próbálja lekérdezni, győződjön meg arról, hogy a kérés az API-végpontra leképezett magánhálózati IP-címnek van elküldve. Ebben a példában *ez a következő: 172.17.0.9.*

    Megjegyzés: Egyes böngészők más DNS-beállításokat is használhatnak (lásd: [Böngésző DNS-beállításai).](#browser-dns-settings) Ellenőrizze, hogy a DNS-beállítások érvényesek-e.

* Annak érdekében, hogy a munkaterület vagy az összetevő ne fogadjon kéréseket nyilvános hálózatokról (amelyek nem ampls-kapcsolaton keresztül vannak csatlakoztatva), állítsa az erőforrás nyilvános betöltés és lekérdezésjelzőit Nem beállításra a Hozzáférés kezelése privát kapcsolatok hatókörén kívülről témakörben leírtak [szerint.](#manage-access-from-outside-of-private-links-scopes) 

* A védett hálózat egyik ügyfeléről a használatával a DNS-zónáiban `nslookup` felsorolt végpontok bármelyikét használhatja. A DNS-kiszolgálónak az alapértelmezés szerint használt nyilvános IP-cím helyett a leképezett magánhálózati IP-címre kell feloldani.


## <a name="configure-log-analytics"></a>A Log Analytics konfigurálása

Nyissa meg az Azure Portalt. A Log Analytics-munkaterület erőforrásmenüje bal  oldalán található egy Hálózatelszigetelés nevű elem. Ebben a menüben két különböző államot szabályozhat.

![LA hálózatelszigetelés](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Csatlakoztatott Azure Monitor Private Link hatókörök
Ezen a képernyőn a munkaterülethez csatlakoztatott összes hatókör megjelenik. A hatókörök (AMPLS)-hez való csatlakozás lehetővé teszi, hogy az egyes AMPLS-hez csatlakoztatott virtuális hálózat hálózati forgalma elérje ezt a munkaterületet. A kapcsolat létrehozása itt ugyanazokkal a hatásokkal rendelkezik, mint a hatókör beállítása, mint a Csatlakozás Azure Monitor [erőforrásokhoz.](#connect-azure-monitor-resources) Új kapcsolat hozzáadásához válassza a Hozzáadás **lehetőséget,** majd válassza ki Azure Monitor Private Link hatókört. A **csatlakozáshoz** válassza az Alkalmaz lehetőséget. Vegye figyelembe, hogy a munkaterület 5 AMPLS-objektumhoz tud csatlakozni a [Korlátozások és korlátozások dokumentumban említettek szerint.](#restrictions-and-limitations) 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>Hozzáférés kezelése a privát kapcsolatok hatókörein kívülről
A lap alsó részén található beállítások a nyilvános hálózatokról való hozzáférést szabályozják, ami azt jelenti, hogy a hálózatok nem a fent felsorolt hatókörökben vannak csatlakoztatva. Az **Allow public network access for ingestion** (Nyilvános hálózati hozzáférés engedélyezése a betekehez) beállítást **No** blocks ingestion of machines outside the connected scopes (Nem blokkolja a csatlakoztatott hatókörön kívüli gépek naplóit). Az **Allow public network access for queries (Nyilvános hálózati hozzáférés engedélyezése a lekérdezésekhez)** beállítást No blocks queries from machines from outside the scopes **(Nem** blokkolja a hatóköröken kívüli gépekről érkező lekérdezéseket) beállítás. Ez magában foglalja többek között a munkafüzetek, irányítópultok, API-alapú ügyfélélmények, a Azure Portal használatával futtatott lekérdezéseket. Az alkalmazáson kívül Azure Portal, és a Log Analytics-adatokat lekérdező szolgáltatásnak a privát kapcsolatú virtuális hálózatban is futnia kell.

### <a name="exceptions"></a>Kivételek
A hozzáférés fent leírtak szerint való korlátozása nem vonatkozik a Azure Resource Manager ezért a következő korlátozásokkal rendelkezik:
* Adatokhoz való hozzáférés – bár a nyilvános hálózatokról érkező lekérdezések blokkolása/engedélyezésének a legtöbb Log Analytics-élményre vonatkozik, egyes szolgáltatások az Azure Resource Manager-t használva adatokat fognak lekérdezni, ezért csak akkor fognak tudni adatokat lekérdezni, ha Private Link Resource Manager-beállításokat is alkalmaznak (a funkció hamarosan elérhető lesz). Ilyenek például Azure Monitor megoldások, munkafüzetek és elemzések, valamint a LogicApp-összekötő.
* Munkaterület-kezelés – A munkaterület beállításainak és konfigurációjának módosításait (beleértve a hozzáférési beállítások be- és kikapcsolása) az Azure Resource Manager. Korlátozza a munkaterület-kezeléshez való hozzáférést a megfelelő szerepkörök, engedélyek, hálózati vezérlők és naplózás használatával. További információ: [Szerepkörök Azure Monitor, Engedélyek és Biztonság.](../roles-permissions-security.md)

> [!NOTE]
> A munkaterületre a diagnosztikai [](../essentials/diagnostic-settings.md) beállításokon keresztül feltöltött naplók és metrikák egy biztonságos privát Microsoft-csatornán keresztül mennek, és ezeket a beállításokat nem vezérlik.

### <a name="log-analytics-solution-packs-download"></a>Log Analytics-megoldáscsomagok letöltése

Ha engedélyeznie kell, hogy a Log Analytics-ügynök letöltsön megoldáscsomagokat, adja hozzá a megfelelő teljes tartományneveket a tűzfal engedélyezési listához. 


| Felhőalapú környezet | Ügynök erőforrása | Portok | Irány |
|:--|:--|:--|:--|
|Azure Public     | scadvisorcontent.blob.core.windows.net         | 443 | Kimenő
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Kimenő
|Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Kimenő


>[!NOTE]
> 2021. április 19-től a fenti beállítás nem szükséges, és a privát kapcsolaton keresztül elérheti a megoldáscsomagok tárfiókját. Az új funkcióhoz újra létre kell hozva az AMPLS-t (2021. április 19-én vagy újabb), valamint a hozzá csatlakoztatott privát végpontot. A meglévő AMPLS-ekkel és Private Endpints-ekkel nem fog vonatkozni.

## <a name="configure-application-insights"></a>Az Application Insights konfigurálása

Nyissa meg az Azure Portalt. A Azure Monitor Application Insights-összetevő erőforrásában található egy  Hálózatelszigetelés menüpont a bal oldalon. Ebben a menüben két különböző államot szabályozhat.

![AI-hálózat elkülönítése](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Először is csatlakoztathatja ezt Application Insights erőforrást olyan Azure Monitor Private Link, amelyekhez hozzáféréssel rendelkezik. Válassza **a Hozzáadás lehetőséget,** majd válassza ki **Azure Monitor Private Link hatókört.** A csatlakozáshoz válassza az Alkalmaz lehetőséget. Ezen a képernyőn az összes csatlakoztatott hatókör megjelenik. Ennek a kapcsolatnak a létrehozása lehetővé teszi, hogy a csatlakoztatott virtuális hálózatok hálózati forgalma elérje ezt az összetevőt, és ugyanaz a hatása, mintha a hatókörből csatlakoztatta volna, mint a Connecting Azure Monitor resources (Erőforrások csatlakoztatása) Azure Monitor [esetében.](#connect-azure-monitor-resources) 

Ezután szabályozhatja, hogy az erőforrás hogyan érhető el a korábban felsorolt privát kapcsolati hatókörök (AMPLS) hatóköreiből. Ha a Nyilvános **hálózati** hozzáférés engedélyezése a betöltéshez beállításnál a **Nem** lehetőséget adja meg, akkor a csatlakoztatott hatókörön kívüli gépek vagy AZDK-k nem tölthetnek fel adatokat ebbe az összetevőbe. Ha a Nyilvános hálózati **hozzáférés** engedélyezése a lekérdezésekhez lehetőséget **No**(Nem) beállításra adja meg, akkor a hatóköröken kívüli gépek nem férhetnek hozzá az ebben az erőforrásban Application Insights adatokhoz. Ezek az adatok tartalmazzák az APM-naplókhoz, metrikákhoz és az élő metrikastreamhez való hozzáférést, valamint az olyan, a szolgáltatásra épülő felületeket, mint a munkafüzetek, az irányítópultok, a lekérdezési API-alapú ügyfélélmények, a Azure Portal-elemzések stb. 

> [!NOTE]
> A nem portálon keresztüli használatot a privát kapcsolatú virtuális hálózatban is futtatni kell, amely tartalmazza a figyelt számítási feladatokat.

A figyelt számítási feladatokat üzemeltető erőforrásokat hozzá kell adni a privát kapcsolathoz. Lásd például: Using Private Endpoints for Azure Web App (Privát [végpontok használata az Azure-webalkalmazáshoz).](../../app-service/networking/private-endpoint.md)

A hozzáférés ilyen módon való korlátozása csak a Application Insights adatokra vonatkozik. A konfigurációs módosításokat , beleértve a hozzáférési beállítások be- és kikapcsolása is, az Azure Resource Manager. Ezért korlátoznia kell a hozzáférését Resource Manager szerepkörök, engedélyek, hálózati vezérlők és naplózás használatával. További információkért lásd a [Azure Monitor, az engedélyeket és a biztonságot.](../roles-permissions-security.md)

> [!NOTE]
> A munkaterület-alapú Application Insights teljes körű biztonság érdekében le kell zárnia a hozzáférést Application Insights erőforráshoz és az alapul szolgáló Log Analytics-munkaterülethez is.
>
> Kódszintű diagnosztika (Profiler/hibakereső) – [](../app/profiler-bring-your-own-storage.md) saját tárfiókot kell biztosítania a privát kapcsolat támogatásához.

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>A privát hivatkozások "mindent vagy semmit" jellegének kezelése
A Planning [your Private Link setup](#planning-your-private-link-setup)(Az Private Link beállításának megtervezése) című részben leírtak szerint a Private Link beállítása akár egyetlen erőforráshoz is hatással van az adott hálózatokban és más, azonos DNS-sel osztozó hálózatokon Azure Monitor erőforrásra. Ez a viselkedés kihívást jelenthet az ön számára. Vegye figyelembe a következő lehetőségeket:

* Mindent a következőben: a legegyszerűbb és legbiztonságosabb módszer az összes Application Insights hozzáadása az AMPLS-hez. A más hálózatokról továbbra is elérni kívánt összetevők esetében hagyja meg a "Nyilvános internet-hozzáférés engedélyezése a betekintéshez/lekérdezéshez" jelzőt Igen (ez az alapértelmezett beállítás).
* Hálózatok elkülönítése – ha küllő virtuális hálózatokat használ (vagy igazodhat a hálózathoz), kövesse az Azure küllős hálózati [topológiáját.](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) Ezután külön privát kapcsolatbeállításokat kell beállítania a megfelelő küllő virtuális hálózatokon. Ügyeljen arra, hogy a DNS-zónákat is külön válassza el, mivel a DNS-zónák más küllős hálózatokkal való megosztása [DNS-felülbírálásokat okoz.](#the-issue-of-dns-overrides)
* Egyéni DNS-zónák használata adott alkalmazásokhoz – ez a megoldás lehetővé teszi, hogy egy adott Application Insights-összetevőt egy Private Link- és minden más forgalmat a nyilvános útvonalakon keresztül tartsunk.
    - Állítson be [egy egyéni privát DNS-zónát,](../../private-link/private-endpoint-dns.md)és adjon neki egy egyedi nevet, például internal.monitor.azure.com
    - HOZZon létre egy AMPLS-t  és egy privát végpontot, és ne integrálja automatikusan a privát DNS-t
    - A Privát végpont -> DNS-konfigurációban tekintse át a teljes tartományneveket javasolt leképezéseket.
    - Válassza a Konfiguráció hozzáadása lehetőséget, és válassza ki internal.monitor.azure.com létrehozott internal.monitor.azure.com zónát
    - Rekordok hozzáadása a fentihez Képernyőkép a ![ konfigurált DNS-zónáról](./media/private-link-security/private-endpoint-global-dns-zone.png)
    - A saját Application Insights másolja a [kapcsolati sztringjét.](../app/sdk-connection-string.md)
    - Az olyan alkalmazásoknak vagy szkripteknek, amelyek az összetevőt Private Link kell használniuk az EndpointSuffix=internal.monitor.azure.com kapcsolati sztringet
* Végpontok leképezése gazdagépfájlokon keresztül DNS helyett – hogy Private Link csak egy adott gépről/virtuális gépről legyen hozzáférése a hálózatban:
    - ÁLLÍTSon be egy AMPLS-t  és egy privát végpontot, és ne integrálja automatikusan a privát DNS-t 
    - Konfigurálja a fenti A rekordokat egy olyan gépen, amely futtatja az alkalmazást a hosts fájlban


## <a name="use-apis-and-command-line"></a>API-k és parancssor használata

A korábban ismertetett folyamatot automatizálhatja a Azure Resource Manager, REST és parancssori felületek használatával.

Privát kapcsolati hatókörök létrehozásához és kezeléséhez használja a REST API [vagy](/rest/api/monitor/private%20link%20scopes%20(preview)) az [Azure CLI-t (az monitor private-link-scope)](/cli/azure/monitor/private-link-scope).

A hálózati hozzáférés kezeléséhez használja a és a jelölőket a `[--ingestion-access {Disabled, Enabled}]` `[--query-access {Disabled, Enabled}]` Log [Analytics-munkaterületeken](/cli/azure/monitor/log-analytics/workspace) vagy [Application Insights összetevőkön.](/cli/azure/ext/application-insights/monitor/app-insights/component)

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Egyéni naplók és IIS-naplók gyűjtése Private Link

A tárfiókok az egyéni naplók feldolgozásának folyamatában használatosak. Alapértelmezés szerint a rendszer szolgáltatás által felügyelt tárfiókokat használ. Az egyéni naplók privát kapcsolatokon való tárolásához azonban saját tárfiókokat kell használnia, és társítva kell őket a Log Analytics-munkaterület(ök)hez. További részletek az ilyen fiókok parancssor használatával való [beállításáról.](/cli/azure/monitor/log-analytics/workspace/linked-storage)

A saját tárfiókok hozzuk létre a következőt: Ügyfél tulajdonában lévő tárfiókok [naplózási adatokhoz](private-storage.md)

## <a name="restrictions-and-limitations"></a>Korlátozások

### <a name="ampls"></a>AMPLS
Az AMPLS-objektum számos korlátozással rendelkezik, amit figyelembe kell vennie a Private Link tervezésekor:

* Egy VNet csak 1 AMPLS-objektumhoz tud csatlakozni. Ez azt jelenti, hogy az AMPLS-objektumnak hozzáférést kell adnia az összes Azure Monitor erőforráshoz, amelyekhez a virtuális hálózatnak hozzáféréssel kell lennie.
* Egy Azure Monitor erőforrás (munkaterület vagy Application Insights összetevő) 5 AMPLS-hez csatlakozhat.
* Az AMPLS-objektumok 50 Azure Monitor csatlakozhatnak.
* Az AMPLS-objektumok 10 privát végponthoz csatlakozhatnak.

A [korlátok mélyebb áttekintését](#consider-limits) lásd: Korlátok figyelembe venni.

### <a name="agents"></a>Ügynökök

A Windows- és Linux-ügynökök legújabb verzióit kell használni a Log Analytics-munkaterületek biztonságos be- és beúsításának támogatásához. A régebbi verziók nem tudnak monitorozási adatokat feltölteni magánhálózaton.

**Log Analytics Windows-ügynök**

Használja a Log Analytics-ügynök 10.20.18038.0-s vagy újabb verzióját.

**A Log Analytics Linux-ügynöke**

Az ügynök 1.12.25-ös vagy újabb verzióját használja. Ha nem tudja, futtassa a következő parancsokat a virtuális gépen.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure Portal

Az Azure Monitor portál Application Insights és a Log Analytics használata esetén engedélyeznie kell, hogy az Azure Portal- és Azure Monitor-bővítmények elérhetők legyenek a magánhálózaton. Adja hozzá az [](../../firewall/service-tags.md) **AzureActiveDirectory,** az **AzureResourceManager,** az **AzureFrontDoor.FirstParty** és az **AzureFrontdoor.Frontend szolgáltatáscímkéket** a hálózati biztonsági csoporthoz.

### <a name="querying-data"></a>Adatok lekérdezése
Az [ `externaldata` operátor](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) nem támogatott egy Private Link, mivel adatokat olvas a tárfiókból, de nem garantálja a tárterület privát hozzáférését.

### <a name="programmatic-access"></a>Szoftveres hozzáférés

Ha a REST API, a [parancssori](/cli/azure/monitor) felület vagy a PowerShell Azure Monitor-nal szeretne privát hálózatokon használni, adja hozzá az [](../../virtual-network/service-tags-overview.md)**AzureActiveDirectory** és **az AzureResourceManager** szolgáltatáscímkéket a tűzfalhoz.  

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights SDK-letöltések tartalomk kézbesítési hálózatról

Csomagolja a JavaScript-kódot a szkriptbe, hogy a böngésző ne kísérelje meg letölteni a kódot a CDN-ről. Erre mutatunk be egy példát a [GitHubon](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>Böngésző DNS-beállításai

Ha az erőforrásokhoz egy Azure Monitor keresztül csatlakozik, Private Link erőforrások forgalmának a hálózaton konfigurált privát végponton kell áthaladnia. A privát végpont engedélyezéséhez frissítse a DNS-beállításokat [a Csatlakozás privát végponthoz című részben leírtak szerint.](#connect-to-a-private-endpoint) Egyes böngészők saját DNS-beállításokat használnak az Ön által beállítottak helyett. Előfordulhat, hogy a böngésző megpróbál csatlakozni Azure Monitor nyilvános végponthoz, és megkerülni Private Link végpontot. Ellenőrizze, hogy a böngésző beállításai nem bírálják felül vagy gyorsítótárazják-e a régi DNS-beállításokat. 

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [privát tárolókról](private-storage.md)