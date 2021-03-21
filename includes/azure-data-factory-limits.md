---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: chez-charlie
ms.service: data-factory
ms.topic: include
ms.date: 11/16/2020
ms.author: chez
ms.custom: include file
ms.openlocfilehash: 10aa9b06af439fe701c53ef736ec691167560f95
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102109049"
---
Azure Data Factory egy több-bérlős szolgáltatás, amely a következő alapértelmezett korlátokkal rendelkezik, hogy az ügyfél-előfizetések védve legyenek egymás munkaterhelésével szemben. Ha a korlátot az előfizetése maximumán szeretné növelni, forduljon az ügyfélszolgálathoz.

### <a name="version-2"></a>2-es verzió

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| -------- | ------------- | ------------- |
| Azure-előfizetéshez tartozó adatfeldolgozók | 800 | 800 |
| Egy adat-előállítón belüli entitások (például folyamatok, adathalmazok, eseményindítók, társított szolgáltatások, magánhálózati végpontok és integrációs modulok) teljes száma | 5000 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Azure-SSIS integrációs modulokhoz tartozó összes CPU-mag egy előfizetésben | 256 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| A gyárban található összes folyamat között megosztott, egyidejű adatfeldolgozási folyamat | 10,000  | 10,000 |
| Egyidejű külső tevékenység-előfizetések száma [Azure Integration Runtime régiónként](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>A külső tevékenységek kezelése az integrációs modulon történik, de a társított szolgáltatásokon, például a Databricks, a tárolt eljáráson, a Hdinsight, a weben és másokon végezhető el. Ez a korlát nem vonatkozik a saját üzemeltetésű IR-re.</small> | 3,000 | 3,000 |
| Egyidejű folyamat tevékenység-előfizetések száma [Azure Integration Runtime régiónként](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location) <br><small>A folyamat tevékenységei az integrációs modulon futnak, beleértve a keresést, a GetMetadata és a törlést is. Ez a korlát nem vonatkozik a saját üzemeltetésű IR-re.</small> | 1,000 | 1,000                                                        |
| Előfizetések egyidejű létrehozási műveletei [Azure Integration Runtime régiónként](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>Többek között a kapcsolatok tesztelése, a mappák listájának és a táblázatok listájának, valamint az előzetes verziónak a megtekintése Ez a korlát nem vonatkozik a saját üzemeltetésű IR-re.</small> | 200 | 200                                                          |
| Egyidejű adatintegrációs egységek<sup>1</sup> előfizetések száma [Azure Integration Runtime régiónként](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| 1. régió<sup>2</sup>. csoport: 6 000<br>2. régió<sup>2</sup>. csoport: 3 000<br>3. régió<sup>2</sup>. csoport: 1 500 | 1. régió<sup>2</sup>. csoport: 6 000<br/>2. régió<sup>2</sup>. csoport: 3 000<br/>3. régió<sup>2</sup>. csoport: 1 500 |
| Tevékenységek maximális száma folyamaton keresztül, amely belső tevékenységeket tartalmaz a tárolók számára | 40 | 40 |
| Egy saját üzemeltetésű integrációs modulon keresztül létrehozható összekapcsolt integrációs modulok maximális száma | 100 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximális paraméterek/folyamat | 50 | 50 |
| ForEach-elemek | 100.000 | 100.000 |
| ForEach-párhuzamosság | 20 | 50 |
| Várólistán várakozó futtatások maximális száma folyamatban | 100 | 100 |
| Karakter/kifejezés | 8,192 | 8,192 |
| Minimális késleltetésű ablak-trigger időköze | 15 perc | 15 perc |
| A folyamat által futtatott tevékenységek maximális időtúllépése | 7 nap | 7 nap |
| Bájtok száma objektumként a (<sup>3</sup> .) folyamat objektumaihoz | 200 KB | 200 KB |
| Bájt/objektum az adatkészlet és a társított szolgáltatási objektumok esetében<sup>3</sup> | 100 KB | 2 000 KB |
| Bájt/adattartalom minden egyes tevékenységhez<sup>4</sup> . Futtatás | 896 KB | 896 KB |
| Adatintegrációs egységek<sup>1</sup> /másolási tevékenységének futtatása | 256 | 256 |
| API-hívások írása | 1200/h | 1200/h<br/><br/> Ezt a korlátot Azure Resource Manager, nem Azure Data Factory. |
| API-hívások olvasása | 12500/h | 12500/h<br/><br/> Ezt a korlátot Azure Resource Manager, nem Azure Data Factory. |
| Lekérdezések figyelése percenként | 1,000 | 1,000 |
| Az adatfolyam hibakeresési munkamenetének maximális ideje | 8 óra | 8 óra |
| Adatfolyamatok egyidejű száma integrációs futtatókörnyezetben | 50 | [Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Adatfolyam-hibakeresési munkamenetek egyidejű száma felhasználónként | 3 | 3 |
| Adatfolyam Azure IR ÉLETTARTAMának korlátja | 4 óra |  4 óra |

<sup>1</sup> az adatintegrációs egység (DIU) felhőből felhőbe másolási műveletben használatos, további információ az [adatintegrációs egységekről (2. verzió)](../articles/data-factory/copy-activity-performance.md#data-integration-units). További információ a számlázásról: [Azure Data Factory díjszabása](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) [globálisan elérhető](https://azure.microsoft.com/global-infrastructure/services/) az adatmegfelelőség, a hatékonyság és a csökkentett hálózati kimenő költségek biztosításához. 

| Régió csoport | Régiók |
| -------- | ------ |
| 1. régió | USA középső régiója, USA keleti régiója, USA 2. keleti régiója, Észak-Európa, Nyugat-Európa, USA 2. nyugati régiója |
| 2. régió | Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-India, Kelet-Japán, USA északi középső régiója, USA déli középső régiója, Délkelet-Ázsia, USA nyugati középső régiója |
| 3. régió | Más régiók |

<sup>3</sup> a folyamat, az adathalmaz és a társított szolgáltatási objektumok a számítási feladatok logikai csoportosítását jelölik. Ezeknek az objektumoknak a korlátai nem kapcsolódnak a Azure Data Factory használatával áthelyezhető és feldolgozható adatmennyiséghez. Data Factory úgy van kialakítva, hogy az petabájt kezeljen.

<sup>4</sup> az egyes tevékenységekhez tartozó adattartalom magában foglalja a tevékenység konfigurációját, a kapcsolódó adatkészleteket és a társított szolgáltatás (oka) t, ha van ilyen, és a Rendszertulajdonságok egy kis részét a tevékenység típusa szerint generálták. A hasznos adatok méretének korlátozása nem vonatkozik a Azure Data Factorysal áthelyezhető és feldolgozható adatmennyiségre. Ha ezt a korlátot találta, megismerheti a [tüneteket és az ajánlásokat](../articles/data-factory/data-factory-troubleshoot-guide.md#payload-is-too-large) .

### <a name="version-1"></a>1-es verzió

| **Erőforrás** | **Alapértelmezett korlát** | **Maximális korlát** |
| --- | --- | --- |
| Adatfeldolgozón belüli folyamatok |2500 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Adatkészletek az adat-előállítón belül |5000 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Egyidejű szeletek egy adatkészletben |10 |10 |
| Bájt/objektum a folyamat objektumaihoz<sup>1</sup> |200 KB |200 KB |
| Adathalmaz és társított szolgáltatási objektumok bájtjainak száma (bájt/objektum<sup>) 1</sup> |100 KB |2 000 KB |
| Azure HDInsight igény szerinti fürt magok a<sup>2</sup> . előfizetésen belül |60 |[Forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Felhőbeli adatáthelyezési egység/másolási tevékenység<sup>3</sup> . futtatása |32 |32 |
| Újrapróbálkozások száma a folyamathoz tartozó tevékenységek futtatásához |1,000 |MaxInt (32 bit) |

<sup>1</sup> a folyamat, az adathalmaz és a társított szolgáltatás objektumok a számítási feladatok logikai csoportosítását jelölik. Ezeknek az objektumoknak a korlátai nem kapcsolódnak a Azure Data Factory használatával áthelyezhető és feldolgozható adatmennyiséghez. Data Factory úgy van kialakítva, hogy az petabájt kezeljen.

<sup>2</sup> az igény szerinti HDInsight magok az adatelőállítót tartalmazó előfizetésből vannak kiosztva. Ennek eredményeképpen az előző korlát az igény szerinti HDInsight magok Data Factory által kényszerített alapszintű korlátja. Ez eltér az Azure-előfizetéséhez társított alapszinttől.

<sup>3</sup> az 1. verzióhoz tartozó Cloud adatáthelyezési egység (DMU) felhőből felhőbe másolási műveletben használatos, további információ a [Felhőbeli adatáthelyezési egységekről (1. verzió)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). További információ a számlázásról: [Azure Data Factory díjszabása](https://azure.microsoft.com/pricing/details/data-factory/).

| **Erőforrás** | **Alapértelmezett alsó korlát** | **Minimális korlát** |
| --- | --- | --- |
| Ütemezési időköz |15 perc |15 perc |
| Újrapróbálkozási kísérletek közötti intervallum |1 másodperc |1 másodperc |
| Újrapróbálkozás időtúllépési értéke |1 másodperc |1 másodperc |

#### <a name="web-service-call-limits"></a>Webszolgáltatás-hívások korlátai
A Azure Resource Manager API-hívásokra vonatkozó korlátozásokkal rendelkezik. Az API-hívásokat a [Azure Resource Manager API korlátain](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)belül is elvégezheti.
