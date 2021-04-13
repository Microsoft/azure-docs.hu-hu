---
title: Azure Monitor naplók használatának és költségeinek kezelése
description: Megtudhatja, hogyan módosíthatja a díjszabási tervet, és hogyan kezelheti a Log Analytics munkaterület adatmennyiségét és adatmegőrzési szabályzatát Azure Monitorben.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/28/2021
ms.author: bwren
ms.openlocfilehash: ac2d1ea17460c56a3369d00d2cc8e41cca616363
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310920"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>A használat és a költségek felügyelete Azure Monitor-naplókkal    

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan értelmezhető és szabályozható a Azure Monitor naplók költségei. A kapcsolódó cikkek, a [monitorozási használat és a becsült költségek](../usage-estimated-costs.md) azt írják le, hogyan lehet megtekinteni a használati és becsült költségeket több Azure-figyelési funkció között különböző díjszabási modellekhez. Az ebben a cikkben látható árak és költségek csak példaként szolgálnak. 

Azure Monitor naplókat úgy tervezték, hogy a nagyvállalati forrásból származó vagy az Azure-ban üzembe helyezett nagy mennyiségű adat gyűjtését, indexelését és tárolását naponta kialakítsa és támogassa.  Habár ez lehet a szervezet elsődleges illesztőprogramja, a költséghatékonyság végül a mögöttes illesztőprogram. Ebből a célból fontos tisztában lenni azzal, hogy egy Log Analytics munkaterület díja nem csak a gyűjtött adatok mennyiségétől függ, hanem a kiválasztott csomagtól, és azt is, hogy mennyi ideig kell tárolnia a csatlakoztatott forrásokból generált adatokat.  

Ebben a cikkben áttekintjük, hogyan lehet proaktív módon figyelni a betöltött adatmennyiséget és a tárterület növekedését, valamint korlátokat meghatározni a kapcsolódó költségek szabályozására. 

## <a name="pricing-model"></a>Díjszabási modell

Az Log Analytics alapértelmezett díjszabása az adatmennyiség betöltését és opcionálisan **a hosszú** adatmegőrzést is igénybe véve. Az adatmennyiséget a rendszer a GB-ban (10 ^ 9 bájt) tárolt adat méretének megfelelően méri. Minden Log Analytics munkaterület külön szolgáltatásként lesz felszámítva, és hozzájárul az Azure-előfizetéshez tartozó számlához. Az adatfeldolgozás mennyisége az alábbi tényezőktől függően jelentős lehet: 

  - Az engedélyezett felügyeleti megoldások száma és a konfigurációjuk
  - Figyelt virtuális gépek száma
  - Az egyes figyelt virtuális gépekről összegyűjtött adatok típusa 
  
Az utólagos elszámolású modellen kívül a Log Analytics **kapacitás-foglalási** csomagokkal rendelkezik, amelyek lehetővé teszik, hogy az utólagos elszámolású díjszabáshoz képest akár 25%-ot is mentsen. A kapacitás foglalásának díjszabása lehetővé teszi, hogy a foglalást 100 GB/nap után vásárolja meg. A foglalási szint feletti használati díjakat az utólagos elszámolású díjszabás szerint számítjuk fel. A kapacitás foglalási szintjeinek 31 napos kötelezettségvállalási időszaka van. A kötelezettségvállalás időtartama alatt magasabb szintű kapacitást biztosító foglalási szintre válthat (amely a 31 napos kötelezettségvállalási időszakot újraindítja), de az utólagos elszámolású vagy az alacsonyabb kapacitású foglalási szintig nem léphet vissza a kötelezettségvállalási időszak végéig. A kapacitás foglalási szintjeinek számlázása napi rendszerességgel történik. [További](https://azure.microsoft.com/pricing/details/monitor/) információ a log Analytics utólagos elszámolású és a kapacitás foglalásának díjszabásáról. 

Az összes díjszabási szinten az esemény adatméretét az adott esemény Log Analytics tárolt tulajdonságok karakterlánc-ábrázolása alapján számítja ki a rendszer, függetlenül attól, hogy az adatok egy ügynöktől származnak, vagy a betöltési folyamat során lettek-e hozzáadva. Ebbe beletartozik az adatokként hozzáadott [Egyéni mezők](custom-fields.md) gyűjtése, majd a log Analytics tárolása. Számos, az összes adattípussal közös tulajdonság, beleértve a [log Analytics standard tulajdonságokat](./log-standard-columns.md)is, ki van zárva az esemény méretének kiszámításakor. Ide tartoznak a következők:,,, `_ResourceId` `_SubscriptionId` `_ItemId` `_IsBillable` `_BilledSize` és `Type` . A Log Analyticsban tárolt összes többi tulajdonságot az esemény méretének kiszámítása tartalmazza. Egyes adattípusok teljes egészében díjmentesek az adatfeldolgozási díjaktól, például a AzureActivity, a Szívveréstől és a használati típustól. Annak megállapításához, hogy egy esemény ki lett-e zárva az adatok betöltésének számlázása alól, használhatja a `_IsBillable` tulajdonságot az [alább](#data-volume-for-specific-events)látható módon. A használatot GB-ban (1,0 E9 bájt) kell jelenteni. 

Azt is vegye figyelembe, hogy egyes megoldások, például az [Azure Defender (Security Center)](https://azure.microsoft.com/pricing/details/azure-defender/), az [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) és a [Configuration Management](https://azure.microsoft.com/pricing/details/automation/) saját díjszabási modellel rendelkeznek. 

### <a name="log-analytics-dedicated-clusters"></a>Dedikált fürtök Log Analytics

Log Analytics dedikált fürtök egyetlen felügyelt Azure Adatkezelő-fürtbe tartozó munkaterületek gyűjteményei, amelyek olyan speciális forgatókönyveket támogatnak, mint például az [ügyfél által felügyelt kulcsok](customer-managed-keys.md).  Log Analytics dedikált fürtök kapacitása foglalási díjszabási modellt használnak, amelyet legalább 1000 GB/nap értékre kell konfigurálni. Ez a kapacitási szint 25%-os kedvezményt biztosít az utólagos elszámolású díjszabáshoz képest. A foglalási szint feletti használati díjakat az utólagos elszámolású díjszabás szerint számítjuk fel. A fürt kapacitásának foglalása a foglalási szint növelése után 31 napos kötelezettségvállalási időszakot tartalmaz. A kötelezettségvállalási időszak alatt a kapacitás foglalási szintje nem csökkenthető, de bármikor megnövelhető. Ha a munkaterületek egy fürthöz vannak társítva, a munkaterületek adatfeldolgozási számlázása a fürt szintjén történik a beállított kapacitás foglalási szintjének használatával. További információ a [log Analytics-fürtök létrehozásáról](customer-managed-keys.md#create-cluster) és [a munkaterületek társításáról](customer-managed-keys.md#link-workspace-to-cluster). A kapacitás foglalásának díjszabási információi a [Azure monitor díjszabási oldalon]( https://azure.microsoft.com/pricing/details/monitor/)érhetők el.  

A fürt kapacitásának foglalási szintje programozott módon van konfigurálva a Azure Resource Manager használatával a (z `Capacity` ) paraméterrel `Sku` . A a `Capacity` GB egységben van megadva, és 1000 GB/nap vagy több értékkel rendelkezhet a 100 GB/nap növekményekben. Ezt [Azure monitor ügyfél által felügyelt kulcs](customer-managed-keys.md#create-cluster)részletezi. Ha a fürtnek 2000 GB/nap feletti foglalásra van szüksége, lépjen kapcsolatba velünk a következő címen: [LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com) .

A fürtön két számlázási mód van használatban. Ezeket a paraméter megadhatja a `billingType` [fürt konfigurálásakor](customer-managed-keys.md#customer-managed-key-operations). A két mód a következő: 

1. **Fürt**: ebben az esetben (ez az alapértelmezett beállítás) a betöltött adatmennyiség számlázása a fürt szintjén történik. A rendszer összesíti a fürthöz társított egyes munkaterületekről betöltött adatmennyiségeket a fürt napi számlájának kiszámításához. Vegye figyelembe, hogy az [Azure Defender (Security Center)](../../security-center/index.yml) csomóponton belüli kiosztásait a rendszer a munkaterület szintjén alkalmazza az összesített adatoknak a fürt összes munkaterülete közötti összesítése előtt. 

2. **Munkaterületek**: a fürt kapacitásának foglalási költségei arányosak a fürtben lévő munkaterületekkel (az egyes munkaterületek esetében az [Azure Defender (Security Center)](../../security-center/index.yml) által az egyes munkaterületekhez tartozó csomópont-hozzárendelések elszámolása után.) Ha egy adott munkaterületre betöltött teljes adatmennyiség kevesebb, mint a kapacitás foglalása, akkor az egyes munkaterületek díjait a rendszer a GB-os kapacitású foglalási díjszabás alapján számlázza, és a kapacitás foglalásának fel nem használt részét számlázza a fürt erőforrásának. Ha a napi munkaterületre betöltött teljes adatmennyiség meghaladja a kapacitás foglalását, akkor az egyes munkaterületek számlázása a kapacitás foglalásának töredékét határozza meg a napi betöltött adat töredéke alapján, valamint az egyes munkaterületek a kapacitás foglalása felett lévő betöltött adat töredékének megfelelően. A fürterőforrás nem számít fel díjat, ha a napi munkaterületre betöltött teljes adatmennyiség meghaladja a kapacitás foglalását.

A fürt számlázási beállításaiban az adatmegőrzési díj a munkaterületen történik. Vegye figyelembe, hogy a fürt számlázása a fürt létrehozásakor kezdődik, függetlenül attól, hogy a munkaterületek hozzá lettek-e rendelve a fürthöz. Azt is vegye figyelembe, hogy a fürthöz társított munkaterületek már nem rendelkeznek díjszabási csomaggal.

## <a name="estimating-the-costs-to-manage-your-environment"></a>A környezet kezelésével kapcsolatos költségek becslése 

Ha még nem használ Azure Monitor naplókat, a [Azure monitor árképzési számológép](https://azure.microsoft.com/pricing/calculator/?service=monitor) használatával megbecsülheti a log Analytics használatának költségeit. Először írja be a "Azure Monitor" kifejezést a keresőmezőbe, és kattintson az eredményül kapott Azure Monitor csempére. Görgessen le az oldalról Azure Monitorre, majd válassza a legördülő menüből a Log Analytics lehetőséget.  Itt megadhatja a virtuális gépek számát és a begyűjteni kívánt GB-nyi adatot. Egy tipikus Azure-beli virtuális gépről általában 1 – 3 GB adathónapot kell betölteni. Ha már kiértékeli Azure Monitor naplókat, saját környezetében is használhatja az adatstatisztikát. A [figyelt virtuális gépek számának](#understanding-nodes-sending-data) és a [munkaterület által betöltött adatmennyiségnek](#understanding-ingested-data-volume)a meghatározását alább találja. 

## <a name="understand-your-usage-and-estimate-costs"></a>A használati és becsült költségek megismerése

Ha most Azure Monitor naplókat használ, könnyen megismerheti, hogy milyen költségek várhatók a legutóbbi használati szokások alapján. Ehhez használja a  **log Analytics használati és becsült költségeket** az adatfelhasználás áttekintéséhez és elemzéséhez. Ez azt mutatja, hogy az egyes megoldások milyen mennyiségű adatot gyűjtenek, mennyi adatot tartanak fenn, valamint a költségek becslését a betöltött adatok mennyisége és a benne foglalt mennyiség utáni további megőrzés alapján.

:::image type="content" source="media/manage-cost-storage/usage-estimated-cost-dashboard-01.png" alt-text="Felhasználás és becsült költségek":::

Az adatok részletesebb megismeréséhez kattintson a **használati és becsült költségek** oldalon található diagramok valamelyikének jobb felső sarkában található ikonra. Ezzel a lekérdezéssel a használat további részleteit is megismerheti.  

:::image type="content" source="media/manage-cost-storage/logs.png" alt-text="Naplók nézet":::

A **használat és a becsült költségek** lapon áttekintheti az adatmennyiséget a hónapban. Ebbe beletartozik az Log Analytics munkaterületen fogadott és megőrzött számlázandó összes adat.  
 
Log Analytics díjak hozzáadódnak az Azure-számlához. Az Azure-számlázás részleteit a Azure Portal vagy a [Azure elszámolási portál](https://account.windowsazure.com/Subscriptions)számlázási szakaszában tekintheti meg.  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Log Analytics használatának megtekintése az Azure-számlán 

Az Azure nagyszerű hasznos funkciókat biztosít a [Azure Cost Management + számlázási](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=%2fazure%2fbilling%2fTOC.json) központban. A "Cost Analysis" funkció például lehetővé teszi az Azure-erőforrások költségeinek megtekintését. Először vegyen fel egy szűrőt "erőforrástípus" (a Microsoft. operationalinsights/munkaterületre Log Analytics és a Microsoft. operationalinsights/Cluster Log Analytics fürtök esetében) lehetővé teszi, hogy nyomon kövesse a Log Analytics költeni. Ezután a "csoportosítás" lehetőségnél válassza a "mérési kategória" vagy a "mérőszám" lehetőséget.  Vegye figyelembe, hogy más szolgáltatások, például az Azure Defender (Security Center) és az Azure Sentinel is számlázzák a használatot Log Analytics munkaterület-erőforrásokon. Ha meg szeretné tekinteni a hozzárendelést a szolgáltatásnév számára, akkor a diagram helyett a táblázat nézetet is kiválaszthatja. 

Használati adatait még részletesebben megismerheti, ha [letölti a használati adatokat az Azure Portalról](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). A letöltött táblázatban láthatja a napi használatot az Azure-erőforrás (például a Log Analytics-munkaterület) szerinti bontásban. Ebben az Excel-számolótáblában a Log Analytics-munkaterületek használatáról az első szűrés a "kategória kategóriája" oszlopban, hogy megjelenjen a "Log Analytics", "Insight and Analytics" (az örökölt díjszabási szintek némelyike által használt) és a "Azure Monitor" (a kapacitás foglalásának díjszabása alapján), majd hozzáad egy szűrőt a "példány azonosítója" oszlophoz, amely a "munkaterület tartalmaz" vagy a "fürt tartalmaz" (az utóbbi a Log Analytics-fürt használatát tartalmazza). A használat megjelenik a "felhasznált mennyiség" oszlopban, és az egyes bejegyzések egysége a "mértékegység" oszlopban látható.  További részletek is elérhetők, ha [meg szeretné ismerni Microsoft Azure-számláját](../../cost-management-billing/understand/review-individual-bill.md). 

## <a name="changing-pricing-tier"></a>Árképzési réteg módosítása

A munkaterület Log Analytics díjszabási szintjének módosításához 

1. A Azure Portal nyissa meg a **használati és becsült költségeket** a munkaterületről, ahol megjelenik a munkaterület számára elérhető díjszabási szintek listája.

2. Tekintse át az egyes díjszabási szintek becsült költségeit. Ez a becslés az elmúlt 31 nap használaton alapul, ezért ez a költségbecslés az utolsó 31 napra támaszkodik, amely jellemző a szokásos használatra. Az alábbi példában láthatja, hogy az elmúlt 31 nap adatmintái alapján ez a munkaterület a 100 GB/nap kapacitás foglalási szintjéhez képest (#1) alacsonyabb, mint az utólagos elszámolású csomagnál (#2).  

:::image type="content" source="media/manage-cost-storage/pricing-tier-estimated-costs.png" alt-text="Árképzési szintek":::
    
3. A becsült költségeknek az utolsó 31 nap használaton alapuló áttekintése után, ha úgy dönt, hogy megváltoztatja az árképzési szintet, kattintson a **kiválasztás** elemre.  

[Az árképzési szintet Azure Resource Manager használatával is beállíthatja](./resource-manager-workspace.md) a `sku` ( `pricingTier` Azure Resource Manager sablonban) paraméterrel. 

## <a name="legacy-pricing-tiers"></a>Örökölt tarifacsomagok

Azok az előfizetések, amelyek Log Analytics munkaterülettel vagy Application Insights-erőforrással rendelkeztek, 2018. április 2. előtt, vagy egy, a 2019. február 1. előtt elindított Nagyvállalati Szerződéshoz kapcsolódnak, továbbra is hozzáférhetnek a korábbi díjszabási szintek használatához: **ingyenes**, **különálló (GB-onként)** és **/node (OMS)**.  Az ingyenes díjszabási szinten lévő munkaterületek napi adatfeldolgozása a 500 MB-ra korlátozódik (kivéve az Azure Defender által gyűjtött biztonsági adattípusokat [(Security Center)](../../security-center/index.yml)), és az adatmegőrzés legfeljebb 7 napig tart. Az ingyenes díjszabási csomag kizárólag értékelési célokra szolgál. Az önálló vagy a csomópontok díjszabási szintjein lévő munkaterületek esetében a felhasználó által konfigurálható megőrzési idő 30 – 730 nap.

Az önálló árképzési szinten történő használatért a betöltött adatmennyiséget számoljuk fel. A jelentés a **log Analytics** szolgáltatásban szerepel, és a mérőszám neve "adatelemzés". 

A/csomópontok díjszabási szintjei a figyelt virtuális gépeken (csomópontokon) óránkénti részletességgel jelennek meg. Minden figyelt csomópont esetében a munkaterület 500 MB adatmennyiséget foglal le naponta, amely nem számlázható. Ezt a foglalást óránként részletességgel számítjuk ki, és minden nap a munkaterület szintjén összesítjük. Az összesített napi adatmennyiség fölött betöltött adatokat GB-onként számítjuk fel adatmennyiségként. Vegye figyelembe, hogy a számlán a szolgáltatás Log Analytics használat **Insight and Analytics** lesz, ha a munkaterület a csomópontok közötti díjszabási szinten van. A használatot három fogyasztásmérőn kell jelenteni:

1. Csomópont: a megfigyelt csomópontok (VM-EK) száma a Node * hónapok egységében.
2. Adatkereten túli adatmennyiség: ez az összesített adatmennyiséget meghaladóan betöltött adat GB-ban megadott száma.
3. A csomópontok által tartalmazott adatmennyiség: ez az összesített adatfoglalás által érintett betöltött adat mennyisége. Ezt a mérőszámot akkor is használja a rendszer, ha a munkaterület az összes díjszabási szinten szerepel, hogy megjelenjen az Azure Defender által érintett adatmennyiség (Security Center).

> [!TIP]
> Ha a munkaterület a **csomópontok** díjszabási szintjéhez fér hozzá, de azt szeretné tudni, hogy az utólagos elszámolású szinten olcsóbb-e, akkor [az alábbi lekérdezéssel](#evaluating-the-legacy-per-node-pricing-tier) egyszerűen kérhet javaslatot. 

A 2016. április előtt létrehozott munkaterületek az eredeti **standard** és **prémium** szintű díjszabáshoz is hozzáférnek, amely a 30 és 365 nap rögzített adatmegőrzéssel rendelkezik. Nem hozhatók létre új munkaterületek a **standard** vagy a **prémium** szintű díjszabásban, és ha a munkaterületet kihelyezték ezekből a rétegekből, nem helyezhető vissza. Az örökölt rétegek adatfeldolgozási mérőszámait "adatelemzésnek" nevezzük.

Vannak olyan viselkedések is, amelyek az örökölt Log Analytics rétegek használata, valamint az [Azure Defender (Security Center)](../../security-center/index.yml)használati díjának számlázását is magukban foglalhatják. 

1. Ha a munkaterület az örökölt standard vagy prémium szinten található, akkor az Azure Defender csak Log Analytics adatfeldolgozásra, nem pedig csomópontra történik.
2. Ha a munkaterület a csomópontok közötti örökölt, akkor az Azure Defender az aktuális [Azure Defender Node-based díjszabási modell](https://azure.microsoft.com/pricing/details/security-center/)alapján számítja fel a díjat. 
3. Más díjszabási csomagokban (beleértve a kapacitás-foglalásokat), ha az Azure Defender a 2017. június 19. előtt volt engedélyezve, az Azure Defender csak Log Analytics adatfeldolgozásra lesz kiszámlázva. Ellenkező esetben az Azure Defender a jelenlegi Azure Defender node-alapú díjszabási modell alapján kerül számlázásra.

Az árképzési szintek korlátozásait az Azure- [előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)is elérhetők.

A korábbi díjszabási szintek egyike sem rendelkezik regionális alapú díjszabással.  

> [!NOTE]
> A OMS E1 Suite, OMS E2 Suite vagy OMS Add-On for System Center csomagból származó jogosultságok használatához válassza a Log Analytics */csomópont* díjszabási szintet.

## <a name="log-analytics-and-azure-defender-security-center"></a>Log Analytics és az Azure Defender (Security Center)

Az [Azure Defender (Security Center)](../../security-center/index.yml) számlázása szorosan kötődik log Analytics számlázáshoz. Az Azure Defender 500 MB/csomópont/nap kiosztást biztosít a [biztonsági adattípusok](/azure/azure-monitor/reference/tables/tables-category#security) (WindowsEvent, SecurityAlert, SecurityBaseline, SecurityBaselineSummary, SecurityDetection, SecurityEvent, WindowsFirewall, MaliciousIPCommunication, LinuxAuditLog, SysmonEvent, ProtectionStatus) és a frissítési és updateSummary típusú adattípusok következő részhalmazára vonatkozóan, ha az Update Management megoldás nem fut a munkaterületen, vagy ha a megoldás célcsoportja engedélyezve van, további [információ](https://docs.microsoft.com/azure/security-center/security-center-pricing#what-data-types-are-included-in-the-500-mb-free-data-limit). Ha a munkaterület a csomópontok közötti örökölt árképzési szinten található, akkor az Azure Defender és a Log Analytics foglalások összevonva lesznek, és a rendszer közösen alkalmazza az összes számlázható bevitt adatot.  

## <a name="change-the-data-retention-period"></a>Az adatmegőrzési időtartam módosítása

A következő lépések azt írják le, hogyan kell konfigurálni, hogy a rendszer mennyi ideig tárolja a napló adatait a munkaterületen. Az adatmegőrzés a munkaterület szintjén 30 – 730 nap (2 év) lehet az összes munkaterülethez, kivéve, ha az örökölt ingyenes díjszabási szintet használják. Az egyes adattípusok megőrzése akár 4 napig is beállítható. [További](https://azure.microsoft.com/pricing/details/monitor/) információ a hosszabb adatmegőrzés díjszabásáról.  Ha 730 napnál hosszabb ideig szeretné megőrizni az adatmegőrzést, érdemes lehet [log Analytics munkaterület-adatexportálást](logs-data-export.md)használni.

### <a name="workspace-level-default-retention"></a>Munkaterület-szint alapértelmezett megőrzése

A munkaterület alapértelmezett megőrzésének beállításához 
 
1. A Azure Portal a munkaterületen válassza a **használat és becsült költségek** lehetőséget a bal oldali panelen.
2. A **Felhasználás és becsült költségek** oldalon kattintson az oldal tetején található **Adatmegőrzés** elemre.
3. A panelen a csúszka mozgatásával növelje vagy csökkentse a napok számát, majd kattintson az **OK** gombra.  Ha az *ingyenes* szintet választja, nem fogja tudni módosítani az adatmegőrzési időszakot, és frissítenie kell a fizetős szintre a beállítás szabályozása érdekében.

:::image type="content" source="media/manage-cost-storage/manage-cost-change-retention-01.png" alt-text="Munkaterület adatmegőrzési beállításának módosítása":::

A megőrzési idő csökkentése után az új megőrzési beállításnál régebbi adatok törlődnek. 

Az **adatmegőrzési** oldal lehetővé teszi a 30, 31, 60, 90, 120, 180, 270, 365, 550 és 730 nap megőrzési beállításait. Ha másik beállításra van szükség, akkor a paraméter használatával konfigurálható [Azure Resource Manager](./resource-manager-workspace.md) használatával `retentionInDays` . Ha az adatmegőrzést 30 napra állítja be, a `immediatePurgeDataOn30Days` (több napos türelmi időszakot megszüntető) paraméter használatával azonnal törölheti a régebbi adatok törlését. Ez akkor lehet hasznos, ha a megfelelőséggel kapcsolatos forgatókönyvek esetében azonnali adateltávolításra van szükség. Ez az azonnali törlési funkció csak Azure Resource Manageron keresztül érhető el. 

A 30 napos megőrzéssel rendelkező munkaterületek 31 napig ténylegesen megőrzik az adatmegőrzési időt. Ha fontos, hogy az adatok csak 30 napig legyenek tárolva, a Azure Resource Manager segítségével állítsa be az adatmegőrzést 30 napra és a `immediatePurgeDataOn30Days` paraméterrel.  

Alapértelmezés szerint 90 a rendszer két adattípust (-- `Usage` és `AzureActivity` --) tart fenn, és ez a 90 nap megtartása esetén nem számít fel díjat. Ha a munkaterület megtartása 90 nap fölé emelkedik, az adattípusok megőrzése is megnövekszik.  Ezek az adattípusok az adatfeldolgozási díjaktól is mentesek. 

A munkaterületen alapuló Application Insights-erőforrások (,,,,,,,, és) adattípusai `AppAvailabilityResults` `AppBrowserTimings` `AppDependencies` alapértelmezés szerint `AppExceptions` `AppEvents` `AppMetrics` `AppPageViews` `AppPerformanceCounters` `AppRequests` `AppSystemEvents` `AppTraces` 90 napra is megmaradnak, és ez a 90 nap megtartása esetén nem számítunk fel díjat. A megőrzésük adattípusú funkció használatával állítható be. 

Vegye figyelembe, hogy a Log Analytics [kiürítési API-ja](/rest/api/loganalytics/workspacepurge/purge) nincs hatással az adatmegőrzés számlázására, és nagyon körülhatárolt esetekben való használatra készült. Az adatmegőrzési számla csökkentése érdekében a megőrzési időtartamot csökkenteni kell a munkaterület vagy adott adattípusok esetében. 

### <a name="retention-by-data-type"></a>Megőrzés adattípus szerint

Az egyes adattípusok eltérő megőrzési beállításait is megadhatja 4 – 730 nap (kivéve az örökölt ingyenes díjszabási szinten lévő munkaterületek esetében), amelyek felülbírálják a munkaterület alapértelmezett megőrzését. Minden adattípus a munkaterület alerőforrása. A SecurityEvent tábla például a következő módon kezelhető [Azure Resource Managerban](../../azure-resource-manager/management/overview.md) :

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Vegye figyelembe, hogy az adattípus (tábla) megkülönbözteti a kis-és nagybetűket.  Egy adott adattípus aktuális adattípus-megőrzési beállításainak lekéréséhez (ebben a példában SecurityEvent) használja a következőt:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

> [!NOTE]
> Adattípusok megőrzése csak akkor történik meg, ha az adatmegőrzés kifejezetten be van állítva.  Azok az adattípusok, amelyek nem rendelkeznek explicit módon beállított megőrzéssel (és így öröklik a munkaterület megőrzését) nem adnak vissza semmit ebből a hívásból. 

A munkaterületen lévő összes adattípushoz tartozó, adattípust tartalmazó adatmegőrzési beállítások beszerzéséhez csak hagyja ki az adott adattípust, például:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Ha egy adott adattípus (ebben a példában SecurityEvent) megőrzését 730 napra szeretné beállítani, tegye a következőt:

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Az érvényes értékek `retentionInDays` 30 és 730 között vannak.

Az `Usage` és az `AzureActivity` adattípusok nem állíthatók be egyéni megőrzéssel. Ezek az alapértelmezett munkaterület-megőrzési vagy a 90 napos maximális értékkel lesznek végrehajtva. 

Egy nagyszerű eszköz, amellyel közvetlenül csatlakozhat Azure Resource Managerhoz az adattípusok megőrzésének beállításához az OSS-eszköz [ARMclient](https://github.com/projectkudu/ARMClient).  További információ a cikkek ARMclient: [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) és [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Íme egy példa a ARMClient használatával, amely a SecurityEvent-adatok 730 napos megőrzését állítja be:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> Az adatmegőrzési költségek csökkentése érdekében az egyes adattípusok megőrzésének beállítása felhasználható.  A 2019 októberi (ha ez a funkció megjelent) óta gyűjtött adatok esetében az egyes adattípusok megőrzésének csökkentése csökkentheti az adatmegőrzési költségeket az idő múlásával.  A korábban összegyűjtött adatok esetében az egyes típusok alacsonyabb megőrzésének beállítása nem befolyásolja a megőrzési költségeket.  

## <a name="manage-your-maximum-daily-data-volume"></a>A maximális napi adatmennyiség kezelése

Beállíthatja a napi korlátot, és korlátozhatja a napi betöltést a munkaterülethez, de körültekintően használhatja, mert a cél nem a napi korlát.  Ellenkező esetben a nap hátralevő részében elveszíti az adatok mennyiségét, ami hatással lehet más Azure-szolgáltatásokra és-megoldásokra, amelyek működése a munkaterületen elérhető naprakész adatoktól függ.  Mindez az informatikai szolgáltatásokat támogató erőforrások állapotára vonatkozó riasztások észlelésére és fogadására vonatkozó képességre is hatással van.  A napi korlátot arra használjuk, hogy az adatmennyiséget a felügyelt erőforrásokból érkező **váratlan növekedéssel** kezelje, és a korláton belül maradjon, vagy ha korlátozni szeretné a munkaterülethez tartozó nem tervezett díjakat. A napi korlát beállítása nem annak biztosítására szolgál, hogy minden nap elérjék a korlátot a munkaterületen.

Minden munkaterület napi korlátja a nap egy másik óráján érvényes. Az alaphelyzetbe állítási óra a **napi korlát** oldalon látható (lásd alább). Ez az átállítási óra nem konfigurálható. 

A napi korlát elérésekor a számlázható adattípusok gyűjteménye a nap hátralevő részében leáll. A napi korlát alkalmazásával járó késés azt jelenti, hogy a kupakot nem alkalmazzák pontosan a megadott napi korlát szintjén. A kijelölt Log Analytics munkaterülethez tartozó oldal tetején megjelenik egy figyelmeztető szalagcím, és a **LogManagement** kategóriában a *művelet* táblába egy műveleti esemény érkezik. Az adatgyűjtés a *napi korlát* alatt megadott alaphelyzetbe állítás időpontját követően fog folytatódni. Javasoljuk, hogy a műveleti esemény alapján határozzon meg egy riasztási szabályt, amely úgy van konfigurálva, hogy értesítést kapjon a napi adatkorlát elérésekor (lásd [alább](#alert-when-daily-cap-reached)). 

> [!NOTE]
> A napi korlát nem tudja leállítani az adatgyűjtést a megadott Cap-szintnek megfelelően, és néhány felesleges adatmennyiség várható, különösen akkor, ha a munkaterület nagy mennyiségű adattal rendelkezik. Tekintse meg az [alább](#view-the-effect-of-the-daily-cap) egy olyan lekérdezést, amely hasznos a Cap napi viselkedésének tanulmányozásához. 

> [!WARNING]
> A napi korlát nem állítja le a WindowsEvent, a SecurityAlert, a SecurityBaseline, a SecurityBaselineSummary, a SecurityDetection, a SecurityEvent, a WindowsFirewall, a MaliciousIPCommunication, a LinuxAuditLog, a SysmonEvent, a ProtectionStatus, az Update és a updateSummary típusú adattípusok gyűjteményét, kivéve azokat a munkaterületeket, amelyekben az Azure Defender (Security Center) telepítése a 2017. június 19. 

### <a name="identify-what-daily-data-limit-to-define"></a>A definiálni kívánt napi adatkorlát meghatározása

Tekintse át [log Analytics használati és becsült költségét](../usage-estimated-costs.md) , és Ismerje meg az adatfeldolgozási trendet, valamint azt, hogy mi a napi mennyiségi korlát. Figyelembe kell venni az ellátást, mivel elnyerte az erőforrásait a korlát elérésekor. 

### <a name="set-the-daily-cap"></a>A napi korlát beállítása

A következő lépések azt ismertetik, hogyan konfigurálható egy korlát a Log Analytics munkaterület által naponta betöltött adatmennyiség kezelésére.  

1. A munkaterületen válassza a **Felhasználás és becsült költségek** lehetőséget a bal oldali panelen.
2. A kiválasztott munkaterület **használati és becsült költségek** lapján kattintson a lap tetején található **adatkorlát** elemre. 
3. Alapértelmezés szerint a napi korlát **ki van kapcsolva** ? kattintson **a be** gombra a engedélyezéséhez, majd állítsa be az adatmennyiség korlátját GB/nap értékre.

:::image type="content" source="media/manage-cost-storage/set-daily-volume-cap-01.png" alt-text="Az adatkorlát konfigurálása Log Analytics":::
    
A napi korlát az ARM-ben konfigurálható úgy, hogy a `dailyQuotaGb` paramétert a `WorkspaceCapping` [munkaterületek – létrehozás vagy frissítés](/rest/api/loganalytics/workspaces/createorupdate#workspacecapping)című szakaszban leírtak szerint állítja be. 

A napi korláton végrehajtott módosításokat a következő lekérdezéssel követheti nyomon:

```kusto
_LogOperation | where Operation == "Workspace Configuration" | where Detail contains "Daily quota"
```

További információ a [_LogOperation](https://docs.microsoft.com/azure/azure-monitor/logs/monitor-workspace) függvényről. 

### <a name="view-the-effect-of-the-daily-cap"></a>A napi korlát hatásának megtekintése

A napi korlát hatásának megtekintéséhez fontos figyelembe venni a napi korlátban nem szereplő biztonsági adattípusokat, és a munkaterület alaphelyzetbe állítási óráját. A napi korlát alaphelyzetbe állításának órája a **napi korlát** oldalon látható.  A következő lekérdezéssel nyomon követheti az adatmennyiséget, amely a napi sapka-visszaállítások közötti napi korlátra vonatkozik. Ebben a példában a munkaterület alaphelyzetbe állításának órája 14:00.  Ezt a munkaterülethez kell frissítenie.

```kusto
let DailyCapResetHour=14;
Usage
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend TimeGenerated=datetime_add("hour",-1*DailyCapResetHour,TimeGenerated)
| where TimeGenerated > startofday(ago(31d))
| where IsBillable
| summarize IngestedGbBetweenDailyCapResets=sum(Quantity)/1000. by day=bin(TimeGenerated, 1d) | render areachart  
```

(A használati adatok típusa mezőben a mértékegysége `Quantity` MB.)

### <a name="alert-when-daily-cap-reached"></a>Riasztás, ha elérte a napi korlátot

Noha a Azure Portal vizualizációs mutatót mutatunk be, amikor eléri az adatkorlátot, ez a viselkedés nem feltétlenül igazodik az azonnali beavatkozást igénylő működési problémák kezeléséhez.  Riasztási értesítés fogadásához létrehozhat egy új riasztási szabályt Azure Monitorban.  További információt a [riasztások létrehozása, megtekintése és kezelése](../alerts/alerts-metric.md)című témakörben talál.

A kezdéshez itt láthatók az ajánlott beállítások a táblázatban a függvény használatával történő lekérdezéséhez `Operation` (további `_LogOperation` [információ](https://docs.microsoft.com/azure/azure-monitor/logs/monitor-workspace)). 

- Cél: válassza ki a Log Analytics erőforrást
- Kritériumok 
   - Jel neve: egyéni naplók keresése
   - Keresési lekérdezés: `_LogOperation | where Operation == "Data collection Status" | where Detail contains "OverQuota"`
   - A következő alapján: az eredmények száma
   - Feltétel: nagyobb, mint
   - Küszöbérték: 0
   - Időszak: 5 (perc)
   - Gyakoriság: 5 (perc)
- Riasztási szabály neve: elérte a napi adatkorlátot
- Súlyosság: figyelmeztetés (1. pont)

Miután meghatározta a riasztást, és elérte a korlátot, a rendszer riasztást indít el, és végrehajtja a műveleti csoportban definiált választ. E-mailben és SMS-ben értesítheti a csapatot, vagy automatizálhatja a műveleteket webhookok, Automation runbookok vagy [külső ITSM-megoldásokkal való integráció](../alerts/itsmc-definition.md#create-itsm-work-items-from-azure-alerts)révén. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>A vártnál magasabb szintű használatot okozó hibák elhárítása

A magasabb szintű használatot a következők okozhatják:
- További csomópontok, mint a várt adatok küldése Log Analytics munkaterületre
- A vártnál több információ Log Analytics munkaterületre (például egy új megoldás használatának megkezdése vagy egy meglévő megoldás konfigurációjának megváltozása miatt)

## <a name="understanding-nodes-sending-data"></a>Az adatokat küldő csomópontok ismertetése

Ha meg szeretné ismerni, hogy hány csomópontot jelentettek a szívverések az ügynöktől az elmúlt hónap minden napján, használja a következőt:

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Az adatokat küldő csomópontok számának lekérése az elmúlt 24 órában a lekérdezés használatával: 

```kusto
find where TimeGenerated > ago(24h) project Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

A következő lekérdezéssel kérheti le az adatokat küldő csomópontok listáját (és az egyesek által küldött adatok mennyiségét):

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

### <a name="nodes-billed-by-the-legacy-per-node-pricing-tier"></a>A csomópontok közötti örökölt díjszabási szinten számlázott csomópontok

A csomópontok közötti [örökölt díjszabási réteg](#legacy-pricing-tiers) az óránkénti részletességgel rendelkező csomópontok esetében nem számítja ki a csomópontok számát, és a csomópontok csak a biztonsági adattípusok készletét küldik. A csomópontok napi száma a következő lekérdezéshez közeledik:

```kusto
find where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now()) project Computer, _IsBillable, Type, TimeGenerated
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| where _IsBillable == true
| summarize billableNodesPerHour=dcount(computerName) by bin(TimeGenerated, 1h)
| summarize billableNodesPerDay = sum(billableNodesPerHour)/24., billableNodeMonthsPerDay = sum(billableNodesPerHour)/24./31.  by day=bin(TimeGenerated, 1d)
| sort by day asc
```

A számlán lévő egységek száma a (z `billableNodeMonthsPerDay` ) által a lekérdezésben reprezentált Node * hónapokban található. Ha a munkaterületen telepítve van a Update Management-megoldás, a fenti lekérdezés WHERE záradékában adja hozzá a frissítési és updateSummary típusú adattípusokat a listához. Végezetül pedig van néhány további összetettség a tényleges számlázási algoritmusban, ha a megoldás célcsoportját olyankor használják, amely nem szerepel a fenti lekérdezésben. 


> [!TIP]
> Ezekben a `find` lekérdezésekben takarékosan használhatja az adattípusokat az [erőforrás-igényes](./query-optimization.md#query-performance-pane) végrehajtáshoz. Ha **számítógépeken** nincs szükség az eredményekre, akkor a használati adattípus lekérdezése (lásd alább).

## <a name="understanding-ingested-data-volume"></a>A betöltött adatmennyiség ismertetése

A **használat és a becsült költségek** lapon az *adatfeldolgozás egy adott megoldási* diagramon megjeleníti az elküldött adatok teljes mennyiségét, valamint azt, hogy az egyes megoldások milyen mértékben küldik el az adatokat. Ez lehetővé teszi olyan trendek meghatározását, mint például, hogy a teljes adatfelhasználás (vagy egy adott megoldás használata) egyre növekszik-e, és továbbra is állandó vagy csökkenő marad. 

### <a name="data-volume-for-specific-events"></a>Adott eseményekhez tartozó adatmennyiség

Ha meg szeretné vizsgálni az egyes események betöltött adatainak méretét, lekérdezheti az adott táblázatot (ebben a példában `Event` ), majd korlátozhatja a lekérdezést a fontos eseményekre (ebben a példában az 5145-es vagy a 5156-es azonosítójú esemény):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Vegye figyelembe, hogy a záradék `where _IsBillable = true` kiszűri az adattípusokat bizonyos olyan megoldásokból, amelyekhez nincs betöltési díj. [További](./log-standard-columns.md#_isbillable) információ: `_IsBillable` .

### <a name="data-volume-by-solution"></a>Adatmennyiség megoldásonként

A számlázható adatmennyiségnek az elmúlt hónapban (kivéve az utolsó részleges napot) való megtekintésére használt lekérdezés a [használati](https://docs.microsoft.com/azure/azure-monitor/reference/tables/usage) adatok típusának használatával hozható össze:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution 
| render columnchart
```

A () záradéka `TimeGenerated` csak annak biztosítására szolgál, hogy a Azure Portal lekérdezési élménye az alapértelmezett 24 órában is megtekinthető maradjon. A használati adatok típusának használatakor, `StartTime` valamint `EndTime` az eredmények megjelenítéséhez használt időgyűjtők. 

### <a name="data-volume-by-type"></a>Adatmennyiség típus szerint

További részletezéssel tekintheti meg az adattípussal kapcsolatos adattrendeket:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType 
| render columnchart
```

Vagy megtekintheti az előző hónapra vonatkozó táblát megoldás és típus szerint.

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000 by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Adatmennyiség számítógépenként

Az `Usage` adattípus nem tartalmaz információkat a számítógép szintjén. Ha szeretné megtekinteni a feldolgozott számlázandó adat **méretét** a számítógépen, használja a `_BilledSize` [tulajdonságot](./log-standard-columns.md#_billedsize), amely a méretet adja meg bájtban:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, Computer, Type
| where _IsBillable == true and Type != "Usage"
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName 
| sort by BillableDataBytes desc nulls last
```

A `_IsBillable` [tulajdonság](./log-standard-columns.md#_isbillable) azt határozza meg, hogy a betöltött adatok díjkötelesek-e. A használat típusa nincs megadva, mivel ez csak az adattrendek elemzéséhez szükséges. 

A számítógépeken betöltött számlázható események **számának** megtekintéséhez használja a következőt: 

```kusto
find where TimeGenerated > ago(24h) project _IsBillable, Computer
| where _IsBillable == true and Type != "Usage"
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  
| sort by eventCount desc nulls last
```

> [!TIP]
> Ezekben a `find` lekérdezésekben takarékosan használhatja az adattípusokat az [erőforrás-igényes](./query-optimization.md#query-performance-pane) végrehajtáshoz. Ha **számítógépeken** nincs szükség az eredményekre, akkor a lekérdezés a használati adattípuson történik.

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Adatmennyiség Azure-erőforrás, erőforráscsoport vagy előfizetés alapján

Az Azure-ban üzemeltetett csomópontokból származó adatok esetén a __számítógépeken__ tárolt adatok **mérete** megszerezhető, a _ResourceId [tulajdonságot](./log-standard-columns.md#_resourceid)használva, amely az erőforrás teljes elérési útját biztosítja:

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by BillableDataBytes nulls last
```

Az Azure-ban üzemeltetett csomópontokból származó adatok esetében az __Azure-előfizetések által__ beolvasott adatok **mérete** az alábbiak `_SubscriptionId` szerint használható:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, _SubscriptionId
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _SubscriptionId | sort by BillableDataBytes nulls last
```

Az adatmennyiség erőforráscsoport szerinti lekéréséhez elemezheti a `_ResourceId` következőket:

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend resourceGroup = tostring(split(_ResourceId, "/")[4] )
| summarize BillableDataBytes = sum(BillableDataBytes) by resourceGroup | sort by BillableDataBytes nulls last
```

Azt is megteheti `_ResourceId` , hogy a teljes mértékben, ha szükséges, a

```Kusto
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
```

> [!TIP]
> Ezekben a `find` lekérdezésekben takarékosan használhatja az adattípusokat az [erőforrás-igényes](./query-optimization.md#query-performance-pane) végrehajtáshoz. Ha nincs szüksége az eredményekre az előfizetés, a erőforrás-csoport vagy az erőforrás neve alapján, akkor a lekérdezés a használati adatok típusát adja meg.

> [!WARNING]
> A használati adattípus egyes mezői, miközben még mindig a séma része, elavultak, és az értékek már nem lesznek feltöltve. Ezek a **számítógépek** , valamint a betöltéssel kapcsolatos mezők (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** és **AverageProcessingTimeMs**).


### <a name="querying-for-common-data-types"></a>Gyakori adattípusok lekérdezése

Az adatforrások egy adott adattípussal való mélyebb feltárásához Íme néhány hasznos példa a lekérdezésekre:

+ **Munkaterület-alapú Application Insights** erőforrások
  - További információ a [Application Insights használatának és költségeinek kezeléséhez](../app/pricing.md#data-volume-for-workspace-based-application-insights-resources)
+ **Biztonsági** megoldás
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **Naplókezelési** megoldás
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **Perf** adattípus
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **Esemény** adattípus
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Rendszernapló** adattípus
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics** adattípus
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

## <a name="tips-for-reducing-data-volume"></a>Tippek az adatmennyiség csökkentéséhez

Néhány javaslat a gyűjtött naplók mennyiségének csökkentésére:

| A nagy adatmennyiség forrása | Az adatmennyiség csökkentésének módja |
| -------------------------- | ------------------------- |
| Adatgyűjtés szabályai      | A [Azure monitor ügynök](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) adatgyűjtési szabályokat használ az adatgyűjtés kezelésére. Az egyéni XPath-lekérdezésekkel [korlátozhatja az adatgyűjtést](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-azure-monitor-agent#limit-data-collection-with-custom-xpath-queries) . | 
| Tárolóval kapcsolatos megállapítások         | [Konfigurálja a tároló](../containers/container-insights-cost.md#controlling-ingestion-to-reduce-cost) -elemzéseket úgy, hogy csak a szükséges adatokat gyűjtsön. |
| Biztonsági események            | Válassza a [gyakori vagy minimális biztonsági események](../../security-center/security-center-enable-data-collection.md#data-collection-tier) lehetőséget <br> Módosítsa a biztonsági naplózási szabályzatot, hogy csak a szükséges eseményeket gyűjtse be. Tekintse át a következőkhöz való eseménygyűjtés szükségességét: <br> - [szűrőplatform naplózása](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772749(v=ws.10)) <br> - [beállításjegyzék naplózása](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [fájlrendszer naplózása](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [kernelobjektum naplózása](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [leírókezelés naplózása](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> – Cserélhető tároló naplózása |
| Teljesítményszámlálók       | Módosítsa a [teljesítményszámlálók konfigurációját](../agents/data-sources-performance-counters.md): <br> – Csökkentse a gyűjtés gyakoriságát <br> – Csökkentse a teljesítményszámlálók számát |
| Eseménynaplók                 | Módosítsa az [eseménynaplók konfigurációját](../agents/data-sources-windows-events.md): <br> – Csökkentse a gyűjtött eseménynaplók számát <br> – Csak a szükséges eseményszinteket gyűjtse. Ne gyűjtsön például *Tájékoztatás* szintű eseményeket |
| Rendszernapló                     | Módosítsa a [rendszernapló konfigurációját](../agents/data-sources-syslog.md): <br> – Csökkentse a gyűjtésben részt vevő létesítmények számát <br> – Csak a szükséges eseményszinteket gyűjtse. Ne gyűjtsön például *Tájékoztatás* vagy *Hibakeresés* szintű eseményeket |
| AzureDiagnostics           | [Erőforrás-napló gyűjteményének](../essentials/diagnostic-settings.md#create-in-azure-portal) módosítása a következőre: <br> – Csökkentse a Log Analytics számára naplókat küldő erőforrások számát <br> – Csak a szükséges naplókat gyűjtse |
| Megoldásadatok olyan számítógépekről, amelyeknek nincs szükségük a megoldásra | A [megoldás célcsoportja](../insights/solution-targeting.md) csak a szükséges számítógépek adatainak gyűjtésére használható. |
| Application Insights | A [Application Insights adatmennyiség kezelésére](../app/pricing.md#managing-your-data-volume) vonatkozó beállítások áttekintése |
| [SQL-elemzés](../insights/azure-sql.md) | A naplózási beállítások finomhangolásához használja a [set-AzSqlServerAudit](/powershell/module/az.sql/set-azsqlserveraudit) . |
| Azure Sentinel | Tekintse át azokat a [Sentinel-adatforrásokat](../../sentinel/connect-data-sources.md) , amelyeket mostanában engedélyezett a további adatmennyiség forrásaként. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Csomópontok beolvasása az egyes csomópontok díjszabási szintjei szerint

Azon számítógépek listájának lekéréséhez, amelyek csomópontként lesznek kiszámlázva, ha a munkaterület a csomópontok közötti örökölt díjszabási szinten található, keresse meg a **számlázott adattípusokat** küldő csomópontokat (egyes adattípusok ingyenesek). Ehhez használja a `_IsBillable` [tulajdonságot](./log-standard-columns.md#_isbillable) , és használja a teljes tartománynév bal szélső mezőjét. Ez visszaadja a számlázott adatokat tartalmazó számítógépek számát (amely a csomópontok számlálásának és számlázásának részletessége):

```kusto
find where TimeGenerated > ago(24h) project Computer, TimeGenerated
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Biztonsági és automatizálási csomópontok számának beolvasása

A különböző biztonsági csomópontok számának megtekintéséhez használhatja a lekérdezést:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

A különböző Automation-csomópontok számának megtekintéséhez használja a következő lekérdezést:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>Az örökölt csomópontok díjszabási szintjeinek kiértékelése

Az, hogy az örökölt **csomópontok** díjszabási szintjéhez hozzáféréssel rendelkező munkaterületek jobban ki vannak-e kapcsolva az adott szinten, vagy az aktuális **utólagos** elszámolású vagy **kapacitás foglalási** szinten gyakran nehéz az ügyfelek számára felmérni az értékelést.  Ez magában foglalja a megfigyelt csomópontok közötti rögzített költség és a csomópontok díjszabási szintje közötti kompromisszumot, valamint a 500 MB/csomópont/nap adatfoglalást, valamint a betöltött adatmennyiség (GB-onként) költségét. 

Az értékelés megkönnyítése érdekében a következő lekérdezéssel javaslatot tehet az optimális díjszabási csomagra a munkaterület használati mintái alapján.  Ez a lekérdezés az elmúlt 7 napban egy munkaterületre betöltött figyelt csomópontokat és adatmennyiségeket vizsgálja, és minden nap esetében kiértékeli, hogy melyik árképzési csomag legyen optimális. A lekérdezés használatához meg kell adnia a

1. azt jelzi, hogy a munkaterület az Azure Defendert (Security Center) használja-e a vagy a értékre `workspaceHasSecurityCenter` `true` `false` , 
2. frissítse az árakat, ha vannak meghatározott kedvezmények, és
3. Itt adhatja meg, hogy hány napig kell visszakeresni és elemezni a beállítást `daysToEvaluate` . Ez akkor hasznos, ha a lekérdezésen túl sokáig próbálkozik a 7 napos adatmennyiség megkeresésével. 

Az árképzési csomagra vonatkozó javaslat lekérdezése:

```kusto
// Set these parameters before running query
// Pricing details available at https://azure.microsoft.com/pricing/details/monitor/
let daysToEvaluate = 7; // Enter number of previous days to analyze (reduce if the query is taking too long)
let workspaceHasSecurityCenter = false;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let CarRes100Price = 196.; // Enter your price for the 100 GB/day Capacity Reservation
let CarRes200Price = 368.; // Enter your price for the 200 GB/day Capacity Reservation
let CarRes300Price = 540.; // Enter your price for the 300 GB/day Capacity Reservation
let CarRes400Price = 704.; // Enter your price for the 400 GB/day Capacity Reservation
let CarRes500Price = 865.; // Enter your price for the 500 GB/day Capacity Reservation
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend billableGB = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)), DataGB )
| extend PerGBDailyCost = billableGB * PerGBPrice
| extend CapRes100DailyCost = CarRes100Price + max_of(billableGB - 100, 0.)* PerGBPrice
| extend CapRes200DailyCost = CarRes200Price + max_of(billableGB - 200, 0.)* PerGBPrice
| extend CapRes300DailyCost = CarRes300Price + max_of(billableGB - 300, 0.)* PerGBPrice
| extend CapRes400DailyCost = CarRes400Price + max_of(billableGB - 400, 0.)* PerGBPrice
| extend CapResLevel500AndAbove = max_of(floor(billableGB, 100),500)
| extend CapRes500AndAboveDailyCost = CarRes500Price*CapResLevel500AndAbove/500 + max_of(billableGB - CapResLevel500AndAbove, 0.)* PerGBPrice
| extend MinCost = min_of(
    PerNodeDailyCost,PerGBDailyCost,CapRes100DailyCost,CapRes200DailyCost,
    CapRes300DailyCost, CapRes400DailyCost, CapRes500AndAboveDailyCost)
| extend Recommendation = case(
    MinCost == PerNodeDailyCost, "Per node tier",
    MinCost == PerGBDailyCost, "Pay-as-you-go tier",
    MinCost == CapRes100DailyCost, "Capacity Reservation (100 GB/day)",
    MinCost == CapRes200DailyCost, "Capacity Reservation (200 GB/day)",
    MinCost == CapRes300DailyCost, "Capacity Reservation (300 GB/day)",
    MinCost == CapRes400DailyCost, "Capacity Reservation (400 GB/day)",
    MinCost == CapRes500AndAboveDailyCost, strcat("Capacity Reservation (",CapResLevel500AndAbove," GB/day)"),
    "Error"
)
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, 
    CapRes100DailyCost, CapRes200DailyCost, CapRes300DailyCost, CapRes400DailyCost, CapRes500AndAboveDailyCost, Recommendation 
| sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

Ez a lekérdezés nem a használat kiszámításának pontos replikálása, hanem a legtöbb esetben a díjszabási csomagra vonatkozó javaslatok nyújtásán fog működni.  

> [!NOTE]
> A OMS E1 Suite, OMS E2 Suite vagy OMS Add-On for System Center csomagból származó jogosultságok használatához válassza a Log Analytics */csomópont* díjszabási szintet.

## <a name="create-an-alert-when-data-collection-is-high"></a>Riasztás létrehozása, ha az adatgyűjtés magas

Ez a szakasz azt ismerteti, hogyan lehet riasztást létrehozni az elmúlt 24 órában tárolt adatmennyiség meghaladta a megadott összeget, Azure Monitor [naplózási riasztások](../alerts/alerts-unified-log.md)használatával. 

Ha riasztást szeretne kapni, ha az elmúlt 24 órában betöltött számlázható adatmennyiség meghaladja a 50 GB-ot, kövesse az alábbi lépéseket: 

- A **riasztási feltétel megadásával** határozza meg a célerőforrásként használt Log Analytics-munkaterületet.
- A **Riasztási feltételek** résznél az alábbiakat adja meg:
   - A **Jel neve** legyen **Egyéni naplókeresés**
   - **Lekérdezés keresése** a következőre: `Usage | where IsBillable | summarize DataGB = sum(Quantity / 1000.) | where DataGB > 50` . Ha másikat szeretne 
   - A **Riasztási logika****alapja legyen az** *eredmények száma*, a **Feltétel** pedig legyen *nagyobb mint* a következő **küszöbérték**: *0*
   - *1440* perces **időtartam** , a **riasztások gyakorisága** minden *1440* percre, naponta egyszer fut.
- **Határozza meg a riasztás részleteit** az alábbiak megadásával:
   - Az *50 GB-nál nagyobb számlázható adatmennyiség 24 órán belüli* **neve**
   - A **Súlyosság** legyen *Figyelmeztetés*

Megadhat egy meglévő [műveletcsoportot](../alerts/action-groups.md), illetve létrehozhat egy újat, hogy értesítést kapjon, amikor egy naplóriasztás megfelel a feltételeknek.

Ha riasztást kap, kövesse a fenti fejezetekben ismertetett lépéseket arról, hogy a használat miért nagyobb a vártnál.

## <a name="data-transfer-charges-using-log-analytics"></a>Adatátviteli díjak az Log Analytics használatával

Az adatok Log Analytics való küldése adatsávszélességi díjat eredményezhet, azonban a Virtual Machines, ahol a Log Analytics-ügynök telepítve van, és nem vonatkozik a diagnosztikai beállítások vagy az Azure Sentinelbe beépített más összekötők használatára. Az [Azure sávszélesség-díjszabási oldalán](https://azure.microsoft.com/pricing/details/bandwidth/)leírtak szerint a két régióban található Azure-szolgáltatások közötti adatforgalom a normál díjszabás szerint kifelé irányuló kimenő adatforgalom. A bejövő adatforgalom ingyenes. Ez a díj azonban nagyon kicsi (néhány%) az adatfeldolgozás Log Analytics költségeihez képest. Ennek következtében a Log Analytics szükséges költségek szabályozása a betöltött [adatmennyiségre](#understanding-ingested-data-volume)koncentrálhat. 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Hibaelhárítás, hogy miért Log Analytics már nem gyűjti az adatgyűjtést

Ha az örökölt ingyenes díjszabási szinten van, és naponta több mint 500 MB-nyi adat lett elküldve, az adatgyűjtés a nap hátralevő részében leáll. A napi korlát elérésének gyakori oka, hogy Log Analytics leállítja az adatgyűjtést, vagy az adatok hiányoznak.  A Log Analytics egy típusú eseményt hoz létre az adatgyűjtés indításakor és leállításakor. Futtassa a következő lekérdezést a keresésben, és ellenőrizze, hogy eléri-e a napi korlátot és a hiányzó adatértékeket: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Az adatgyűjtés leállításakor az OperationStatus állapota: **Figyelmeztetés**. Az adatgyűjtés indításakor az OperationStatus állapota: **Sikeres**. Az alábbi táblázat leírja, hogy az adatgyűjtés miért leáll és egy javasolt művelet az adatgyűjtés folytatásához:  

|Az OK gyűjtése leáll| Megoldás| 
|-----------------------|---------|
|Elérte a munkaterület napi korlátját|Várjon, amíg a gyűjtemény automatikusan újraindul, vagy növelje a napi adatmennyiség kezelése című témakörben leírt napi adatmennyiség korlátját. A napi korlát alaphelyzetbe állításának ideje a **napi korlát** oldalon látható. |
| A munkaterület elérte az [adatfeldolgozási kötet sebességét](../service-limits.md#log-analytics-workspaces) | Az Azure-erőforrásokról diagnosztikai beállítások használatával küldött adatok alapértelmezett maximális adatfeldolgozási sebessége munkaterületenként körülbelül 6 GB/perc. Ez egy hozzávetőleges érték, mivel a tényleges méret a napló hosszától és a tömörítési aránytól függően eltérő lehet az adattípusok között. Ez a korlátozás nem vonatkozik az ügynökökről vagy a Data Collector API-ról küldött adatokra. Ha egy adott munkaterületre nagyobb sebességgel küld adatokat, egyes adatok elvesznek, és a rendszer 6 óránként eseményt küld a munkaterület műveleti táblájára, amíg meg nem szűnik a küszöb túllépése. Ha a betöltési mennyiség továbbra is meghaladja a sebességkorlátot, vagy ha úgy gondolja, hogy nemsokára el fogja az érni, akkor az LAIngestionRate@microsoft.com címre küldött e-mailben vagy egy támogatási kérés megnyitásával kérheti a sebesség növelését a munkaterületen. Az adatfeldolgozási sebességkorlátot jelző esemény az `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The rate of data crossed the threshold"` lekérdezéssel kereshető meg. |
|Elérte az örökölt ingyenes díjszabási csomag napi korlátját |Várjon, amíg a gyűjtemény automatikusan újraindul, vagy váltson egy fizetős díjszabási csomagra.|
|Az Azure-előfizetés felfüggesztett állapotban van, a következő okból:<br> Az ingyenes próbaverzió véget ért<br> Az Azure pass lejárt<br> Elérte a havi költségkeretet (például MSDN-vagy Visual Studio-előfizetéssel)|Átállás a fizetős verzióra<br> Törlési korlát, vagy várjon, amíg a korlát alaphelyzetbe nem áll|

Ha értesítést szeretne kapni az adatgyűjtés leállításakor, kövesse a *napi adatsapka* -riasztás létrehozása című témakörben leírt lépéseket az adatgyűjtés leállításakor. A következő témakörben ismertetett lépések végrehajtásával konfigurálhat egy e-mailt, webhookot vagy runbook műveletet a riasztási szabályhoz: [műveleti csoport létrehozása](../alerts/action-groups.md) . 

## <a name="late-arriving-data"></a>Későn érkező adatvesztés   

Olyan helyzetek merülhetnek fel, amikor az adatok nagyon régi időbélyegzővel vannak betöltve, például ha egy ügynök nem tud kommunikálni Log Analytics kapcsolódási probléma miatt, vagy ha egy gazdagép nem megfelelő időpontot/időpontot használ. A problémák diagnosztizálásához használja az oszlopot `_TimeReceived` ([További információ](https://docs.microsoft.com/azure/azure-monitor/logs/log-standard-columns#_timereceived)) az `TimeGenerated` oszlop mellett. `TimeReceived` az az idő, amikor a rekordot a Azure Monitor betöltési pontja fogadta az Azure-felhőben.  

## <a name="limits-summary"></a>Korlátok összegzése

Vannak további Log Analytics korlátok, amelyek némelyike a Log Analytics díjszabási szintjétől függ. Ezeket az Azure- [előfizetések és-szolgáltatások korlátozásai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)dokumentálják.


## <a name="next-steps"></a>Következő lépések

- A keresési nyelv használatának megismeréséhez tekintse meg a [naplóban megkeresett Azure monitor naplókat](../logs/log-query-overview.md) . A keresési lekérdezésekkel további elemzéseket végezhet a használati adatokon.
- Az [új naplózási riasztás létrehozásával kapcsolatos](../alerts/alerts-metric.md) szakaszban leírt lépéseket követve beállíthatja, hogy értesítést kapjon, ha teljesül egy keresési feltétel.
- A [megoldás célcsoportja](../insights/solution-targeting.md) csak a szükséges számítógépek adatainak gyűjtésére használható.
- Egy hatékony esemény-gyűjtési házirend konfigurálásához tekintse át az [Azure Defender (Security Center) szűrési szabályzatát](../../security-center/security-center-enable-data-collection.md).
- [Teljesítményszámláló konfigurációjának](../agents/data-sources-performance-counters.md)módosítása
- Az események gyűjtési beállításainak módosításához tekintse át az [Eseménynapló konfigurációját](../agents/data-sources-windows-events.md).
- A syslog-gyűjtemény beállításainak módosításához tekintse át a [syslog konfigurációját](../agents/data-sources-syslog.md).
- A syslog-gyűjtemény beállításainak módosításához tekintse át a [syslog konfigurációját](../agents/data-sources-syslog.md).
