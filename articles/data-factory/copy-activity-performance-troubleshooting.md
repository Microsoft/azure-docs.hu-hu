---
title: A másolási tevékenység teljesítményével kapcsolatos hibák
description: További információ a másolási tevékenység teljesítményének hibakereséséről a Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/07/2021
ms.openlocfilehash: ce7c97abfb879e9298edac5f38540bbc026274da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584400"
---
# <a name="troubleshoot-copy-activity-performance"></a>A másolási tevékenység teljesítményével kapcsolatos hibák

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan lehet elhárítani a másolási tevékenység teljesítményével kapcsolatos problémát Azure Data Factoryban. 

A másolási tevékenység futtatása után összegyűjtheti a Futtatás eredményét és a teljesítmény statisztikáit a [másolási tevékenység figyelése](copy-activity-monitoring.md) nézetben. Például:

![A másolási tevékenység futtatási részleteinek figyelése](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>Teljesítmény-finomhangolási tippek

Bizonyos helyzetekben, amikor másolási tevékenységet futtat Data Factoryban, a fenti példában látható **"Performance tuning tippek"** láthatók. A tippekből megtudhatja, hogy az ADF milyen szűk keresztmetszetet észlelt ehhez a másolási menethez, valamint javaslatot tesz a másolási teljesítmény növelésére. Próbálja megismételni a módosításokat, majd futtassa újra a másolást.

A teljesítmény-hangolási tippek jelenleg a következő esetekben nyújtanak javaslatokat:

| Kategória              | Teljesítmény-finomhangolási tippek                                      |
| --------------------- | ------------------------------------------------------------ |
| Adattár-specifikus   | Adatok betöltése az **Azure szinapszis analyticsbe**: javasolt a Base vagy a copy utasítás használata, ha nincs használatban. |
| &nbsp;                | Adatok másolása innen/ből **Azure SQL Database**: Ha a DTU magas kihasználtságú, javasoljuk, hogy magasabb szintre frissítsen. |
| &nbsp;                | Adatok másolása a (z)/rendszerről **Azure Cosmos DBre**: Ha ru magas kihasználtsággal rendelkezik, javasoljuk, hogy nagyobb ru-re frissítsen. |
|                       | Adatok másolása az **SAP-táblából**: nagy mennyiségű adatok másolása esetén a párhuzamos terhelés engedélyezéséhez és a partíciók maximális számának növeléséhez javasolt az SAP-összekötő partíciós beállításának kihasználása. |
| &nbsp;                | Az **Amazon vöröseltolódásról** származó adatok beolvasása |
| Adattár-szabályozás | Ha az adattár a másolás során több írási/olvasási műveletet is szabályoz, akkor az adattároló megengedett kérelmi arányának ellenőrzését és növelését, illetve az egyidejű munkaterhelés csökkentését javasolja. |
| Integration Runtime  | Ha saját üzemeltetésű **Integration Runtime (IR)** és másolási tevékenységet használ a várólistán mindaddig, amíg az IR nem rendelkezik a végrehajtáshoz rendelkezésre álló erőforrással, javasoljuk, hogy bővítse/felskálázást hajtson végre az IR-ben. |
| &nbsp;                | Ha olyan **Azure Integration Runtime** használ, amely nem optimális régióban van, ami lassú olvasási/írási kísérletet eredményez, akkor azt javasoljuk, hogy egy másik régióban lévő IR használatára konfigurálja a konfigurálást. |
| Hibatűrés       | Ha a hibatűrést konfigurálja, és a nem kompatibilis sorok kihagyása lassú teljesítményt eredményez, a forrás-és fogadói adat kompatibilitásának biztosítása javasolt. |
| Előkészített másolás           | Ha a szakaszos másolás konfigurálva van, de nem hasznos a forrás-fogadó pár számára, javasoljuk, hogy távolítsa el azt. |
| Folytatás                | Ha a másolási tevékenység az utolsó meghibásodási pontról folytatódik, de az eredeti Futtatás után módosítja a DIU beállítást, vegye figyelembe, hogy az új DIU beállítás nem lép érvénybe. |

## <a name="understand-copy-activity-execution-details"></a>A másolási tevékenység végrehajtási részleteinek ismertetése

A másolási tevékenység figyelése nézet alján a végrehajtás részletei és időtartama a másolási tevékenység lépéseit ismerteti (lásd a cikk elején található példát), amely különösen hasznos a másolási teljesítmény hibaelhárításához. A másolási Futtatás szűk keresztmetszete a leghosszabb időtartamú. Tekintse meg az alábbi táblázatot az egyes fázisok definíciójában, és Ismerje meg, hogyan lehet [elhárítani a másolási tevékenységet a Azure IR](#troubleshoot-copy-activity-on-azure-ir) és a [másolási tevékenységet a saját](#troubleshoot-copy-activity-on-self-hosted-ir) üzemeltetésű integrációs modulban az ilyen adatokkal.

| Fázis           | Leírás                                                  |
| --------------- | ------------------------------------------------------------ |
| Üzenetsor           | Az az eltelt idő, amíg a másolási tevékenység ténylegesen nem indul el az integrációs modulban. |
| Másolás előtti parancsfájl | A másolási tevékenység és a másolási tevékenység közötti eltelt idő a fogadó adattárban lévő előmásolási parancsfájl végrehajtásának befejezése után. Az adatbázis-nyelők előmásolási parancsfájljának konfigurálásakor alkalmazza, például amikor az adatírást Azure SQL Database az új Adatmásolás előtt törli az adatbevitelt. |
| Átvitel        | Az előző lépés vége és a forrás és a fogadó közötti összes adatok átvitele között eltelt idő. <br/>Figyelje meg, hogy az átvitel alatt álló allépések párhuzamosan futnak, és egyes műveletek nem jelennek meg, pl. a fájlformátum elemzése/létrehozása.<br><br/>- **Első bájtig eltelt idő:** Az előző lépés vége és az az idő, amikor az IR megkapja az első bájtot a forrás adattárból. A nem fájl alapú forrásokra vonatkozik.<br>- **Listaelem forrása:** A forrásfájlok vagy az adatpartíciók számbavételére fordított idő mennyisége. Az utóbbi akkor érvényes, ha az adatbázis-források partíciós beállításait konfigurálja, például az adatok olyan adatbázisokból való másolásakor, mint például az Oracle/SAP HANA/Teradata/Netezza/etc.<br/>-**Olvasás a forrástól:** Az adatok forrás adattárból való beolvasásához felhasznált idő mennyisége.<br/>- **Írás a** fogadóba: Az adattárolási adattárba való adatírás során eltöltött idő mennyisége. Megjegyzés: egyes összekötők jelenleg nem rendelkeznek ezzel a metrikával, beleértve az Azure Cognitive Search, az Azure Adatkezelő, az Azure Table Storage, az Oracle, a SQL Server, a Common Data Service, a Dynamics 365, a Dynamics CRM, a Salesforce/Salesforce Service Cloud szolgáltatást. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Azure IR másolási tevékenységének hibáinak megoldása

Kövesse a [teljesítmény-hangolás lépéseit](copy-activity-performance.md#performance-tuning-steps) a teljesítmény-ellenőrzés megtervezéséhez és a forgatókönyvhöz való végrehajtásához. 

Ha a másolási tevékenység teljesítménye nem felel meg az elvárásoknak, a Azure Integration Runtime futó egypéldányos másolási tevékenységek hibáinak megoldása érdekében alkalmazza a javaslatot a másolás figyelése nézetben, és próbálkozzon újra. [](#performance-tuning-tips) Ellenkező esetben olvassa el a [másolási tevékenység végrehajtásának részleteit](#understand-copy-activity-execution-details), tekintse meg a **leghosszabb** időtartamú szakaszt, és alkalmazza az alábbi útmutatást a másolási teljesítmény növeléséhez:

- **"A másolás előtti parancsfájl" hosszú** ideig tart: az azt jelenti, hogy a fogadó adatbázison futó másolás előtti parancsfájl hosszú ideig tart. A teljesítmény növelése érdekében állítsa be a megadott előmásolási parancsfájl-logikát. Ha további segítségre van szüksége a parancsfájl fejlesztéséhez, forduljon az adatbázis-csapathoz.

- **"Átvitel – az első bájtig eltelt idő" hosszú ideig tartó munkavégzést** eredményezett: Ez azt jelenti, hogy a forrás lekérdezése sokáig tart, és az adatok visszaadása A lekérdezés vagy a kiszolgáló keresése és optimalizálása. Ha további segítségre van szüksége, vegye fel a kapcsolatot az adattár csapatával.

- **"A továbbítási lista forrása" hosszú munkaidőtartamot tapasztalt**: a forrásfájlok vagy a forrás-adatbázis adatpartícióinak számbavétele lassú.
  - Ha fájl-alapú forrásból másol adatokat, ha **helyettesítő szűrőt** használ a mappa elérési útján vagy a fájlnéven ( `wildcardFolderPath` vagy `wildcardFileName` ), vagy a **fájl utolsó módosításának időszűrőjét** ( `modifiedDatetimeStart` vagy) használja `modifiedDatetimeEnd` , vegye figyelembe, hogy az ilyen szűrő a másolási tevékenységet fogja eredményezni a megadott mappában lévő összes fájlnak a kliens oldalára való listázásakor, majd alkalmazza a szűrőt. Ilyenkor előfordulhat, hogy a fájl enumerálása különösen szűk keresztmetszetet jelenthet, ha csak a fájlok kis halmaza felel meg a szűrési szabálynak.

    - Győződjön meg arról, hogy [a fájlok másolását a DateTime partíciós fájl elérési útja vagy neve alapján](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)végezheti el. Ez a módszer nem jelent terhet a forrás oldal listázására.

    - Ellenőrizze, hogy használhatja-e az adattár natív szűrőjét, azaz az Amazon S3/Azure Blob/Azure File Storage és a "**listAfter/listBefore**"**előtagot** az ADLS Gen1hoz. Ezek a szűrők az adattár kiszolgálóoldali szűrői, és sokkal jobb teljesítményt biztosítanak.

    - Vegye figyelembe, hogy az egyetlen nagyméretű adathalmazt több kisebb adatkészletre osztja szét, és lehetővé teszi, hogy a másolási feladatok párhuzamosan fussanak az adatok egyes részein. Ezt megteheti a lookup/GetMetadata + ForEach + másolás lehetőséggel. Tekintse át a [több tárolóból származó fájlok másolását](solution-template-copy-files-multiple-containers.md) , vagy az [Amazon S3-ból származó adatok átimportálását](solution-template-migration-s3-azure.md) általános példaként ADLS Gen2 megoldási sablonokra.

  - Ellenőrizze, hogy az ADF bármilyen szabályozási hibát jelez-e a forrásnál, vagy ha az adattár magas kihasználtságú állapotban van. Ha igen, csökkentse a számítási feladatokat az adattáron, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával, és növelje a sávszélesség-szabályozási korlátot vagy az elérhető erőforrást.

  - Használja a Azure IR a forrás adattároló-régiójában lévő vagy azzal megegyezően.

- **"A forrástól való olvasás" hosszú munkaidőtartamot tapasztalt**: 

  - Az összekötő-specifikus betöltés ajánlott eljárás alkalmazása, ha alkalmazható. Ha például az [Amazon vöröseltolódásról](connector-amazon-redshift.md)másol be Adatmásolást, konfigurálja a következőt: a vöröseltolódás eltávolításának használata.

  - Ellenőrizze, hogy az ADF bármilyen szabályozási hibát jelez-e a forráson, vagy ha az adattár magas kihasználtságú. Ha igen, csökkentse a számítási feladatokat az adattáron, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával, és növelje a sávszélesség-szabályozási korlátot vagy az elérhető erőforrást.

  - A másolási forrás és a fogadó minta keresése: 

    - Ha a másolási minta támogatja a 4 adatintegrációs egységet (DIUs), tekintse meg [ezt a szakaszt](copy-activity-performance-features.md#data-integration-units) a részletek részben, és a jobb teljesítmény érdekében próbálkozzon a DIUs növelésével. 

    - Ellenkező esetben érdemes lehet egy nagyméretű adathalmazt több kisebb adatkészletbe bontani, és lehetővé kell tennie, hogy a másolási feladatok párhuzamosan fussanak az adatok egyes részein. Ezt megteheti a lookup/GetMetadata + ForEach + másolás lehetőséggel. Tekintse át a [fájlok több tárolóból való másolását](solution-template-copy-files-multiple-containers.md), az [Amazon S3-ból történő adatáttelepítést ADLS Gen2](solution-template-migration-s3-azure.md), vagy egy általános példaként egy vezérlőelem-tábla megoldási sablonjaival történő [tömeges másolást](solution-template-bulk-copy-with-control-table.md) .

  - Használja a Azure IR a forrás adattároló-régiójában lévő vagy azzal megegyezően.

- **"Átvitel – írás a fogadóba" – hosszú munkavégzés időtartama**:

  - Az összekötő-specifikus betöltés ajánlott eljárás alkalmazása, ha alkalmazható. Ha például az adatok [Azure szinapszis analyticsbe](connector-azure-sql-data-warehouse.md)való másolását használja, használjon a Base vagy a copy utasítást. 

  - Ellenőrizze, hogy az ADF jelent-e szabályozási hibát a fogadón, vagy ha az adattár magas kihasználtságú. Ha igen, csökkentse a számítási feladatokat az adattáron, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával, és növelje a sávszélesség-szabályozási korlátot vagy az elérhető erőforrást.

  - A másolási forrás és a fogadó minta keresése: 

    - Ha a másolási minta támogatja a 4 adatintegrációs egységet (DIUs), tekintse meg [ezt a szakaszt](copy-activity-performance-features.md#data-integration-units) a részletek részben, és a jobb teljesítmény érdekében próbálkozzon a DIUs növelésével. 

    - Ellenkező esetben, fokozatosan hangolja a [párhuzamos másolatokat](copy-activity-performance-features.md), vegye figyelembe, hogy túl sok párhuzamos másolat is megsértheti a teljesítményt.

  - Használja Azure IR a fogadó adattároló-régiójának azonos vagy záró területére.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>A másolási tevékenység a saját üzemeltetésű integrációs modulban – problémamegoldás

Kövesse a [teljesítmény-hangolás lépéseit](copy-activity-performance.md#performance-tuning-steps) a teljesítmény-ellenőrzés megtervezéséhez és a forgatókönyvhöz való végrehajtásához. 

Ha a másolási teljesítmény nem felel meg az elvárásoknak, a Azure Integration Runtimeon futó egypéldányos másolási tevékenységek [](#performance-tuning-tips) hibáinak megoldása érdekében alkalmazza a javaslatot a másolás figyelése nézetben, és próbálkozzon újra. Ellenkező esetben olvassa el a [másolási tevékenység végrehajtásának részleteit](#understand-copy-activity-execution-details), tekintse meg a **leghosszabb** időtartamú szakaszt, és alkalmazza az alábbi útmutatást a másolási teljesítmény növeléséhez:

- A **"várólista" hosszú időtartamot észlelt:** az azt jelenti, hogy a másolási tevékenység hosszú ideig várakozik a várólistán, amíg a saját üzemeltetésű integrációs modulnak erőforrást nem kell végrehajtania. Tekintse át az IR-kapacitást és-használatot, és a számítási feladatok alapján [fel-vagy kibővítheti](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

- **"Átvitel – az első bájtig eltelt idő" hosszú ideig tartó munkavégzést** eredményezett: Ez azt jelenti, hogy a forrás lekérdezése sokáig tart, és az adatok visszaadása A lekérdezés vagy a kiszolgáló keresése és optimalizálása. Ha további segítségre van szüksége, vegye fel a kapcsolatot az adattár csapatával.

- **"A továbbítási lista forrása" hosszú munkaidőtartamot tapasztalt**: a forrásfájlok vagy a forrás-adatbázis adatpartícióinak számbavétele lassú.

  - Ellenőrizze, hogy a saját üzemeltetésű IR-gép kevés késéssel kapcsolódik-e a forrás-adattárhoz. Ha a forrás az Azure-ban található, [ezzel az eszközzel](http://www.azurespeed.com/Azure/Latency) ellenőrizhető a saját ÜZEMELTETÉSű IR-gép késése az Azure-régióban, annál kevesebb a jobb.

  - Ha fájl-alapú forrásból másol adatokat, ha **helyettesítő szűrőt** használ a mappa elérési útján vagy a fájlnéven ( `wildcardFolderPath` vagy `wildcardFileName` ), vagy a **fájl utolsó módosításának időszűrőjét** ( `modifiedDatetimeStart` vagy) használja `modifiedDatetimeEnd` , vegye figyelembe, hogy az ilyen szűrő a másolási tevékenységet fogja eredményezni a megadott mappában lévő összes fájlnak a kliens oldalára való listázásakor, majd alkalmazza a szűrőt. Ilyenkor előfordulhat, hogy a fájl enumerálása különösen szűk keresztmetszetet jelenthet, ha csak a fájlok kis halmaza felel meg a szűrési szabálynak.

    - Győződjön meg arról, hogy [a fájlok másolását a DateTime partíciós fájl elérési útja vagy neve alapján](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)végezheti el. Ez a módszer nem jelent terhet a forrás oldal listázására.

    - Ellenőrizze, hogy használhatja-e az adattár natív szűrőjét, azaz az Amazon S3/Azure Blob/Azure File Storage és a "**listAfter/listBefore**"**előtagot** az ADLS Gen1hoz. Ezek a szűrők az adattár kiszolgálóoldali szűrői, és sokkal jobb teljesítményt biztosítanak.

    - Vegye figyelembe, hogy az egyetlen nagyméretű adathalmazt több kisebb adatkészletre osztja szét, és lehetővé teszi, hogy a másolási feladatok párhuzamosan fussanak az adatok egyes részein. Ezt megteheti a lookup/GetMetadata + ForEach + másolás lehetőséggel. Tekintse át a [több tárolóból származó fájlok másolását](solution-template-copy-files-multiple-containers.md) , vagy az [Amazon S3-ból származó adatok átimportálását](solution-template-migration-s3-azure.md) általános példaként ADLS Gen2 megoldási sablonokra.

  - Ellenőrizze, hogy az ADF bármilyen szabályozási hibát jelez-e a forrásnál, vagy ha az adattár magas kihasználtságú állapotban van. Ha igen, csökkentse a számítási feladatokat az adattáron, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával, és növelje a sávszélesség-szabályozási korlátot vagy az elérhető erőforrást.

- **"A forrástól való olvasás" hosszú munkaidőtartamot tapasztalt**: 

  - Ellenőrizze, hogy a saját üzemeltetésű IR-gép kevés késéssel kapcsolódik-e a forrás-adattárhoz. Ha a forrás az Azure-ban található, [ezzel az eszközzel](http://www.azurespeed.com/Azure/Latency) ellenőrizhető a saját ÜZEMELTETÉSű IR-gép késése az Azure-régiók számára, annál kevesebb a jobb.

  - Ellenőrizze, hogy a saját üzemeltetésű IR-gép rendelkezik-e elegendő bejövő sávszélességgel az adatok hatékony olvasásához és átviteléhez. Ha a forrásoldali adattár az Azure-ban található, az [eszköz](https://www.azurespeed.com/Azure/Download) használatával ellenőrizze a letöltési sebességet.

  - Tekintse meg a saját üzemeltetésű integrációs modul CPU-és memóriahasználat-trendjét a Azure Portal-> a > – áttekintés lapot. Ha a CPU-használat magas vagy kevés a rendelkezésre álló memória, vegye fontolóra a vertikális [felskálázást](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

  - Az összekötő-specifikus betöltés ajánlott eljárás alkalmazása, ha alkalmazható. Például:

    - Az [Oracle](connector-oracle.md#oracle-as-source), a [Netezza](connector-netezza.md#netezza-as-source), a [Teradata](connector-teradata.md#teradata-as-source), az [SAP HANA](connector-sap-hana.md#sap-hana-as-source), az [SAP Table](connector-sap-table.md#sap-table-as-source)és az [SAP Open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)) adatainak másolása során az adatpartíciós beállítások lehetővé teszik az adatok párhuzamos másolását.

    - Az adatok [HDFS](connector-hdfs.md)való másolása esetén konfigurálja a DistCp használatát.

    - Az [Amazon vöröseltolódásból](connector-amazon-redshift.md)történő adatmásoláskor konfigurálja a következőt: a vöröseltolódás eltávolításának használata.

  - Ellenőrizze, hogy az ADF bármilyen szabályozási hibát jelez-e a forráson, vagy ha az adattár magas kihasználtságú. Ha igen, csökkentse a számítási feladatokat az adattáron, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával, és növelje a sávszélesség-szabályozási korlátot vagy az elérhető erőforrást.

  - A másolási forrás és a fogadó minta keresése: 

    - Ha az adatok másolása a partícióról engedélyezett adattárakból, a [párhuzamos másolatok](copy-activity-performance-features.md)fokozatos finomhangolása érdekében vegye figyelembe, hogy túl sok párhuzamos másolat is megsértheti a teljesítményt.

    - Ellenkező esetben érdemes lehet egy nagyméretű adathalmazt több kisebb adatkészletbe bontani, és lehetővé kell tennie, hogy a másolási feladatok párhuzamosan fussanak az adatok egyes részein. Ezt megteheti a lookup/GetMetadata + ForEach + másolás lehetőséggel. Tekintse át a [fájlok több tárolóból való másolását](solution-template-copy-files-multiple-containers.md), az [Amazon S3-ból történő adatáttelepítést ADLS Gen2](solution-template-migration-s3-azure.md), vagy egy általános példaként egy vezérlőelem-tábla megoldási sablonjaival történő [tömeges másolást](solution-template-bulk-copy-with-control-table.md) .

- **"Átvitel – írás a fogadóba" – hosszú munkavégzés időtartama**:

  - Az összekötő-specifikus betöltés ajánlott eljárás alkalmazása, ha alkalmazható. Ha például az adatok [Azure szinapszis analyticsbe](connector-azure-sql-data-warehouse.md)való másolását használja, használjon a Base vagy a copy utasítást. 

  - Ellenőrizze, hogy a saját üzemeltetésű IR-gép kevés késéssel kapcsolódik-e a fogadó adattárhoz. Ha a fogadója az Azure-ban található, [ezzel az eszközzel](http://www.azurespeed.com/Azure/Latency) ellenőrizhető a saját ÜZEMELTETÉSű IR-gép késése az Azure-régióban, annál kevesebb a jobb.

  - Ellenőrizze, hogy a saját üzemeltetésű IR-gép rendelkezik-e elegendő kimenő sávszélességgel az adatok hatékony átviteléhez és írásához. Ha a fogadó adattár az Azure-ban található, az [eszköz](https://www.azurespeed.com/Azure/UploadLargeFile) használatával ellenőrizze a feltöltési sebességet.

  - Ellenőrizze, hogy a saját üzemeltetésű integrációs modul CPU-és memóriahasználat-trendje Azure Portal-> a saját adatfeldolgozó-> – áttekintés lapot. Ha a CPU-használat magas vagy kevés a rendelkezésre álló memória, vegye fontolóra a vertikális [felskálázást](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

  - Ellenőrizze, hogy az ADF jelent-e szabályozási hibát a fogadón, vagy ha az adattár magas kihasználtságú. Ha igen, csökkentse a számítási feladatokat az adattáron, vagy próbálja meg felvenni a kapcsolatot az adattár rendszergazdájával, és növelje a sávszélesség-szabályozási korlátot vagy az elérhető erőforrást.

  - Gondolja át, hogy fokozatosan hangolja a [párhuzamos másolatokat](copy-activity-performance-features.md), és ne feledje, hogy túl sok párhuzamos másolat is sérült a teljesítmény.


## <a name="connector-and-ir-performance"></a>Összekötő és IR teljesítmény 

Ez a szakasz az adott összekötő-típushoz vagy az integrációs modulhoz tartozó teljesítmény-hibaelhárítási útmutatók megismerését ismerteti.

### <a name="activity-execution-time-varies-using-azure-ir-vs-azure-vnet-ir"></a>A tevékenység végrehajtási ideje a Azure IR vs Azure VNet IR használatával változik

A tevékenység végrehajtási ideje akkor változik, ha az adatkészlet különböző Integration Runtime alapul.

- **Tünetek**: egyszerűen az adatkészlet társított szolgáltatásának legördülő menüjében ugyanazokat a folyamatokat hajtja végre, de a futási idő drasztikusan eltér. Ha az adatkészlet a felügyelt Virtual Network Integration Runtimeon alapul, az alapértelmezett Integration Runtime alapján az átlagosnál több időt vesz igénybe, mint a Futtatás.  

- **OK**: a folyamat futtatási részleteinek ellenőrzésekor láthatja, hogy a lassú folyamat fut a felügyelt VNet (Virtual Network) IR-ben, miközben a normál Azure IR fut. A felügyelt VNet IR-t úgy tervezze meg, hogy a várakozási idő hosszabb ideig tart, mint a Azure IR, mivel nem áll rendelkezésre egy számítási csomópont egy adat-előállítóban, így az egyes másolási tevékenységek elAzure IR indulnak. 



    
### <a name="low-performance-when-loading-data-into-azure-sql-database"></a>Alacsony teljesítmény az adatAzure SQL Databaseba való betöltéskor

- **Tünetek**: az adatok a Azure SQL Databaseba való másolása lassúnak bizonyul.

- **OK**: a probléma kiváltó okát többnyire a Azure SQL Database oldal szűk keresztmetszete váltja ki. A következő okok lehetséges okai:

    - Azure SQL Database szintje nem elég nagy.

    - Azure SQL Database DTU használata a 100%-os közelségbe kerül. Nyomon követheti [a teljesítményt](../azure-sql/database/monitor-tune-overview.md) , és megtekintheti a Azure SQL Databasei csomag frissítését.

    - Az indexek nincsenek megfelelően beállítva. Távolítsa el az összes indexet az adatterhelés előtt, majd hozza létre újra őket a betöltés befejeződése után.

    - A WriteBatchSize nem elég nagy ahhoz, hogy illeszkedjen a séma sorainak méretéhez. Próbálja meg bővíteni a probléma tulajdonságát.

    - A tömeges beszúrta helyett a tárolt eljárás használatban van, ami várhatóan rosszabb teljesítményű. 


### <a name="timeout-or-slow-performance-when-parsing-large-excel-file"></a>Időtúllépés vagy lassú teljesítmény a nagyméretű Excel-fájlok elemzésekor

- **Tünetek**:

    - Amikor létrehoz egy Excel-adatkészletet, és importálja a sémát a kapcsolatból/tárból, előnézeti adatokat, listát vagy frissítési munkalapokat, akkor időtúllépési hiba léphet fel, ha az Excel-fájl mérete nagyméretű.

    - Ha a másolási tevékenység használatával nagyméretű Excel-fájlból (>= 100 MB) másol adatokba egy másik adattárba, előfordulhat, hogy lassú teljesítményt vagy a bácsi problémát tapasztal.

- **OK**: 

    - Az olyan műveletek esetében, mint a séma importálása, az adatok megtekintése és az Excel-adatkészletek listázása, az időkorlát 100 s és statikus. Nagyméretű Excel-fájl esetén előfordulhat, hogy ezek a műveletek nem fejeződik be az időtúllépési értéken belül.

    - Az ADF másolási tevékenység beolvassa a teljes Excel-fájlt a memóriába, majd megkeresi a megadott munkalapot és cellákat az adatolvasáshoz. Ezt a viselkedést a mögöttes SDK ADF használja.

- **Megoldás**: 

    - A séma importálásához létrehozhat egy kisebb mintát, amely az eredeti fájl egy részhalmaza, és a "séma importálása a fájlból" lehetőséget választja a "séma importálása a hálózatról/áruházból" lehetőség helyett.

    - A felsorolási munkalap legördülő listájában kattintson a "szerkesztés" lehetőségre, és adja meg a lap nevét/indexét.

    - A nagyméretű Excel-fájlok (>100 MB) más tárolóba való másolásához használhatja az adatforgalom Excel-forrását, melyben a sport stream olvasása és végrehajtása jobb.
    
## <a name="other-references"></a>Egyéb referenciák

Az alábbiakban a támogatott adattárak némelyikének teljesítmény-figyelési és hangolási referenciái találhatók:

* Azure Blob Storage: a blob Storage-hoz szükséges [méretezhetőségi és teljesítményi célok](../storage/blobs/scalability-targets.md) , valamint a blob Storage-hoz kapcsolódó [teljesítmény-és méretezhetőségi ellenőrzőlista](../storage/blobs/storage-performance-checklist.md).
* Azure Table Storage: a táblázatos tároláshoz [szükséges méretezhetőségi](../storage/tables/scalability-targets.md) és teljesítményi célok, [valamint a Table Storage teljesítményére és méretezhetőségére vonatkozó ellenőrzőlista](../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: nyomon követheti [a teljesítményt](../azure-sql/database/monitor-tune-overview.md) , és ellenőrizheti az adatbázis-tranzakciós egység (DTU) százalékos arányát.
* Azure szinapszis Analytics: az adatraktár-egységekben (DWU) mérhető a funkció. Lásd: [a számítási teljesítmény kezelése az Azure szinapszis Analyticsben (áttekintés)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Azure Cosmos db teljesítmény szintjei](../cosmos-db/performance-levels.md).
* SQL Server: [a teljesítmény figyelése és finomhangolása](/sql/relational-databases/performance/monitor-and-tune-for-performance).
* Helyszíni fájlkiszolgáló: a [fájlkiszolgálók teljesítményének finomhangolása](/previous-versions//dn567661(v=vs.85)).

## <a name="next-steps"></a>Következő lépések
Lásd a másolási tevékenység egyéb cikkeit:

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Útmutató a másolási tevékenységek teljesítményéhez és skálázhatóságához](copy-activity-performance.md)
- [Másolási tevékenység teljesítményének optimalizálási funkciói](copy-activity-performance-features.md)
- [Az adatok áttelepíthetők a Azure Data Factory használatával az Azure-ba vagy az adattárházból](data-migration-guidance-overview.md)
- [Adatok migrálása az Amazon S3-ból az Azure Storage-ba](data-migration-guidance-s3-azure-storage.md)
