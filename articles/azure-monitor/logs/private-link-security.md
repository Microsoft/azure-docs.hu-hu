---
title: Hálózatok biztonságos csatlakoztatása az Azure Monitorhoz az Azure Private Linkkel
description: Hálózatok biztonságos csatlakoztatása az Azure Monitorhoz az Azure Private Linkkel
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 97e589755602c14a11873fee5288ee8c6e24ba83
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714285"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Hálózatok biztonságos csatlakoztatása az Azure Monitorhoz az Azure Private Linkkel

[Azure Private Link](../../private-link/private-link-overview.md) lehetővé teszi az Azure PaaS-szolgáltatások biztonságos csatolását a virtuális hálózathoz privát végpontok használatával. Sok szolgáltatáshoz csak be kell állítania egy végpontot erőforrásonként. A Azure Monitor azonban számos különböző összekapcsolt szolgáltatás, amelyek együtt figyelik a számítási feladatokat. Ennek eredményeképpen kiépítettünk egy Azure Monitor Private Link (AMPLS) nevű erőforrást. Az AMPLS lehetővé teszi, hogy meghatározza a monitorozási hálózat határait, és csatlakozzon a virtuális hálózathoz. Ez a cikk bemutatja, mikor használható, és hogyan állíthat be Azure Monitor Private Link hatókört.

## <a name="advantages"></a>Előnyök

A Private Link a következőt tudja:

- Privát csatlakozás a Azure Monitor nyilvános hálózati hozzáférés megnyitása nélkül
- Győződjön meg arról, hogy a monitorozási adatok csak engedélyezett magánhálózaton keresztül érhetők el
- A magánhálózatok adatkiszivárgásának megakadályozása adott Azure Monitor, amelyek a privát végponton keresztül csatlakoznak
- A helyszíni magánhálózat biztonságos csatlakoztatása Azure Monitor ExpressRoute és Private Link
- Minden forgalom a gerinchálózaton Microsoft Azure belül

További információ:  [A](../../private-link/private-link-overview.md#key-benefits)Private Link.

## <a name="how-it-works"></a>Működés

Azure Monitor Private Link hatókör (AMPLS) privát végpontokat (és az őket tartalmazó virtuális hálózatokat) kapcsol össze egy vagy több Azure Monitor-erőforrással – Log Analytics-munkaterületekkel és Application Insights összetevőkkel.

![Alapszintű erőforrás-topológia ábrája](./media/private-link-security/private-link-basic-topology.png)

* A virtuális hálózat privát végpontja lehetővé teszi, hogy Azure Monitor végpontokat a hálózat készletéből származó magánhálózati IP-címekkel érje el ahelyett, hogy a végpontok nyilvános IP-címeit használná. Ez lehetővé teszi, hogy továbbra is használja a Azure Monitor anélkül, hogy megnyitja a virtuális hálózatát a nem szükséges kimenő forgalom számára. 
* A privát végpontról a Azure Monitor felé a forgalom a Microsoft Azure a gerinchálózaton, és nem lesz nyilvános hálózatokra irányítva. 
* Minden munkaterületet vagy összetevőt konfigurálhat úgy, hogy engedélyezze vagy megtagadja a nyilvános hálózatokról származó be- és lekérdezéseket. Ez erőforrásszintű védelmet biztosít, hogy szabályozni tudja az adott erőforrásokhoz való forgalmat.

> [!NOTE]
> Egyetlen Azure Monitor erőforrás több AMPLS-hez is tartozhat, de egyetlen virtuális hálózat nem csatlakoztatható több AMPLS-hez. 

## <a name="planning-your-private-link-setup"></a>A Private Link megtervezése

A konfiguráció beállítása Azure Monitor Private Link fontolja meg a hálózati topológiát, és különösen a DNS útválasztási topológiáját. 

### <a name="the-issue-of-dns-overrides"></a>A DNS-felülbírálások problémája
Néhány Azure Monitor szolgáltatás globális végpontokat használ, ami azt jelenti, hogy bármely munkaterületre/összetevőre vonatkozó kéréseket szolgálnak ki. Néhány példa a Application Insights végpontja, valamint a Application Insights és a Log Analytics lekérdezési végpontja.

Amikor beállít egy Private Link kapcsolatot, a DNS frissül, hogy Azure Monitor végpontokat magánhálózati IP-címekre leképezve a virtuális hálózat IP-címtartományában. Ez a módosítás felülírja ezen végpontok minden korábbi leképezését, ami jelentős következményekkel járhat, az alábbiakban áttekintve. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor Private Link az összes Azure Monitor erőforrásra vonatkozik – mind vagy Semmi
Mivel egyes Azure Monitor végpontok globálisak, lehetetlen létrehozni Private Link kapcsolatot egy adott összetevő vagy munkaterület számára. Ehelyett ha egyetlen Private Link vagy Log Analytics-munkaterületre Application Insights, a DNS-rekordok az összes  Application Insights frissülnek. Az összetevők be- vagy lekérdezésére tett minden kísérlet a Private Link sikertelen lesz. A Log Analytics esetében a be- és konfigurációs végpontok munkaterület-specifikusak, ami azt jelenti, hogy a privát kapcsolat beállítása csak a megadott munkaterületeken érvényes. A többi munkaterület be- és konfigurációja az alapértelmezett nyilvános Log Analytics-végpontokra lesz irányítva.

![Egyetlen virtuális hálózat DNS-felülbírálásokat ábrázoló diagramja](./media/private-link-security/dns-overrides-single-vnet.png)

Ez nem csak egy adott virtuális hálózatra igaz, hanem az összes olyan virtuális hálózatra is, amelyek ugyanazt a DNS-kiszolgálót osztják meg (lásd: [A DNS-felülbírálások problémája).](#the-issue-of-dns-overrides) Így például a naplók bármely összetevőnek való Application Insights a rendszer mindig az útvonalon Private Link kérést. Az AMPLS-hez nem kapcsolódó összetevők nem Private Link ellenőrzésen, és nem mennek keresztül.

> [!NOTE]
> Zárásképpen: Miután a Private Link kapcsolatot hoz létre egyetlen erőforrással, az a hálózaton Azure Monitor erőforrásokra is vonatkozik. A Application Insights az "Összes vagy Semmi" lehet. Ez gyakorlatilag azt jelenti, hogy a Application Insights összes erőforrását hozzá kell adni az AMPLS-hez, vagy egyiket sem.
> 
> Az adatkiszivárgási kockázatok kezelése érdekében javasoljuk, hogy adjon hozzá minden Application Insights- és Log Analytics-erőforrást az AMPLS-hez, és a lehető legnagyobb mértékben blokkolja a hálózatok bejövő forgalmát.

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor Private Link a teljes hálózatra vonatkozik
Egyes hálózatok több virtuális hálózatból állnak. Ha a virtuális hálózatok ugyanazt a DNS-kiszolgálót használják, felülírják egymás DNS-leképezéseit, és esetleg megszakítják egymás és a Azure Monitor közötti kommunikációt (lásd: A DNS-felülbírálások [problémája).](#the-issue-of-dns-overrides) Végső soron csak az utolsó virtuális hálózat fog tudni kommunikálni az Azure Monitor-val, mivel a DNS Azure Monitor-végpontokat fog leképezni a virtuális hálózatok tartományból származó privát IP-címekkel (amelyek más virtuális hálózatokról nem hajthatóak végre).

![Több virtuális hálózat DNS-felülbírálásokat ábrázoló ábrája](./media/private-link-security/dns-overrides-multiple-vnets.png)

A fenti ábrán a 10.0.1.x virtuális hálózat először csatlakozik az AMPLS1-hez, és leképezi Azure Monitor globális végpontokat IP-címekre a tartományból. Később a 10.0.2.x virtuális hálózat csatlakozik az AMPLS2-hez, és felülbírálja ugyanazon globális végpontok DNS-leképezését a tartomány IP-címekkel.  Mivel ezek a virtuális hálózatok nincsenek társviszonyban, az első virtuális hálózat most nem éri el ezeket a végpontokat.

> [!NOTE]
> Zárásképpen: Az AMPLS beállítása minden olyan hálózatot érint, amely azonos DNS-zónával osztozik. A DNS-végpontleképezések felülbírálása érdekében ajánlott egyetlen privát végpontot beállítani egy társhálózaton (például egy központi virtuális hálózaton), vagy elkülönítani a hálózatokat DNS-szinten (például DNS-továbbítók vagy teljesen különálló DNS-kiszolgálók használatával).

### <a name="hub-spoke-networks"></a>Küllős hálózatok
A küllős topológiák úgy kerülik el a DNS-felülbírálások kérdését, hogy Private Link-t a központi (fő) virtuális hálózaton ják meg ahelyett, hogy külön-külön külön Private Link külön-külön egy virtuális hálózathoz. Ez a beállítás különösen akkor hasznos, Azure Monitor küllő virtuális hálózatok által használt összes erőforrás meg van osztva. 

![Küllős-egyszeres PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Előfordulhat, hogy szándékosan különálló privát kapcsolatokat szeretne létrehozni a küllő virtuális hálózatokhoz, például hogy az egyes virtuális hálózatok csak korlátozott számú monitorozási erőforráshoz férnek hozzá. Ilyen esetekben dedikált privát végpontot és AMPLS-t hozhat létre mindegyik virtuális hálózathoz, de azt is ellenőriznie kell, hogy nem osztoznak-e ugyanazon DNS-zónákon a DNS-felülbírálások elkerülése érdekében.


### <a name="consider-limits"></a>A korlátok figyelembe vere

Ahogy az [a Korlátozások és korlátozások](#restrictions-and-limitations)alatt szerepel, az AMPLS objektumnak több korlátja is van, amelyek az alábbi topológiában láthatók:
* Minden virtuális hálózat csak **1** AMPLS-objektumhoz csatlakozik.
* Az AMPLS B két virtuális hálózat (VNet2 és VNet3) privát végpontjaihoz csatlakozik a 10 lehetséges privát végpont kapcsolat közül 2 használatával.
* Az AMPLS A két munkaterülethez és egy Application Insight-összetevőhez csatlakozik az 50 lehetséges erőforrás-kapcsolat közül 3 Azure Monitor használatával.
* A 2. munkaterület az 5 lehetséges AMPLS-kapcsolatból 2-t használva csatlakozik az AMPLS A-hez és az AMPLS B-hez.

![Az AMPLS korlátainak ábrája](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>Példakapcsolat

Először hozzon létre egy Azure Monitor Private Link Scope erőforrást.

1. A **hatókörben keresse meg az** Erőforrás létrehozása Azure Portal, és keressen a **Azure Monitor Private Link kifejezésre.**

   ![Hatókör Azure Monitor Private Link megkeresve](./media/private-link-security/ampls-find-1c.png)

2. Válassza a **Létrehozás** lehetőséget.
3. Válasszon ki egy előfizetést és egy erőforráscsoportot.
4. Adjon nevet az AMPLS-nek. A legjobb, ha jelentéssel bíró és egyértelmű nevet használ, például az "AppServerProdTelem" nevet.
5. Válassza a **Felülvizsgálat és létrehozás** lehetőséget. 

   ![Hatókör Azure Monitor Private Link létrehozása](./media/private-link-security/ampls-create-1d.png)

6. Hagyja, hogy az érvényesítés le legyen edzve, majd válassza a **Létrehozás lehetőséget.**

### <a name="connect-azure-monitor-resources"></a>Csatlakozás Azure Monitor erőforrásokhoz

Az Azure Monitor (Log Analytics-munkaterületek és Application Insights összetevők) csatlakoztatása az AMPLS-hez.

1. A Azure Monitor Private Link a bal **Azure Monitor válassza** az Erőforrások elemet. Kattintson a **Hozzáadás gombra.**
2. Adja hozzá a munkaterületet vagy összetevőt. A **Hozzáadás gombra** kattintva megjelenik egy párbeszédpanel, ahol kiválaszthatja Azure Monitor erőforrásokat. Tallózhat az előfizetések és az erőforráscsoportok között, vagy begépelheti a nevüket a szűrésükbe. Válassza ki a munkaterületet vagy összetevőt, majd válassza az **Alkalmaz** lehetőséget, hogy hozzáadja őket a hatóköréhez.

    ![A hatókör kiválasztásának felhasználói felületének képernyőképe](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> A Azure Monitor törléséhez először le kell bontani azokat az AMPLS-objektumokról, amelyekhez csatlakoznak. Az AMPLS-hez csatlakoztatott erőforrások nem törölhetők.

### <a name="connect-to-a-private-endpoint"></a>Csatlakozás privát végponthoz

Most, hogy az erőforrások csatlakoztatva vannak az AMPLS-hez, hozzon létre egy privát végpontot a hálózat csatlakoztatásához. Ezt a feladatot a következő Azure Portal Private Link [használhatja,](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)Azure Monitor Private Link hatókörben, ahogyan ebben a példában is tette.

1. A hatókör-erőforrásban válassza a **Privát végponti** kapcsolatok elemet a bal oldali erőforrásmenüben. Válassza **a Privát végpont** lehetőséget a végpont létrehozási folyamatának létrehozásához. Az itt található Private Link központi központban indított kapcsolatokat is jóváhagyhatja, ha kiválasztja őket, majd a Jóváhagyás **et választják.**

    ![Privát végponti kapcsolatok felhasználói felületének képernyőképe](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Válassza ki az előfizetést, az erőforráscsoportot és a végpont nevét, valamint azt a régiót, ahol a végpontnak lennie kell. A régiónak ugyanannak a régiónak kell lennie, mint annak a virtuális hálózatnak, amelyhez csatlakoztatja.

3. Válassza **a Tovább: Erőforrás lehetőséget.** 

4. Az Erőforrás képernyőn:

   a. Válassza ki **azt az** előfizetést, amely Azure Monitor a Privát hatókör erőforrást. 

   b. Az **erőforrástípushoz** válassza a **Microsoft.insights/privateLinkScopes lehetőséget.** 

   c. Az erőforrás **legördülő** menüből válassza ki a Private Link létrehozott hatókört. 

   d. Válassza **a Tovább: Konfigurációs >** lehetőséget.
      ![Képernyőkép a Privát végpont létrehozása kiválasztásáról](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. A konfigurációs panelen:

   a.    Válassza ki azt  **a virtuális hálózatot** és alhálózatot, amelyről csatlakozni szeretne a Azure Monitor erőforrásaihoz. 
 
   b.    Válassza **az Igen** lehetőséget a Privát DNS-zónával való **integrációhoz,** és hagyja, hogy az automatikusan létrehoz egy új saját DNS zónát. A tényleges DNS-zónák különbözhetnek az alábbi képernyőképen láthatótól. 
   > [!NOTE]
   > Ha a Nem **lehetőséget** választja, és inkább manuálisan szeretné kezelni a DNS-rekordokat, először is Private Link a privát végpontot és az AMPLS-konfigurációt is beleértve. Ezután konfigurálja a DNS-t az [Azure-beli privát végpont DNS-konfigurálása](../../private-link/private-endpoint-dns.md) című dokumentumban leírt utasítások szerint. Ügyeljen arra, hogy ne hozzon létre üres rekordokat a privát kapcsolat beállításának előkészítése közben. Az Ön által létrehozott DNS-rekordok felülírják a meglévő beállításokat, és hatással vannak az Azure Monitorral létesített kapcsolatra.
 
   c.    Válassza az **Áttekintés + létrehozás** lehetőséget.
 
   d.    Hagyja, hogy az érvényesítés le legyen ásva. 
 
   e.    Válassza a **Létrehozás** lehetőséget. 

    ![Képernyőkép a Privát végpont részletei kiválasztásáról.](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

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
> Ezek a zónák konkrét végpontokat Azure Monitor leképeznek a virtuális hálózat IP-címkészletének privát IP-címére. Az alábbi képeken látható IP-címek csak példák. A konfigurációnak ehelyett a saját hálózatán lévő magánhálózati IP-eket kell mutatnia.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-azure-com
Ez a zóna lefedi a Azure Monitor által használt globális végpontokat, ami azt jelenti, hogy ezek a végpontok az összes erőforrást figyelembe véve szolgálják ki a kéréseket, nem pedig egy konkrétat. Ennek a zónának a következő végpontokra kell leképezve lennie:
* `in.ai` - Application Insights végpont (globális és regionális bejegyzés is)
* `api` - Application Insights Log Analytics API-végpont
* `live` - Application Insights élő metrikavégpont
* `profiler` - Application Insights Profiler-végpont
* `snapshot`- Application Insights pillanatképek végpont képernyőképe [ ![ saját DNS zónamonitor-azure-com képernyőképe.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-oms-opinsights-azure-com
Ez a zóna az OMS-végpontokra való munkaterület-specifikus leképezést fedi le. A privát végponthoz csatlakoztatott AMPLS-hez csatolt munkaterületek mindegyikéhez meg kell lennie egy bejegyzésnek.
[![Képernyőkép saját DNS oms-opinsights-azure-com zónáról.](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ods-opinsights-azure-com
Ez a zóna a munkaterület-specifikus leképezést fedi le ODS-végpontokra , azaz a Log Analytics adatbeabbi végpontjára. A privát végponttal összekapcsolt AMPLS-hez csatolt munkaterületek mindegyikéhez meg kell lennie egy bejegyzésnek.
[![Képernyőkép saját DNS ods-opinsights-azure-com zónáról.](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-azure-automation-net
Ez a zóna az ügynökszolgáltatás-automatizálási végpontokra való munkaterület-specifikus leképezést fedi le. A privát végponttal összekapcsolt AMPLS-hez csatolt munkaterületek mindegyikéhez meg kell lennie egy bejegyzésnek.
[![Képernyőkép saját DNS svc-azure-automation-net zónaügynökről.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

#### <a name="privatelink-blob-core-windows-net"></a>privatelink-blob-core-windows-net
Ez a zóna konfigurálja a globális ügynökök megoldáscsomag tárfiókjára való kapcsolódást. Ezen keresztül az ügynökök letölthetik az új vagy frissített megoldáscsomagokat (más néven felügyeleti csomagokat). A Log Analytics-ügynökök kezeléséhez csak egy bejegyzésre van szükség, függetlenül attól, hogy hány munkaterületet használ.
[![A saját DNS blob-core-windows-net képernyőképe.](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net.png)](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net-expanded.png#lightbox)
> [!NOTE]
> Ez a bejegyzés csak a 2021. április 19-én vagy azt követően létrehozott Privát hivatkozások beállításhoz lesz hozzáadva.


### <a name="validating-you-are-communicating-over-a-private-link"></a>Egy adott kiszolgálóval folytatott kommunikáció Private Link
* A kérések privát végponton és a magánhálózati IP-leképezett végponton keresztüli elküldését egy hálózatkövető eszközzel vagy akár a böngészővel ellenőrizheti. Amikor például a munkaterületet vagy alkalmazást próbálja lekérdezni, győződjön meg arról, hogy a kérés az API-végpontra leképezett magánhálózati IP-címnek van elküldve. Ebben a példában *ez a következő: 172.17.0.9.*

    Megjegyzés: Egyes böngészők más DNS-beállításokat is használhatnak (lásd: [Böngésző DNS-beállításai).](#browser-dns-settings) Ellenőrizze, hogy a DNS-beállítások érvényesek-e.

* Annak érdekében, hogy a munkaterület vagy az összetevő ne fogadjon kéréseket nyilvános hálózatokról (amelyek nem AMPLS-sel vannak csatlakoztatva), állítsa az erőforrás nyilvános adatbetöltési és lekérdezési jelzőit Nem beállításra a Hozzáférés kezelése privát kapcsolatok hatókörén kívülről témakörben leírtak [szerint.](#manage-access-from-outside-of-private-links-scopes) 

* A védett hálózat egyik ügyfeléről használja a et a DNS-zónáiban `nslookup` felsorolt végpontok egyikében. A DNS-kiszolgálónak az alapértelmezés szerint használt nyilvános IP-cím helyett a leképezett magánhálózati IP-címre kell feloldani.


## <a name="configure-log-analytics"></a>A Log Analytics konfigurálása

Nyissa meg az Azure Portalt. A Log Analytics-munkaterület erőforrásmenüje bal  oldalán található egy Hálózatelszigetelés nevű elem. Ebben a menüben két különböző államot szabályozhat.

![LA hálózatelszigetelés](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Csatlakoztatott Azure Monitor Private Link hatókörök
Ezen a képernyőn a munkaterülethez csatlakoztatott összes hatókör megjelenik. A hatókörök (AMPLS-ek) csatlakoztatása lehetővé teszi, hogy az egyes AMPLS-hez csatlakoztatott virtuális hálózat hálózati forgalma elérje ezt a munkaterületet. A kapcsolat létrehozása itt ugyanazokkal a hatásokkal rendelkezik, mint a hatókörre való beállítás, mint a Connecting Azure Monitor resources (Erőforrások [csatlakoztatása) Azure Monitor esetén.](#connect-azure-monitor-resources) Új kapcsolat hozzáadásához válassza a Hozzáadás **lehetőséget,** majd válassza ki a Azure Monitor Private Link hatókört. A **csatlakozáshoz** válassza az Alkalmaz lehetőséget. Vegye figyelembe, hogy a munkaterület 5 AMPLS-objektumhoz tud csatlakozni, a [Korlátozások és korlátozások dokumentumban említettek szerint.](#restrictions-and-limitations) 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>Hozzáférés kezelése a privát kapcsolatok hatókörein kívülről
A lap alsó részén található beállítások a nyilvános hálózatokról való hozzáférést szabályozják, ami azt jelenti, hogy a felsorolt hatókörök (AMPLS-ekkel) nem csatlakozó hálózatok. Az **Allow public network access for ingestion** (Nyilvános hálózati hozzáférés engedélyezése a bebeükléshez) beállítást No blocks ingestion of machines from machines outside the connected scopes (A nyilvános hálózati hozzáférés engedélyezése a bebeükléshez) beállítás **No** blocks ingestion of logs from machines outside the connected scopes (Nincs blokkok a csatlakoztatott hatókörön kívüli gépekről). Az Allow public network access for queries (Nyilvános hálózati hozzáférés engedélyezése **a lekérdezésekhez)** beállítás No blocks queries from machines from outside the scopes **(Nem** blokkolja a hatóköröken kívüli gépekről érkező lekérdezéseket) beállítást. Ez magában foglalja többek között a munkafüzetek, irányítópultok, API-alapú ügyfélélmények, az Azure Portal lekérdezések futtatását. Az alkalmazáson kívül Azure Portal, és a log analytics-adatok lekérdezésének a privát virtuális hálózatban is futnia kell.

### <a name="exceptions"></a>Kivételek
A hozzáférés fent leírtaknak megfelelően való korlátozása nem vonatkozik a Azure Resource Manager ezért a következő korlátozásokkal rendelkezik:
* Adatokhoz való hozzáférés – bár a nyilvános hálózatokról érkező lekérdezések blokkolása/engedélyezésének a legtöbb Log Analytics-élményre vonatkozik, egyes szolgáltatások az Azure Resource Manager-ről is lekérdezik az adatokat, ezért csak akkor lesznek képesek adatokat lekérdezni, ha Private Link Resource Manager-beállításokat is alkalmaznak (a funkció hamarosan elérhető lesz). Ilyenek például Azure Monitor megoldások, munkafüzetek és elemzések, valamint a LogicApp-összekötő.
* Munkaterület-kezelés – A munkaterület beállításainak és konfigurációjának módosításait (beleértve a hozzáférési beállítások be- és kikapcsolása) az Azure Resource Manager. Korlátozza a munkaterület felügyeletéhez való hozzáférést a megfelelő szerepkörök, engedélyek, hálózati vezérlők és naplózás használatával. További információkért lásd a [Azure Monitor, az engedélyeket és a biztonságot.](../roles-permissions-security.md)

> [!NOTE]
> A munkaterületre a diagnosztikai [](../essentials/diagnostic-settings.md) beállításokon keresztül feltöltött naplók és metrikák egy biztonságos privát Microsoft-csatornán keresztül mennek keresztül, és ezek a beállítások nem vezérlik őket.

### <a name="log-analytics-solution-packs-download"></a>Log Analytics-megoldáscsomagok letöltése
A Log Analytics-ügynököknek globális tárfiókhoz kell hozzáférniük a megoldáscsomagok letöltéséhez. Private Link 2021. április 19-én vagy azt követően létrehozott beállítások a privát kapcsolaton keresztül elérhetik az ügynökök megoldáscsomagok tárolóját. Ez a szolgáltatáshoz létrehozott új [DNS-zónán keresztül blob.core.windows.net.](#privatelink-blob-core-windows-net)

Ha a Private Link 2021. április 19. előtt lett létrehozva, az nem éri el a megoldáscsomagok tárolóját privát kapcsolaton keresztül. Ennek kezelésével a következők egyikét használhatja:
* Hozza létre újra az AMPLS-t és a hozzá csatlakoztatott privát végpontot
* Engedélyezze az ügynökök számára, hogy a nyilvános végponton keresztül elérjék a tárfiókot a következő szabályok a tűzfal engedélyezési listához való hozzáadásával:

    | Felhőalapú környezet | Ügynök erőforrása | Portok | Irány |
    |:--|:--|:--|:--|
    |Azure Public     | scadvisorcontent.blob.core.windows.net         | 443 | Kimenő
    |Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Kimenő
    |Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Kimenő


## <a name="configure-application-insights"></a>Az Application Insights konfigurálása

Nyissa meg az Azure Portalt. A Azure Monitor Application Insights-erőforrásban található egy Hálózatelszigetelés menüpont a bal oldalon.  Ebben a menüben két különböző államot szabályozhat.

![AI-hálózat elkülönítése](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Először is csatlakoztathatja ezt Application Insights erőforrást olyan Azure Monitor Private Link, amelyekhez hozzáféréssel rendelkezik. Válassza **a Hozzáadás lehetőséget,** majd Azure Monitor Private Link **hatókört.** A csatlakozáshoz válassza az Alkalmaz lehetőséget. Ezen a képernyőn az összes csatlakoztatott hatókör megjelenik. Ennek a kapcsolatnak a létrehozása lehetővé teszi, hogy a csatlakoztatott virtuális hálózatok hálózati forgalma elérje ezt az összetevőt, és ugyanaz a hatása, mintha a hatókörből csatlakoztatta volna, mint a Connecting Azure Monitor resources (Erőforrások [csatlakoztatása) Azure Monitor esetében.](#connect-azure-monitor-resources) 

Ezután szabályozhatja, hogyan használhatja ezt az erőforrást a korábban felsorolt privát kapcsolati hatókörök (AMPLS) hatóköreiből. Ha a Nyilvános hálózati hozzáférés engedélyezése **a** betöltéshez beállításnál a **Nem,** akkor a csatlakoztatott hatókörön kívüli gépek vagy az SDK-k nem tölthetnek fel adatokat ebbe az összetevőbe. Ha a **Lekérdezések nyilvános** hálózati hozzáférésének engedélyezése **beállításnál** a Nem lehetőséget adja meg, akkor a hatóköröken kívüli gépek nem férhetnek hozzá az ebben az erőforrásban Application Insights adatokhoz. Ezek az adatok tartalmazzák az APM-naplókhoz, metrikákhoz és az élő metrikastreamhez való hozzáférést, valamint az olyan, a szolgáltatásra épülő felületeket, mint a munkafüzetek, az irányítópultok, a lekérdezési API-alapú ügyfélélmények, a Azure Portal-elemzések stb. 

> [!NOTE]
> A nem portálon keresztüli használatot a monitorozási számítási feladatokat tartalmazó, privát kapcsolatú VNET-en is futnia kell.

A figyelt számítási feladatokat üzemeltető erőforrásokat hozzá kell adni a privát kapcsolathoz. Lásd például: [Using Private Endpoints for Azure Web App (Privát végpontok használata az Azure-webalkalmazáshoz).](../../app-service/networking/private-endpoint.md)

A hozzáférés ilyen módon való korlátozása csak a Application Insights adatokra vonatkozik. A konfiguráció módosításait, beleértve a hozzáférési beállítások be- és kikapcsolása is, az Azure Resource Manager. Ezért a megfelelő szerepkörök, engedélyek, hálózati vezérlők és naplózás Resource Manager kell korlátoznia a hozzáférését. További információ: [Szerepkörök Azure Monitor, Engedélyek és Biztonság.](../roles-permissions-security.md)

> [!NOTE]
> A munkaterület-alapú Application Insights teljes körű biztonság érdekében le kell zárnia a hozzáférést Application Insights erőforráshoz és az alapul szolgáló Log Analytics-munkaterülethez is.
>
> A kódszintű diagnosztika (Profiler/hibakereső) [](../app/profiler-bring-your-own-storage.md) a privát kapcsolat támogatásához saját tárfiókot kell biztosítania.

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>A privát hivatkozások "mindent vagy semmit" jellegének kezelése
A Planning [your Private Link setup](#planning-your-private-link-setup)(Az Private Link beállításának megtervezése) című részben leírtak szerint a Private Link beállítása akár egyetlen erőforráshoz is hatással van az adott hálózatokban és más, azonos DNS-sel osztozó hálózatokban Azure Monitor erőforrásokra. Ez a viselkedés kihívást jelenthet az ön számára. Vegye figyelembe a következő lehetőségeket:

* Mindent rendben – a legegyszerűbb és legbiztonságosabb módszer, ha az összes Application Insights hozzá az AMPLS-hez. A más hálózatokról továbbra is elérni kívánt összetevők esetében hagyja meg a "Nyilvános internet-hozzáférés engedélyezése a betekintéshez/lekérdezéshez" jelzőt Igen (ez az alapértelmezett beállítás).
* Hálózatok elkülönítése – ha küllő virtuális hálózatokat használ (vagy képes igazodni a hálózathoz), kövesse az Azure küllős hálózati [topológiájáról (Hub-spoke network topology in Azure)](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)(Küllős hálózati topológia az Azure-ban) útmutatót. Ezután külön privát kapcsolati beállításokat kell beállítania a megfelelő küllő virtuális hálózatokon. Ügyeljen arra, hogy a DNS-zónákat is külön válassza el, mivel a DNS-zónák más küllős hálózatokkal való megosztása [DNS-felülbírálásokat fog okozhatni.](#the-issue-of-dns-overrides)
* Egyéni DNS-zónák használata adott alkalmazásokhoz – ez a megoldás lehetővé teszi, hogy egy adott Application Insights-összetevőhöz egy adott Private Link- és minden más forgalmat a nyilvános útvonalakon tartva.
    - Állítson be [egy egyéni privát DNS-zónát,](../../private-link/private-endpoint-dns.md)és adjon neki egyedi nevet, például internal.monitor.azure.com
    - HOZZon létre egy AMPLS-t  és egy privát végpontot, és ne integrálja automatikusan a privát DNS-t
    - A Privát végpont -> DNS-konfigurációban tekintse át a teljes tartománynév javasolt leképezését.
    - Válassza a Konfiguráció hozzáadása lehetőséget, és válassza ki internal.monitor.azure.com létrehozott internal.monitor.azure.com zónát
    - Rekordok hozzáadása a fentihez Képernyőkép a ![ konfigurált DNS-zónáról](./media/private-link-security/private-endpoint-global-dns-zone.png)
    - A saját Application Insights másolja ki a [kapcsolati sztringjét.](../app/sdk-connection-string.md)
    - Az olyan alkalmazásoknak vagy szkripteknek, amelyek az összetevőt Private Link kell használniuk az EndpointSuffix=internal.monitor.azure.com kapcsolati sztringet
* Végpontok leképezése gazdagépfájlokon keresztül DNS helyett – hogy Private Link csak egy adott gépről/virtuális gépről legyen hozzáférése a hálózatban:
    - ÁLLÍTSon be egy AMPLS-t  és egy privát végpontot, és ne integrálja automatikusan a privát DNS-t 
    - Konfigurálja a fenti A-rekordokat egy olyan gépen, amely futtatja az alkalmazást a hosts fájlban


## <a name="use-apis-and-command-line"></a>API-k és parancssor használata

A korábban ismertetett folyamatot automatizálhatja a Azure Resource Manager, REST és parancssori felületek használatával.

Privát kapcsolati hatókörök létrehozásához és kezeléséhez használja a REST API [vagy](/rest/api/monitor/privatelinkscopes(preview)/private%20link%20scoped%20resources%20(preview)) az Azure CLI-t [(az monitor private-link-scope)](/cli/azure/monitor/private-link-scope).

A hálózati hozzáférés kezeléséhez használja a és a jelölőt a `[--ingestion-access {Disabled, Enabled}]` `[--query-access {Disabled, Enabled}]` Log [Analytics-munkaterületeken](/cli/azure/monitor/log-analytics/workspace) vagy Application Insights [összetevőkön.](/cli/azure/ext/application-insights/monitor/app-insights/component)

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Egyéni naplók és IIS-naplók gyűjtése Private Link

A tárfiókok az egyéni naplók feldolgozásának folyamatában használatosak. Alapértelmezés szerint a rendszer szolgáltatás által felügyelt tárfiókokat használ. Ha azonban egyéni naplókat kell behozni a privát kapcsolatokhoz, saját tárfiókokat kell használnia, és társítva kell őket a Log Analytics-munkaterület(ök)hez. További részletek az ilyen fiókok parancssor használatával [való beállításáról.](/cli/azure/monitor/log-analytics/workspace/linked-storage)

A saját tárfiókok hozzuk létre a következőt: Ügyfél tulajdonában lévő tárfiókok [a naplókhoz való adatbeukításhoz](private-storage.md)

## <a name="restrictions-and-limitations"></a>Korlátozások

### <a name="ampls"></a>AMPLS
Az AMPLS-objektum számos korlátozással rendelkezik, amelyek figyelembe veendők a Private Link tervezésekor:

* Egy virtuális hálózat csak 1 AMPLS-objektumhoz tud csatlakozni. Ez azt jelenti, hogy az AMPLS-objektumnak hozzáférést kell adnia az összes Azure Monitor erőforráshoz, amelyekhez a virtuális hálózatnak hozzáféréssel kell rendelkezik.
* Egy Azure Monitor erőforrás (munkaterület vagy Application Insights összetevő) 5 AMPLS-hez csatlakozhat.
* Az AMPLS-objektumok 50 Azure Monitor csatlakozhatnak.
* Az AMPLS-objektumok 10 privát végponthoz csatlakozhatnak.

A [korlátok mélyebb áttekintését](#consider-limits) lásd: Korlátok figyelembe venni.

### <a name="agents"></a>Ügynökök

A Windows- és Linux-ügynökök legújabb verzióit kell használni a Log Analytics-munkaterületek biztonságos be- és beúsításának támogatásához. A régebbi verziók nem tudnak monitorozási adatokat feltölteni magánhálózaton.

**Log Analytics Windows-ügynök**

Használja a Log Analytics-ügynök 10.20.18038.0-s vagy újabb verzióját.

**A Log Analytics Linux-ügynöke**

Az ügynök 1.12.25-ös vagy újabb verzióját használja. Ha nem, futtassa az alábbi parancsokat a virtuális gépen.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure Portal

Az Azure Monitor portál Application Insights és a Log Analytics használata esetén engedélyeznie kell, hogy a Azure Portal- és Azure Monitor-bővítmények elérhetők legyenek a magánhálózaton. Adja hozzá az [](../../firewall/service-tags.md) **AzureActiveDirectory,** az **AzureResourceManager,** az **AzureFrontDoor.FirstParty** és az **AzureFrontdoor.Frontend szolgáltatáscímkéket** a hálózati biztonsági csoporthoz.

### <a name="querying-data"></a>Adatok lekérdezése
Az [ `externaldata` operátor](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) nem támogatott egy Private Link, mivel adatokat olvas a tárfiókból, de nem garantálja a tárterület privát hozzáférését.

### <a name="programmatic-access"></a>Szoftveres hozzáférés

Ha a REST API, a [parancssori](/cli/azure/monitor) felület vagy a PowerShell Azure Monitor-nal szeretne privát hálózatokon használni, adja hozzá az [](../../virtual-network/service-tags-overview.md)**AzureActiveDirectory** és az **AzureResourceManager** szolgáltatáscímkéket a tűzfalhoz.  

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights SDK-letöltések tartalomk kézbesítési hálózatról

Csomagolja a JavaScript-kódot a szkriptbe, hogy a böngésző ne kísérelje meg letölteni a kódot a CDN-ről. Erre mutatunk be egy példát a [GitHubon](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>Böngésző DNS-beállításai

Ha az erőforrásokhoz egy Azure Monitor keresztül csatlakozik, az erőforrásokra Private Link forgalomnak a hálózaton konfigurált privát végponton kell áthaladnia. A privát végpont engedélyezéséhez frissítse a DNS-beállításokat [a Csatlakozás privát végponthoz című részben leírtak szerint.](#connect-to-a-private-endpoint) Egyes böngészők saját DNS-beállításokat használnak az Ön által beállítottak helyett. Előfordulhat, hogy a böngésző megpróbál csatlakozni Azure Monitor nyilvános végponthoz, és megkerülni a Private Link teljes egészében. Ellenőrizze, hogy a böngészők beállításai nem bírálják-e felül vagy gyorsítótárazhatják-e a régi DNS-beállításokat. 

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [privát tárolókról](private-storage.md)
