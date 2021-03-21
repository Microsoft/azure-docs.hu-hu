---
title: Útmutató a másolási tevékenységek teljesítményéhez és skálázhatóságához
description: Ismerje meg azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés teljesítményére Azure Data Factory a másolási tevékenység használatakor.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/15/2020
ms.openlocfilehash: cba248d3f254c9bb97c66ff7a3d39275b4b912c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102616077"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Útmutató a másolási tevékenységek teljesítményéhez és skálázhatóságához

> [!div class="op_single_selector" title1="Válassza ki a használt Azure Data Factory verzióját:"]
> * [1-es verzió](v1/data-factory-copy-activity-performance.md)
> * [Aktuális verzió](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Előfordulhat, hogy nagy léptékű adatáttelepítést szeretne végrehajtani a (EDW) adat-Lake vagy Enterprise adattárházból az Azure-ba. Más időpontokban, amikor nagy mennyiségű adatot szeretne bevenni különböző forrásokból az Azure-ba, big data elemzésekhez. Az optimális teljesítmény és méretezhetőség érdekében minden esetben kritikus fontosságú.

Azure Data Factory (ADF) az adatgyűjtési mechanizmust biztosít. Az ADF a következő előnyökkel jár:

* Nagy mennyiségű adattal kezeli
* Nagy teljesítményű
* Költséghatékony

Ezek az előnyök kiválóan alkalmasak az olyan adatmérnökök számára, akik nagy teljesítményű, méretezhető adatfeldolgozási folyamatokat kívánnak készíteni.

A cikk elolvasása után a következő kérdésekre tud válaszolni:

* Milyen szintű teljesítményt és méretezhetőséget érhet el az ADF másolási tevékenység az adatáttelepítési és adatfeldolgozási forgatókönyvek esetében?
* Milyen lépéseket kell tennie az ADF másolási tevékenység teljesítményének finomhangolásához?
* Milyen ADF-optimalizálási gombokat használhatok egy adott másolási tevékenység teljesítményének optimalizálására?
* Az ADF-en kívül más tényezők is figyelembe veszik a másolási teljesítmény optimalizálásakor?

> [!NOTE]
> Ha nem ismeri a másolási tevékenységet általánosságban, tekintse meg a [másolási tevékenység áttekintését](copy-activity-overview.md) a cikk elolvasása előtt.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>A teljesítmény és a méretezhetőség elérhetővé másolása ADF használatával

Az ADF olyan kiszolgáló nélküli architektúrát kínál, amely különböző szinteken teszi lehetővé a párhuzamosságot.

Ez az architektúra lehetővé teszi olyan folyamatok fejlesztését, amelyek maximalizálják az adatátviteli sebességet a környezet számára. Ezek a folyamatok teljes mértékben a következő erőforrásokat használják:

* A forrás és a cél adattárolói közötti hálózati sávszélesség
* Forrás vagy cél adattároló bemeneti/kimeneti műveletei másodpercenként (IOPS) és sávszélesség

Ez a teljes kihasználtság azt jelenti, hogy a következő erőforrásokhoz elérhető minimális átviteli sebesség mérésével megbecsülheti a teljes átviteli sebességet:

* Forrás adattár
* Céladattár
* A forrás és a cél adattárolói közötti hálózati sávszélesség

Az alábbi táblázat kiszámítja a másolás időtartamát. Az időtartam az adatméret és a hálózat/adattár sávszélesség-korlátja alapján történik.

&nbsp;

| Adatméret/ <br/> sávszélesség | 50 Mbit/s    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 GB/s   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 perc    | 1,4 perc   | 0,3 perc   | 0,1 perc  | 0,03 perc | 0,01 perc | 0,0 perc   |
| **10 GB**                   | 27,3 perc   | 13,7 perc  | 2,7 perc   | 1,3 perc  | 0,3 perc  | 0,1 perc  | 0,03 perc  |
| **100 GB**                  | 4,6 óra    | 2,3 óra   | 0,5 óra   | 0,2 óra  | 0,05 óra | 0,02 óra | 0,0 óra   |
| **1 TB**                    | 46,6 óra   | 23,3 óra  | 4,7 óra   | 2,3 óra  | 0,5 óra  | 0,2 óra  | 0,05 óra  |
| **10 TB**                   | 19,4 nap  | 9,7 nap  | 1,9 nap  | 0,9 nap | 0,2 nap | 0,1 nap | 0,02 nap |
| **100 TB**                  | 194,2 nap | 97,1 nap | 19,4 nap | 9,7 nap | 1,9 nap | 1 nap    | 0,2 nap  |
| **1 PB**                    | 64,7 mo    | 32,4 Mo   | 6,5 Mo    | 3,2 Mo   | 0,6 Mo   | 0,3 Mo   | 0,06 Mo   |
| **10 PB**                   | 647,3 Mo   | 323,6 Mo  | 64,7 mo   | 31,6 Mo  | 6,5 Mo   | 3,2 Mo   | 0,6 Mo    |
| | |  | | |  | | |

Az ADF-másolás különböző szinteken méretezhető:

![Az ADF másolási méretezése](media/copy-activity-performance/adf-copy-scalability.png)

* Az ADF-vezérlési folyamat egyszerre több másolási tevékenységet is elindíthat, például az [egyes hurkok esetében](control-flow-for-each-activity.md).

* Egyetlen másolási tevékenység kihasználhatja a méretezhető számítási erőforrások előnyeit.
  * Az Azure Integration Runtime (IR) használata esetén [akár 256 adatintegrációs egységet (DIUs)](#data-integration-units) is megadhat az egyes másolási tevékenységekhez, kiszolgáló nélküli módon.
  * Saját üzemeltetésű integrációs modul használata esetén a következő módszerek egyikét hajthatja végre:
    * A gép manuális skálázása.
    * Felskálázás több gépre ([legfeljebb 4 csomópontra](create-self-hosted-integration-runtime.md#high-availability-and-scalability)), és egyetlen másolási tevékenység particionálja az összes csomóponton beállított fájlját.

* Egy másolási tevékenység [párhuzamosan](#parallel-copy)több szál használatával olvas be és ír az adattárba.

## <a name="performance-tuning-steps"></a>Teljesítmény-finomhangolási lépések

A következő lépésekkel hangolhatja be Azure Data Factory szolgáltatás teljesítményét a másolási tevékenységgel:

1. **Válasszon ki egy tesztelési adatkészletet, és hozzon létre egy alaptervet.**

    A fejlesztés során tesztelje a folyamatot a másolási tevékenységgel egy reprezentatív adatminta alapján. A kiválasztott adatkészlet a jellemző adatmintákat szemlélteti a következő attribútumok mentén:

    * Mappa szerkezete
    * Fájl mintája
    * Adatséma

    A másolási teljesítmény kiértékeléséhez az adathalmaznak elég nagynak kell lennie. A másolási tevékenység befejezéséhez a megfelelő méret legalább 10 percet vesz igénybe. A [másolási tevékenység figyelését](copy-activity-monitoring.md)követő végrehajtási adatok és teljesítmény-jellemzők gyűjtése.

2. **Egy másolási tevékenység teljesítményének maximalizálása**:

    Javasoljuk, hogy először egyetlen másolási tevékenység használatával maximalizálja a teljesítményt.

    * **Ha a másolási tevékenységet egy _Azure_ Integration Runtime hajtja végre:**

        Az [adatintegrációs egységek (DIU)](#data-integration-units) és a [párhuzamos másolási](#parallel-copy) beállítások alapértelmezett értékeinek megkezdése.

    * **Ha a másolási tevékenységet egy _saját_ üzemeltetésű integrációs modulon hajtja végre:**

        Javasoljuk, hogy használjon dedikált gépet az IR üzemeltetéséhez. A gépnek különállónak kell lennie az adattárt futtató kiszolgálótól. A [párhuzamos másolási](#parallel-copy) beállítás alapértelmezett értékeivel kezdődik, és egyetlen csomópontot használ a saját üzemeltetésű IR-hez.

    Teljesítményteszt futtatásának elvégzése. Jegyezze fel az elért teljesítményt. Adja meg a ténylegesen használt értékeket, például a DIUs és a párhuzamos másolást. Tekintse át a [másolási tevékenység figyelését](copy-activity-monitoring.md) a futtatási eredmények és a használt teljesítmény-beállítások gyűjtésének módjáról. Útmutató a [másolási tevékenység teljesítményének hibaelhárításához](copy-activity-performance-troubleshooting.md) a szűk keresztmetszetek azonosítása és megoldása érdekében.

    A hibaelhárítási és finomhangolási útmutatást követve további teljesítménytesztek futtatását is elvégezheti. Ha az Egypéldányos másolási tevékenység nem tud jobb teljesítményt elérni, gondolja át, hogy az összesített átviteli sebesség maximalizálható-e több példány párhuzamos futtatásával. Ezt a lehetőséget a következő számozott felsorolásban tárgyaljuk.

3. **Az összesített átviteli sebesség maximalizálása több példány párhuzamos futtatásával:**

    Mostantól maximalizálta egyetlen másolási tevékenység teljesítményét. Ha még nem érte el a környezet felső korlátját, több másolási tevékenységet is futtathat párhuzamosan. Párhuzamosan is futtathatja az ADF-vezérlési folyamat szerkezetét. Az egyik ilyen szerkezet az [egyes hurkok esetében](control-flow-for-each-activity.md). További információkért tekintse meg a következő cikkeket a megoldási sablonokról:

    * [Fájlok másolása több tárolóból](solution-template-copy-files-multiple-containers.md)
    * [Adatok migrálása az Amazon S3-ból ADLS Gen2ba](solution-template-migration-s3-azure.md)
    * [Tömeges másolás vezérlőelem-táblázattal](solution-template-bulk-copy-with-control-table.md)

4. **Bontsa ki a konfigurációt a teljes adatkészletre.**

    Ha elégedett a végrehajtás eredményeivel és teljesítményével, kiterjesztheti a definíciót és a folyamatot a teljes adatkészlet lefedéséhez.

## <a name="troubleshoot-copy-activity-performance"></a>A másolási tevékenység teljesítményével kapcsolatos hibák

Kövesse a [teljesítmény-hangolás lépéseit](#performance-tuning-steps) a teljesítmény-ellenőrzés megtervezéséhez és a forgatókönyvhöz való végrehajtásához. És megtudhatja, hogyan lehet elhárítani az egyes másolási tevékenységek futtatásának teljesítményét a Azure Data Factory a [másolási tevékenység teljesítményének hibakeresésével](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Teljesítmény-optimalizálási funkciók másolása

Azure Data Factory a következő teljesítmény-optimalizálási funkciókat biztosítja:

* [Adatintegrációs egységek](#data-integration-units)
* [Saját üzemeltetésű Integration Runtime skálázhatósága](#self-hosted-integration-runtime-scalability)
* [Párhuzamos másolás](#parallel-copy)
* [Előkészített másolás](#staged-copy)

### <a name="data-integration-units"></a>Adatintegrációs egységek

Az adatintegrációs egységek (DIU-EK) egy mérték, amely a Azure Data Factory egyetlen egységének erejét jelöli. A Power a CPU, a memória és a hálózati erőforrások kiosztásának kombinációja. A DIU csak az [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)verzióra vonatkozik. A DIU nem vonatkozik a [saját üzemeltetésű integrációs](concepts-integration-runtime.md#self-hosted-integration-runtime)modulra. [További információt itt talál](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Saját üzemeltetésű Integration Runtime skálázhatósága

Előfordulhat, hogy növekvő párhuzamos számítási feladatot szeretne üzemeltetni. Vagy lehet, hogy nagyobb teljesítményt szeretne elérni a munkaterhelési szinten. A következő megközelítések segítségével növelheti a feldolgozás skálázását:

* A saját üzemeltetésű integrációs modul vertikális _felskálázásával növelheti a_ csomóponton futtatható [egyidejű feladatok](create-self-hosted-integration-runtime.md#scale-up) számát.  
A vertikális felskálázás csak akkor működik, ha a csomópont processzora és memóriája kevesebb, mint teljes mértékben kihasználva.
* _A saját_ üzemeltetésű integrációs modult további csomópontok (gépek) hozzáadásával is kibővítheti.

További információkért lásd:

* [Másolási tevékenység teljesítmény-optimalizálási funkciói: saját üzemeltetésű integrációs modul skálázhatósága](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)
* [Saját üzemeltetésű integrációs modul létrehozása és konfigurálása: méretezési szempontok](create-self-hosted-integration-runtime.md#scale-considerations)

### <a name="parallel-copy"></a>Párhuzamos másolás

Megadhatja a `parallelCopies` tulajdonságot, hogy jelezze a másolási tevékenység által használni kívánt párhuzamosságot. Ezt a tulajdonságot úgy gondolja, mint a másolási tevékenységben lévő szálak maximális száma. A szálak párhuzamosan működnek. A szálak vagy a forrásból beolvasott adatok, vagy a fogadó adattárakba való írás. [További információ](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Előkészített másolás

Az adatmásolási művelet _közvetlenül_ a fogadó adattárba küldheti az adattárolási műveletet. Azt is megteheti, hogy a blob Storage-t _átmeneti átmeneti_ tárolóként használja. [További információ](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Következő lépések

Lásd a másolási tevékenység egyéb cikkeit:

* [Másolási tevékenység – áttekintés](copy-activity-overview.md)
* [A másolási tevékenység teljesítményével kapcsolatos hibák](copy-activity-performance-troubleshooting.md)
* [Másolási tevékenység teljesítményének optimalizálási funkciói](copy-activity-performance-features.md)
* [Az adatok áttelepíthetők a Azure Data Factory használatával az Azure-ba vagy az adattárházból](data-migration-guidance-overview.md)
* [Adatok migrálása az Amazon S3-ból az Azure Storage-ba](data-migration-guidance-s3-azure-storage.md)
