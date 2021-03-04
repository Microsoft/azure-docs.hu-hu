---
title: Egyéni metrikák a Azure Monitorban (előzetes verzió)
description: Ismerkedjen meg az egyéni metrikákkal a Azure Monitorban és azok modellezésében.
author: anirudhcavale
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 01/25/2021
ms.openlocfilehash: c6e946d5aedb06899a44851b79581dbc518f41b0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052313"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Egyéni metrikák a Azure Monitorban (előzetes verzió)

Amikor erőforrásokat és alkalmazásokat helyez üzembe az Azure-ban, érdemes elkezdeni a telemetria gyűjtését, hogy betekintést nyerjen a teljesítménybe és az állapotba. Az Azure néhány mérőszámot elérhetővé tesz a dobozból. Ezeket a metrikákat [standard vagy platformnak](./metrics-supported.md)nevezzük. Azonban a természetük korlátozott. 

Érdemes lehet összegyűjteni néhány egyéni teljesítménymutatót vagy üzleti specifikus mérőszámot a mélyebb elemzések biztosításához. Ezek az **Egyéni** metrikák az alkalmazás telemetria, az Azure-erőforrásokon futtatott ügynökkel, vagy akár egy külső figyelési rendszerrel is összegyűjthetők, és közvetlenül a Azure monitorhoz lesznek elküldve. A Azure Monitor közzétételét követően az Azure-erőforrások és-alkalmazások egyéni metrikáit tallózással, lekérdezéssel és riasztással láthatja el egymás mellett az Azure által kibocsátott szabványos metrikákkal.

Azure Monitor egyéni metrikák jelenleg nyilvános előzetes verzióban érhetők el. 

## <a name="methods-to-send-custom-metrics"></a>Egyéni metrikák küldésének módszerei

Az egyéni metrikák több módszer használatával is elküldhetők Azure Monitorba:
- Az alkalmazást az Azure Application Insights SDK használatával, az egyéni telemetria pedig a Azure Monitorba küldheti. 
- Telepítse a Azure Monitor-ügynököt (előzetes verzió) a [Windows vagy Linux rendszerű Azure-beli virtuális gépre](../agents/azure-monitor-agent-overview.md) , és használjon egy [adatgyűjtési szabályt](../agents/data-collection-rule-azure-monitor-agent.md) a teljesítményszámlálók Azure monitor metrikák küldéséhez.
- Telepítse a Windows Azure Diagnostics (WAD) bővítményt az [Azure](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)-beli virtuális gépre, a [virtuálisgép-méretezési csoportra](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md), a [klasszikus virtuális gépre](../essentials/collect-custom-metrics-guestos-vm-classic.md)vagy a [klasszikus Cloud Servicesre](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md) , és küldje el a teljesítményszámlálók Azure monitor. 
- Telepítse a [InfluxData-előgrafi ügynököt](../essentials/collect-custom-metrics-linux-telegraf.md) az Azure Linux rendszerű virtuális gépre, és küldje el a metrikákat a Azure monitor kimeneti beépülő modullal.
- Egyéni metrikák küldése [közvetlenül a Azure Monitor REST API](./metrics-store-custom-rest-api.md) `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics` .

## <a name="pricing-model-and-retention"></a>Díjszabási modell és adatmegőrzés

Az egyéni metrikák és metrikák lekérdezései esetén a számlázás engedélyezésével kapcsolatos részletekért olvassa el a [Azure monitor díjszabási oldalát](https://azure.microsoft.com/pricing/details/monitor/) . Ezen az oldalon az összes mérőszámra vonatkozó egyedi ár, beleértve az egyéni metrikákat és a metrikai lekérdezéseket is. Összefoglalva, a standard mérőszámok (platform-metrikák) Azure Monitor metrikai tárolóba való betöltésének költsége nem terhelhető, de az egyéni metrikák költségei az általános rendelkezésre állás megadásával járnak. A metrikus API-lekérdezések költségei is felmerülnek.

Az egyéni metrikák megmaradnak a [platform metrikáinak azonos](../essentials/data-platform-metrics.md#retention-of-metrics)időtartamára. 

> [!NOTE]  
> A Application Insights SDK-n keresztül Azure Monitor elküldhető metrikák számlázása betöltött naplóként történik. Csak akkor számítanak fel további metrikákat, ha a Application Insights szolgáltatás [engedélyezi a riasztást az egyéni metrikai dimenziók](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) beállításban. Ez a jelölőnégyzet az egyéni metrikák API használatával küldi az adatokat a Azure Monitor metrikák adatbázisába, hogy az összetettebb riasztást engedélyezze.  További információ a [Application Insights díjszabási modelljéről](../app/pricing.md#pricing-model) és [árairól a régiójában](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="how-to-send-custom-metrics"></a>Egyéni metrikák küldése

Ha egyéni metrikákat küld Azure Monitorre, akkor a jelentett adatpontoknak vagy értékeknek a következő információkat kell tartalmazniuk.

### <a name="authentication"></a>Hitelesítés
Ha egyéni metrikákat szeretne beküldeni a Azure Monitorba, a metrikát elküldő entitásnak érvényes Azure Active Directory (Azure AD) tokenre van szüksége a kérelem **tulajdonosi** fejlécében. Az érvényes tulajdonosi jogkivonatok beszerzésének néhány támogatott módja:
1. [Felügyelt identitások az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md). Identitást ad az Azure-erőforrásokhoz, például egy virtuális géphez. Managed Service Identity (MSI) úgy van kialakítva, hogy erőforrás-engedélyeket biztosítson bizonyos műveletek végrehajtásához. Ilyen például, ha egy erőforrás saját magáról bocsát ki mérőszámokat. Egy erőforrás vagy annak MSI-je egy másik erőforráson is megadhat **figyelési metrikák közzétevői** engedélyeit. Ezzel az engedéllyel az MSI más erőforrásokhoz is kibocsáthat metrikákat.
2. [Azure ad-szolgáltatásnév](../../active-directory/develop/app-objects-and-service-principals.md). Ebben a forgatókönyvben egy Azure AD-alkalmazás vagy-szolgáltatás engedélyeket rendelhet az Azure-erőforrásokra vonatkozó mérőszámok kibocsátásához.
A kérelem hitelesítéséhez Azure Monitor érvényesíti az alkalmazás tokenjét az Azure AD nyilvános kulcsaival. A meglévő **figyelési metrikák közzétevői** szerepköre már rendelkezik ezzel az engedéllyel. Ez elérhető a Azure Portalban. Az egyszerű szolgáltatásnév attól függően, hogy milyen erőforrásokra bocsát ki egyéni metrikákat, megadhatja a **figyelési metrikák közzétevői** szerepkörét a szükséges hatókörben. Ilyenek például az előfizetés, az erőforráscsoport vagy az adott erőforrás.

> [!TIP]  
> Ha Azure AD-jogkivonatot kér egyéni metrikák kibocsátására, győződjön meg arról, hogy a tokenhez tartozó célközönség vagy erőforrás a (z `https://monitoring.azure.com/` ). Ügyeljen arra, hogy tartalmazza a "/" zárót.

### <a name="subject"></a>Tárgy
Ez a tulajdonság rögzíti, hogy az egyéni metrika melyik Azure-erőforrás-AZONOSÍTÓval van jelezve. Ezek az adatok az API-hívás URL-címében lesznek kódolva. Az egyes API-k csak egyetlen Azure-erőforráshoz küldhetnek metrikai értékeket.

> [!NOTE]  
> Egy erőforráscsoport vagy előfizetés erőforrás-azonosítójával nem lehet egyéni metrikákat kibocsátani.


### <a name="region"></a>Region
Ez a tulajdonság azt az Azure-régiót rögzíti, amelybe a metrikákat kibocsátó erőforrást telepíti. A metrikákat ugyanarra a Azure Monitor regionális végpontra kell kiadni, mint az erőforrás üzembe helyezésének régióját. Az USA nyugati régiójában üzembe helyezett virtuális gépek egyéni metrikáit például a WestUS regionális Azure Monitor végpontjának kell elküldeni. A régió információi az API-hívás URL-címében is kódolva vannak.

> [!NOTE]  
> A nyilvános előzetes verzióban az egyéni metrikák csak az Azure-régiók egy részhalmazában érhetők el. A támogatott régiók listáját a cikk egy későbbi szakaszában dokumentáljuk.
>
>

### <a name="timestamp"></a>Időbélyeg
A Azure Monitor elküldett minden adatpontot időbélyeg-ként kell megjelölni. Ez az időbélyeg rögzíti azt a dátumot, amikor a metrika értékét méri vagy gyűjti. A Azure Monitor a metrikákkal rendelkező metrikus adatokat az elmúlt 20 percen belül, a későbbiekben pedig 5 percet fogad el. Az időbélyegnek ISO 8601 formátumúnak kell lennie.

### <a name="namespace"></a>Névtér
A névterek segítségével kategorizálhatja vagy csoportosíthatja a hasonló metrikákat. A névterek segítségével elkülönítheti a metrikák csoportjai között, amelyek különböző adatokat gyűjthetnek vagy teljesítménymutatókat. Előfordulhat például, hogy rendelkezik egy **contosomemorymetrics** nevű névtérrel, amely nyomon követi az alkalmazás profilját használó memóriahasználat mérőszámait. Egy másik, **contosoapptransaction** nevű névtér nyomon követheti az alkalmazás felhasználói tranzakcióinak összes mérőszámát.

### <a name="name"></a>Name
A **név** a jelentett metrika neve. Általában a név elég leíró a mért értékek azonosításához. Egy példa egy metrika, amely az adott virtuális gépen használt memória bájtjainak számát méri. Lehet, hogy a metrika neve például a **használatban lévő memória bájtjai**.

### <a name="dimension-keys"></a>Dimenzió kulcsai
A dimenzió egy kulcs vagy érték pár, amely segít a begyűjtött metrika további jellemzőinek leírásában. A további jellemzők használatával további információkat gyűjthet a metrikáról, ami mélyebb elemzéseket tesz lehetővé. Előfordulhat például, hogy a **használatban lévő memória bájtjainak** egy **folyamat** nevű dimenzió kulcsa van, amely rögzíti, hogy a virtuális gép minden egyes folyamata hány bájt memóriát használ fel. Ennek a kulcsnak a használatával szűrheti a metrikát, hogy megtudja, milyen mennyiségű memória-specifikus folyamat használja, vagy az első öt folyamatot a memóriahasználat alapján azonosítsa.
A méretek nem kötelezőek, nem minden metrika rendelkezhet dimenziókkal. Egy egyéni metrika legfeljebb 10 dimenzióval rendelkezhet.

### <a name="dimension-values"></a>Dimenzió értékei
A metrikai adatpontok jelentésekor a jelentésben szereplő összes dimenzió kulcshoz tartozik egy megfelelő dimenzióérték. Előfordulhat például, hogy a virtuális gépen a ContosoApp által használt memóriát szeretné jelenteni:

* A metrika neve a **használatban lévő memória bájtjai**.
* A dimenzió kulcsa **feldolgozható**.
* A dimenzió értéke **ContosoApp.exe**.

Metrikus érték közzétételekor csak egy dimenzió érték adható meg dimenzió-kulcsként. Ha a virtuális gépen több folyamat esetében is ugyanazt a memóriát keresi, több metrikai értéket is jelenthet az adott időbélyeghez. Minden metrika értéke eltérő dimenzióértéket határozhat meg a **folyamat** dimenzió kulcsához.
A méretek nem kötelezőek, nem minden metrika rendelkezhet dimenziókkal. Ha egy metrikai bejegyzés definiálja a dimenzió kulcsait, a megfelelő dimenzióértékeket kötelező megadni.

### <a name="metric-values"></a>Metrika értékei
Azure Monitor az összes mérőszámot egyperces részletességi intervallumokban tárolja. Tisztában vagyunk azzal, hogy egy adott percben egy mérőszámot többször is fel kell venni. Ilyen például a processzorkihasználtság. Vagy előfordulhat, hogy sok különálló esemény esetében is mérhetőnek kell lennie. Ilyen például a bejelentkezési tranzakció késése. Ha korlátozni szeretné a Azure Monitor által kibocsátani és kifizetni kívánt nyers értékek számát, helyileg előre összesítheti és kibocsáthatja az értékeket:

* **Min**: a percben a minták és a mérések minimálisan megfigyelt értéke.
* **Max**: a percenként megfigyelt értékek maximális száma az összes mintából és mérésből.
* **Sum**: az összes mintából és mérésből származó összes megfigyelt érték összegzése a percben.
* **Darabszám**: a percben végrehajtott minták és mérések száma.

Ha például négy bejelentkezési tranzakciót adott meg az alkalmazásnak egy percen belül, az eredményül kapott mért késések a következők lehetnek:

|1. tranzakció|2. tranzakció|3. tranzakció|4. tranzakció|
|---|---|---|---|
|7 MS|4 MS|13 MS|16 MS|

Ezután az eredményül kapott metrikai kiadvány Azure Monitor a következő lesz:
* Minimum: 4
* Max.: 16
* Összeg: 40
* Darabszám: 4

Ha az alkalmazás nem tud helyileg előre összesíteni, és minden különálló mintát vagy eseményt azonnal ki kell bocsátani a gyűjteményből, a nyers mérték értékeit is kibocsáthatja. Például minden alkalommal, amikor bejelentkezik egy bejelentkezési tranzakció az alkalmazáson, egy mérőszámot tesz közzé, amely csak egyetlen méréssel Azure Monitor. Tehát egy 12 ms-os bejelentkezési tranzakció esetében a metrikus kiadvány a következő lesz:
* Perc: 12
* Max.: 12
* Összeg: 12
* Darabszám: 1

Ezzel a folyamattal több értéket is kibocsáthat ugyanahhoz a metrikai és dimenzió kombinációhoz egy adott percben. Azure Monitor ezután végrehajtja az adott percenként kiállított összes nyers értéket, és összesíti azokat.

### <a name="sample-custom-metric-publication"></a>Példa egyéni metrika közzétételére
A következő példában létrehozunk egy, a virtuális gép metrikai névtérének **memóriájában** **használatos memória-bájtok** nevű egyéni metrikát. A metrika egyetlen, **folyamat** nevű dimenzióval rendelkezik. Az adott időbélyeg esetében két különböző folyamat metrikáit bocsátjuk ki:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"
        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> A Application Insights, a diagnosztikai bővítmény és a InfluxData-Graf ügynök már úgy van konfigurálva, hogy metrikai értékeket állítson ki a megfelelő regionális végponton, és az összes korábbi tulajdonságot az összes kibocsátásban elvégezze.
>
>

## <a name="custom-metric-definitions"></a>Egyéni metrika-definíciók
A kibocsátása előtt nem kell előre definiálni egy egyéni metrikát Azure Monitor. Minden közzétett metrikai adatpont névteret, nevet és dimenzió információt tartalmaz. Tehát amikor a rendszer először egy egyéni metrikát bocsát ki Azure Monitorre, a rendszer automatikusan létrehoz egy metrika-definíciót. Ez a metrikai definíció ezt követően minden olyan erőforráson felderíthető, amelyet a metrika a metrika-definíciók használatával bocsát ki.

> [!NOTE]  
> A Azure Monitor még nem támogatja az egyéni metrika **egységének** definiálását.

## <a name="using-custom-metrics"></a>Egyéni metrikák használata
Miután elküldte az egyéni metrikákat Azure Monitorre, böngészheti őket a Azure Portal, és a Azure Monitor REST API-kon keresztül kérdezheti le őket. Riasztásokat is létrehozhat rajtuk, hogy értesítést kapjon, ha bizonyos feltételek teljesülnek.

> [!NOTE]
> Az egyéni metrikák megtekintéséhez olvasó vagy közreműködői szerepkörnek kell lennie. Lásd: az [olvasó figyelése](../../role-based-access-control/built-in-roles.md#monitoring-reader). 

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Egyéni metrikák böngészése az Azure Portal használatával
1.    Nyissa meg az [Azure Portal](https://portal.azure.com).
2.    Válassza a **figyelés** ablaktáblát.
3.    Válassza a **Metrikák** lehetőséget.
4.    Válassza ki azt az erőforrást, amelyről egyéni metrikákat adott ki.
5.    Válassza ki az egyéni metrika metrikai névterét.
6.    Válassza ki az egyéni metrikát.

> [!NOTE]
> A Azure Portal metrikáinak megtekintésével kapcsolatos további információkért tekintse meg a [Bevezetés az Azure Metrikaböngésző](./metrics-getting-started.md) használatába című témakört.

## <a name="supported-regions"></a>Támogatott régiók
A nyilvános előzetes verzióban az egyéni metrikák közzétételének lehetősége csak az Azure-régiók egy részhalmazában érhető el. Ez a korlátozás azt jelenti, hogy a metrikák csak az egyik támogatott régió erőforrásai számára tehetők közzé. Az Azure-régiókkal kapcsolatos további információkért tekintse meg az [Azure földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/) területeit. Az alábbi végpontokban használt Azure-régiókód csak a régió neve, ahol a szóköz el van távolítva. a következő táblázat felsorolja az egyéni metrikák támogatott Azure-régióinak készletét. Emellett felsorolja azokat a végpontokat is, amelyek az adott régiókban lévő erőforrások metrikáit teszik közzé:

|Azure-régió |Regionális végpont előtagja|
|---|---|
| Minden nyilvános Felhőbeli régió | https://<azure_region_code>. monitoring.azure.com |
| **Azure Government** | |
| USA-beli államigazgatás – Arizona | https: \/ /usgovarizona.monitoring.Azure.us |
| **Kína** | |
| Kelet-Kína 2 | https: \/ /chinaeast2.monitoring.Azure.cn |

## <a name="latency-and-storage-retention"></a>Késés és tárolás megőrzése

Ha hozzáad egy új mérőszámot, vagy egy mérőszámhoz hozzáadott új dimenziót, akár 2 – 3 percet is igénybe vehet. A rendszeren egyszer az adatmennyiség az idő 99%-ában kevesebb, mint 30 másodperc. 

Ha töröl egy metrikát, vagy eltávolít egy dimenziót, a módosítás elvégzése egy hétig is elvégezheti a rendszerből a hónapot.

## <a name="quotas-and-limits"></a>Kvóták és korlátok
A Azure Monitor a következő használati korlátokat szabja meg az egyéni metrikák esetében:

|Kategória|Korlát|
|---|---|
|Aktív idősorozat/előfizetések/régió|50,000|
|Dimenzió kulcsok/metrika|10|
|A metrikai névterek, a metrikák nevei, a dimenzió kulcsai és a dimenzió értékeinek karakterlánc-hossza|256 karakter|

Az aktív idősorozatok a metrika, a dimenzióérték vagy a dimenzió érték bármely egyedi kombinációja, amely az elmúlt 12 órában közzétett metrikai értékeket tartalmaz.

## <a name="next-steps"></a>Következő lépések
Egyéni metrikák használata különböző szolgáltatásokból: 
 - [Virtual Machines](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Virtuálisgép-méretezési csoport](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure Virtual Machines (klasszikus)](../essentials/collect-custom-metrics-guestos-vm-classic.md)
 - [Linux rendszerű virtuális gép a saját Graf ügynök használatával](../essentials/collect-custom-metrics-linux-telegraf.md)
 - [REST API](./metrics-store-custom-rest-api.md)
 - [Klasszikus Cloud Services](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md)
