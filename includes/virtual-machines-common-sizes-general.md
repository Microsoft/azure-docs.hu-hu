---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 10/08/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 8297e324f03d263acaa30dfa2147f7ee713bd265
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48904645"
---
Általános célú virtuális gépek méreteit adja meg a kiegyensúlyozott Processzor-memória arány. Ideális választás tesztelési-fejlesztési feladatokhoz, kis és közepes méretű adatbázisokhoz, valamint kis és közepes adatforgalmú webkiszolgálókhoz. Ez a cikk ismerteti a vcpu-k, az adatlemezeket és a hálózati adapterek, valamint az ennél a csoportosításnál méretek esetében a tárterületek átviteli sebességének számát. 

- A [DC-sorozat](#dc-series) új családba tartozó virtuális gépek az Azure-ban, amelyek segítségével a bizalmas és az adatok sértetlenségének védelme és a kód a nyilvános felhőben feldolgozása közben. Ezek a gépek élvezik legújabb generációja, 3,7 GHz-es Intel XEON E - 2176G processzor SGX technológiával. Ezek a gépek az Intel Turbo Boost Technology az 4.7 GHz meg. DC-sorozat példányai ügyfeleink a kód és az adatok védelmét, miközben használatban van a biztonságos enklávé-alapú alkalmazásokat fejleszthet.

- Az Av2-sorozat virtuális gépei különféle hardvertípusokon és processzorokon is telepíthető. Az A sorozatú virtuális gépek a belépő szintű számítási feladatokhoz – például fejlesztéshez és teszteléshez – igazodó CPU-teljesítmény- és memóriakonfigurációkkal rendelkeznek. A méretük a hardvernek megfelelően szabályozott, hogy egyenletes processzorteljesítményt nyújtsanak a futó példány számára, a futtató hardvertől függetlenül. Az adott méretet futtató fizikai hardver meghatározásához kérdezze le a virtuális hardvert a virtuális gépen belülről.

  Használati példák közé tartozik a fejlesztési és tesztelése kiszolgálók, kis forgalmú webkiszolgálók, kicsi, közepes méretű adatbázisokhoz, megvalósíthatósági próbamegoldásokhoz és kódtárházak.

- A Dv2-sorozat az eredeti D-sorozat következő generációját képviselő funkciókat egy nagyobb teljesítményű CPU és optimális CPU-memória konfigurációs teszi őket a legtöbb éles számítási feladathoz. A Dv2-sorozathoz használt processzor mintegy 35%-kal gyorsabb, mint a D-sorozathoz használt processzorok. -Alapú, a legújabb generációs Intel Xeon® E5-2673 v3 (Haswell) 2,4 GHz vagy E5-2673 v4 (Broadwell) 2,3 GHz processzor, és az Intel Turbo Boost Technology 2.0-akár 3,1 GHz-es. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.

- A Dv3-sorozat funkciói a 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzor- és a legújabb 2,3 GHz-es Intel XEON® E5-2673 v4 (Broadwell) processzoron többszálú konfiguráció esetén a legtöbb általános célú számítási feladatok esetében jobb értékajánlat biztosít.  Memória (a ~3.5 GiB/vCPU, 4 GB/vcpu-ira) ki lett terjesztve, amíg a lemez és a hálózati korlátok hozzá lett igazítva, helyezze át a Hyper-Threading technológia igazodva magonként alapon.  A Dv3 már nem rendelkezik a D/Dv2 családok magas memóriahasználat Virtuálisgép-méretek, ezek az új Ev3 termékcsalád lettek áthelyezve.

  A D-sorozat használati Példák többek között a nagyvállalati alkalmazások, relációs adatbázisok, memórián belüli gyorsítótárazás és elemzés. 
  

## <a name="b-series"></a>B sorozat

A Premium Storage: támogatott

Prémium szintű Storage gyorsítótárazási: Nem támogatott

A B sorozat – adatlöket-kezelés virtuális gépek ideálisak a nagy számítási feladatokhoz, amelyek nem a teljes teljesítmény, a processzor folyamatos kell például a webkiszolgálók, kis méretű adatbázisokhoz, és a fejlesztési és tesztelési környezetek. Ezeket a feladatokat jellemzően rendelkeznek – adatlöket-kezelés teljesítményre vonatkozó követelmények. A B sorozat teszi lehetővé ezen ügyfelek vásárolhat egy tudatában alapkonfiguráció árteljesítménnyel, amely lehetővé teszi, hogy a Virtuálisgép-példány krediteket felépíthető, a virtuális gép kevesebb, mint az alapszintű teljesítmény használatakor a Virtuálisgép-méretét. A virtuális gép kredit keletkezett, amikor a a virtuális Géphez is megnövelheti arra legfeljebb 100 %-a CPU-használatát, amikor az alkalmazás magasabb processzorteljesítményre van szükség a virtuális gép eredeti felett.

Használati Példák többek között a fejlesztési és tesztelési kiszolgálók, alacsony adatforgalmú webkiszolgálók, kis méretű adatbázisokhoz, mikroszolgáltatásokhoz, kiszolgálók megvalósíthatósági próbamegoldásokhoz, lemezképfájl-kiszolgálókhoz.


| Méret             | vCPU  | Memória: GiB | Helyi SSD: GiB | Az alapszintű alap Teljesítményoptimalizált | A kreditek banki / óra | Maximális banki kreditek | Adatlemezek max. száma | Maximális helyi lemez teljesítményoptimalizált: IOPS / MBps | Maximális lemez teljesítményoptimalizált teljesítményterhelési: IOPS / MBps | Hálózati adapterek maximális száma |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 6                  | 144            | 2                                      | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 12                 | 288            | 2                                      | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |


## <a name="dsv3-series-sup1sup"></a>A Dsv3 sorozatú <sup>1</sup>

ACU: 160–190

A Premium Storage: támogatott

Prémium szintű Storage gyorsítótárazási: támogatott

A Dsv3 sorozatú méretek alapulnak a 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzor- és a legújabb 2,3 GHz-es Intel XEON® E5-2673 v4 (Broadwell) processzoron, amely az Intel Turbo Boost Technology 2.0 3,5 GHz- és prémium szintű storage használata. A Dsv3-sorozat méretei vCPU, memória és ideiglenes tárterület kombinációját nyújtják a legtöbb éles számítási feladathoz.


| Méret             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4,000 / 32 (50)                                                       | 3,200 / 48                                | 2 / 1,000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8,000 / 64 (100)                                                      | 6,400 / 96                                | 2 / 2,000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16,000 / 128 (200)                                                    | 12,800 / 192                              | 4 / 4,000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32,000 / 256 (400)                                                    | 25,600 / 384                              | 8 / 8,000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64,000 / 512 (800)                                                    | 51,200 / 768                              | 8 / 16,000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128,000 / 1024 (1600)                                                    | 80,000 / 1200                              | 8 / 30,000                                               |

<sup>1</sup> Dsv3 sorozatú virtuális gépek Intel® Hiperszálkezelési technológiát funkció

## <a name="dv3-series-sup1sup"></a>Dv3-sorozat <sup>1</sup>

ACU: 160–190

A Premium Storage: Nem támogatott.

Prémium szintű Storage gyorsítótárazási: Nem támogatott

Dv3-sorozat méretei alapulnak a 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzor- és 2,3 GHz-es Intel XEON® E5-2673 v4 (Broadwell) processzoron, amely az Intel Turbo Boost Technology 2.0 3,5 GHz-es érhető el. A Dv3-sorozat méretei vCPU, memória és ideiglenes tárterület kombinációját nyújtják a legtöbb éles számítási feladathoz.

Az adatlemezes tárolást a virtuális gépektől függetlenül számlázzuk. Prémium szintű tárolólemezek használatához Dsv3-méreteket vegyen igénybe. A Dsv3-méretek díjszabása és számlázási mérőszámai megegyeznek a Dv3-sorozatéval. 


| Méret            | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Hálózati adapterek max. száma/hálózati sávszélesség |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3000/46/23                                               | 2 / 1,000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6000/93/46                                               | 2 / 2,000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12000/187/93                                             | 4 / 4,000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24000/375/187                                            | 8 / 8,000                    |
| Standard d32 v3 | 32        | 128          | 800            | 32             | 48000/750/375                                            | 8 / 16,000                             |
| Standard D64 v3 | 64        | 256          | 1600            | 32             | 96000/1000/500                                            | 8 / 30,000                             |

<sup>1</sup> Dv3-sorozat virtuális gép a Intel® Hiperszálkezelési technológiát funkció


## <a name="dsv2-series"></a>DSv2-sorozat

ACU: 210–250

A Premium Storage: támogatott

Prémium szintű Storage gyorsítótárazási: támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3.5 |7 |4 |4,000 / 32 (43) |3,200 / 48 |2 / 750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8,000 / 64 (86) |6,400 / 96 |2 / 1500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16,000 / 128 (172) |12,800 / 192 |4 / 3000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32,000 / 256 (344) |25,600 / 384 |8 / 6000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64,000 / 512 (688) |51,200 / 768 |8 / 12000 |



## <a name="dv2-series"></a>Dv2-sorozat

ACU: 210–250

A Premium Storage: Nem támogatott.

Prémium szintű Storage gyorsítótárazási: Nem támogatott

| Méret           | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma | Átviteli sebesség: IOPS | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 4              | 4x500            | 2 / 750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 8              | 8x500            | 2 / 1500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             | 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32             | 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             | 64x500           | 8 / 12000                                    |


## <a name="av2-series"></a>Av2-sorozat

ACU: 100

A Premium Storage: Nem támogatott.

Prémium szintű Storage gyorsítótárazási: Nem támogatott


| Méret            | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16x500             | 8 / 2000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16x500             | 8 / 2000                     |

<br>


## <a name="dc-series"></a>DC-sorozat

A Premium Storage: támogatott

Prémium szintű Storage gyorsítótárazási: támogatott



| Méret          | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000-es / 32 (43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |







