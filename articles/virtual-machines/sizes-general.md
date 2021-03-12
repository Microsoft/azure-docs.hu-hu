---
title: Azure VM-méretek – általános célú | Microsoft Docs
description: Felsorolja az Azure-beli virtuális gépekhez elérhető különböző általános célú méreteket. A vCPU, az adatlemezek és a hálózati adapterek számával, valamint a tárolási teljesítményével és a hálózat sávszélességével kapcsolatos információkat sorolja fel ebben a sorozatban.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: 46a9b7e630001161fbf2790cfb9be474379864b6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557624"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Általános célú virtuálisgép-méretek

Az általános célú virtuálisgép-méretek kiegyensúlyozott processzor-memória arányt biztosítanak. Ideális választások a teszteléshez és a fejlesztéshez, a kicsi és közepes adatbázisokhoz, illetve az alacsony és közepes forgalmú webkiszolgálókhoz. Ez a cikk az általános célú számítástechnikai ajánlatokkal kapcsolatos információkat tartalmaz.

- A [Av2 sorozatú](av2-series.md) virtuális gépek számos különböző hardvereszközön és processzoron is üzembe helyezhetők. Az A sorozatú virtuális gépeknél a CPU-teljesítmény és a memória-konfigurációk a legmegfelelőbbek a belépési szintű munkaterhelésekhez, például a fejlesztéshez A méretük a hardvernek megfelelően szabályozott, hogy egyenletes processzorteljesítményt nyújtsanak a futó példány számára, a futtató hardvertől függetlenül. Az adott méretet futtató fizikai hardver meghatározásához kérdezze le a virtuális hardvert a virtuális gépen belülről. Ilyenek például a fejlesztési és tesztelési kiszolgálók, az alacsony forgalmú webkiszolgálók, a kis-és közepes adatbázisok, a bizonyítási fogalmak és a kódok tárházai.

  > [!NOTE]
  > Az A8-as, A9-es, A10-es virtuális gépek a 3/2021-es kivonulásra vannak tervezve. További információ: [HPC áttelepítési útmutató](https://azure.microsoft.com/resources/hpc-migration-guide/). Ezek a virtuálisgép-méretek az eredeti "A_v1" sorozatban találhatók, nem "v2".

- [B sorozat – feltört](sizes-b-series-burstable.md) A virtuális gépek olyan számítási feladatokhoz ideálisak, amelyeknek nincs szükségük a CPU teljes teljesítményére, például a webkiszolgálók, a kisméretű adatbázisok és a fejlesztési és tesztelési környezetek számára. Ezek a számítási feladatok általában feltört teljesítménnyel kapcsolatos követelményekkel rendelkeznek. A B sorozat biztosítja, hogy ezek az ügyfelek a virtuálisgép-méretet olyan tudatos alapszintű teljesítmény mellett vásárolják meg, amely lehetővé teszi, hogy a virtuálisgép-példány krediteket hozzon létre, amikor a virtuális gép az alapteljesítménynél kevesebbet használ. Ha a virtuális gép felhalmozott Kredittel rendelkezik, a virtuális gép a CPU alapkonfigurációja felett akár 100%-ot is megadhat, ha az alkalmazás a nagyobb CPU-teljesítményt igényli.

- A [Dav4 és a Dasv4 sorozat](dav4-dasv4-series.md) új méretek az AMD 2.35 GHz EPYC<sup>TM</sup> 7452 processzorát egy többszálas konfigurációban, amely akár 256 MB L3-os gyorsítótárat is felhasznál, amely 8 MB-ot tesz elérhetővé az összes 8 mag számára az általános célú számítási feladatok futtatásához. A Dav4-sorozat és a Dasv4-sorozat ugyanazokkal a memória-és lemez-konfigurációval rendelkezik, mint a D & Dsv3 sorozat.

- [DV4 és Dsv4 sorozat](dv4-dsv4-series.md) A DV4 és a Dsv4 sorozat az Intel® Xeon® Platinum 8272CL (Cascade Lake) processzorokon fut egy Hyper-threaded konfigurációban, és jobb értéket biztosít a legtöbb általános célú számítási feladathoz. Ez az összes Core Turbo órajel 3,4 GHz-es sebességgel rendelkezik.

- [Ddv4 és Ddsv4 sorozat](ddv4-ddsv4-series.md) A Ddv4 és a Ddsv4 sorozat az Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) processzorokon fut egy Hyper-threaded konfigurációban, és jobb ár-arányt biztosít a legtöbb általános célú munkaterheléshez. A szolgáltatás a 3,4 GHz-es és az Intel [ &reg; Turbo Boost Technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), az [Intel &reg; Hyper-Threading technológia](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) és az [Intel &reg; Advanced Vector Extensions 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)minden alapvető Turbo órajelét tartalmazza. Emellett támogatják az [Intel &reg; Deep learning fellendítését](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)is. Ezek az új virtuálisgép-méretek 50%-kal nagyobb helyi tárterülettel rendelkeznek, valamint jobb helyi lemez-IOPS is használhatók, mint a [Dv3-/Dsv3](./dv3-dsv3-series.md) -méretek és a Gen2-alapú virtuális [gépek](./generation-2.md).

- [Dv3 és Dsv3 sorozat](dv3-dsv3-series.md) A virtuális gépek 2. generációs Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz-es (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), vagy az Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processzorok egy Hyper-threaded konfigurációban, így jobb az általános célú számítási feladatokhoz. A memória ki lett bontva (~ 3,5 GiB/vCPU – 4 GiB/vCPU), míg a lemez-és hálózati korlátokat a rendszer alapszinten igazította ki, hogy az a feleznie-re legyen igazítva. A Dv3-sorozat már nem rendelkezik a D/Dv2 sorozat nagy memóriabeli virtuálisgép-méretével, ezeket a rendszer áthelyezte a memória-optimalizált [Ev3 és Esv3-sorozatba](ev3-esv3-series.md).

- [Dv2 és Dsv2 sorozat](dv2-dsv2-series.md) A virtuális gépek, amelyek az eredeti D sorozaton alapulnak, hatékonyabb CPU-és optimális CPU-memória-konfigurációt biztosítanak, így a legtöbb éles számítási feladathoz megfelelőek. A Dv2 sorozat körülbelül 35%-kal gyorsabb a D sorozatnál. A Dv2 sorozat 2. generációs Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), vagy az Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processzorok az Intel Turbo Boost Technology 2,0. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.

- A [DCv2 sorozat](dcv2-series.md) a nyilvános felhőben feldolgozott adatok és kódok titkosságának és integritásának védelmét is lehetővé teszi. Ezeket a gépeket a SGX ENKLÁVÉHOZ technológiával rendelkező Intel XEON E-2288G processzor legújabb generációja támogatja. Az Intel Turbo Boost technológiával ezek a gépek akár 5,0 GHz-re is felmehetnek. A DCv2 sorozat példányai lehetővé teszik, hogy az ügyfelek biztonságos enklávé-alapú alkalmazásokat hozzanak létre a kódok és az adataik használat közbeni védelméhez.

## <a name="other-sizes"></a>Egyéb méretek

- [Számításoptimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.

További információ arról, hogy az Azure Hogyan nevezi el a virtuális gépeket: az [Azure virtuálisgép-méretek elnevezési konvenciói](./vm-naming-conventions.md).
