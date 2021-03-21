---
title: Microsoft Azure Stack Edge Pro GPU technikai specifikációkkal és megfelelőséggel | Microsoft Docs
description: Ismerje meg az Azure Stack Edge Pro-eszköz GPU-val való technikai specifikációit és megfelelőségét
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 937f0d1feb0c45c1c158b5e88daf268aeb383509
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034744"
---
# <a name="technical-specifications-and-compliance-for-azure-stack-edge-pro-with-gpu"></a>A GPU-val Azure Stack Edge Pro műszaki specifikációi és megfelelőségi követelményei 

A Azure Stack Edge Pro és egy beépített grafikus processzorral (GPU) kapcsolatos hardver-összetevők megfelelnek a jelen cikkben ismertetett technikai előírásoknak és szabályozási előírásoknak. A műszaki specifikációk a hardverek, az áramellátási egységek (PSUs), a tárolókapacitás, a bekerítések és a környezeti szabványok leírását írják le.

## <a name="compute-and-memory-specifications"></a>Számítási és memória-specifikációk

Az Azure Stack Edge Pro-eszköz a következő specifikációkkal rendelkezik a számításhoz és a memóriához:

| Specifikáció           | Érték                  |
|-------------------------|----------------------------|
| CPU                     | 2 X Intel Xeon Silver 4214 (Cascade-tó) CPU<br> 24 fizikai mag (12/CPU)<br>48 logikai mag (vCPU) (24 processzor)       |
| Memória                  | 128 (8x16 GB) GB RAM <br> Dell-kompatibilis 16 GB PC4-23400 DDR4-2933Mhz 2Rx8 1.2 v ECC regisztrált RDIMM       |


## <a name="compute-acceleration-specifications"></a>Számítási gyorsítási specifikációk

A grafikus processzort (GPU) minden olyan Azure Stack Edge Pro-eszköz tartalmazza, amely lehetővé teszi a Kubernetes, a Deep learning és a gépi tanulási forgatókönyvek használatát.

| Specifikáció           | Érték                  |
|-------------------------|----------------------------|
| GPU   | Egy vagy két nVidia T4 GPU <br> További információ: [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).| 


## <a name="power-supply-unit-specifications"></a>Tápegység-egységek specifikációi

Az Azure Stack Edge Pro-eszköz két 100-240 V-os tápegységgel (PSUs) rendelkezik, nagy teljesítményű ventilátorokkal. A két PSUs redundáns energiaellátási konfigurációt biztosít. Ha egy PSU meghibásodik, az eszköz továbbra is általában a másik PSU-gépen működik, amíg le nem cseréli a hibás modult. A következő táblázat a PSUs műszaki specifikációit sorolja fel.

| Specifikáció           | 750 W PSU                  |
|-------------------------|----------------------------|
| Maximális kimeneti teljesítmény    | 750 W                     |
| Gyakoriság               | 50/60 Hz                   |
| Feszültség-tartomány kiválasztása | Automatikus hatókör: 100-240 V AC |
| Gyors csatlakoztatás           | Yes                        |


## <a name="network-interface-specifications"></a>Hálózati adapterek specifikációi

Az Azure Stack Edge Pro-eszköz hat hálózati adapterrel rendelkezik, a PORT1-PORT6.

| Specifikáció           | Description                 |
|-------------------------|----------------------------|
|  Hálózati adapterek    | **2 X 1 GbE-illesztő** – 1 felügyeleti felület az 1. port a kezdeti beállításhoz van használatban, és alapértelmezés szerint statikus. A kezdeti beállítás befejeződése után bármely IP-címmel rendelkező adatkapcsolatot használhat. Alaphelyzetbe állításkor azonban a felület visszaáll a statikus IP-címekre. <br>A másik 2. port a felhasználó által konfigurálható, adatátvitelre is használható, és alapértelmezés szerint a DHCP. <br>**4 X 25 GbE interfész** – ezeket az adatillesztőket, a 3-as portot a 6-os porton keresztül, a felhasználó DHCP-ként (alapértelmezett) vagy statikusként is konfigurálhatja. Akár 10 GbE interfészként is működhetnek.  | 

Az Azure Stack Edge Pro-eszköz a következő hálózati hardverrel rendelkezik:

* **Egyéni Microsoft Qlogic Cavium 25G NDC adapter** – 1. port a 4. porton keresztül.
* **Mellanox Dual port 25G ConnectX-4 csatornás hálózati adapter** -5. és 6. port.

A Mellanox kártya részletei:

| Paraméter           | Leírás                 |
|-------------------------|----------------------------|
| Modellezés    | ConnectX®-4 LX EN hálózati csatolókártya                      |
| Modell leírása               | 25 GbE Dual-port SFP28; PCIe 3.0 x8; ROHS R6                    |
| Eszköz részének száma (R640) | MCX4121A – ACAT  |
| PSID (R640)           | MT_2420110034                         |

A hálózati kártyák által támogatott kábelek, kapcsolók és adóvevők teljes listájáért keresse fel a következőt:

- [Qlogic Cavium 25G NDC adapter együttműködési mátrixa](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
- [Mellanox Dual port 25G ConnectX-4 csatornás hálózati adapter kompatibilis termékek](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).  

## <a name="storage-specifications"></a>Tárolási specifikációk

Az Azure Stack Edge Pro-eszközök öt 2,5 "NVMe DC SSD-P4610 rendelkeznek, amelyek mindegyike 1,6 TB kapacitással rendelkezik. A rendszerindító meghajtó 240 GB SATA SSD. Az eszköz teljes felhasználható kapacitása körülbelül 4,19 TB. A következő táblázat felsorolja az eszköz tárolókapacitását.

|     Specifikáció                          |     Érték             |
|--------------------------------------------|-----------------------|
|    SSD-NVMe száma                     |    5                  |
|    Egyetlen NVMe SSD-kapacitás                |    1,6 TB             |
|    Rendszerindítási SATA SSD-meghajtók (SSD)      |    1                  |
|    Rendszerindítási SSD-kapacitás                       |    240 GB             |
|    Teljes kapacitás                          |    8,0 TB             |
|    Teljes felhasználható kapacitás                   |    ~ 4,19 TB          |
|    RAID-konfiguráció                      |    Közvetlen tárolóhelyek tükrözés és paritás együttesével  |
|    SAS-vezérlő                          |    HBA330 12 GB/s     |

<!--Remove based on feedback from Ravi
## Other hardware specifications

Your Azure Stack Edge Pro device also contains the following hardware:

* iDRAC baseboard management
* Performance fans
* Custom ID module-->

## <a name="enclosure-dimensions-and-weight-specifications"></a>A bekerítés méretei és súlyozási jellemzői

A következő táblázatok a méretek és a súlyozás különböző területekre vonatkozó specifikációit sorolja fel.

### <a name="enclosure-dimensions"></a>Bekerítés méretei

A következő táblázat a 1U-eszköz bekerítésének méretét mutatja be milliméterben és hüvelykben.

|     Ház     |     Milliméter     |     Hüvelyk     |
|-------------------|---------------------|----------------|
|    Magasság         |    44,45            |    1,75 "       |
|    Szélesség          |    434,1            |    17,09 "      |
|    Hossz         |    740,4            |    29,15 "      |

A következő táblázat a szállítási csomag dimenzióit mutatja be milliméterben és hüvelykben.

|     Csomag     |     Milliméter     |     Hüvelyk     |
|-------------------|---------------------|----------------|
|    Magasság         |    311,2            |    12,25 "          |
|    Szélesség          |    642,8            |    25,31 "          |
|    Hossz         |    1 051,1          |    41,38 "          |

### <a name="enclosure-weight"></a>Ház súlya

Az alkalmazáscsomag súlya 66 lbs. és két személyt igényel a kezeléséhez. Az eszköz súlya a ház konfigurációjától függ.

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
| Behatolás elleni védelem (IP)                 |    Az ilyen típusú, beltéri használatra szánt berendezések általában nem teszteltek a behatolás elleni védelemre (a szilárd anyagok és a folyadékok elleni védelem egy elektromos ház esetében). A gyártó biztonsági értékelése a IPXO (nincs beáramló védelem) jeleníti meg.  |
|    Maximális magasság, működési        |    3048 méter (10 000 láb), amely az üzemeltetési hőmérséklet által meghatározott maximális működési hőmérsékletet határozza meg, az [üzemi hőmérséklet-minősítési specifikációk](#operating-temperature-de-rating-specifications)alapján.                                                                                |
|    Maximális magasság, nem működő    |    12 000 méter (39 370 láb)                                                                                                                                                                                         |
|    Sokk, működési                   |    6 G 11 ezredmásodpercnél 6 percnél                                                                                                                                                                         |
|    Sokk, nem működő               |    71 G 2 ezredmásodpercnél 6 tájolással                                                                                                                                                                           |
|    Vibráció, működési               |    0,26 G<sub>RMS</sub> 5 hz – 350 Hz véletlenszerű                                                                                                                                                                                     |
|    Vibráció, nem működő           |    1,88 G<sub>RMS</sub> 10 hz – 500 Hz 15 percre (mind a hat oldal tesztelte.)                                                                                                                                                  |
|    Tájolás és csatlakoztatás             |    Standard 19 – Rack csatlakoztatása (1U)                                                                                                                                                                                       |
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

[Az Azure Stack Edge Pro üzembe helyezése](azure-stack-edge-gpu-deploy-prep.md)
