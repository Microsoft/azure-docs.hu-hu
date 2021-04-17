---
title: Egyéni metrikák a Azure Monitor (előzetes verzió)
description: Ismerje meg az egyéni metrikákat Azure Monitor modellezésükről.
author: anirudhcavale
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: f4ba3763dd781053349417fe3fed3a2848a06fc7
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515838"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Egyéni metrikák a Azure Monitor (előzetes verzió)

Amikor erőforrásokat és alkalmazásokat helyez üzembe az Azure-ban, elkezdhet telemetriai adatokat gyűjteni, hogy betekintést nyerjen a teljesítményükbe és állapotukba. Az Azure néhány metrikát használatra elérhetővé tesz. Ezeket a metrikákat standardnak [vagy platformnak nevezzük.](./metrics-supported.md) A jellegük azonban korlátozott. 

Érdemes lehet egyéni teljesítménymutatókat vagy üzletspecifikus metrikákat gyűjteni a mélyebb elemzésekhez. Ezek **az** egyéni metrikák az alkalmazás telemetriával, az Azure-erőforrásokon futó ügynökkel, vagy akár egy külső monitorozási rendszerrel gyűjthetőek, és továbbíthatóak közvetlenül a Azure Monitor. Miután közzétette őket a Azure Monitor, az Azure-erőforrások és -alkalmazások egyéni metrikai között tallózhat, lekérdezhet és riasztásokat kaphat az Azure által kibocsátott standard metrikák mellett.

Azure Monitor egyéni metrikák jelenleg nyilvános előzetes verzióban érhetőek el. 

## <a name="methods-to-send-custom-metrics"></a>Egyéni metrikák küldési módszerei

Az egyéni metrikák a Azure Monitor különböző módszerekkel küldhetőek el:
- Az Azure Application Insights SDK használatával rendszereket hozhat létre alkalmazásával, és egyéni telemetriát küldhet a Azure Monitor. 
- Telepítse az Azure Monitor Agent (előzetes verzió) alkalmazást Windows vagy [](../agents/data-collection-rule-azure-monitor-agent.md) [Linux rendszerű Azure-beli](../agents/azure-monitor-agent-overview.md) virtuális gépére, és egy adatgyűjtési s szabály használatával küldjön teljesítményszámlálókat Azure Monitor metrikáknak.
- Telepítse a Windows Azure Diagnostics (WAD) bővítményt az Azure-beli virtuális [gépre,](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)a virtuálisgép-méretezési készletre, a klasszikus virtuális gépre vagy [](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) [Cloud Services](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md) virtuális gépre, és küldjön teljesítményszámlálókat a Azure Monitor. [](../essentials/collect-custom-metrics-guestos-vm-classic.md) 
- Telepítse az [InfluxData Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) ügynököt az Azure-beli Linux rendszerű virtuális gépre, és küldjön metrikákat Azure Monitor kimeneti beépülő modul használatával.
- Egyéni metrikák [küldése közvetlenül](./metrics-store-custom-rest-api.md)a Azure Monitor REST API , `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics` .

## <a name="pricing-model-and-retention"></a>Díjszabási modell és adatmegőrzés

Az Azure Monitor [metrikákra](https://azure.microsoft.com/pricing/details/monitor/) és metrikákra vonatkozó lekérdezések számlázásának beállítására vonatkozó részletekért tekintse meg a díjszabást tartalmazó oldalt. Ezen az oldalon az összes metrika, köztük az egyéni metrikák és a metrikalekérdezések konkrét árának részletei érhetők el. Összefoglalva, nincs költség a standard metrikák (platformmetrikák) Azure Monitor metrikatárolóba való beszámítása, az egyéni metrikák azonban költségekkel fognak kerülni, amikor általánosan elérhetőek lesznek. A Metric API-lekérdezések költségekkel járnak.

Az egyéni metrikákat a rendszer ugyanakkora ideig őrzi [meg, mint a platformmetrikákat.](../essentials/data-platform-metrics.md#retention-of-metrics) 

> [!NOTE]  
> A Azure Monitor SDK Application Insights keresztül küldött metrikák számlázása betöltött naplóadatokként lesz kiszámlázva. Csak akkor kell további metrikákat fizetniük, [](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) ha be van jelölve Application Insights Riasztás engedélyezése egyéni metrikadimenziókhoz funkció. Ez a jelölőnégyzet adatokat küld a Azure Monitor metrics adatbázisba az egyéni metrics API-val, hogy lehetővé tegye az összetettebb riasztásokat.  További információ a Application Insights [díjszabási](../app/pricing.md#pricing-model) modelljéről és [árairól.](https://azure.microsoft.com/pricing/details/monitor/)


## <a name="how-to-send-custom-metrics"></a>Egyéni metrikák küldése

Amikor egyéni metrikákat küld egy Azure Monitor jelentésnek minden egyes adatpontnak vagy értéknek tartalmaznia kell a következő információkat.

### <a name="authentication"></a>Hitelesítés
Ahhoz, hogy egyéni metrikákat küld Azure Monitor-nek, a metrikát küldő entitásnak érvényes Azure Active Directory -jogkivonatra van szüksége a kérés **Bearer** fejlécében. Érvényes jogkivonatot többféleképpen is beszerezhet:
1. [Azure-erőforrások felügyelt identitása.](../../active-directory/managed-identities-azure-resources/overview.md) Identitást ad magának az Azure-erőforrásnak, például egy virtuális gépnek. A Managed Service Identity (MSI) úgy lett kialakítva, hogy engedélyeket adjon az erőforrásoknak bizonyos műveletek végrehajtásához. Például lehetővé teszi egy erőforrás számára, hogy saját magára vonatkozó metrikákat bocsát ki. Egy erőforrás vagy annak MSI-je **monitorozási metrikák** közzétevői jogosultságot kaphat egy másik erőforráshoz. Ezzel az engedéllyel az MSI más erőforrásokhoz is kibocsáthat metrikákat.
2. [Azure AD-szolgáltatásnév.](../../active-directory/develop/app-objects-and-service-principals.md) Ebben a forgatókönyvben egy Azure AD-alkalmazáshoz vagy -szolgáltatáshoz olyan engedélyek rendelhetők hozzá, amelyek metrikákat bocsátanak ki egy Azure-erőforrásról.
A kérelem hitelesítéséhez a Azure Monitor Azure AD nyilvános kulcsokkal ellenőrzi az alkalmazás jogkivonatát. A meglévő **Monitoring Metrics Publisher szerepkör** már rendelkezik ezzel az engedéllyel. A következőben érhető el: Azure Portal. A szolgáltatásnév attól függően, hogy milyen erőforrásokhoz bocsát ki egyéni metrikákat, a **Monitorozási** metrikák közzétevője szerepkört kaphatja a szükséges hatókörben. Ilyenek például az előfizetések, az erőforráscsoportok vagy az adott erőforrások.

> [!TIP]  
> Amikor egy Azure AD-jogkivonatot kér egyéni metrikák kibocsátására, győződjön meg arról, hogy a célközönség vagy az erőforrás, amely számára a jogkivonatot kéri, a következő `https://monitoring.azure.com/` legyen: . Győződjön meg arról, hogy a záró "/" legyen.

### <a name="subject"></a>Tárgy
Ez a tulajdonság rögzíti, hogy az egyéni metrika melyik Azure-erőforrás-azonosítóra szolgál. Ezek az információk az API-hívás URL-címében lesznek kódolva. Minden API csak egyetlen Azure-erőforrás metrikaértékét küldheti el.

> [!NOTE]  
> Nem bocsáthat ki egyéni metrikákat egy erőforráscsoport vagy előfizetés erőforrás-azonosítójára.


### <a name="region"></a>Region
Ez a tulajdonság rögzíti, hogy melyik Azure-régióban van üzembe helyezni az erőforrás, amely számára metrikákat bocsát ki. A metrikákat ugyanannak a régióvégpontnak Azure Monitor kell kiadva, ahol az erőforrás üzembe van adva. Például az USA nyugati régiója alatt üzembe helyezett virtuális gépek egyéni metrikákat kell elküldeni a WestUS regionális Azure Monitor végpontra. A régió információit az API-hívás URL-címe is kódolja.

> [!NOTE]  
> A nyilvános előzetes verzióban az egyéni metrikák csak az Azure-régiók egy részében érhetők el. A támogatott régiók listáját a cikk egy későbbi szakaszában dokumentáljuk.
>
>

### <a name="timestamp"></a>Időbélyeg
A rendszernek küldött Azure Monitor időbélyegzővel kell megjelölni. Ez az időbélyegző rögzíti a dátum/idő értékét, amikor a rendszer a metrika értékét méri vagy gyűjti. Azure Monitor a metrikaadatokat az elmúlt 20 percre és a 5 perces jövőbeli időpontra vonatkozó időbélyegekkel fogadja el. Az időbélyegzőnek ISO 8601 formátumúnak kell lennie.

### <a name="namespace"></a>Névtér
A névterekkel kategorizálhatja vagy csoportosíthatja a hasonló metrikákat. A névterek használatával elkülönítheti a metrikák csoportjait, amelyek különböző elemzéseket vagy teljesítménymutatókat gyűjtenek. Előfordulhat például, hogy van egy **contosomemorymetrics** nevű névtér, amely nyomon követi az alkalmazás profilját mutató memóriahasználati metrikákat. Egy másik, **contosoapptransaction nevű** névtér nyomon követheti az alkalmazás felhasználói tranzakcióinak összes metrikát.

### <a name="name"></a>Name
**A** név a jelentett metrika neve. A név általában elég leíró ahhoz, hogy segítsen a mért értékek azonosításában. Ilyen metrika például egy adott virtuális gépen felhasznált memóriabájtok számát méri. A metrika neve lehet például Memória **bájt használatban.**

### <a name="dimension-keys"></a>Dimenziókulcsok
A dimenziók olyan kulcs- vagy értékpárok, amelyek segítenek az összegyűjtött metrika további jellemzőinek leírásában. A további jellemzők használatával további információkat gyűjthet a metrikával kapcsolatban, ami mélyebb elemzéseket tesz lehetővé. A Memória  bájtok használatban metrika például  egy Folyamat nevű dimenziókulccsal is rendelkezik, amely rögzíti, hogy egy virtuális gépen az egyes folyamatok hány bájt memóriát fogyasztanak. Ezzel a kulccsal szűrheti a metrikát, hogy lássa, mennyi memóriaspecifikus folyamat használja, vagy hogy a memóriahasználat alapján azonosítsa az első öt folyamatot.
A dimenziók nem kötelezőek, nem minden metrika rendelkezik dimenziókkal. Egy egyéni metrika legfeljebb 10 dimenzióval rendelkezik.

### <a name="dimension-values"></a>Dimenzióértékek
Metrikaadatpont jelentésekor a jelentett metrika minden dimenziókulcsa egy megfelelő dimenzióértékkel rendelkezik. Előfordulhat például, hogy a ContosoApp által a virtuális gépen használt memóriát szeretné jelenteni:

* A metrika neve memóriabájt **a Use (Használat) alatt.**
* A dimenziókulcs a Következő lenne: **Folyamat.**
* A dimenzió értéke a **következő:ContosoApp.exe.**

Metrikaérték közzétételekor dimenziókulcsonként csak egyetlen dimenzióértéket lehet megadni. Ha ugyanazt a memóriakihasználtságot gyűjti a virtuális gépen futó több folyamathoz, több metrikaértéket is jelenthet az időbélyeghez. Minden metrikaérték más dimenzióértéket adna meg a **Folyamat dimenziókulcshoz.**
A dimenziók nem kötelezőek, nem minden metrika rendelkezik dimenziókkal. Ha egy metrika oszlop dimenziókulcsokat definiál, a megfelelő dimenzióértékek megadása kötelező.

### <a name="metric-values"></a>Metrika értékei
Azure Monitor metrikákat egyperces részletességi időközönként tárolja. Tisztában vagyunk vele, hogy egy adott percben a metrikát többször is mintavételre lehet szükség. Ilyen például a processzorkihasználtság. Az is előfordulhat, hogy számos különálló eseménynél meg kell mérni. Ilyen például a bejelentkezési tranzakciók késése. Ha korlátoznia kell a nyers értékek számát, amelyekért a Azure Monitor kell fizetnie, helyileg előre összesíthet és kibocsáthat értékeket:

* **Min:** Az összes minta és mérés minimális megfigyelt értéke a perc során.
* **Max:** Az összes minta és mérés perc során megfigyelt legnagyobb megfigyelt értéke.
* **Összeg:** A perc összes mintáiból és méréseiből megfigyelt összes megfigyelt érték összege.
* **Darabszám:** A perc során vett minták és mérések száma.

Ha például egy adott percben négy bejelentkezési tranzakció volt az alkalmazásba, az eredményül kapott mért késések a következők lehetnek:

|1. tranzakció|2. tranzakció|3. tranzakció|4. tranzakció|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|

Ezután az eredményül kapott metrika közzététele Azure Monitor következő lesz:
* Min: 4
* Maximum: 16
* Összeg: 40
* Darabszám: 4

Ha az alkalmazás nem tudja helyben előre összesíteni az adatokat, és a gyűjtéskor azonnal ki kell bocsátanunk minden különálló mintát vagy eseményt, kibocsáthatja a nyers mértékértékeket. Például minden alkalommal, amikor bejelentkezési tranzakció történik az alkalmazásban, közzé kell tenni egy metrikát, Azure Monitor egyetlen méréssel. Így egy 12 ms-ig tartott bejelentkezési tranzakcióhoz a metrika közzététele a következő lenne:
* Perc: 12
* Maximum: 12
* Összeg: 12
* Darabszám: 1

Ezzel a folyamattal több értéket is kibocsáthat ugyanannak a metrika- és dimenziókombinációnak egy adott percben. Azure Monitor ezután az adott percig kibocsátott összes nyers értéket összesíti.

### <a name="sample-custom-metric-publication"></a>Egyéni metrikák közzétételének mintája
A következő példában létrehoz egy  Memóriabájtok nevű egyéni metrikát a Virtuális gép memóriaprofilja metrikanévtér Használatában.  A metrika egyetlen, Process nevű **dimenzióval rendelkezik.** Az adott időbélyeghez két különböző folyamat metrikaértékét bocsátjuk ki:

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
> Application Insights, a diagnosztikai bővítmény és az InfluxData Telegraf ügynök már konfigurálva van arra, hogy metrikaértékeket bocsátson ki a megfelelő regionális végpontra, és minden egyes fogban tartalmazza az összes fenti tulajdonságot.
>
>

## <a name="custom-metric-definitions"></a>Egyéni metrikadefiníciók
A kimenet előtt nem kell előre Azure Monitor egyéni metrikát. Minden közzétett metrika-adatpont névteret, nevet és dimenzióinformációt tartalmaz. Így amikor először bocsát ki egyéni metrikát a Azure Monitor, a rendszer automatikusan létrehoz egy metrikadefiníciót. Ez a metrikadefiníció ezután felderíthető minden olyan erőforráson, amelyből a metrika a metrikadefiníciókból lesz kiadva.

> [!NOTE]  
> Azure Monitor még nem támogatja az egységek definiálása **egyéni** metrikákhoz.

## <a name="using-custom-metrics"></a>Egyéni metrikák használata
Miután egyéni metrikákat elküldött a Azure Monitor, tallózhatja őket a Azure Portal, és lekérdezheti őket a Azure Monitor REST API-kon keresztül. Riasztásokat is létrehozhat, amelyek értesítik, ha bizonyos feltételek teljesülnek.

> [!NOTE]
> Az egyéni metrikák megtekintéséhez olvasói vagy közreműködői szerepkör szükséges. Lásd: [Figyelési olvasó.](../../role-based-access-control/built-in-roles.md#monitoring-reader) 

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Egyéni metrikák tallózása a Azure Portal
1.    Nyissa meg az [Azure Portal](https://portal.azure.com).
2.    Válassza a **Figyelás** panelt.
3.    Válassza a **Metrikák** lehetőséget.
4.    Válasszon ki egy erőforrást, amely számára egyéni metrikákat adott ki.
5.    Válassza ki az egyéni metrika metrika-névterét.
6.    Válassza ki az egyéni metrikát.

> [!NOTE]
> A [metrikák](./metrics-getting-started.md) megtekintésével kapcsolatos további Metrikaböngésző Az Azure Azure Portal.

## <a name="supported-regions"></a>Támogatott régiók
A nyilvános előzetes verzióban az egyéni metrikák közzététele csak az Azure-régiók egy részében érhető el. Ez a korlátozás azt jelenti, hogy a metrikák csak a támogatott régiók valamelyikében található erőforrásokhoz tehetőek közzé. Az [Azure-régiókkal](https://azure.microsoft.com/global-infrastructure/geographies/) kapcsolatos további információkért tekintse meg az Azure földrajzi helyekkel kapcsolatos tudnivalókat. Az alábbi végpontok által használt Azure-régiókód csak annak a régiónak a neve, amelyből el lett távolítva az eltolt tér. A következő táblázat felsorolja az egyéni metrikákhoz támogatott Azure-régiókat. Emellett felsorolja a megfelelő végpontokat, amelyeken az adott régiókban található erőforrások metrikáit közzé kell tenni:

|Azure-régió |Regionális végpont előtagja|
|---|---|
| Minden nyilvános felhőrégió | https://<azure_region_code>.monitoring.azure.com |
| **Azure Government** | |
| USA-beli államigazgatás – Arizona | https: \/ /usgovarizona.monitoring.azure.us |
| **Kína** | |
| Kína 2. keleti régiója | https: \/ /chinaeast2.monitoring.azure.cn |

## <a name="latency-and-storage-retention"></a>Késés és tárterület-megőrzés

Egy teljesen új metrika vagy a metrikákhoz adott új dimenzió hozzáadása akár 2–3 percet is igénybe vehet. A rendszerben az adatoknak az idő 99%-ában kevesebb mint 30 másodperc alatt kell megjelennie. 

Ha töröl egy metrikát vagy eltávolít egy dimenziót, a módosítás egy héttől egy hónapig is eltelhet, hogy törlődjön a rendszerből.

## <a name="quotas-and-limits"></a>Kvóták és korlátok
Azure Monitor a következő használati korlátokat szabja meg az egyéni metrikákra:

|Kategória|Korlát|
|---|---|
|Aktív idősorok/előfizetések/régió|50,000|
|Dimenziókulcsok metrikánként|10|
|A metrikanévterek, metrikanevek, dimenziókulcsok és dimenzióértékek sztringhossza|256 karakter|

Az aktív idősorok a metrika, dimenziókulcs vagy dimenzióérték bármely egyedi kombinációjaként vannak definiálva, amely az elmúlt 12 órában metrikaértékekkel rendelkezik.

Az 50 000 idősorozat-korlátot az alábbi metrika alapján értheti meg:

*Kiszolgáló válaszideje dimenziókkal:* *Régió,* *Részleg,* *Ügyfélazonosító*

Ezzel a metrikával 10 régió, 20 részleg és 100 ügyfél 10 x 20 x 100 = 2000 idősorozatot biztosít. 

Ha 100 régióval, 200 részleg és 2000 ügyfél 100 x 200 x 2000 = 40 000 000 idősorozattal dolgozik, amely jóval túllépi a korlátot csupán ezen a mérőszámon. 

Ez a korlát ezúttal sem az egyes metrikákra van korlátozva. Ez az összes ilyen metrika összegére van összeszámezve egy előfizetésben és régióban.  

## <a name="design-limitations"></a>Tervezési korlátozások

**Ne használjon Application Insights** naplózási célra – a Application Insights folyamat az egyéni metrics API-t használja a színfalak mögött. A folyamat nagy mennyiségű telemetriához van optimalizálva, és minimális hatással van az alkalmazásra. Így szabályozást vagy mintákat (csak a telemetria egy százalékát veszi figyelembe, a többit figyelmen kívül hagyja), ha a bejövő adatfolyam túl nagy lesz. Emiatt a viselkedés miatt nem használhatja naplózási célokra, mivel egyes rekordok valószínűleg el lesznek dobva. 

**Változót a** névben megerősítő metrikák – Ne használjon változót a metrika nevének részeként, például egy GUID-t vagy egy időbélyeget. Ez gyorsan 50 000 idősorozat-korlátozást okoz. 
 
**Nagy számosságú** metrikadimenziók – A dimenzióban túl sok érvényes értéket (magas számosságot) mutató metrikák sokkal valószínűbbek, hogy elérték az 50 000-es korlátot. Általában soha ne használjon folyamatosan változó értéket egy dimenzió- vagy metrikanévben. Az időbélyegzőnek például SOHA nem szabad dimenziónak lennie. A kiszolgáló, az ügyfél vagy a termékazonosító használható, de csak akkor, ha kevesebb ilyen típus van. Tesztként kérdezze meg magától, hogy minden diagramon ilyen adatokat szeretne-e ábrázolni egy grafikonon.  Ha 10 vagy akár 100 kiszolgálóval is van, hasznos lehet, ha mindegyiket egy grafikonon összehasonlítja. Ha azonban 1000-et ad meg, az eredményül kapott gráfot valószínűleg nehéz lenne elolvasni, ha nem lehetetlen volna elolvasni. Ajánlott eljárásként tartsa 100-ig az érvényes értékeket. Legfeljebb 300 szürke terület.  Ha át kell mennie ezen a mennyiségen, használja inkább Azure Monitor naplókat.   

Ha változó van a névben vagy egy nagy számosságú dimenzió, a következő történhet. 
- A metrikák a szabályozás miatt megbízhatatlanná válnak
- Metrikaböngésző nem működik
- A riasztások és az értesítések kiszámíthatatlanná válnak
- A költségek váratlanul növekednek – A Microsoft nem díjkalkul, amíg a dimenziókat bemutató egyéni metrikák nyilvános előzetes verzióban létezik. Ha azonban a díjak a jövőben kezdődnek, váratlan díjakat kell fizetni. A terv a metrikák használatának díjának felszámozása az idősorozat-figyelt és az API-hívások száma alapján.  

## <a name="next-steps"></a>Következő lépések
Különböző szolgáltatásokból származó egyéni metrikák használata: 
 - [Virtual Machines](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Virtuálisgép-méretezési csoport](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure Virtual Machines (klasszikus)](../essentials/collect-custom-metrics-guestos-vm-classic.md)
 - [Linux rendszerű virtuális gép Telegraf-ügynökkel](../essentials/collect-custom-metrics-linux-telegraf.md)
 - [REST API](./metrics-store-custom-rest-api.md)
 - [Klasszikus Cloud Services](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md)
