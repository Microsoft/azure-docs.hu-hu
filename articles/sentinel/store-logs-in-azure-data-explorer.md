---
title: Integráció Azure Data Explorer naplók hosszú távú megőrzéséhez és | Microsoft Docs
description: Küldje Azure Sentinel naplókat a Azure Data Explorer hosszú távú megőrzésre az adattárolási költségek csökkentése érdekében.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/21/2021
ms.author: bagol
ms.openlocfilehash: c7d9ef58d4bb15afe59c6734ce887c2cc3c07c26
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836226"
---
# <a name="integrate-azure-data-explorer-for-long-term-log-retention"></a>Integráció Azure Data Explorer naplók hosszú távú megőrzéséhez

<!--Info not included:>

Script - can't xref out to a private github repo from docs
-->

A naplókba betöltött naplók alapértelmezés szerint Azure Sentinel Log Analyticsben Azure Monitor tárolódnak. Ez a cikk azt ismerteti, hogyan csökkenthetők a Azure Sentinel megtartási költségei azáltal, hogy elküldi őket Azure Data Explorer (ADX) számára a hosszú távú megőrzés érdekében.

A naplók ADX-ben való tárolása csökkenti a költségeket, miközben megőrzi az adatok lekérdezésének képességét, és különösen hasznos az adatok növekedésével együtt. Előfordulhat például, hogy a biztonsági adatok idővel elveszítik az értékeket, de előfordulhat, hogy szabályozási követelményeknek kell megőriznie a naplókat, vagy rendszeres vizsgálatokat kell futtatnia a régebbi adatokon.

## <a name="about-azure-data-explorer"></a>Tudnivalók az Azure Data Explorerről

Az ADX egy big data elemzési platform, amely nagy mértékben van optimalizálva a napló- és adatelemzéshez. Mivel az ADX kusto lekérdezési nyelvet (KQL) használ lekérdezési nyelvként, jó alternatíva az adattároláshoz Azure Sentinel használata. Az ADX adattároláshoz való használatával platformfüggetlen lekérdezéseket futtathat, és vizualizálhatja az adatokat az ADX és a Azure Sentinel.

További információért tekintse meg az ADX dokumentációját [és](/azure/data-explorer/) [blogját.](https://azure.microsoft.com/en-us/blog/tag/azure-data-explorer/)

### <a name="when-to-integrate-with-adx"></a>Mikor kell integrálni az ADX-sel?

Azure Sentinel SIEM- és SOAR-képességeket, gyors üzembe helyezést és konfigurálást, valamint speciális, beépített biztonsági funkciókat biztosít az SOC-csapatok számára. A biztonsági adatok néhány Azure Sentinel hónap után is csökkenhetnek, ha az SOC-felhasználóknak nem kell olyan gyakran hozzáférniük az adatokhoz, mint az újabb adatokhoz.

Ha alkalmanként csak bizonyos táblákhoz kell hozzáférnie, például rendszeres vizsgálatokhoz vagy auditálásokhoz, érdemes megfontolni, hogy az adatok Azure Sentinel megőrzése már nem költséghatékony. Ezen a ponton azt javasoljuk, hogy tárolja az adatokat az ADX-ben, amely alacsonyabb költségekkel jár, de lehetővé teszi a feltárásokat ugyanazokkal a KQL-lekérdezésekkel, amelyeket a Azure Sentinel.

Az ADX-adatokat közvetlenül a Azure Sentinel a [Log Analytics ADX proxy funkcióval.](//azure/azure-monitor/logs/azure-monitor-data-explorer-proxy) Erre a naplókeresésben vagy munkafüzetekben használhat fürtközi lekérdezéseket. 

> [!IMPORTANT]
> Az alapvető SIEM-képességek, például az analytic rules, az UEBA és a vizsgálati gráf, nem támogatják az ADX-ben tárolt adatokat.
>

> [!NOTE]
> Az ADX-integráció lehetővé teszi az adatok szabályozását és részletességét is. További információ: [Kialakítási szempontok.](#design-considerations)
> 
## <a name="send-data-directly-to-azure-sentinel-and-adx-in-parallel"></a>Adatok közvetlen küldése a Azure Sentinel és az ADX-nek párhuzamosan

Előfordulhat, hogy meg  szeretné őrizni a biztonsági értékkel Azure Sentinel adatait az észlelések, incidensek kivizsgálása, veszélyforrás-veszélyforrás-felderítés, UEBA stb. során. Az adatok Azure Sentinel a Security Operations Center (SOC) felhasználói számára, ahol általában 3–12 hónapig elegendő a tárolás.

Azt is beállíthatja, hogy  az összes adata biztonsági értékétől függetlenül egyszerre legyen elküldve az ADX-nek, ahol hosszabb ideig tárolhatja őket. Bár az adatok egyszerre Azure Sentinel ADX-nek való küldése némi duplikációt eredményez, a költségmegtakarítás jelentős lehet, mivel csökkenti a megőrzési költségeket a Azure Sentinel.

> [!TIP]
> Ez a beállítás lehetővé teszi az adattárak közötti adatok korrelálatát, például az Azure Sentinel-ban tárolt biztonsági adatok az ADX-ban tárolt működési vagy hosszú távú adatokkal való gazdagítását. További információ: [Erőforrásközi](/azure/azure-monitor/logs/azure-monitor-data-explorer-proxy)lekérdezés Azure Data Explorer a Azure Monitor.
>

Az alábbi kép bemutatja, hogyan őrizheti meg az összes adatot az ADX-ban, miközben csak a biztonsági adatokat küldi el a Azure Sentinel napi használatra.

:::image type="content" source="media/store-logs-in-adx/store-data-in-sentinel-and-adx-in-parallel.png" alt-text="Az adatokat ADX-Azure Sentinel tárolja.":::

További információ az architektúra beállításának megvalósításáról: Azure Data Explorer [monitorozása.](/azure/architecture/solution-ideas/articles/monitor-azure-data-explorer)

## <a name="export-data-from-log-analytics-into-adx"></a>Adatok exportálása a Log Analyticsből az ADX-be

Ahelyett, hogy az adatokat közvetlenül az ADX-be külde, exportálhatja az adatokat a Log Analyticsből az ADX-be egy ADX eseményközponton vagy Azure Data Factory.

### <a name="data-export-architecture"></a>Adatexportarchitektúra

Az alábbi képen az exportált adatok mintafolyama látható a Azure Monitor folyamaton keresztül. Az adatok alapértelmezés szerint a Log Analyticsbe vannak irányítva, de konfigurálhatók úgy is, hogy Azure Storage-fiókba vagy eseményközpontba exportálják őket.

:::image type="content" source="media/store-logs-in-adx/export-data-from-azure-monitor.png" alt-text="Adatok exportálása Azure Monitor architektúrából.":::

Az adatexportolási szabályok konfigurálásakor válassza ki az exportálni kívánt naplótípusokat. A konfigurálást követően a Rendszer a Log Analytics-adatbeérkezési végpontra érkező és a kiválasztott táblák munkaterületére célzott új adatokat exportálja a Tárfiókba vagy az Eseményközpontba.

Az adatok exportálásra való konfigurálásakor vegye figyelembe a következőket:

|Megfontolandó  | Részletek |
|---------|---------|
|**Az exportált adatok hatóköre**     |  Miután konfigurálta az exportálást egy adott táblához, a rendszer az adott táblába küldött összes adatot exportálja, kivétel nélkül. Az adatok szűrt részkészletének exportálása vagy az exportálás adott eseményekre való korlátozása nem támogatott.       |
|**Helykövetelmények**     |   A Azure Monitor/Azure Sentinel munkaterületnek, valamint a célhelynek (Azure Storage-fióknak vagy eseményközpontnak) ugyanabban a földrajzi régióban kell lennie.      |
|**Támogatott táblák**     | Az exportálás nem minden táblát támogat, például az egyéni naplótáblákat, amelyek nem támogatottak. <br><br>További információ: [Log Analytics-munkaterület](/azure/azure-monitor/logs/logs-data-export) adatainak exportálása a Azure Monitor támogatott [táblák listájában.](/azure/azure-monitor/logs/logs-data-export#supported-tables)         |
|     |         |

### <a name="data-export-methods-and-procedures"></a>Adatexport módszerek és eljárások

Az alábbi eljárások egyikével exportálhatja az adatokat a Azure Sentinel ADX-be:

- **Egy ADX-eseményközponton keresztül.** Adatokat exportálhat a Log Analyticsből egy eseményközpontba, ahol az ADX-be használhatja őket. Ez a módszer néhány adatot (az első X hónapot) tárol mind a Azure Sentinel, mind az ADX-ban.

- **Az Azure Storage és a Azure Data Factory.** Exportálja az adatokat a Log Analyticsből a Azure Blob Storage- Azure Data Factory, majd a rendszer egy rendszeres másolási feladat futtatásával tovább exportálja az adatokat az ADX-be. Ezzel a módszerrel csak akkor másolhatja az adatokat a Azure Data Factory, ha az adatmegőrzési korlátja a Azure Sentinel Log Analyticsben van, így elkerülhető az ismétlődés.

### <a name="adx-event-hub"></a>[ADX-eseményközpont](#tab/adx-event-hub)

Ez a szakasz azt ismerteti, hogyan exportálhat Azure Sentinel adatokat a Log Analyticsből egy eseményközpontba, ahol az adatokat az ADX-be használhatja. Ahhoz hasonlóan, hogy az adatokat közvetlenül a Azure Sentinel és [az ADX-be](#send-data-directly-to-azure-sentinel-and-adx-in-parallel)párhuzamosan küldi, ez a metódus adatdedlikációt is tartalmaz, mivel az adatok a Log Analyticsbe való érkezésekor az ADX-be vannak streamelve.

Az alábbi képen az exportált adatok egy eseményközpontba való exportálásának mintája látható, amelyből azokat az ADX-be betöltötték.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-event-hub.png" alt-text="Adatok exportálása az ADX-be egy ADX-eseményközponton keresztül.":::

Az előző képen látható architektúra biztosítja a teljes Azure Sentinel SIEM-élményt, beleértve az incidenskezelést, a vizuális vizsgálatokat, a veszélyforrás-keresést, a fejlett vizualizációkat, az UEBA-t stb. az olyan adatokhoz, amelyekhez gyakran, X hónaponként kell *hozzáférni.* Ezzel az architektúrával emellett hosszú távú adatokat is lekérdezheti közvetlenül az ADX-en keresztül, vagy az ADX proxy funkciónak köszönhetően Azure Sentinel használatával. Az ADX-beli hosszú távú adattárolásra vonatkozó lekérdezések az ADX-ről az ADX-be való Azure Sentinel nélkül portosak.

> [!TIP]
> További információ erről az [eljárásról: Oktatóanyag: Monitorozási](/azure/data-explorer/ingest-data-no-code)adatok be- és lekérdezése a Azure Data Explorer.
>

**Adatok exportálása az ADX-be egy eseményközponton keresztül:**

1. **Konfigurálja a Log Analytics-adatexportot egy eseményközpontba.** További információ: [Log Analytics-munkaterület adatainak exportálása a Azure Monitor.](/azure/azure-monitor/platform/logs-data-export)

1. **Hozzon létre egy ADX-fürtöt és -adatbázist.** További információkért lásd:

    - [Fürt és adatbázis Azure Data Explorer létrehozása](/azure/data-explorer/create-cluster-database-portal)
    - [Válassza ki a megfelelő számítási termékváltozatot a Azure Data Explorer fürthöz](/azure/data-explorer/manage-cluster-choose-sku)

1. **Céltáblák létrehozása.** A nyers adatok először egy köztes táblába vannak behozva, ahol a nyers adatok tárolva, manipulálva és kibontva vannak.

    Az összes új adatra alkalmazott függvényhez hasonló frissítési szabályzat a kibontott adatokat a végleges táblába használja, amely ugyanazokkal a sémával rendelkezik, mint az eredeti Azure Sentinel.

    Állítsa a megőrzést **0** napra a nyers táblán. A rendszer csak a megfelelően formázott táblában tárolja az adatokat, és az átalakítás után a nyers táblában törli őket.

    További információ: [Monitorozási adatok be- és lekérdezése a Azure Data Explorer.](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)

1. <a name="mapping"></a>**Táblaleképezés létrehozása.** A JSON-táblák leképezése annak meghatározásához, hogy a rekordok hogyan jönek be a nyers eseménytáblába, amikor egy eseményközpontból jönnek. További információ: Frissítési szabályzat [létrehozása metrika- és naplóadatokhoz.](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)

1. **Hozzon létre egy frissítési szabályzatot, és csatolja a nyers rekordok táblához.** Ebben a lépésben hozzon létre egy frissítési szabályzat nevű függvényt, és csatolja azt a céltáblához, hogy az adatok a bevéséskor át is alakulnak.

    > [!NOTE]
    > Erre a lépésre csak akkor van szükség, ha az ADX-hez az adattáblák sémája és formátuma megegyezik a Azure Sentinel.
    >

    További információ: [Eseményközpont csatlakoztatása](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs)a Azure Data Explorer.

1. **Hozzon létre egy adatkapcsolatot az eseményközpont** és a nyers adattábla között az ADX-ban. Konfigurálja az ADX-et az adatok eseményközpontba való exportálásának részleteivel.

    A dokumentációban található Azure Data Explorer [adja](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) meg a következő adatokat:

    - **Cél:**. Adja meg az adott táblát a nyers adatokkal.
    - **Formázza a formátumot.** `.json`Táblaformátumként adja meg a következőt: .
    - **Alkalmazandó leképezés.** Adja meg a fenti [4. lépésben létrehozott leképezési](#mapping) táblát.


1. **Módosítsa a céltábla megőrzési beállítását.** Az [alapértelmezett Azure Data Explorer adatmegőrzési](/azure/data-explorer/kusto/management/retentionpolicy) szabályzat jóval hosszabb lehet a szükségesnél.

    A következő paranccsal frissítheti a megőrzési szabályzatot egy évre:

    ```kusto
    .alter-merge table <tableName> policy retention softdelete = 365d recoverability = disabled
    ```
### <a name="azure-storage--azure-data-factory"></a>[Azure Storage / Azure Data Factory](#tab/azure-storage-azure-data-factory)

Ez a szakasz azt ismerteti, hogyan exportálhat Azure Sentinel adatokat a Log Analyticsből az Azure Storage-ba, ahol Azure Data Factory hagyományos feladat futtatásával exportálhatja az adatokat az ADX-be.

Az Azure Storage és Azure Data Factory használatával csak akkor másolhatja az adatokat az Azure Storage-ból, ha az közel van a Azure Sentinel/Log Analyticsben található megőrzési korláthoz. Nincs adatded duplikáció, és  az ADX csak a megőrzési korlátnál régebbi adatok elérésére használatos a Azure Sentinel.

> [!TIP]
> Bár az Azure Storage és a régi adatok Azure Data Factory architektúra összetettebb, ez a módszer összességében nagyobb költségmegtakarítást biztosít.
>
Az alábbi képen az azure storage-ba exportált adatok mintafolyama látható, amelyből a Azure Data Factory egy normál feladatot futtat a további ADX-be való exportáláshoz.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-azure-storage-azure-data-factory.png" alt-text="Adatok exportálása az ADX-be az Azure Storage-on keresztül, Azure Data Factory.":::

**Adatok exportálása az ADX-be egy Azure Storage-on keresztül, majd Azure Data Factory:**

1. **Konfigurálja a Log Analytics-adatexportot egy eseményközpontba.** További információ: [Log Analytics-munkaterület adatainak exportálása a Azure Monitor.](/azure/azure-monitor/logs/logs-data-export?tabs=portal#enable-data-export)

1. **Hozzon létre egy ADX-fürtöt és -adatbázist.** További információkért lásd:

    - [Fürt és adatbázis Azure Data Explorer létrehozása](/azure/data-explorer/create-cluster-database-portal)
    - [Válassza ki a megfelelő számítási termékváltozatot a Azure Data Explorer fürthöz](/azure/data-explorer/manage-cluster-choose-sku)

1. **Céltáblák létrehozása.** A nyers adatok először egy köztes táblába vannak behozva, ahol a nyers adatok tárolva, manipulálva és kibontva vannak.

    Az összes új adatra alkalmazott függvényhez hasonló frissítési szabályzat a kibontott adatokat a végső táblába használja, amely ugyanazokkal a sémával rendelkezik, mint az Azure Sentinel.

    Állítsa a megőrzést **0 napra** a nyers táblán. A rendszer csak a megfelelően formázott táblában tárolja az adatokat, és az átalakítás után a nyers táblában törli őket.

    További információ: [Monitorozási adatok be- és lekérdezése a Azure Data Explorer.](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)

1. <a name="mapping"></a>**Táblaleképezés létrehozása.** A JSON-táblák leképezése annak meghatározásához, hogy a rekordok hogyan jönek be a nyers eseménytáblába, amikor egy eseményközpontból jönnek. További információ: Frissítési szabályzat [létrehozása metrika- és naplóadatokhoz.](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)

1. **Hozzon létre egy frissítési szabályzatot, és csatolja a nyers rekordok táblához.** Ebben a lépésben hozzon létre egy frissítési szabályzat nevű függvényt, és csatolja azt a céltáblához, hogy az adatok a bevéséskor át is alakulnak.

    > [!NOTE]
    > Erre a lépésre csak akkor van szükség, ha az ADX-hez az adattáblák sémája és formátuma megegyezik a Azure Sentinel.
    >

    További információ: [Eseményközpont csatlakoztatása](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs)a Azure Data Explorer.

1. **Hozzon létre egy adatkapcsolatot az eseményközpont** és a nyers adattábla között az ADX-ban. Konfigurálja az ADX-et az adatok eseményközpontba való exportálásának részleteivel.

    A dokumentációban található Azure Data Explorer [adja](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) meg a következő adatokat:

    - **Cél:**. Adja meg az adott táblát a nyers adatokkal.
    - **Formázza a formátumot.** `.json`Táblaformátumként adja meg a következőt: .
    - **Alkalmazandó leképezés.** Adja meg a fenti [4. lépésben létrehozott leképezési](#mapping) táblát.

1. **A Azure Data Factory beállítása:**

    - Hozzon létre csatolt szolgáltatásokat az Azure Storage-hoz és Azure Data Explorer. További információkért lásd:

        - [Adatok másolása és átalakítása az Azure Blob Storage-ban az Azure Data Factory](/azure/data-factory/connector-azure-blob-storage)
        - [Adatok másolása a Azure Data Explorer a következővel: Azure Data Factory.](/azure/data-factory/connector-azure-data-explorer)

    - Adatkészlet létrehozása az Azure Storage-ból. További információ: [Adatkészletek a Azure Data Factory.](/azure/data-factory/concepts-datasets-linked-services)

    - Hozzon létre egy másolási művelettel egy adat folyamatot a **LastModifiedDate tulajdonságai** alapján.

        További információ: [Copy new and changed files by **LastModifiedDate** with Azure Data Factory.](/azure/data-factory/solution-template-copy-new-files-lastmodifieddate)

---

## <a name="design-considerations"></a>Kialakítási szempontok

Amikor az adatokat Azure Sentinel ADX-ban tárolja, vegye figyelembe a következő elemeket:

|Megfontolandó  |Description  |
|---------|---------|
|**Fürtméret és termékváltozat**     | Gondosan tervezze meg a csomópontok számát és a fürtben lévő virtuálisgép-termékváltozatot. Ezek a tényezők határozzák meg a feldolgozási teljesítmény mennyiségét és a gyors gyorsítótár (SSD és memória) méretét. Minél nagyobb a gyorsítótár, annál több adat lesz lekérdezhető nagyobb teljesítményen. <br><br>Javasoljuk, hogy látogasson el az [ADX](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html)méretezési kalkulátorhoz, ahol különböző konfigurációkat játszhat, és láthatja az eredményül kapott költségeket. <br><br>Az ADX egy automatikus skálázási képességgel is rendelkezik, amely intelligens döntéseket hoz a csomópontok szükség szerint, a fürtterhelés alapján való hozzáadásáról és eltávolításáról. További információ: Fürtök horizontális skálázásának (horizontális felskálolásának) kezelése a Azure Data Explorer a változó [igényeknek való igazodás érdekében.](/azure/data-explorer/manage-cluster-horizontal-scaling)      |
|**Gyors/hideg gyorsítótár**     | Az ADX biztosítja a gyors gyorsítótárban lévő adattáblák vezérlését, és gyorsabban adja vissza az eredményeket. Ha nagy mennyiségű adat található az ADX-fürtben, akkor előfordulhat, hogy hónap szerint szeretné lebontani a táblákat, hogy részletesebben tudjanak adatokat látni a gyors adatokat tároló gyorsítótárban. <br><br>További információ: [Gyorsítótár-szabályzat (forró és hideg gyorsítótár)](/azure/data-explorer/kusto/management/cachepolicy)       |
|**Megőrzés**     |   Az ADX-ban konfigurálhatja, hogy mikor törlődnek adatok egy adatbázisból vagy egy önálló táblából, ami szintén fontos része a tárolási költségek korlátozásának. <br><br> További információ: [Adatmegőrzési szabályzat.](/azure/data-explorer/kusto/management/retentionpolicy)       |
|**Biztonság**     |  Számos ADX-beállítás segíthet az adatok védelmében, például az identitáskezelésben, a titkosításban stb. Kifejezetten a szerepköralapú hozzáférés-vezérléshez (RBAC) az ADX konfigurálható úgy, hogy korlátozza a hozzáférést egy táblán belüli adatbázisokhoz, táblákhoz vagy akár sorokhoz. További információ: [Biztonság](/azure/data-explorer/security) az Azure Data Explorer és [Sorszintű biztonság.](/azure/data-explorer/kusto/management/rowlevelsecuritypolicy)|
|**Adatok megosztása**     |   Az ADX lehetővé teszi, hogy más felek, például partnerek vagy szállítók számára elérhetővé tegye az adatokat, és akár más felektől is vásároljon adatokat. További információkért lásd: [Azure Data Share adatok megosztása a](/azure/data-explorer/data-share)Azure Data Explorer.      |
| **Egyéb költségösszetevők** | Vegye figyelembe a többi költségösszetevőt a következő módszerekhez: <br><br>**Adatok exportálása ADX-eseményközponton keresztül:** <br>– A Log Analytics-adatok exportálási költségei, exportált gbs-enként felszámtöltve. <br>– Eseményközpont költségei, átviteli egység szerint felszámtöltve.  <br><br>**Adatok exportálása az Azure Storage és a Azure Data Factory:** <br>– Log Analytics-adatexport, exportált GBs-enként kell fizetnie. <br>– Azure Storage, tárolt GB-okkal kell fizetnie. <br>– Azure Data Factory, a futtatott tevékenységek példányonkénti díja.
|     |         |

## <a name="next-steps"></a>Következő lépések

Függetlenül attól, hogy hol tárolja az adatokat, folytassa a további vizsgálatot a Azure Sentinel.

További információkért lásd:

- [Oktatóanyag: Incidensek kivizsgálása Azure Sentinel](tutorial-investigate-cases.md)
- [Fenyegetések elleni Azure Sentinel](hunting.md)
