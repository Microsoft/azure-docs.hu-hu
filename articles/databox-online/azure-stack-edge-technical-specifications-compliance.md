---
title: A Microsoft Azure Stack Edge Pro műszaki specifikációi és megfelelőségi adatai | Microsoft Docs
description: Ismerje meg az Azure Stack Edge Pro műszaki specifikációit és megfelelőségét
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: 8b36bb34f4c9081d807998cb8287797443625a6b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460293"
---
# <a name="azure-stack-edge-pro-technical-specifications"></a>Azure Stack Edge Pro technikai specifikációi

Az Microsoft Azure Stack Edge Pro-eszköz hardveres összetevői megfelelnek a jelen cikkben ismertetett technikai előírásoknak és szabályozási előírásoknak. A műszaki specifikációk a tápegységek (PSUs), a tárolókapacitás, a bekerítések és a környezeti szabványok leírását írják le.

## <a name="compute-memory-specifications"></a>Számítási, memória-specifikációk

Az Azure Stack Edge Pro-eszköz a következő specifikációkkal rendelkezik a számításhoz és a memóriához:

| Specifikáció           | Érték                  |
|-------------------------|----------------------------|
| CPU    | 2 X 10 mag CPU Intel Xeon Silver 4114 2.2 G                    |
| Memória              | 128 GB RAM (8x 16GB RDIMM)                 |

## <a name="fpga-specifications"></a>FPGA-specifikációk

A rendszer minden olyan Azure Stack Edge Pro-eszközön tartalmaz egy programozható Gate tömböt (FPGA), amely Machine Learning (ML) forgatókönyveket tesz lehetővé.

| Specifikáció           | Érték                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> A rendelkezésre álló Deep neurális hálózati (DNN) modellek ugyanazok, mint a [Cloud FPGA-példányok által támogatottak](../machine-learning/how-to-deploy-fpga-web-service.md#fpga-support-in-azure).|

## <a name="power-supply-unit-specifications"></a>Tápegység-egységek specifikációi

Az Azure Stack Edge Pro-eszköz két 100-240 V-os tápegységgel (PSUs) rendelkezik, nagy teljesítményű ventilátorokkal. A két PSUs redundáns energiaellátási konfigurációt biztosít. Ha egy PSU meghibásodik, az eszköz továbbra is általában a másik PSU-gépen működik, amíg le nem cseréli a hibás modult. A következő táblázat a PSUs műszaki specifikációit sorolja fel.

| Specifikáció           | 750 W PSU                  |
|-------------------------|----------------------------|
| Maximális kimeneti teljesítmény    | 750 W                     |
| Gyakoriság               | 50/60 Hz                   |
| Feszültség-tartomány kiválasztása | Automatikus hatókör: 100-240 V AC |
| Gyors csatlakoztatás           | Yes                        |

### <a name="azure-stack-edge-pro-power-cord-specifications-by-region"></a>Azure Stack Edge Pro tápkábel-specifikációk régiónként

Az Azure Stack Edge Pro-eszköznek szüksége van egy olyan tápkábelre, amely az Azure-régiótól függően változhat.
Az összes támogatott tápkábel műszaki specifikációjának megtekintéséhez lásd: [Azure stack Edge Pro tápkábel-specifikációk régiónként](azure-stack-edge-technical-specifications-power-cords-regional.md).

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Azure Stack Edge Pro device.-->

## <a name="network-interface-specifications"></a>Hálózati adapterek specifikációi

Azure Stack Edge Pro-eszközének 6 hálózati adaptere van, a PORT1-PORT6.

| Specifikáció           | Description                 |
|-------------------------|----------------------------|
|  Hálózati adapterek    | 2 db 1 GbE sávszélességű illesztő – 1 a kezeléshez, a felhasználó által nem konfigurálható, a kezdeti beállításhoz való. A másik felület a felhasználó által konfigurálható, adatátvitelre használható, és alapértelmezés szerint a DHCP. <br>2 db 25 GbE sávszélességű illesztő – 10 GbE sávszélességű illesztőként is üzemelnek. Az adatillesztők a felhasználó által megadott konfigurációja DHCP (alapértelmezett) vagy statikus lehet. <br> 2 db 25 GbE sávszélességű illesztő – Az adatillesztők a felhasználó által megadott konfigurációja DHCP (alapértelmezett) vagy statikus lehet.                  |

A használt hálózati adapterek a következők: 

| Specifikáció           | Description                 |
|-------------------------|----------------------------|
|Hálózati lánya kártya (rNDC) |QLogic FastLinQ 41264 Dual port 25GbE SFP +, Dual port 1 GbE, rNDC|
|PCI hálózati adapter |QLogic FastLinQ 41262 Zwei-portok 25Gbit/s SFP28 adapter|

Tekintse meg a kompatibilis Gigabit Interface Converter (GBIC) hardveres kompatibilitási listáját az Intel QLogic. A Gigabit Interface Converter (GBIC) nem szerepel az Azure Stack Edge kézbesítésében. 

## <a name="storage-specifications"></a>Tárolási specifikációk

Az Azure Stack Edge Pro-eszközök 9 X 2,5 "NVMe SSD-k rendelkeznek, amelyek mindegyike 1,6 TB kapacitású. Ezek az SSD-k, 1 egy operációsrendszer-lemez, a másik 8 pedig adatlemez. Az eszköz teljes felhasználható kapacitása körülbelül 12,5 TB. Az alábbi táblázat az eszköz tárolókapacitásának részleteit tartalmazza.

|     Specifikáció                          |     Érték             |
|--------------------------------------------|-----------------------|
|    SSD-meghajtók száma     |    8                  |
|    Egyetlen SSD-kapacitás                     |    1,6 TB             |
|    Teljes kapacitás                          |    12,8 TB            |
|    Teljes felhasználható kapacitás *                  |    ~ 12,5 TB            |

**Bizonyos területek belső használatra vannak fenntartva.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>A bekerítés méretei és súlyozási jellemzői

A következő táblázatok a méretek és a súlyozás különböző területekre vonatkozó specifikációit sorolja fel.

### <a name="enclosure-dimensions"></a>Bekerítés méretei

A következő táblázat a ház dimenzióit mutatja milliméterben és hüvelykben.

|     Ház     |     Milliméter     |     Hüvelyk     |
|-------------------|---------------------|----------------|
|    Magasság         |    44,45            |    1,75 "          |
|    Szélesség          |    434,1           |    17,09 "          |
|    Hossz          |    740,4           |    29,15 "          |

A következő táblázat a szállítási csomag dimenzióit mutatja be milliméterben és hüvelykben.

|     Csomag     |     Milliméter     |     Hüvelyk     |
|-------------------|---------------------|----------------|
|    Magasság         |    311,2            |    12,25 "          |
|    Szélesség          |    642,8          |    25,31 "          |
|    Hossz          |   1 051,1          |    41,38 "          |

### <a name="enclosure-weight"></a>Ház súlya

Az alkalmazáscsomag súlya 61 lbs. és két személyt igényel a kezeléséhez. Az eszköz súlya a ház konfigurációjától függ.

|     Ház                                 |     Tömeg          |
|-----------------------------------------------|---------------------|
|    Teljes súly, beleértve a csomagolást       |    61 lbs.          |
|    Az eszköz súlya                       |    35 lbs.          |

## <a name="enclosure-environment-specifications"></a>Bekerítési környezet specifikációi

Ez a szakasz felsorolja a bekerítési környezettel kapcsolatos specifikációkat, például a hőmérsékletet, a páratartalmat és a magasságot.

### <a name="temperature-and-humidity"></a>Hőmérséklet és páratartalom

|     Ház         |     Környezeti hőmérséklet tartománya     |     Környezeti relatív páratartalom     |     Maximális harmatpont     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Működik        |    10 °C – 35 °C (50 °F-86 °F)         |    10%-80% nem kondenzációs.         |    29 °C (84 °F)            |
|    Nem működő    |    -40 °C-tól 65 °C-ig (-40 °F-149 °F)     |    5%-95%-os nem kondenzációs.          |    33 °C (91 °F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Légáram, magasság, sokk, vibráció, orientáció, biztonság és EMC

|     Ház                           |     Üzemeltetési specifikációk                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Légáramlás                              |    A rendszer légáram elölről hátrafelé. A rendszert alacsony terhelésű, hátsó kimeneti telepítéssel kell működtetni. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maximális magasság, működési        |    3048 méter (10 000 láb), amely az üzemeltetési hőmérséklet által meghatározott maximális működési hőmérsékletet határozza meg, az [üzemi hőmérséklet-minősítési specifikációk](#operating-temperature-de-rating-specifications)alapján.                                                                                |
|    Maximális magasság, nem működő    |    12 000 méter (39 370 láb)                                                                                                                                                                                         |
|    Sokk, működési                   |    6 G 11 ezredmásodpercnél 6 percnél                                                                                                                                                                         |
|    Sokk, nem működő               |    71 G 2 ezredmásodpercnél 6 tájolással                                                                                                                                                                           |
|    Vibráció, működési               |    0,26 G<sub>RMS</sub> 5 hz – 350 Hz véletlenszerű                                                                                                                                                                                     |
|    Vibráció, nem működő           |    1,88 G<sub>RMS</sub> 10 hz – 500 Hz 15 percre (mind a hat oldal tesztelte.)                                                                                                                                                  |
|    Tájolás és csatlakoztatás             |    19 "rack csatlakoztatása                                                                                                                                                                                        |
|    Biztonság és jóváhagyások                 |    EN 60950-1:2006 + a1:2010 + a2:2013 + A11:2009 + A12:2011/IEC 60950-1:2005 ED2 + a1:2009 + a2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (D osztály)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energia             |    A bizottsági rendelet (EU) nem. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>Üzemeltetési hőmérséklet – de-minősítési specifikációk

|     Működési hőmérséklet – de-minősítés     |     Környezeti hőmérséklet tartománya                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Akár 35 °C-ig (95 °C)                       |    A maximális hőmérsékletet 1 °C/300 m (1 °F/547 Ft) csökkenti 950 m felett (3 117 Ft).    |
|    35 °C – 40 °C (95 °f – 104 °F)            |    A maximális hőmérsékletet 1 °C/175 m (1 °F/319 Ft) csökkenti 950 m felett (3 117 Ft).    |
|    40 °C-ról 45 °C-ra (104 °F – 113 °F)           |    A maximális hőmérsékletet 1 °C/125 m (1 °F/228 Ft) csökkenti 950 m felett (3 117 Ft).    |

## <a name="next-steps"></a>Következő lépések

- [Az Azure Stack Edge Pro üzembe helyezése](azure-stack-edge-deploy-prep.md)