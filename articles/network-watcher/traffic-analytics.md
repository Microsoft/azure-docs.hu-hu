---
title: Azure Traffic Analytics | Microsoft Docs
description: Ismerje meg, hogyan elemezheti az Azure hálózati biztonsági csoportok flow-naplóit a Traffic Analytics használatával.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.reviewer: vinigam
ms.custom: references_regions
ms.openlocfilehash: 87dacd28223fd88866edb90266aa76fd3cdf86cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095364"
---
# <a name="traffic-analytics"></a>Forgalmi elemzések

Traffic Analytics egy felhőalapú megoldás, amely láthatóságot biztosít a felhasználói és alkalmazási tevékenységeknek a felhőalapú hálózatokban. A Traffic Analytics Network Watcher hálózati biztonsági csoport (NSG) folyamatábráit elemzi, hogy betekintést nyújtson az Azure-felhőbe irányuló forgalomba. A Traffic Analytics segítségével a következőket teheti:

- Megjelenítheti a hálózati tevékenységeket az Azure-előfizetések között, és azonosíthatja a gyors helyeket.
- Azonosítsa a biztonsági fenyegetéseket, és gondoskodjon a hálózat biztonságáról, olyan információkkal, mint a nyílt portok, az internet-hozzáférést megkísérlő alkalmazások és a virtuális gépek (VM) a támadó hálózatokhoz való csatlakozáskor.
- Ismerje meg az Azure-régiók és az Internet forgalmi forgalmának mintáit a hálózati üzembe helyezés teljesítményének és kapacitásának optimalizálása érdekében.
- A hálózat hibás kapcsolataihoz vezető hálózati helytelen konfigurációkat.

> [!NOTE]
> A Traffic Analytics mostantól támogatja a NSG adatfolyam-naplók adatok gyűjtését 10 percnél nagyobb gyakorisággal

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Miért érdemes a Traffic Analytics?

A biztonság, a megfelelőség és a teljesítmény érdekében elengedhetetlen a saját hálózat figyelése, kezelése és megismerése. A saját környezetének ismerete rendkívül fontos a védelemhez és az optimalizáláshoz. Gyakran ismernie kell a hálózat aktuális állapotát, amely összeköti a csatlakozást, és hogy mely portok állnak nyitva az interneten, a várt hálózati működés, a szabálytalan hálózati viselkedés és a forgalom hirtelen emelkedése.

A felhőalapú hálózatok különböznek a helyszíni vállalati hálózatoktól, ahol netflow vagy egyenértékű protokoll-kompatibilis útválasztók és kapcsolók találhatók, amelyek lehetővé teszik az IP-hálózati forgalom gyűjtését, amikor belépnek vagy kilépnek egy hálózati adapterből. A forgalmi adatok elemzésével a hálózati forgalom és a kötet elemzését is létrehozhatja.

Az Azure-beli virtuális hálózatok NSG rendelkeznek, amelyek az egyes hálózati adapterekhez, virtuális gépekhez vagy alhálózatokhoz társított hálózati biztonsági csoportokon keresztül információt biztosítanak a bejövő és kimenő IP-forgalomról. A Traffic Analytics a nyers NSG-naplók elemzésével, valamint a biztonság, a topológia és a földrajz intelligenciának behelyezésével betekintést nyújt az adatforgalom forgalmára a környezetében. Traffic Analytics olyan információkat biztosít, mint például a legtöbb kommunikációs gazdagép, a legtöbb kommunikációs alkalmazási protokoll, a legtöbb kommunikációt biztosító fogadó pár, az engedélyezett/letiltott forgalom, a bejövő/kimenő forgalom, az internetes portok megnyitása, a legtöbb blokkolási szabály, az Azure-adatközpont, a virtuális hálózat, az alhálózatok vagy a szélhámos hálózatok forgalma.

## <a name="key-components"></a>A legfontosabb összetevők

- **Hálózati biztonsági csoport (NSG)**: olyan biztonsági szabályok listáját tartalmazza, amelyek engedélyezik vagy megtagadják a hálózati forgalmat az Azure Virtual Networkhoz csatlakoztatott erőforrásokhoz. Az NSG-k társíthatóak alhálózatokhoz, egyedi virtuális gépekhez (klasszikus) vagy virtuális gépekhez (Resource Manager) kapcsolt hálózati adapterekhez (NIC). További információ: [hálózati biztonsági csoport áttekintése](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Hálózati biztonsági csoport (NSG) folyamatábrái**: lehetővé teszi, hogy egy hálózati biztonsági csoporton keresztül megtekintse a bejövő és kimenő IP-forgalomra vonatkozó információkat. A NSG JSON formátumban vannak beírva, és a kimenő és a bejövő folyamatok megjelenítése egy szabály alapján történik, a folyamat a hálózati adapterre vonatkozik, öt rekordos információ a folyamatról (forrás/cél IP-cím, forrás/cél port és protokoll), és ha a forgalom engedélyezett vagy megtagadva. További információ a NSG: [NSG flow-naplók](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics**: olyan Azure-szolgáltatás, amely figyeli az adatokat, és egy központi tárházban tárolja az adatokat. Ezek az információk lehetnek az Azure API-n keresztül biztosított események, teljesítményadatok vagy egyéni adat. Az összegyűjtésüket követően az adatok használhatók riasztáshoz, elemzéshez vagy exportáláshoz. Az alkalmazások figyelése, például a Network Performance monitor és a Traffic Analytics, Azure Monitor naplók használatával épül fel. További információ: [Azure monitor naplók](../azure-monitor/logs/log-query-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Log Analytics munkaterület**: Azure monitor naplók egy példánya, ahol az Azure-fiókhoz tartozó adatmennyiséget tárolja a rendszer. Log Analytics munkaterületekről további információt a [log Analytics munkaterület létrehozása](../azure-monitor/logs/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)című témakörben talál.
- **Network Watcher**: egy regionális szolgáltatás, amely lehetővé teszi a feltételek figyelését és diagnosztizálását az Azure-beli hálózati forgatókönyvek szintjén. A NSG flow-naplókat be-és kikapcsolhatja Network Watcher segítségével. További információ: [Network Watcher](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>A Traffic Analytics működése

A Traffic Analytics megvizsgálja a nyers NSG-naplókat, és rögzíti a csökkentett naplókat az azonos forrás IP-cím, cél IP-cím, célport és protokoll közötti közös folyamatok összesítésével. Tegyük fel például, hogy az 1. állomás (IP-cím: 10.10.10.10) a 2-es gazdagépre (IP-cím: 10.10.20.10), 100-re, a port (például 80) és a protokoll (például a http) használatával kommunikál. A csökkentett napló egyetlen bejegyzést tartalmaz, amely az 1. & gazdagép 2. állomása 1 órán keresztül 100 alkalommal kommunikált a *80* -es és a *http*-protokollon keresztül, a 100-bejegyzések helyett. A csökkentett naplók a földrajz, a biztonság és a topológia adataival bővülnek, majd egy Log Analytics munkaterületen tárolódnak. Az alábbi ábrán az adatfolyam látható:

![Adatáramlás a NSG-folyamat naplói feldolgozásához](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions-nsg"></a>Támogatott régiók: NSG 

A NSG Traffic Analytics a következő támogatott régiókban használható:
:::row:::
   :::column span="":::
      Ausztrália középső régiója  
      Kelet-Ausztrália  
      Délkelet-Ausztrália  
      Dél-Brazília  
      Közép-Kanada  
      Kelet-Kanada  
      Közép-India  
      Az USA középső régiója  
      Kelet-Kína 2  
      Észak-Kína 2  
      Kelet-Ázsia       
   :::column-end:::
   :::column span="":::
      USA keleti régiója  
      USA 2. keleti régiója  
      USA 2. keleti – EUAP  
      Közép-Franciaország  
      Középnyugat-Németország Kelet-Japán  
      Nyugat-Japán  
      Dél-Korea középső régiója  
      Dél-Korea déli régiója  
      USA északi középső régiója  
      Észak-Európa    
   :::column-end:::
   :::column span="":::
      Dél-Afrika északi régiója  
      USA déli középső régiója  
      Dél-India  
      Délkelet-Ázsia  
      Észak-Svájc  
      Nyugat-Svájc  
      Észak-Egyesült Arab  
      Az Egyesült Királyság déli régiója  
      Az Egyesült Királyság nyugati régiója   
      USGov Arizona  
      USGov Texas
   :::column-end:::
   :::column span="":::
      USGov Virginia  
      USNat keleti régiója  
      USNat nyugati régiója  
      USSec keleti régiója  
      USSec nyugati régiója  
      USA nyugati középső régiója  
      Nyugat-Európa  
      USA nyugati régiója  
      USA 2. nyugati régiója  
   :::column-end:::
:::row-end:::

## <a name="supported-regions-log-analytics-workspaces"></a>Támogatott régiók: Log Analytics munkaterületek

A Log Analytics munkaterület a következő régiókban kell, hogy legyen:
:::row:::
   :::column span="":::
      Ausztrália középső régiója  
      Kelet-Ausztrália  
      Délkelet-Ausztrália  
      Dél-Brazília  
      Közép-Kanada  
      Közép-India  
      Az USA középső régiója  
      Kelet-Kína 2  
      Kelet-Ázsia  
      USA keleti régiója  
   :::column-end:::
   :::column span="":::
      USA 2. keleti régiója  
      USA 2. keleti – EUAP  
      Közép-Franciaország  
      Középnyugat-Németország  
      Kelet-Japán  
      Dél-Korea középső régiója  
      USA északi középső régiója  
      Észak-Európa  
      Dél-Afrika északi régiója  
      USA déli középső régiója  
   :::column-end:::
   :::column span="":::
      Délkelet-Ázsia  
      Észak-Svájc  
      Nyugat-Svájc  
      UAE középső régiója  
      Észak-Egyesült Arab  
      Az Egyesült Királyság déli régiója  
      Az Egyesült Királyság nyugati régiója   
      USGov Arizona  
      USGov Virginia  
      USNat keleti régiója   
   :::column-end:::
   :::column span="":::
      USNat nyugati régiója   
      USSec keleti régiója  
      USSec nyugati régiója  
      USA nyugati középső régiója  
      Nyugat-Európa  
      USA nyugati régiója  
      USA 2. nyugati régiója  
   :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>Előfeltételek

### <a name="user-access-requirements"></a>Felhasználói hozzáférési követelmények

A fiókjának a következő [Azure beépített szerepkörök](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)egyikének kell lennie:

|Üzembehelyezési modell   | Szerepkör                   |
|---------          |---------               |
|Resource Manager   | Tulajdonos                  |
|                   | Közreműködő            |
|                   | Olvasó                 |
|                   | Hálózati közreműködő    |

Ha a fiókja nincs hozzárendelve az egyik beépített szerepkörhöz, azt hozzá kell rendelni egy [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) , amely a következő műveletekhez van hozzárendelve az előfizetési szinten:

- "Microsoft. Network/applicationGateways/Read"
- "Microsoft. Network/Connections/Read"
- "Microsoft. Network/loadBalancers/Read"
- "Microsoft. Network/localNetworkGateways/Read"
- "Microsoft. Network/networkInterfaces/Read"
- "Microsoft. Network/networkSecurityGroups/Read"
- "Microsoft. Network/nyilvános IP/Read"
- "Microsoft. Network/routeTables/Read"
- "Microsoft. Network/virtualNetworkGateways/Read"
- "Microsoft. Network/virtualNetworks/Read"
- "Microsoft. Network/expressRouteCircuits/Read"

A felhasználói hozzáférési engedélyek vizsgálatával kapcsolatos információkért lásd: [Traffic Analytics – gyakori kérdések](traffic-analytics-faq.md).

### <a name="enable-network-watcher"></a>A Network Watcher engedélyezése

A forgalom elemzéséhez rendelkeznie kell egy meglévő hálózati figyelővel, vagy [engedélyeznie kell egy hálózati figyelőt](network-watcher-create.md) minden olyan régióban, amelyhez NSG szeretné elemezni a forgalmat. A Traffic Analytics a [támogatott régiókban](#supported-regions-nsg)üzemeltetett NSG is engedélyezhető.

### <a name="select-a-network-security-group"></a>Hálózati biztonsági csoport kiválasztása

A NSG folyamat naplózásának engedélyezése előtt hálózati biztonsági csoporttal kell rendelkeznie a folyamatok naplózásához. Ha nem rendelkezik hálózati biztonsági csoporttal, a létrehozásához tekintse meg [a hálózati biztonsági csoport létrehozása](../virtual-network/manage-network-security-group.md#create-a-network-security-group) című témakört.

A Azure Portal területen lépjen a **Network Watcher** elemre, majd válassza a **NSG flow-naplók** lehetőséget. Válassza ki azt a hálózati biztonsági csoportot, amely számára engedélyezni kívánja a NSG flow-naplóját a következő képen látható módon:

![A NSG flow naplójának engedélyezését igénylő NSG kiválasztása](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Ha olyan NSG próbálja meg a Traffic Analytics szolgáltatást, amely a [támogatott régión](#supported-regions-nsg)kívül más régióban található, "nem található" hibaüzenet jelenik meg.

## <a name="enable-flow-log-settings"></a>Folyamat naplójának beállításainak engedélyezése

A folyamat naplójának beállításainak engedélyezése előtt el kell végeznie a következő feladatokat:

Regisztrálja az Azure-beli bepillantást nyújtó szolgáltatót, ha még nincs regisztrálva az előfizetésében:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

Ha még nem rendelkezik Azure Storage-fiókkal a NSG-flow-naplók tárolásához a alkalmazásban, létre kell hoznia egy Storage-fiókot. Hozzon létre egy Storage-fiókot az alábbi parancs használatával. A parancs futtatása előtt cserélje le a `<replace-with-your-unique-storage-account-name>` karaktert az összes Azure-helyen található egyedi névre, amely 3-24 karakter hosszúságú, és csak számokat és kisbetűket használ. Szükség esetén módosíthatja az erőforráscsoport nevét is.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Válassza ki a következő beállításokat a képen látható módon:

1. **Állapot** *kiválasztása*
2. Válassza a *2. verziót* a flow- **naplók verziójának** kiválasztásához. A 2. verzió folyamat-munkameneti statisztikát tartalmaz (bájtok és csomagok)
3. Válasszon ki egy meglévő Storage-fiókot a folyamat naplófájljainak tárolásához a alkalmazásban. Győződjön meg arról, hogy a tároló nem rendelkezik "Data Lake Storage Gen2 hierarchikus névtér engedélyezve" beállítás értéke TRUE (igaz).
4. Állítsa be a **megőrzési** időt arra, hogy hány napig szeretné tárolni az adatok tárolását. Ha örökre szeretné tárolni az adattárolást, állítsa az értéket *0-ra*. A Storage-fiókhoz Azure Storage-díjakat kell fizetnie. 
5. **Traffic Analytics állapothoz** válassza *a* be lehetőséget.
6. Válassza ki a feldolgozási időközt. Az Ön választása alapján a rendszer begyűjti a flow-naplókat a Storage-fiókból, és Traffic Analytics dolgozza fel. 1 óránként vagy 10 percenként is kiválaszthatja a feldolgozási időközt. 
7. Válasszon ki egy meglévő Log Analytics (OMS) munkaterületet, vagy válassza az **Új munkaterület létrehozása** lehetőséget egy új létrehozásához. A Traffic Analytics a Log Analytics munkaterületet használja az elemzés létrehozásához használt összesített és indexelt adatokat tárolására. Ha egy meglévő munkaterületet választ ki, akkor azt a [támogatott régiók](#supported-regions-log-analytics-workspaces) egyikében kell megadnia, és az új lekérdezési nyelvre kell frissíteni. Ha nem szeretne frissíteni egy meglévő munkaterületet, vagy nem rendelkezik egy támogatott régióbeli munkaterülettel, hozzon létre egy újat. További információ a lekérdezési nyelvekről: az [Azure log Analytics frissítése az új naplók keresésére](../azure-monitor/logs/log-query-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

> [!NOTE]
>A Traffic Analytics-megoldást üzemeltető log Analytics-munkaterületnek és a NSG nem kell ugyanabban a régióban lennie. Előfordulhat például, hogy a Traffic Analytics egy olyan munkaterületen található a Nyugat-európai régióban, amely NSG az USA keleti régiójában és az USA nyugati régiójában. Több NSG is konfigurálható ugyanabban a munkaterületen.

8. Kattintson a **Mentés** gombra.

    ![A Storage-fiók kiválasztása, a Log Analytics munkaterület és a Traffic Analytics engedélyezése](./media/traffic-analytics/ta-customprocessinginterval.png)

Ismételje meg az előző lépéseket minden olyan NSG, amelyhez engedélyezni szeretné a Traffic Analytics szolgáltatást. A rendszer a munkaterületre küldi a folyamatokból származó adatok adatait, ezért ügyeljen arra, hogy az országában/régiójában lévő helyi törvények és rendeletek engedélyezzék az adattárolást abban a régióban, ahol a munkaterület létezik. Ha eltérő feldolgozási intervallumokat állított be különböző NSG, az adatok gyűjtése különböző időközönként történik. Például: engedélyezheti a 10 perces feldolgozási időközt a kritikus virtuális hálózatok esetében, és 1 órát a nem kritikus virtuális hálózatok.

A Traffic Analytics szolgáltatást a Azure PowerShell [set-AzNetworkWatcherConfigFlowLog PowerShell-](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) parancsmagjának használatával is konfigurálhatja. Futtassa `Get-Module -ListAvailable Az` a parancsot a telepített verzió megkereséséhez. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

## <a name="view-traffic-analytics"></a>Traffic Analytics megtekintése

Traffic Analytics megtekintéséhez keresse meg a **Network Watchert** a portál keresési sávján. Network Watcher belül, a Traffic Analytics és annak képességeinek megismeréséhez válassza a bal oldali menüben a **Traffic Analytics** lehetőséget. 

![A Traffic Analytics irányítópult elérése](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Az irányítópult az első alkalommal akár 30 percet is igénybe vehet, mert Traffic Analytics először elegendő adatokat kell összeállítania ahhoz, hogy értelmes elemzéseket lehessen bevezetni, mielőtt bármilyen jelentést hozna létre.

## <a name="usage-scenarios"></a>Használati forgatókönyvek

A Traffic Analytics teljes konfigurálása után érdemes megtekinteni az alábbi elemzéseket:

### <a name="find-traffic-hotspots"></a>Forgalmi pontok keresése

**keresni**

- Mely gazdagépek, alhálózatok és virtuális hálózatok küldik vagy fogadják a legtöbb forgalmat, áthaladva a maximális kártékony forgalmat, és blokkolja a jelentős folyamatokat?
    - A gazdagép, az alhálózat és a virtuális hálózat összehasonlító diagramjának megtekintése. Annak megértése, hogy mely gazdagépek, alhálózatok és virtuális hálózatok küldik vagy fogadják a legtöbb forgalmat, segíthetnek azonosítani azokat a gazdagépeket, amelyek a legnagyobb forgalmat dolgozzák fel, és hogy a forgalom eloszlása megfelelően történik-e.
    - Kiértékelheti, hogy a forgalom mennyisége megfelelő-e a gazdagép számára. A forgalom normális viselkedésű, vagy további vizsgálatot érdemel?
- Mennyi bejövő/kimenő forgalom van?
    -   A gazdagép várhatóan több bejövő forgalmat fogad, mint a kimenő, vagy fordítva?
- A letiltott forgalom statisztikája.
    - Miért blokkolja a gazdagép a jóindulatú forgalom jelentős mennyiségét? Ennek a viselkedésnek további vizsgálatra és valószínűleg a konfiguráció optimalizálására van szüksége
- A kártékony engedélyezett/letiltott forgalom statisztikája
  - Miért van olyan gazdagép, amely kártékony forgalmat fogad, és miért van engedélyezve a rosszindulatú források forgalma? Ennek a viselkedésnek további vizsgálatra és valószínűleg a konfiguráció optimalizálására van szüksége.

    Válassza az **összes** megjelenítése lehetőséget a **gazdagép** területen az alábbi ábrán látható módon:

    ![Az irányítópulton a legtöbb forgalmi adattal rendelkező gazdagép bemutatása](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- Az alábbi képen az első öt beszélő gazdagép és a flow-val kapcsolatos részletek (engedélyezett – bejövő/kimenő és megtagadott bejövő/kimenő folyamatok) időtrendjét láthatja a gazdagép számára:

    ![Az első öt leginkább beszélő gazda-trend](media/traffic-analytics/top-five-most-talking-host-trend.png)

**keresni**

- Melyek a leginkább megbeszélő gazda párok?
    - A várt viselkedés, például az előtér-vagy háttér-kommunikáció vagy a szabálytalan viselkedés, például a háttérbeli internetes forgalom.
- Az engedélyezett/letiltott forgalom statisztikája
    - Miért engedélyezi vagy blokkolja a gazdagép a jelentős adatforgalom mennyiségét
- Leggyakrabban használt alkalmazási protokoll a legtöbb megbeszélő fogadó pár közül:
    - Engedélyezve vannak ezek az alkalmazások ezen a hálózaton?
    - Megfelelően vannak konfigurálva az alkalmazások? A megfelelő protokollt használják a kommunikációhoz? Válassza az **összes megtekintése** a **gyakori beszélgetés** alatt lehetőséget, ahogy az a következő képen látható:

        ![A leggyakoribb beszélgetéseket bemutató irányítópult](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- Az alábbi ábrán az első öt beszélgetés és a folyamattal kapcsolatos részletek, például az engedélyezett és a megtagadott bejövő és kimenő folyamatok időbeli alakulása látható:

    ![Az első öt csevegő beszélgetés részletei és trend](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**keresni**

- Melyik alkalmazási protokollt használják leginkább a környezetben, és hogy a fogadó párok hogyan használják a legjobban az alkalmazási protokollt?
    - Engedélyezve vannak ezek az alkalmazások ezen a hálózaton?
    - Megfelelően vannak konfigurálva az alkalmazások? A megfelelő protokollt használják a kommunikációhoz? A várt viselkedés gyakori portok, például 80 és 443. Ha bármilyen szokatlan port jelenik meg, a normál kommunikációhoz szükség lehet a konfiguráció módosítására. Válassza az **összes** megjelenítése az **alkalmazás portja** területen az alábbi képen láthatót:

        ![Az irányítópultot bemutató legfontosabb alkalmazási protokollok](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Az alábbi képek az első öt L7 protokollok és a flow-val kapcsolatos részletek (például az engedélyezett és a megtagadott folyamatok) időtrendjét mutatják egy L7 protokoll esetében:

    ![A legfontosabb öt réteg 7 protokoll részletei és trend](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![A naplóbeli keresésben használt Application Protocol folyamat részletei](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**keresni**

- A VPN-átjáró kapacitás-kihasználtsági trendjei a környezetben.
    - Minden egyes VPN-SKU bizonyos mennyiségű sávszélességet tesz lehetővé. A VPN-átjárók nincsenek kihasználva?
    - Elérik-e az átjárók a kapacitást? A következő magasabb SKU-ra kell frissíteni?
- Melyek a leginkább megbeszélő gazdagépek, amelyeken keresztül a VPN-átjárón keresztül melyik portot?
    - Ez a minta normális? Válassza az **összes** megjelenítése a **VPN-átjáró** alatt lehetőséget, ahogyan az a következő képen látható:

        ![A legfelső szintű aktív VPN-kapcsolatokat bemutató irányítópult](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- Az alábbi ábrán egy Azure-VPN Gateway kapacitásának kihasználtsága és a folyamattal kapcsolatos részletek (például az engedélyezett folyamatok és portok) használatának időbeli alakulása látható:

    ![A VPN Gateway kihasználtságának trendje és a folyamat részletei](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Forgalom eloszlásának megjelenítése földrajz szerint

**keresni**

- Az adatközpontok forgalmának eloszlása, például egy adatközpontba irányuló adatforgalom fő forrásai, az adatközponttal beszélgető leggyakoribb gazember hálózatok és az alkalmazási protokollok legfelső szintű védelme.
  - Ha további terhelést figyel egy adatközpontban, megtervezheti a forgalom hatékony eloszlását.
  - Ha a támadó hálózatok beszélgetnek az adatközpontban, akkor a NSG-szabályok kiírásával letilthatják azokat.

    Az alábbi ábrán látható módon válassza ki a **Térkép megtekintése** **a környezetben**:

    ![A forgalom terjesztését bemutató irányítópult](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- A Geo-Térkép a felső menüszalagot jeleníti meg az olyan paraméterek kiválasztásához, mint például az adatközpontok (központilag telepített/nem telepíthető/aktív/inaktív/Traffic Analytics engedélyezve/Traffic Analytics nincs engedélyezve), valamint az olyan országok/régiók, amelyek hozzájárulnak a jóindulatú/rosszindulatú adatforgalomhoz az aktív

    ![Az aktív üzembe helyezést bemutató földrajzi Térkép nézet](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- A Geo-Térkép az adatközpontok forgalmának eloszlását mutatja országok/régiók és kontinensek között, amelyek a kék (jóindulatú) és a vörös (rosszindulatú) színes vonalakon kommunikálnak.

    ![Földrajzi Térkép nézet, amely az országok/régiók és földrészek forgalmának terjesztését jeleníti meg](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![A forgalom eloszlásának folyamatábrája a naplóbeli keresésben](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>A forgalom terjesztésének megjelenítése virtuális hálózatok alapján

**keresni**

- Forgalom eloszlása virtuális hálózat, topológia, a virtuális hálózat felé irányuló forgalom fő forrásai, a virtuális hálózattal megbeszélő leggyakoribb szabálysértő hálózatok és az alkalmazási protokollok legfelső szintű megbeszélgetése.
  - Annak ismerete, hogy melyik virtuális hálózat beszélget a virtuális hálózattal. Ha a beszélgetés nem várható, akkor kijavítható.
  - Ha a támadó hálózatok egy virtuális hálózattal beszélgetnek, a NSG-szabályok megadhatók a szabálysértő hálózatok blokkolásához.
 
    Válassza a **virtuális hálózatok megtekintése** lehetőséget a **környezetében**, az alábbi ábrán látható módon:

    ![A virtuális hálózatok terjesztését bemutató irányítópult](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- A Virtual Network topológia a felső menüszalagot jeleníti meg olyan paraméterek kiválasztásához, mint a virtuális hálózatok (többek között virtuális hálózati kapcsolatok/aktív/inaktív), külső kapcsolatok, aktív folyamatok és a virtuális hálózat rosszindulatú folyamatai.
- Az Virtual Network topológiát előfizetések, munkaterületek, erőforráscsoportok és időintervallum alapján szűrheti. A folyamat megértését segítő további szűrők: folyamat típusa (virtuális hálózatok közötti, IntraVNET stb.), folyamat iránya (bejövő, kimenő), folyamat állapota (engedélyezett, letiltott), virtuális hálózatok (megcélozva és csatlakoztatva), kapcsolattípus (peering vagy Gateway-P2S és S2S) és NSG. Ezekkel a szűrőkkel a részletesen vizsgálni kívánt virtuális hálózatok koncentrálhat.
- Az Virtual Network topológia a virtuális hálózat forgalmának eloszlását mutatja a folyamatok (engedélyezett/letiltott/bejövő/kimenő/jóindulatú/rosszindulatú), az Application Protocol és a hálózati biztonsági csoportok tekintetében, például:

    ![A forgalom terjesztését és a folyamat részleteit bemutató virtuális hálózati topológia](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![A legfelső szintű és több szűrőt bemutató virtuális hálózati topológia](./media/traffic-analytics/virtual-network-filters.png)

    ![A virtuális hálózati forgalom eloszlásának folyamatábrája a naplóbeli keresésben](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**keresni**

- Adatforgalom eloszlása alhálózat, topológia, az alhálózatra irányuló adatforgalom elsődleges forrásai, az alhálózattal beszélgető leggyakoribb gazember hálózatok és az alkalmazási protokollok legfelső szintű megbeszélgetése.
    - Annak ismerete, hogy mely alhálózatok beszélgetnek az alhálózattal. Ha váratlan beszélgetések láthatók, javítsa ki a konfigurációt.
    - Ha a támadó hálózatok egy alhálózattal beszélgetnek, a NSG szabályok konfigurálásával is kijavítani tudja a szélhámos hálózatok blokkolását.
- Az alhálózati topológia a felső menüszalagot jeleníti meg az olyan paraméterek kiválasztásához, mint az aktív/inaktív alhálózat, a külső kapcsolatok, az aktív folyamatok és az alhálózat rosszindulatú folyamatai.
- Az alhálózati topológia a virtuális hálózat forgalmának eloszlását mutatja a folyamatok tekintetében (engedélyezett/letiltott/bejövő/kimenő/jóindulatú/rosszindulatú), az Application Protocol és a NSG, például:

    ![Hálózati topológia, amely a forgalom elosztását bemutató virtuális hálózati alhálózatot mutat be.](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**keresni**

Az Application Gateway adatforgalmának eloszlása & Load Balancer, topológia, a forgalom legfelső szintű forgalma, az Application Gateway & Load Balancer és a legtöbbet beszélgető alkalmazás-protokollok között megjelenő leggyakoribb gazember hálózatok. 
    
 - Annak ismerete, hogy mely alhálózatok beszélgetnek az Application Gateway vagy a Load Balancer. Ha váratlan beszélgetéseket figyel, javítsa ki a konfigurációt.
 - Ha a támadó hálózatok egy Application Gateway vagy Load Balancer használatával beszélgetnek, a NSG szabályok konfigurálásával is kijavítani tudja a szélhámos hálózatok blokkolását. 

    ![A képernyőfelvétel egy alhálózati topológiát mutat be, amely az Application Gateway-alhálózatra irányuló forgalom eloszlását mutatja a folyamatok tekintetében.](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Az internetről érkező forgalmat fogadó portok és virtuális gépek megtekintése

**keresni**

- Mely nyitott portok találhatók az interneten?
  - Ha a rendszer váratlan portokat talál, javítsa ki a konfigurációt:

    ![Irányítópult, amely az internetre irányuló forgalom fogadását és küldését bemutató portokat mutat be](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Az Azure célport és a gazdagépek részletei](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**keresni**

Rosszindulatú forgalmat észlelt a környezetében? Honnan származnak? Hol van a rendeltetése?

![A rosszindulatú adatforgalom részletei a naplóbeli keresés során](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Trendek megjelenítése a NSG/NSG-szabályok találatai között

**keresni**

- Mely NSG/NSG szabályok rendelkeznek a legtöbb találattal az összehasonlító diagramon a folyamatok eloszlásával?
- Melyek a NSG/NSG szabályok legfontosabb forrás-és cél-beszélgetési párok?

    ![A NSG találati statisztikáit bemutató irányítópult](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- A következő képek a NSG-szabályok és a forrás-cél folyamat részleteinek időbeli alakulását mutatják be egy hálózati biztonsági csoport esetében:

  - Gyorsan megállapíthatja, hogy mely NSG és NSG szabályok haladnak át rosszindulatú folyamatokat, és melyek a Felhőbeli környezethez hozzáférő leggyakoribb kártékony IP-címek
  - Annak meghatározása, hogy mely NSG/NSG szabályok engedélyezik vagy blokkolja a jelentős hálózati forgalmat
  - NSG vagy NSG szabályok részletes ellenőrzéséhez válassza ki a legfelső szintű szűrőket

    ![A NSG-szabályok látogatottsági idejének és a legfelső NSG szabályainak bemutatása](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![A legfontosabb NSG-szabályok statisztikájának részletei a naplóbeli keresésben](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Ha választ szeretne kapni a gyakori kérdésekre, tekintse meg a [Traffic Analytics – gyakori](traffic-analytics-faq.md)kérdések című témakört.

## <a name="next-steps"></a>Következő lépések

- A flow-naplók engedélyezésével kapcsolatos további információkért lásd: a [NSG folyamat naplózásának engedélyezése](network-watcher-nsg-flow-logging-portal.md).
- A Traffic Analytics sémájának és feldolgozási adatainak megismeréséhez tekintse meg a [Traffic Analytics-séma](traffic-analytics-schema.md)című témakört.
