---
title: Valós idejű és adatfolyam-feldolgozási megoldás kiválasztása az Azure-ban
description: Ismerje meg, hogyan választhatja ki a megfelelő valós idejű elemzési és adatfolyam-feldolgozási technológiát az Azure-beli alkalmazás létrehozásához.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 4c10a91971357001723adcb783253c9867cf6d87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019057"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Valós idejű elemzési és adatfolyam-feldolgozási technológia kiválasztása az Azure-ban

Az Azure-ban számos szolgáltatás érhető el a valós idejű elemzésekhez és az adatfolyam-feldolgozáshoz. Ez a cikk azokat az információkat tartalmazza, amelyeket el kell döntenie, hogy melyik technológia a legmegfelelőbb az alkalmazáshoz.

## <a name="when-to-use-azure-stream-analytics"></a>Mikor kell használni a Azure Stream Analytics

Azure Stream Analytics a stream Analytics ajánlott szolgáltatása az Azure-ban. Számos olyan forgatókönyvhöz készült, amely többek között a következőkre korlátozódik:

* Adatvizualizációs irányítópultok
* Valós idejű [riasztások](stream-analytics-set-up-alerts.md) időbeli és térbeli mintákból vagy rendellenességekből
* Kinyerési, átalakítási és betöltési (ETL) feladatok
* [Esemény-beszerzési minta](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Az Azure-ban az Ön által már ismert SQL-nyelv használatával a leggyorsabb módszer az, ha egy Azure Stream Analytics feladatot ad hozzá az alkalmazáshoz. Azure Stream Analytics a feladatok szolgáltatása, így nem kell időt kell fordítania a fürtök kezelésére, és nem kell aggódnia a 99,9%-os SLA-val a feladatok szintjén. A számlázás a feladatok szintjén is megtörténik, így az indítási költségek alacsonyak (egy folyamatos átviteli egység), de méretezhető (akár 192 folyamatos átviteli egység). Sokkal költséghatékonyabb, hogy néhány Stream Analytics feladatot futtasson, mint a fürt futtatásához és karbantartásához.

A Azure Stream Analytics gazdag felülettel rendelkezik. A további beállítások nélkül azonnal kihasználhatja a következő funkciókat:

* Beépített időbeli operátorok, például [ablakos összesítések](stream-analytics-window-functions.md), időbeli illesztések és időbeli elemzési függvények.
* Natív Azure [bemeneti](stream-analytics-add-inputs.md) és [kimeneti](stream-analytics-define-outputs.md) adapterek
* A lassú módosításokra (más néven keresési táblákra [) vonatkozó támogatás](stream-analytics-use-reference-data.md) , beleértve a geokerítések térinformatikai hivatkozásával való csatlakozást is.
* Integrált megoldások, például [anomáliák észlelése](stream-analytics-machine-learning-anomaly-detection.md)
* Egyszerre több idő Windows ugyanabban a lekérdezésben
* Több időbeli operátor tetszőleges sorozatban való összeállításának lehetősége.
* A Event Hubsra érkező bemenettől kezdve a végpontok közötti késéstől a Event Hubsbe való beérkezésig, beleértve a hálózati késleltetést és a Event Hubs, a tartós nagy átviteli sebesség 100

## <a name="when-to-use-other-technologies"></a>Mikor kell más technológiákat használni

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Nem JavaScript vagy C nyelven szeretne UDF, uda és egyéni deszerializált írni. #

A Azure Stream Analytics támogatja a felhasználó által definiált függvényeket (UDF) vagy a felhasználó által definiált összesítéseket (UDA) a JavaScriptben a felhőalapú feladatokhoz és a C#-hoz IoT Edge feladatokhoz. A C# felhasználó által definiált deszerializáló is támogatott. Ha egy deszerializáló, egy UDF vagy egy UDA más nyelvben, például a Java vagy a Python alkalmazásban szeretne megvalósítani, a Spark strukturált streaming használható. Az Event Hubs **EventProcessorHost** is futtathatja a saját virtuális gépeken, így tetszőleges adatfolyam-feldolgozást végezhet.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>A megoldás több Felhőbeli vagy helyszíni környezetben van

Azure Stream Analytics a Microsoft saját technológiája, és csak az Azure-ban érhető el. Ha a megoldás a felhőben vagy a helyszínen is hordozható, olyan nyílt forráskódú technológiákat kell figyelembe venni, mint a Spark strukturált streaming vagy a Storm.

## <a name="next-steps"></a>Következő lépések

* [Stream Analytics-feladatok létrehozása a Azure Portal használatával](stream-analytics-quick-create-portal.md)
* [Stream Analytics-feladat létrehozása az Azure PowerShell használatával](stream-analytics-quick-create-powershell.md)
* [Stream Analytics-feladatok létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Stream Analytics-feladatok létrehozása a Visual Studio Code használatával](quick-create-visual-studio-code.md)
